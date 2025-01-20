# C# zápisky 9. přednáška
## deklarace proměnných stejného jména
```
class A {
	public int X;
}

class B : A {
	public new int X; //pokud se budu koukat skrz B, uvidím tohle, jinak zděděné
}
```
* kdyby měly různé životnosti, bylo by to lepší
* ale už za týden, když se na to dívám, můžu to zničit
>není dobré střílet se do nohy
* v C# narozdíl od C++ NESMÍ být v jedné fukci dvě proměnné, pokud se jejich životnosti překrývají a zároveň nejsou na stejné úrovni (třeba jedna v _if_ větvi, druhá v _else_ větvi) 
```
	int b;
	if (...) {
		int b;	// error: b is already declared in the outer block
		int c;
		int d;
		...
	} else {
		int a;	// error: a is already declared in the outer block (parameter)
		int d;	// ok: no conflict with d in the if block
	}
	for (int i = 0; ...) {...}
	for (int i = 0; ...) {...}	// ok: no conflict with i from the previous loop
	int c;	// error: c is already declared in a nested block
}
```

## přístup k neinicializované proměnné
* v C++ povolen, v C# zakázán!

```
int e = 1, f;
if (e == 1) {
	f = 2;
}
e = f; // error: f is not initialized in every possible execution path
```
* občas třeba neudělám `else` větev, ale všechny `if else` pokryjí všechny případy, jenže to překladač vědět nemůže, trvalo by mu to děsně dlouho
* překladač bere, že všechny `if` po cestě mohou dopadnout jakkoli

### řešení?
* přiřadit tam na začátku zbytečnou hodnotu, ale to může mást lidi, co to tam znamená - patří tam komentář
* lepší je zamyslet se nad tím a udělat to nějak lépe
* explicitní `else`, třeba klidně s vyhozením výjimky, aby e nakonc přece jen nepracovalo s tou zbytečnou hodnotou

## deklarace proměnných v pattern matchingu
```
static void f(int? x) {
	if (x is int a) {
		Console.WriteLine(a + 10);
	}
	// Error: Use of unassigned local variable 'a'
	// Console.WriteLine(a);

	int c;
	if (x is int) {
		c = (int) x;
		Console.WriteLine(c + 100);
	}
	// Error: Use of unassigned local variable 'c'
	// Console.WriteLine(c);
```
#### složený příkaz:
* omezí životnost proměnné
```
    {
		if (x is int b) {
			Console.WriteLine(b + 1000);
		} else {
			// Error: Use of unassigned local variable 'b'
			// Console.WriteLine(b);

			b = 0;
		}
		Console.WriteLine(b + 2000);
	}

	// Error: The name 'b' does not exist in the current context
	// Console.WriteLine(b);
}
```
---
## jakého typu proměnné jsou?
1. **hodnota**
    * hodnotové typy
    * volání new() u struktury není žádná alokace, jako by tomu bylo u class
2. **reference**
    * interně je to adresa, velikost podle platformy
    * adresa vede vždy do GC haldy
    * nesmí vést někam jinam (na globální statickou, kód etc)
    * ukazuje vždy na celou tu věc
    * **nikdy nelze zjistit tu adresu**, protože GC je může přesouvat

3. pointery, viz dál
4. tracking reference viz dál
## CLI type inheritance
* i hodnotové typy jsou potomci object
* `int a` můžu přiřadit do `Object o`
* principielně je to blbě
* co se tam děje? -> **boxing**

## jak alokovat na GC haldě?
#### explicitně
* `new` - udělá se syncblock, odkaz na insanci type etc, ukazuje se na celý objekt

#### boxing
* implicitní alokace hodnotového typu na haldě
* `int a` můžu přiřadit do `Object o`
* vznikne na haldě kopie 4 bytů hodnoty, overhead s Type Int32
> v dotnetu je int jen zkratka pro `System.Int32`
* ale když tam třeba dám struct, nemůžu na tom v Object volat metody struktury a tak
* ale Object má třeba virtuální `ToString()` a struktura může mít override
* `Console.Write(Object o)` - jde, ale má velký overhead kvůli boxingu
* navíc se o to pak musí starat GC - jednoduchá instrukce se mění v hromadu komplikovaných
* existuje cesta zpět **unboxing**

## unboxing
* instance striktně hodnotového typu na haldě, chceme do hodnoty
* vykopíruje se to ven
* nemůžeme sahat přímo do toho objektu na haldě
* dělá se ještě runtime check, může se hodit výjimka
```
Object o = 5;
int i = (int) o;
```
* tohle je boxing i unboxing, pomalé, děsivé:
```
Object o = 5;
o = ((int) o) + 1;
```
kdy to může být užitečné
```
Console.Write("{0} {1}", 5, "ahoj");
```
* první parametr typu string, zbytek `params Object[]`, aby se tam dalo nasypat cokoli
* má o velký overhead
* na druhou stranu, stejně t ocelé vypisování stojí netriviální čas, takže je to snesitelné

ALE:
* když si udělám sčítací funkci s proměnným počtem parametrů, je tam overhead, ale budiž
* co když ale budu chtít inty i longy a dám tam `params Object[]` -> zpomalím to
* slabé typování znovu zavádí ducktyping, to nechceme
---

## params
* když chci proměnný počet parametrů
* poslední parametr funkce můžu označit params

```
void f(int[])
int[] kk = {1,2,3}
f(kk)
```
* funguje to pořád stejně
```
void f(params int[])
f(1,2,3)
```

## referenční intová proměnná pomocí object?
* **blbý nápad**, sice to sdílím, ale musím to pořád uboxovat, po úpravě vzniká nový atd atd
```
class intref {
    int x = 42;
}
```

## boxing nullable hodnotových typů
* pokud má hodnotu, zaboxuje se jen základní typ bez `hasValue`
* můžu klidně unboxovat do nonnullable, ale můžu dostat nullreferenceexception

---
---
# pointery v c#
* je to pořád jen adresa, vypadá to stejně jako reference, ale překladač se k tomu jinak chová
* váže se to k tomu typu, ne k proměnné!
```
int* a,b; //obě pointery
```
* všechno explicitně
```
a = &něco;
*a = něco;
Console.Write(*a);
```
#### rozdíly oproti referencím
* dereference nejsou automatické
* nemají žádná omezení, mohou ukazovat na haldu, statické, doprostřed objektů, kamkoliv na zásobník
* klasické céčkové peklo
#### k čemu se to hodí
* nějaký hodně low level kód chci psát, třeba operační systém v c#
* práce s nativním kódem
* ignoruje je GC - když s objektem šoupe, neopraví se mi to

# tracking reference
* jen na data (na haldě, lokální proměnnou, globální field, statický...)
* je to adresa
* nelze ji zjistit
* GC ji sleduje a opravuje
* automatická dereference
* může vést i doprostřed objektů, ale aspoň **na celé fieldy**

---
## jazyk C++/CLI
* hardcore kombinace C++ a C#, děsivé
* na tracking reference máme `%` - `int%`
* je nebezpečné to používat, ilustrační příkládek byl
