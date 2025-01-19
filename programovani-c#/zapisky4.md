# C# zápisky 4. přednáška

## Jak probíhá překlad v C++
* každý .cpp stroják se přeloží do .obj pro konkrétní platformu, pak se to sjednotí **linkerem** do .exe
* musíme vědět, jakou platformu přesně máme
* apple distribuoval rovnou strojové kódy pro staré i nové platformy
* říká se tomu **nativní/unmanaged code**


## jak funguje v c# zpětná/dopředná kompatibilita?
```
csproj ->buildsystem -> csc(.exe, .dll) -> executable CIL -> CLR, JIT -> binárka pro konkrétní platformu
```
* buildsystem pustí jednou c# překladač, ten dostane všechny zdrojáky projektu najednou (narozdíl od cpp)
* vygeneruje CIL - common intermediate language (u javy bytecode, taky je to intermediate language), nexistuje procesor, který to umí rozjet
* vygenerují se zároveň s CILem i metadata
* když to puštíme v dotnet, JIT (just in time compiler)
* JIT načte CIL do paměti a vygeneruje znovu strojový kód pro konkrétní platformu
* CIL kódu se říká **managed code**, tomu virtuálnímu prostředí (CLR) se říká **assembly**
* není problém s kompatibilitou dopředu i zpět
* **optimalizace dělá až JIT**

## Jak JIT pracuje
* JITování se dělá po metodách
* vygeruje se pro main, když narazí na cizí funkci, ta se vyJITuje, když ji vidím podruhé, už se použije to předtím vyJITované
* takže sice se to nedlá celé naráz, ale i tak to zpomaluje proces (například proti c++), kde je spousta času, kdy se to může přeložit u programátora a udělat nějaké super optimalizace

## AOT
* ahead of time
* přeloží se to předem, jako v c++ pro konkrétní platformu a tak se to distribuuje, je možné vygenerovat efektivnější kód než JITem
* ne pro každý program je to dobré
* defaultní je JIT

## k čemu je dobré mít víc projektů
* csproj -> library .dll CIL 
* generují se i metadata, pro každou třídu a metody v ní
* dejme tomu první projekt spustitelný a.dll, druhý library b.dll, v a.dll volám B.m(1,2)
    v b.dll
```
class B {
    public static string m ( int x, float y) 
}                                           
```
* všechno tohle je v metadatech
* DŮLEŽITÁ JSOU I JMÉNA PARAMETRŮ
* {implementace} - do CIL jen odkaz
* pro debug info potřebuju info o lokalnich promennych, prekladac krome assemblies generuje i pdb - navíc k metadatům lokální proměnné atd
* runtime pdb nepoužívá
```
    překlad a.dll                                   
        ld 1 ->int1
        ld 2 ->float 2
        call
```
* runtime pdb na nic nepoužívá, při novém překladu se znova vygeneruje, ILSpy si z něj bere věci, co nejsou v metadatech
* Jak ten překladač ví, že se má koukat do dll metadat? Buildsystem dodá překladači a odkaz na reference na další assemblies

## Jak spustit programy?
* vygeneruje se a.dll, naše samotné assembly a potom .exe soubor stejného jména, je to program, který neobsahuje nic našeho, hledá CLR, v ní pustí nějkou funkci a té předá naši assembly jako parametr
* tenhle způsob funguje jen na Windowsech
* můžu to úplně obejít na příkazové řádce, aby se to spustilo třeba i na WSL - jednou vykompilované dll běží na víc OS - megacool
* MUSÍM MÍT DOTNETSDK - takže uživatel to typicky jen s runtimem dělat nemůže a potřebuje ten .exe
```
dotnet blabla.dll [args]
        ^^ podobně 
dotnet build - ctrl shift B ve VS dělá to stejné
dotnet clean - vyčistí mezivýsledky překladu
dotnet run [args]
- podívá se to, co tam je za projekt, udělá build, najde to výslednou assembly a tu to pustí
- musíme být ale v tom adresáři toho projektu
dotnet test - vyhledá a spustí testy
```

## Jak si zobrazit metadata?
**první způsob**
* najdu developer command prompt for visual studio
* dostanu se do adresáře, napíšu `ildasm "jméno.dll"` 
* najdeme CIL a metadata
* jména lokálních proměnných tam nejsou
* komentáře tam nejsou

**druhý způsob**
* když chci rozumět CIL kódu
* nástroj **ilspy**, počítá s tím, že soubor je generovaný c#ovým překladačem, rozpozná patterny v tom neoptimalizovaném zdrojáku a odhaduje z toho původní kód
* zvládne to toho hodně
* proč to umí najít názvy lokálních proměnných? Říkali jsme, že nejsou v metadatech
    * pro debugger totiž potřebujeme víc
    * překladač generuje i soubory .pdb s debug infem
    * ilspy si to vytáhne z nich

## Dědičnost - základ
```
class A {
    void m(int x) {} 
}
```
* v CIL `void A.m (A this, int x)` - je to instanční, ne statická, proto je tam this
```
 class B : A {} 
```
* ušetříme CIL kód, dědí se jen přístup, nekopíruje se to
* dědí se instanční metody, fieldy
* dají se věci jen přidávat, nedají se odebírat
```
B b = new B();
b.m(1) ->A.m    
```
* CIL `call A.m(b, 1)` strkám `B` do `A this` - můžu díky typové kompatibilitě
```
A a = new A()
    = new B()
    = b
a.m(2) ->A.m   CIL - call A.m(a,2)
```

* dědí se i statické metody i fieldy, nekopíruje se to, dědí se jen přístup k metodě
```
 class A {
    static void f() -->A.f
    static int y -->A.y
 } 
 class B : A {} - ušetříme CIL kód
```

* JIT vyhradí někde místo pro A.y, A i B mají přístup k tomu stejnému y


## ABSTRACT CLASS 
* zakazuje varábění instancí, povolují se dělat jen proměnné - Person má potomky Customer, Employee etc.
* vůbec nemusí mít abstraktní metody
* něco jako interface vlastně
* role Employee je popsaná v Type


## STROM DĚDIČNOSTI 
* MUSÍ BÝT PRÁVĚ JEDEN PŘEDEK 
* překladač vybere sám, když nevybereme nic, např System.Object, zkratka object
* všechny ostatní struktury kromě object mají právě jednoho předka (object nemá nic)
* **třída může proti tomu implementovat spoustu interfaců**

tohle jde:
```
object o = new A()
         = new B()
         = "ahoj"
         = 2      ano, jde to... ale čemu tím prospějete?
```

* **KONSTRUKTORY SE NIKDY NEDĚDÍ**
```
    class A {
        A() {}
    }
    class B : A {
        B() {}
    }
```
* v B můžou totiž být nové fieldy, nenaalokovaly by se - neinicializovaly by se

## volání konstruktoru předka
* musí se vždy volat
* `:base(parametry)`
* v C# se volá konstruktor předka, potom konstruktor potomka
* mělo by se psát `:base(), B()`
* co když předek nemá bezparametrický konstruktor? musíme ručně napsat a vybrat správný konstruktor
* dotnet nezná to, že konstruktor se jmenuje stejně jako třída - metoda se přeloží jako .ctor

## Jak zjistit, jestli jsou něco stejné typy
* `a is B` - zkoumá, jestli je přiřaditelné, prochází se stromeček navíc, **pomalé**
* `a.GetType() == typeof(A)` - strašně rychlé, jit to prostě umí dobře


