# C# zápisky 5. přednáška

## konstruktory
```
class A {
	public int v = 10;
}
```
* tohle přiřazení je ve všech konstruktorech včetně bezparametrického
```
class A {
	public static int v = 10;
}
```
* statický field je jen jeden pro všechny instance, inicializuje class constructor, pokud nemáme, vygeneruje se za nás

## class constructor
* volá CLR, když je potřeba 
* nedá se to volat ručně
* jak to vypadá?
```
class B {
	static B() { … } //bez viditelnosti
}
```
* pokud si napíšeme vlastní, stejně se nejdřív volá ten generovaný s inicializací statických fieldů
* volají se, až když je to nutné
	* tedy když v CIL se poprvé pracuje s tím typem, JIT před to první použití dogeneruje zavolání class constructoru


```
class A {
	int x = 10;
	public A(int argX)  {} //konstruktor
	public int f() {} //instanční metoda
	static A() {} //class constructor
}

class B : A {
	//zdědilo field x a metodu f()
	int y = 20;
	// rozhodně nezdědilo kostruktr A, ani classconstr A
	public B() 
		: this(P.m()) 
	{
		Console.WriteLine("...");
	}
	public B(int x)
		: base(P.m()) {}
	static B() {}
}
```
co se stane, když:
```
B b = new B();
```
* naalokuje se instance b s field x,y, overhead, syncblock, reference na Type B
* zavolá se class kontructor B
* zavolá se bezparametrický konstruktor B
	* `this(..)`
	* můžeme si vybrat jeden z dalších konstruktorů
	* připraví se argumenty (zavolá se funkce
	* zavolá se nejdřív tenhle druhý konstruktor, potom teprve se pokračuje tělem (Console.WriteLine)

tedy `prep args -> call :this(int) ->body`

* platí, že každý konstruktor bude inicializovat fieldy, nakonec se nějaký zavolá, inicializace se dělá jen jednou

```
B b = new B(2);
```
* `prep args -> call :base(int) ->body`
* inicializace y ještě zbývá
* tím, že voláme konstruktor předka, kdyby tam byla volána nějaká virtuální metoda, měl by se volat override z B, ten ale počítá s tím, že má inicializované všechny fieldy, tedy je potřeba inicializovat y takto:
* `init fields -> prep args -> call :base(int) ->body`
* pokud byl tenhle řádek před jakýmkoli objevením A, musel se volat class constructor A

**nemůžeme mít současně this i base** - buď jedno nebo druhé nebo nic, pak se udělá bezparametrický konstruktor předka

## kdy používat nějaké konstanty
* když tam je nějaká myšlenka, například -1 v implementaci textReaderu
* naopak implementační detaily například 2 v diskriminantu ne

## a jak si je vyrobit?
* asi ne do proměnné
* pokud to dám do r/w fieldu, říkáme tím ostatním, že si to můžou měnit, jak chtějí, a my s tím počítáme
* lepší do readonly fieldu
* a co dát to do private? nezapomínáme, že se chráníme i před sebou samými, lepší je readonly
* jak vynutit konstantu interfacem?
    * property jen s getterem
    * je to v určitém smyslu slabší, než readonly
    * kontrakt neslibuje, že je to vždy stejně ve všech instancích
    * zase to ale šetří místo

## readonly static field
* inicializace nejpozději v class constructoru, navíc je to v programu jen jednou
* ale pro každý běh programu může nabývat jiné hodnoty
* chceme to raději jinak

## opravdu konstanta
```
private const int konstanta = 42;
```
* je v metadatech assembly, jinde za runtimu není
* při jiných přístupech se vždy generuje CIL `read(N)`
* tady už se napí ta konstanta rovnou za překladu, v CIL jsou rovnou ty 42
* můžou tam teda být jen věci, které jsou jasné za překladu)

**constant folding**
* optimalizace, kterou dělá už překladač, spočítá rovnou ty konstantové výrazy (konstanta + konstanta)

```
const int X5 = 5;
int x = 10/X5; // rovnou za překladu se vyřeší, v CIL bude hodnota
```

```
int X5 = 5;
int x = 10/X5; // generuje se pro to CIL
```
* **mohou to být jen základní hodnotové typy** (ne struktury, musel by se volat kód konstrukoru) **nebo string**
* na druhou stranu, když je to něco, co budu v přístích verzí programu měnit, jee lepší to dát do readonly fieldu, aby se to při spušění naJITovalo nové a nebylo to zadrátované v CIL

## statický typ
* nesmí se vyrábět ani instance, ani proměnné













