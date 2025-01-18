# C# z�pisky 1. p�edn�ka

## Jak je to v Pythonu
* **Reference** je pointer, u kter�ho se nem��eme pod�vat na tu adresu n�jak snadno. 
* V Pythonu v�echno typu reference, prom�nn� obsahuje **odkaz na objekt na hald�**, tam je overhead a objekt s�m.

```
a = "hello"
a = "pes"
```
* nic se tam nevyr�b� nov�ho p��mo v prom�nn�, jen se p�ep�e ta reference a ukazuje jinam (ale samoz�ejm� se vyr�b� nov� objekt na hald�)
* **v overheadu** typ, velikost, reference counter, kter� na to ukazuj�
* znam�nka operac� (nap� +) zanmenaj� r�zn� v�ci, podle typ� objekt� (stringov� konkatenace, s��t�n�)

## Jak je to v C/C++
* staticky typovan� jazyk
* nen� overhead, prost� je to tam zapsan�
* **prom�nn� zab�r� p��mo svoji velikost**, tak�e data algoritmu se n�m sn�z vejdou do cache (**rychlost**), **p��mo�ar� p�eklad do strojov�ho k�du**
* ale mus�me si na to d�vat pozor sami

**CLASS vs STRUCT**
* **��dn� z�sadn� rozd�l**, ve t��d� v�e default private, ve struktu�e public
* oboj� je prost� hodnotov�

**POINTERY**
```
C *pc; //vyhrad� se velikost adresy
pc = &c1; //na m�sto vyhrazen� zap�e se adresa c1
*pc = 8; //na m�sto, kam ukazujeme (tedy do c1), se zap�e 8
```

**HALDA**
* v�echno v�� se d�lalo na stacku
* m�me i haldu, ale na t� mus�me alokovat explicitn�
```
pc = new C();
```
* neuvoln� se samy, mus�me d�lat sami (proto je na hald� n�jak� overhead, aby se v�d�lo, kolik to zab�r�)

## DOTNET
* pot�ebujeme nejenom jazyk, ale i b�hov� prost�ed�, runtime, standartn� knihovny 
* .Net
* jazyky dotnetu maj� spole�n�, tak�e pou��vat Console m��u i v F# etc

## Typy prom�nn�ch
**HODNOTOV�**
* alokovan� na m�st�
* **structures** - jednoduch� (int, bool, char...), nullable, Struct (definovan� program�torem)
* **enumy**

**REFEREN�N�**
* alokovan� na hald� spravovan� GC
* Classes, interfaces, arrays, delegates...

## na z�sobn�ku
**typ prom�nn� nen� ur�et deklarac� prom�nn�, ale typu!!!**
```
C c1; //zalo�� na z�sobn�ku prom�nnou typu reference pro adresu na haldu
int i1; //zalo�� na z�sobn�ku hodnotovou prom�nnou, podobn� jako v C
```
**rozd�l mezi t��dami a strukturami je z�sadn�**
```
S s1; //zalo�� na z�sobn�ku hodnotovou prom�nnou velkou jako sou�et field� struktury
```

```
string s = "kokos"; //string je zkratka System.string (nebo tak n�co), tak�e je to t��da -> reference
```
## na hald�
```
C c1 = new C(); //ALOKUJE SE NA HALD�
```
bude tam overhead
* syncblock (kv�li vl�kn�m, viz LS), pointer na *n�co* z toho vede reference na **instanci t��dy Type**
* ta t��da Type vznikne na hald� m� vlastn� overhead s informacemi o t� na�� t��d�, ukazuje na SystemType, ta instance se n�m tam vyrob� �pln� na za��tku, zase m� overhead
```
c1.x = 8; //u te�ky kouk�me, co je vlevo, tady t��da -> automatick� dereference, posun o offset
s1 = 9; //p��mo se vezme offset, adresa, zap�e se 9
```
```
S s2 = new S(); //NIKDE SE NIC NEALOKUJE!!!, na m�st� se zavol� bezparametrick� konstruktor
```
-> **pozor na slovo New, m��e znamenat r�zn� v�ci u hodnotov�ch a referen�n�ch typ�**

## typy a jejich zkratkov� slova
* int je to C# zkratka pro dotnet� typ System.Int32
* jsou to jen jin� n�zvy pro to sam� (narozd�l t�eba od Javy)

## p�ed�v�n� prom�nn�ch do funkc�
* v�dy se p�ed�vaj� hodnotou!!! (p�ipome� si, co je hodnota referen�n� prom�nn�)
* vyhrad� se na stacku m�sto pro n�vratovou hodnotu funkce
* u referen�n� se zkop�ruje adresa objektu a p�ed� se
* u hodnotov�ho se jen zkop�ruje hodnota

## record class/struct
* rclass jsou po��d class, rstruct jsou po��d srtuct
* p�eklada� tam implicitn� dogeneruje n�jak� k�d, abychom si ho nemuseli ps�t sami (nap��klad toString se vyrob� a vypisuje jm�no typu)
