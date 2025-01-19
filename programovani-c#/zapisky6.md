# C# zápisky 6. přednáška

## atributy
```
class A {
	public static void m(int x, int y) { .. }
}
```
* metainformace navíc -public static...
* mění to přístup překladače

## custom attributes
* libovolná třída v assembly dědící od třídy Attribute
```
class CompletelyUselessAttribute : Attribute{
	public CompletelyUselessAttribute(int x) {}
	...
}
```
* je to pořád normální třída, můžu s ní tak pracovat
* pokud použiju:
```
class A {
	public void m1() {}
	[CompletelyUseless(2)] //můžu vynechat suffix attribute
	public void m2() {}
	private int _field;
}
```
* v metadatech pro m1() žádná změna
* v metadatech pro m2() je to napsané v metadatech, jinak nic
* k čemu to je teda dobré? v LS

## užitečné atributy
* **Obsolete** - dáváme najevo ostatním programátorům, že používají zastaralou metodu -> udělá se warning
* StringSyntax  třeba pro zvýraznění v regexech, json v prostředí

## chování výčtových typů
* když si ty hodnoty nastavím na mocniny 2, můžu o používat jako flagy, ale při toString,když am mám třeba 7, vypíše se 7, protože dotnet neví, že to není náhoda
* atribut **Flags** před deklaraci enumu -> vypíše enum věci pro 2,4,1
```
[Flags]
enum E {
	A = 1 << 0; //constant folding
	B = 1 << 1;
	C = 1 << 2;
	All = A | B | C
};
```
* ale pozor! Flags neimplikuje, že jsou to mocniny dvojky!!!

## NUGET packages
* různé přilinkovatelné knihovny/frameworky, přidám do projektu, edit project file pibude package reference
* při překladu probehne nuget restore, stáhne se, nakompiluje etc
* musím ještě připsat using

## SYSTEM.DIAGNOSTICS.STOPWATCH
```
var sw = Stopwatch.StartNew()
 ..
sw.Stop()
```
* ale pozor - první volání metody je i vyJITování, navíc často měříme příliš krátké časové úseky
* lepší používat **atribut Benchmark - ale přepnout do RELEASE!!!**
    

## JIT TIERED COMPILATION
* Tier 0 "quick JIT" neoptimalizuje, jen rychle vygeneruje kód
* Tier 1 "optimized JIT" vygeneruje úplně jinou daleko efektivnější verzi kódu
* defaultně volá quickJIT, metod má counter volání, pokud přesáhne počet, udělá se paralelně reJIT tier 1


## DYNAMIC PGO
* nesbírá to jen statistiku, kolikrát volané, ale i co se tam děje, dělá s ito čárky instance čeho tam byly
* rozdělí optimalizovaný kód na hotpath - pro nejtypičtější scénář, super optimalizované -> devirtualizace virtuální metody
* například nejčastěji tam používám třídu A - zinlinuju to pro A
* cold path pro ostatní scénáře

||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||
--------------------------------------------------------------------------------

## virtuální metody
**virtual** - nový záznam v VMT
**override** - přepsání existujícího záznamu VMT
**abstract** - nový záznam v VMT, ale prázdný           abstract => virtual


## TABULKA VIRTUÁLNÍCH METOD VS TABULKA INTERFACEOVÝCH METOD
* virtuální: odkaz na implemenaci
* interfaceová: odkaz na metodu
* `new` u interfaceových přidá novou metodu, ale 
```
Y dědí od X, new m() 
X x = new Y(); 
x.m()
``` 
* použije se xová, muselo by se přetypovat
``` 
f (X x) {
    x.m() ať dosadím new X() nebo new(Y)
}
f (I1 i1) {
    i1.m() pro new X() x.m pro newY() y.m
}


class C { //jedna metoda, prazdný na nula VMT
    abstract m();
}

class D : C { //kdyby byla prázdná - jedna metoda, prazdný na nula VMT
}

class E : D {
    override m() {...} // jedna metoda, na nula VMT implementace
}

C c = new C();  -> chyba je tady v tom new
c.m() //nullreference - nepřeloží se
``` 
* zakážu instance 
``` 
abstract class C { //zákaz tvoření instancí, jinak to nesouvisí s abstraktní metodou
    abstract m();
}
``` 
``` 
C c = new E();
e.m() v pohodě
``` 

* můžu před D napsat abstract? ano
* můžu před E napsat abstract - ano, ale nebudu moct udělat instanci

``` 
class Animal
    virtual m() // VMT[0] - animal implementace

class Mammal :Animal
    override m() // VMT[0] - zapíše se tam mammal implementace, dědí i to, co znamená
    
class Dog : Mammal
    //zdědil starou VMT[0] - zapíše se tam mammal implementace
    new virtual m() // VMT[1] je ta psí, member hiding, na psovi se bude volat tohle
    
class Beagle : Dog
    override m() //overriduje u psí - na VMT[0] je mammal, na VMT[1] je override té psí

Dog pet = new Beagle()
pet.m() -> volá se Beagle.m()

Animal pet = new Beagle()
pet.m() -> volá se Mammal.m()
``` 
