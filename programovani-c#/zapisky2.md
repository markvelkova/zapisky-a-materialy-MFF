# C# zápisky 2. přednáška

## hodnotový nebo referennčí?
Hodnotovost/referenčnost typu bychom měli volit podle toho, jaká sémantika dává smysl

```
Person John = new Person {firstName = "John", lastName = "Lennon"};
changeLastName(John, "McCartney"); 
//udělá se kopie, ale jen té adresy, pořád ukazuje na stejnou instanci

static void changeLastName(Person person, string lastName) {
	person.lastName = lastName;
}
```
Výše dávala smysl referenční sémantika, kdyby to byla struktura, původní John by se nezměnil. Stejně při tahání z databáze atd, všechny funkce pracují s odkazem na pořád ten samý objekt.

```
class Color {
	public byte r;
	public byte g;
	public byte b;
}
Color red = new Color {r = 255, g = 0, b = 0};
```
Jenže, když potom nastavím dvěma věcem stejnou červenou na začátku, ale v jednom z nich ji potom mění, mění se i barva druhé věci. Tady by dávalo smysl použít struct.

## var
Neznamená to, že se tam za runtimu můžou strkat všechny možné věci, překladač odvodí sám typ proměnné, je to pořád staticky typované. Je to jen zkratka.

`var x;` hodí error

`var y = {1,2,3};` taky error

Pozor na ňoumy, hlavně při použití var u deklarace funkcí
```
var GetPerson() {...} //může klidně vracet třeba string, ne Person
```

## new s prázdnými závorkami
```
//následující je ekvivalentní
var l = new List<int>();
List<int> l = new();
```

## read/write
```
struct S {
	public int a; // r/w
	public readonly int b; // jen read po inicializaci
	public readonly C c; //pozor!
}
```
Ale pozor, nemůžeme měnit jen obsah té proměnné, **je-li referenční** (jako např. třída C výše), potom **nemůžu měnit tu adresu, kam to ukazuje, ale ten objekt sám se měnit může!**


## properties - automaticky implementované

```
struct S {
	public int A {get; set;}
}
```
Překladač vygeneruje privátní *backing field*, k němu se nedá přistupovat, getter vrací typ té vlastnosti, setter nevrací nic, ale bere parametr typu vlastnosti.
Můžu si to napsat sama, ale často chci prostě jen to základní, ušetřím si čas.

## proč používat properties?
* Je to jednoduché, překladač si generuje volání getter/setter, já to používám pořád stejně. 
* Abychom byli připraveni na scénáře v budoucnosti, je lepší všechno používat jako properties.
```
public int pc {get; private set;}
```
* Můžu nastavit různou viditelnost getteru a setteru.
* Můžu se kdykoli rozhodnout přepsat si vlastní getter a seter, ale ve zbytku kódu to nebude nijak poznat, uživatel nic nepozná. 
* Dává nám to mnohem větší možnosti (příklad s dungeonCrawlerem). Je přehlednější potom psát `C.num = 5` než `C.setNum(5);`. 
* Navíc volání je rychlejší než volání metod jako `C.setNum(5);`

## automaticky generovaný bezparametrický konstruktor
Dogeneruje se tam sám, můžeme se spolehnout na nulovou inicializaci.
```
class C {
	int a; // 0
	bool b; // false
	double c; // +0.0
	C d; //zatím plné samých nul
}

C x1 = new C();
```
Alokátor na GC kontrolované haldě zajistí, že jsou všude nuly (nedělá to ten konstruktor).

Nějaký konstruktor se volá vždy, přeskočit volání konstruktoru se nedá.

**readonly fieldy se dají měnit jen za běhu konstruktoru**, nejde to ale z funkce volané v konstruktoru, jen v konstruktoru samotném.

## konstrukce objektu
`konstruktor + inicializace { … }`

Co když chci, aby se dalo přistoupit na readonly field nejen z konstruktoru, ale i z inicializace? **init**
```
public int pc {get; init;}
```

## required fieldy/vlastnosti
* musí se nastavit během konstrukce `konstruktor + inicializace { … }`
```
record class Person {
	public required string firstName { get; set; }
	public string lastName{ get; set; }
}

var p1 = new Person {firstName = "Allen"}
var p2 new Person {lastName = "Ginsberg"} //error - nenastavena required vlastnost
```

Chceme donutit ňoumy **za překladu**, aby to nepoužívali blbě.

## primary constructor
```
struct/class X(int a, int b, int c) {
	public bool GetBool() {
		return !b; 
	}
}
```
Zkratka konstruktoru, libovolný parametr můžu použít i mimo ten konstruktor. V takovém případě se **zachytí** (protože jsou to lokální proměnné konstruktoru) a vyrobí se pro ně backing field. Omylem použití něčeho vyrábí nové fieldy. 

Nešetří místo, šetří programování.

```
record class X(int a, int b, int c) {
	public bool GetBool() {
		return !b; 
	}
}
```

## primary constructor v record class
* ZE VŠECH PARAMETRŮ SE UDĚLAJÍ VLASTNOSTI
* jsou **init only**

## primary constructor v record struct
* ZE VŠECH PARAMETRŮ SE UDĚLAJÍ VLASTNOSTI
* jsou **read/write**

## readonly record struct
* stane se z ní vlastně record class chováním

## nullable **value** types
```
int? a = null;
```
* můžu přiřadit všechny hodnoty + null
* vyrobí se nullable struktura, má hasValue() a getValue()
* automaticky to za nás řeší překladač
