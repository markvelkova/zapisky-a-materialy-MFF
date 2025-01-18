# C# zápisky 1. pøednáška

## Jak je to v Pythonu
* **Reference** je pointer, u kterého se nemùžeme podívat na tu adresu nìjak snadno. 
* V Pythonu všechno typu reference, promìnná obsahuje **odkaz na objekt na haldì**, tam je overhead a objekt sám.

```
a = "hello"
a = "pes"
```
* nic se tam nevyrábí nového pøímo v promìnné, jen se pøepíše ta reference a ukazuje jinam (ale samozøejmì se vyrábí nový objekt na haldì)
* **v overheadu** typ, velikost, reference counter, které na to ukazují
* znaménka operací (napø +) zanmenají rùzné vìci, podle typù objektù (stringová konkatenace, sèítání)

## Jak je to v C/C++
* staticky typovaný jazyk
* není overhead, prostì je to tam zapsané
* **promìnná zabírá pøímo svoji velikost**, takže data algoritmu se nám snáz vejdou do cache (**rychlost**), **pøímoèarý pøeklad do strojového kódu**
* ale musíme si na to dávat pozor sami

**CLASS vs STRUCT**
* **žádný zásadní rozdíl**, ve tøídì vše default private, ve struktuøe public
* obojí je prostì hodnotové

**POINTERY**
```
C *pc; //vyhradí se velikost adresy
pc = &c1; //na místo vyhrazené zapíše se adresa c1
*pc = 8; //na místo, kam ukazujeme (tedy do c1), se zapíše 8
```

**HALDA**
* všechno výš se dìlalo na stacku
* máme i haldu, ale na té musíme alokovat explicitnì
```
pc = new C();
```
* neuvolní se samy, musíme dìlat sami (proto je na haldì nìjaký overhead, aby se vìdìlo, kolik to zabírá)

## DOTNET
* potøebujeme nejenom jazyk, ale i bìhové prostøedí, runtime, standartní knihovny 
* .Net
* jazyky dotnetu mají spoleèné, takže používat Console mùžu i v F# etc

## Typy promìnných
**HODNOTOVÉ**
* alokované na místì
* **structures** - jednoduché (int, bool, char...), nullable, Struct (definované programátorem)
* **enumy**

**REFERENÈNÍ**
* alokované na haldì spravované GC
* Classes, interfaces, arrays, delegates...

## na zásobníku
**typ promìnné není urèet deklarací promìnné, ale typu!!!**
```
C c1; //založí na zásobníku promìnnou typu reference pro adresu na haldu
int i1; //založí na zásobníku hodnotovou promìnnou, podobnì jako v C
```
**rozdíl mezi tøídami a strukturami je zásadní**
```
S s1; //založí na zásobníku hodnotovou promìnnou velkou jako souèet fieldù struktury
```

```
string s = "kokos"; //string je zkratka System.string (nebo tak nìco), takže je to tøída -> reference
```
## na haldì
```
C c1 = new C(); //ALOKUJE SE NA HALDÌ
```
bude tam overhead
* syncblock (kvùli vláknùm, viz LS), pointer na *nìco* z toho vede reference na **instanci tøídy Type**
* ta tøída Type vznikne na haldì má vlastní overhead s informacemi o té naší tøídì, ukazuje na SystemType, ta instance se nám tam vyrobí úplnì na zaèátku, zase má overhead
```
c1.x = 8; //u teèky koukáme, co je vlevo, tady tøída -> automatická dereference, posun o offset
s1 = 9; //pøímo se vezme offset, adresa, zapíše se 9
```
```
S s2 = new S(); //NIKDE SE NIC NEALOKUJE!!!, na místì se zavolá bezparametrický konstruktor
```
-> **pozor na slovo New, mùže znamenat rùzné vìci u hodnotových a referenèních typù**

## typy a jejich zkratková slova
* int je to C# zkratka pro dotnetí typ System.Int32
* jsou to jen jiné názvy pro to samé (narozdíl tøeba od Javy)

## pøedávání promìnných do funkcí
* vždy se pøedávají hodnotou!!! (pøipomeò si, co je hodnota referenèní promìnné)
* vyhradí se na stacku místo pro návratovou hodnotu funkce
* u referenèní se zkopíruje adresa objektu a pøedá se
* u hodnotového se jen zkopíruje hodnota

## record class/struct
* rclass jsou poøád class, rstruct jsou poøád srtuct
* pøekladaè tam implicitnì dogeneruje nìjaký kód, abychom si ho nemuseli psát sami (napøíklad toString se vyrobí a vypisuje jméno typu)
