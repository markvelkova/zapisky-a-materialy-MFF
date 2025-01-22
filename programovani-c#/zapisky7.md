# C# zápisky 7. přednáška

## member hiding
```
class A
    public void m()
    
class B : A
    public void m() // hodí warning, member hiding -> public new void m()
```
* tím slovem new opravdu schválíme ten scénář

kdy se to naopak hodí, aneb proč se to neudělá samo rovnou, ale dá to jen warning?
```
a.dll: class A {
}

b.dll: class B : A {
    public void m() {beta}
}
```
* lidé chtějí dodat m() do a.dll
```
a.dll: class A {
    public void m() {alfa} //není to přesně záměnné, může to dělat něco trošku jiného
}
```
* shodou náhod se to jmenuje stejně

* neupravím b.dll, přeložím vůči nové verzi a.dll, nic se nezměnilo, member hiding, pořád beta
* kdyby se házela chyba, tak by se to najednou nepřeložilo a museli bychom zjišťovat, kde je problém -> proto jen warning

## kdy se co děje s VMT
* **virtual** -  nový záznam do VMT
* **override** - přepsaní existujícího záznamu ve VMT
* **abstract** - nový prázdný záznam ve VMT
* nic - žádný záznam ve VMT

## PROČ VLASTNĚ ROZLIŠUJEME VIRTUÁLNÍ A NEVIRTUÁLNÍ METODY? (třeba Java to nedělá)                               

1) výkonnost

    **nonvirtual** 
    * do CIL přeloží jako `call`, do strojového jako **přímé volání** adresy v paměti, jen JMP -> rychlé (pozor, pouze pokud se to neinlinuje, jinak ještě rychlejší)
    
    **virtual** 
    * CIL > `callvirt`, generuje instrukci **nepřímého volání**, adresa místa, odkud se přečte adresa, možná cesta přes několik -> pomalé
    
    **závěr**
    * tedy hlavní výhoda nonvirtual je, že se dají inlinovat
    * díky PGO ten rozdíl nakonec často není zas tak velký ale

2) slib rozšiřitelnosti
```
    class A (všechny metody dole public, součást API) třeba streamreader
        public f()             třeba read()
        public g() > volá f()  třeba readline()
        public h()
```
**Java:**
```
    class B : A ... v Javě - všechno virtuální, implicitně overridy
        public f() lepší
        public g() lepší
        public h() lepší
```
**ALE TOHLE JE NEBEZPEČNÉ - virtuální metoda je slib rozšiřitelnosti!!!** 
* Java ho dává za nás, můžou být v A implementační detaily private, f i g je používají, muselo by to být tak, že z g() se na f() koukám jakoby zvenku bez private detailů
* v B si můžu nahradit f, že pracuje s jiným bufferem například, g pracuje se starým atd... může se to rozbít velmi snadno
* Java říká: pečlivě si rozmyslete, jestli koukáte jen na vnější API


> **v C# dám virtual jen tam, kde opravdu slibuji rozšiřitelnost**

```
    class A (všechny metody dole public, součást API) třeba streamreader
        public f()             třeba read()
        public virtual g() > volá f()  třeba readline()
        public virtual h()
    
    class B : A 
        public f() - member hiding, zakrývá starou
        public override g() 
        public override h() > ale je provázaná s novou g(), využívá její implementační
    
    class C : B
        public override g() > rozbilo by se nám h(), bylo provázané se starou implementací
```
řešení:
    
* **sealed** znamená, že **žádný potomek nesmí dodat jinou implementaci** -> používá se pro intent, často přinese vliv na výkonnost - JIT to může automaicky devirtualizovat
```
class B : A 
        public f() - member hiding, zakrývá starou
        public sealed override g() 
        public override h() > ale je provázaná s novou g(), využívá její implementační
    
    class C : B
        public override g() //error
```
* můžeme označit jako sealed i celou třídu - zakážeme od ní dědit dál
```
class A { .. }
sealed class B : A { .. }
sealed class C : A { .. }
// B a C jsou listy ve stromu dědičnosti
```

## rozpitvání složení interfaců a virtualních metod
```
interface I1
    public void m1()
    public void m2()
    
abstract class A
    public abstract f() // VMT[0] prázdný záznam
```
|VMT A|        |
|-|---------|
|0| prázdný záznam f()|

```
class B: A, I1 
    public override f() {B.f()} // VMT[0] - reference na B.f()
    public void m1() {B.m1()} //zavádí nevirtuální, je poznamenáno v typu mimo VMT
    public virtual void m2() {B.m2()} // VMT[1] - B.m2()
    
// dědí VMT A
// přibývá tabulka pro I1
```
**ve virtuální adresy na implementaci, na strojový kód**
|VMT B|        |
|-|---------|
|0| B.f()| 
|1|B.m2()|

**odkazy na metody jako takové**
|I1 tab B|        |
|-|---------|
|0| B.m1()|
|1|odkaz na virtuální B.m2(), tedy VMT[1]|
```
class C: B 
    public new void m1() {C.m1()}
    public override void m2() {C.m2()} //VMT[1] - C.m2()
    
// dědí VMT B - VMT[0] - B.f(), VMT[1] - B.m2()
// dědí nevirtuální B.m1
// dědí iterfacovou tabulku i s obsahem, protože neimplementuje znova
```
|VMT C|        |
|-|---------|
|0| B.f()|
|1|C.m2()|

|I1 tab C|        |
|-|---------|
|0| B.m1()|
|1|odkaz na virtuální B.m2(), tedy VMT[1]|

nic se nemění, protože neimplemetujeme znovu I1, nicméně odkaz na virtuální B.m2() vede na VMT[1], kde je C.m2()


```
A a = new B();
a.f(); -> callvirt A.f > call VMT[0] > volá se B.f()

a = new C();
a.f(); -> callvirt A.f > call VMT[0] > volá se B.f()

B b = new B();
b.m2(); -> callvirt B.m2() > call VMT[1] > volá se B.m2()
b.m1(); -> volá se B.m1()

b = new C();
b.m2(); -> callvirt B.m2() > call VMT[1] > volá se C.m2()
b.m1(); -> volá se B.m1(), záleží na typu, do kterého dosazuji novou by vidělo jen C

I1 i1 = new B()
i1.m1(); > B.m1
-> interfacové se volají callvirt, gettype(B), podívám se na I1tab[0]
i1.m2(); > B.m2
-> interfacové se volají callvirt, gettype(B), podívám se na I1tab[1], vidím odkaz na VMT[1], volám to, co tam je, tedy C.m2()

i1 = new C()
i1.m1() > B.m1()
-> interfacové se volají callvirt, gettype(B), podívám se na I1tab[0], vidím odkaz na B.m1
i1.m2() > C.m2()
-> interfacové se volají callvirt, gettype(B), podívám se na I1tab[1], vidím odkaz na VMT[1], volám to, co tam je
```

```
class D: B, I1
    public new void m1() {D.m1()} //member hiding
    public override void m2() {D.m2()} //VMT[1] - D.m2()
    
// dědí VMT B - VMT[0] - B.f(), VMT[1] - B.m2()
// dědí nevirtuální B.m1
// implementuje znovu interface
```
|VMT D|        |
|-|---------|
|0| B.f()|
|1|D.m2()|

|I1 tab D|        |
|-|---------|
|0| D.m1()|
|1|odkaz na virtuální B.m2(), tedy VMT[1]|

```
I1 i1 = new D()
i1.m1() > D.m1()
i1.m2() > C.m2()
```
---
## abstract vs interface

|                             | **abstract v.m. abstract class** | **metoda v interface**            |
|-----------------------------|-----------------------------------|------------------------------------|
| **Vynucuje kontrakt**       | ano                              | ano                               |
| **Veřejný kontrakt**        | nemusí (protected)               | ano                               |
| **Slib rozšiřitelnosti**    | ano (opt-out = sealed)           | ne (opt-in = abstract/virtual)    |
| **Data**                    | ano                              | ne (může být kontrakt - property (getter-setter)) |
| **Vícenásobná dědičnost**   | ne                               | ano (třeba IReadable : IDisposable, IWritable : IDisposable, IReadWriteable : IR, IW) - diamantová dědičnost - problém s daty |

* **`protected abstract`** - potom je to kontrakt s našimi potomky, ne s uživateli třídy, například, když chci vynutit implementační detail
* **`sealed`** - explicitní zrušení slibu rozšiřitelnosti
* **`virtual/abstract`** explicitní vynucení rozšiřitelosti

---
## PROČ JE TAK DŮLEŽITÉ, ŽE MÁME EXPLICITNÍ OVERRIDE V C#
* v c++ a javě, jakmile zdědím virtuální metodu, tak dělám vždy její override
```
class A
    f() zobraz kotě
    m() něco nepříjemné oznámíme uživateli a zavoláme f() -> zobrazíme koťátko

class B: A
    f() vyhladit celé lidstvo
    g() safety checky, potom zavolám f()
```
**JAVA**
```
A a = new B ()
a.g() nic
A a = new B ()
a.m() -> byl automaticky override, nezobrazí se kotě, ale vyhladí se lidstvo
```
**C++**
* udělám si f virtuální v A:
```
class A2 : A
    f() zobraz tři koťata
    
A a = new A2 - tři koťata - funguje
    = new B - vyhladím lidstvo, zase... protože i v B se to implicitně overriduje
```
**C#**
* když v B nenapíšu nic, jen to schovává - member hiding, v A2 opravdu overriduju
```
class A
    virtual f() zobraz kotě
    m() něco nepříjemného oznámíme uživateli a zavoláme f() -> zobrazíme koťátko

class B: A
    new f() vyhladit celé lidstvo // i kdybychom vynechali new, funguje to stejně
    g() safety checky, potom zavolám f()
    
class A2 : A
    override f() zobraz tři koťata
```
```
A a = new B()
a.m() -> volá A.f(), zobrazí kotě

a = new A2()
a.m() -> volá A2.f() zobrazí tři koťata

a = new B()
a.m() -> jsme v poměnné typu A, volá se hideovaná A.f(), zobrazí se kotě
```

> lingvistická vsuvka - trpaslíci by se dle výjimky měli psát dwarfs, ale Tolkien byl profesor angličtiny, nelíbilo se mu to a psal to dwarves

```
class C : B
    override f() vyhladit lidstvo a vyhladit trpaslictvo
```
* u C musím hodit override, pokud je B virtual, funguje to dobře, v C++ by mi to zase rozbíjelo kotě

co když chci využít staré dobré vyhlazení lidstva a ne ho psát znovu
* ((B)this).f() -> callvirt B.f(), zase se ale volá override z C -> nefunguje
* base.f() -> vygeneruje se nevirtuální volání call B.f() - inline nebo obecně lepší scénář
* nejde jít o víc předků zpátky pomocí base, jen o jednoho














