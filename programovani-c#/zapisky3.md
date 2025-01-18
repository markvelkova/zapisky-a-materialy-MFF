# C# zápisky 3. přednáška

## nullable
Velký rozdíl mezi hodnotovými a referenčními typy.

```
class A {

}

A a1 = new A();
A a1 = null;
```
Defaultní chování, že do referenčního typu můžu narvat null je trochu divné. 
```
A a1; //never should be null
```
To tak úplně nefunguje

## nullable reference types
* Když to zapnu, tak referenční z hlediska nullovatelnosti fungují stejně jako hodnotové. Přiřazení null do referenční proměnné bez otazníčku vyvolá chybu. 
* V novém projektu je to automaticky zapnuté ve VS, ale vypnuté v Recodexu!
`#nullable enable`
* Proměnné vypadají úplně stejně (na rozdíl od nullable hodnotových), jen se za překladu kontroluje přítomnost null.
* **Nesmí se dereferencovat nula**, přišlo by null reference exception. To je chyba, která vznikne, až když ten řádek proběhne -> spadne to zákazníkovi. Proto je lepší používat `# nullable enable`. Navíc po
kud se tam ňouma pokusí narvat null, pak se jemu stane překladová chyba, nám ne.
* překladač často hází false positives - když si není jistý, dá jen warning, když je budeme ignorovat, pořád to může za runtimu spadnout
* mnoho funkcí (např toString()) se chová rozumně a neudělá nic, pokud je tam null a nemá tam být, takže to nespadne, ač by vlastně mělo

## duckTyping v Pythonu
Když mají dva objekty stejnou metodu, potom je můžu dosadit do funkce, která volá tu metodu. Té funkci je jedno, jestli je am kachna nebo labuť, pokud obě umí nějaké MakeSound(). 
To funguje díky tomu, že je to dynamicky typovaný jazyk.

Snadno se používá, ale chyba se odhalí až za běhu, nikoli za překladu.

## duckTyping v C#
Udělám si nějaký kontrakt, slíbím, že mám všechno, co ten kontrakt říká (a klidně víc). Když nějakou věc dostanu pod tím kontraktem, můžu na to volat jen věci z toho kontraktu.

## interface
Je to **implicitně public**
```
interface IWhatever {
	//pouze metody nebo properties
	//nesmí být fieldy
	int m(int a, int b); //jména parametrů nejsou důležitá
}

class A : IWhatever {
	public int m (int f, int g) {
		...
	}
	public bool f() {}
}
```
* **interface nemá instanci**!
```
IWhatever I1 = new A();
I1.m(2,3); //ok
I1.f(); //error
```
## co se děje při překladu
* CLR si pamatuje jméno typu a všechny metody, které ten typ má. Pamatuje si adresy těch strojových kódů
```
class A : IWhatever {
	public int m (int f, int g) {
		...
	}
	public bool f() {}
}

class B1 {
	public int m (int f, int g) {
		...
	}
	public bool f() {}
}

class B2 : B1, IWhatever {}
```
* B2 má odkaz na místo, kde je strojový kód metody B1.m
```
IWhatever i1 = new A();
//do i1 se přiřadí instance A

i1.m(10,20);
//kouknu do interfaceové tabulky IWhatever v A
//volá se A.m(int, int)

i1 = new B2();
//do i1 se přiřadí instance B2, změní se reference

i1.m(10,20)
//kouknu do interfaceové tabulky IWhatever v B2
//volá se zděděné B1.m(int, int)
```
* vyplní se **interfaceová tabulka** odkazy na konkrétní metody
* do té se potom koukám při volání funkcí na i1
* neřeším, co se v té proměnné dělo, koukám jen na typ

```
class C1 : IWhatever {
	public int m(int a, int b) {
		return a+b+2000;
	}
}
```

```
class C2 : C1 {
	public new int m(int a, int b) 	{
		return a/b;
	}
}
```
pořád jsme zdědili tu původní, **nejde nezdědit něco**. Udělá se *member hiding*

```
class C3 : C1, IWhatever {
	public new int m(int a, int b) 	{
		return a - b;
	}
}
```
Má metodu C1.m(int int), je member hideovaná, má novou C3.m(int int)

```
C1 c1 = new C1();
c1.m(10,20);
// volá se C1.m(int, int), jde to přímo, za runtime se nic nevymýšlí

C2 c2 = new C2();
c2.m(10,20);
// jsme v typu C2, jde to přímo, volá se C2.m(int, int)

c1 = c2;
c1.m(10,20);
// řešíme typ, co je to za typ, je to C1, voláme member hideovanou C1.m(int, int)

IWhatever i1 = new C1();
i1.m(10,20);
// koukneme do interfaceové tabulky pro IWhatever v C1, vede to na C1.m(int, int)

i1 = new C3();
i1.m(10,20);
// kouknu do C3 interfaceové tabulky pro IWhatever, volá se C3.m(int, int)

```

```
class C4 : C2, IWhatever {
}
```
zdědili jsme C2.m(int, int), do interfaceové tabulky se vyplní C2.m(int, int)

```
i1 = new C4();
i1.m(10,20);
// volá se C2.m(int, int);
```

* **pokud předek implementoval interface, pak ho implementujeme automaticky taky a dědíme i tabulku**
