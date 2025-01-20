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
        f()             třeba read()
        g() > volá f()  třeba readline()
        h()
        
    class B : A ... jako v Javě - všechno virtuální
        f() lepší
        g() lepší
        h() lepší
```
**ALE TOHLE JE NEBEZPEČNÉ - virtuální metoda je slib rozšiřitelnosti!!!** 
* Java ho dává za nás, můžou být v A implementační detaily private, f i g je používají, muselo by to být tak, že z g() se na f() koukám jakoby zvenku bez private detailů

* v B si můžu nahradit f, že pracuje s jiným bufferem například, g pracuje se starým atd... může se to rozbít velmi snadno
* Java říká - > pečlivě si rozmyslete, jestli koukáte jen na vnější API
* **v C# dám virtual jen tam, kde opravdu slibuji rozšiřitelnost**
    
* **sealed** znamená, že **žádný potomek nesmí dodat jinou implementaci** -> používá se pro intent, často přinese vliv na výkonnost

## rozpitvání složení interfaců a virtualních meod
```
interface I1
    m1()
    m2()
    
abstract class A
    public abstract f()

class B: A, I1
    public override f() {B.f()}
    public void m1() {B.m1()}
    public virtual void m2() {B.m2()}
    
class C: B
    public new void m1() {C.m1()}
    public override void m2() {C.m2()}
    
class D: B
    VMT stejné jako u B
    I1 tab povede na béčkové ale D.m1() a B.m2()

A a = new B()
a.f() callvirt A.f > call vmt(0)
B b = new B()
b.m2() callvirt B.m2 -> call vmt(1)
b.m1() -> call B.m1 -> call tu věc prostě

I1 i1 =  new B()
i1.m1() callvirt I1.m1 ->call this.GetType().I1[0] -> B.m1
i1.m2() callvirt I1.m2 ->call this.GetType().I1[1] -> B.m2
```

## abstract vs interface

|                             | **abstract v.m. abstract class** | **metoda v interface**            |
|-----------------------------|-----------------------------------|------------------------------------|
| **Vynucuje kontrakt**       | ano                              | ano                               |
| **Veřejný kontrakt**        | nemusí (protected)               | ano                               |
| **Slib rozšiřitelnosti**    | ano (opt-out = sealed)           | ne (opt-in = abstract/virtual)    |
| **Data**                    | ano                              | ne (může být kontrakt - property (getter-setter)) |
| **Vícenásobná dědičnost**   | ne                               | ano (třeba IReadable : IDisposable, IWritable : IDisposable, IReadWriteable : IR, IW) - diamantová dědičnost - problém s daty |


## PROČ JE TAK DŮLEŽITÉ, ŽE MÁME EXPLICITNÍ OVERRIDE V C#
* v c++ a javě, jakmile zdědím viruální metoru, tak dělám vždy její override
```
class A
    f() zobraz kotě
    m() něco nepříjemné oznámíme uživateli a f() zobrazíme koťátko

class B: A
    f() vyhladit celé lidstvo
    g() safety checky, potom zavolám f()
    
A a = new B ()
a.g() nic
A a = new B ()
```
* Java - a.m() -> pokud tam byl automaticky override, nezobrazí se kotě, ale vyhladí se lidstvo

* v C++ udělám si f virtuální v A:
```
class A2 : A
    f() zobraz tři koťata
    
A a = new A2 - tři koťata - funguje
    = new B - vyhladím lidstvo, zase... protože i v B se to implicitně overriduje
```

* v C# když v B nenapíšu nic, jen to schovává - member hiding, v A2 opravdu overriduju
```
class C : B
    f() vyhladit lidstvo a vyhladit trpaslictvo
```
* TRPASLÍCI JSOU VÝJIMKA A PÍŠOU SE DWARFS - ale Tolkien byl profesor angličtiny a psal to dwarves
* musím hodit override, pokud je B virtual, funguje to dobře, v C++ by mi to zase rozbíjelo kotě

co když chci využít staré dobré vyhlazení lidstva a ne ho psát znovu
* ((B)this).f() -> callvirt B.f(), vygeneruje se nevirtuální volání base.f() tedy B.f() - inline nebo obecně lepší scénář














