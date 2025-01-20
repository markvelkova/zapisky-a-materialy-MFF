# C# zápisky 10. přednáška

## tracking reference - bezpečná podmnožina
* když má tracking reference vnořenou životnost v životnosti toho, na co ukazuje

může být argument funkce
* ukazuje na statickou, globální proměnnou ✅
* ukazuje na lokální proměnnou ✅
* ukazuje na GC haldu - vede na objekt o jednu referenci navíc, až na konci životnosti té proměnné se to může uvolnit ✅

#### použití:
bez:
```
void Inc(int x) {x = x + 1}
void f() {
    int val = 3;
    Inc(val); //val == 3, předala se jen kopie
}
```
s:
```
void Inc(ref int x) {x = x + 1}
void f() {
    int val = 3;
    Inc(ref val); //val == 4
}

```
* klíčové slovo `ref`
* automatická dereference při čtení i zápisu
* v c++ se to děje implicitně, pokud někam prostě narvu něco, ale funkce tam má to &něco, mohou vznikat typické programátorské chyby

## předávání parametrů referencemi
```
class C { int b }
void f (C c1, int a, ref int b)
    1. standartní reference, velikost adresy
    2. hodnota, 4 byty
    3. velikost adresy tracking reference
void g()
    int x = 5
    C y = new C()
```
```
volací zásobník:
    žije tam x a y
    
GC heap:
    žije tam instance C - overhead a zbytek
```
```
f(y, x, ref x)

volací zásobník:
    vznikne c1, zkopíruje se do něj reference z y
    vznikne x přímo s kopií své hodnoty
    vznikne ref x, zkopíruje se tam adresa x
```
co když...
```
void f( ref C c2 )
volací zásobník:
    vznikne proměnná c2 s adresou na místo, kde je očekávaná reference na C, tedy zase ta adresa
```
* dějí se tam dvě dereference, je to zbytečné
* jen to zdržuje

```
// WRONG USAGE of ref to reference type!!!
// We are expecting a reference to a refernce to List instance, and not taking advantage of it at all.
// BETTER would be:
// static void AddZero(List<int> list)
static void AddZero(ref List<int> list) {
	list.Add(0);
}
```
* list je automaticky zvětšovací, proč...
```
// Meaningful usage of ref to reference type.
// However note, that this is a non-effective implementation of "variable length array",
// it has O(N) amortized Add complexity, while List<T> has O(1) amortized Add complexity!
static void AddZero(ref int[] array) {
	var newArray = new int[array.Length + 1];
	Array.Copy(array, newArray, array.Length);
	newArray[array.Length] = 0;
	array = newArray;
}
```
* pole oproti tomu není, takže to může být smysl, chceme staré pole nahradit novým, tedy přiřadit do staré reference referenci na nové pole

## výstupní parametry vs ref
* obojí je tracking reference
```
int val;
int TryParse("123", out val);
```
vs
```
void Inc(ref int x) {x = x + 1}
void f() {
    int val = 3;
    Inc(ref val); //val == 4
}
```
* tady musí být povoleno čtení i zápis, takže věc musí být inicializovaná!!!
* `out` je volnější
* **překladač parametry `out` bere jako neinicializované**
* musí se do nich do každého zapsat alespoň jednou
* například TryParse musí kromě `return false` i zapsat do `out`, hodí se tam dát komentář, že je to jen defaultní hodnota
* co když se vyhodí výjimka a nedosadí se? je to výjimka, neuspěšná volání zapsat nemusí
```
int TryParse("123", out int val);
// ekvivalentní předchozímu
```
### symbol `_`
* discard
* nezajímá mě obsah toho místa
```
int TryParse("123", out _);
// překladač si udělá dočasné lokální proměnné
```
* dřív to bylo validní jméno proměnné, pokud je někde ve funkci něco, co se jmenuje `_`, **pak se tahle syntaxe vypíná**

```
a = b = c = 123
```
```
bool f( .. )
f(); // zahazuju hodnotu
_ = f() // lepší, říkáme ostatním, že to myslíme vážně
```
## vstupní parametry
* existuje i `in`, je v té funkci readonly
* taky dobré u velkých hodnotových typů aniž by se musely kopírovat, třeba v počítačových hrách
* existuje ještě `ref readonly`

## tracking reference na lokální proměnné
* jde to
* hlídá se životnost

## tracking reference jako návratová hodnota
* hlídá se životnost

## interfaces nejsou potomky object, ale...
```
Interface I {
    public void m()
}
class A : I {
    public void m() { .. }
}

A a = new A()
a.m() //nepřekvapivé
a.ToString() //dědí i od Object
I i1 = a
a.m() // jde
a.ToString() //jde to, přestože interface nedědí od Object
```
* překladač vždy přeloží ToString(), i když to voláme na interfacu

|||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||
---
### všechny hodnotové typy jsou sealed
---
* tím pádem **nemůžu dělat potomky struktur**

proč?
* dědičnost zajišťuje i typovou kompatibilitu, tady bychom museli cpát větší potomky do menších
* v C++ se vezme podmnožina potomka a zkopíruje se do předka, není to zúžený pohled
* když je to v C++ pointer, funguje to

```
class C {
    int x;
    void m(int a) { x = a }
    máme skrytý parametr C.this
    volalo se this.x = a;
}

struct S {
    int x;
    void m(int a) { x = a }
    skrytý parametr ref S
}

S s1 = new S();
s1.x =5;
s1.m(9); - získám tracking referenci na s1, zmodifikuje to tam
volalo se S.m(ref s1, 9)
```
adresa jako adresa
```
interface I1 {
    public void m(int b)
    // má I1.this
}
```
#### struktura může implementovat interface
```
struct S :I1 {
    public void m(int b)
    // má S.this
}

S s1 = new S();
I1 i1 = s1; //proběhl boxing

```
