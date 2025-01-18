
# C# z�pisky 19.12.2024
## SPAN

```
readonly ref struct Span<T> //bezpe�n� pohled do pam�ti
	private ref T; //je to prvn�, za n�m jsou dal�� T
	public indexer
		this[int index] //range check jako u klasick�ho pole
	...
```
vypad� to jako siln� typovan� C pointer, ale to jsme necht�li - mohli bychom narazit na z�porn� index 
```
	...
	Lenght
```
zaji��uje, �e se n�m span vejde, nem��eme vyrobit moc velk� do mal� pam�ti atd

### rozd�l oproti norm�ln�mu poli
norm�ln� pole m� pam�, tohle jen n�kam ukazuje
m��u m� pole int� a p�i�adit ho do spanu - na hald� se v tom p��pad� nealokuje ��dn� nov� m�sto, span s�m je na z�sobn�ku.
```
	int[] a1 = new int[4];
	Span<int> s1 = a1;
```
Kdy� pak zm�n�m n�co ve spanu, zm�n� to p�i�azen� pole:
```
	s1[0] = 22; //-> a1[0] = 22;
```
m��eme ud�lat jen span `.Slice` kter� vyrob� v��ez
```
	Span<int> s2 = a1.AsSpan().Slice(2); //kam
	Span<int> s3 = a1.AsSpan().Slice(1,2); //odkud, kam
```
lze i readonly span
```
	ReadOnlySpan<int> s4 = a1.AsSpan().Slice(2); //kam
```
 hod� se obecn� v situaci, kdy chceme zpracov�vat velk� v�ci po kousc�ch
```
	private static void f(int count) {
		ReadOnlySpan<int> s5 = stackalloc int[count];
		for (int i = 0; i < s1.Length; i++)
			s1[i] = 0;
	}
	
```
Jen se posune vr�ek z�sobn�ku o ur�it� po�et, alokace v�t��ho po�tu lok�ln�ch prom�nn�ch najednou. Je to jen do�asn� pam� po dobu b�hu funkce. M��e doj�t k p�ete�en� z�sobn�ku.
## COLLECTION INITIALIZER
```
	int[] a = {1,2}
	int[] b = [1,2] //to je ono
```
```
	List<int> l = new List<int>{1,2};
	// stane se
	l = new List<int>();
	l.Add(1);
	l.Add(2);
```
 ^^ **DUCKTYPING**, funguje na jak�koli typ, kter� um� ud�lat `.Add`, kam pasuje dan� hodnota v z�vorce. Tohle i prvn� je sjednoceno collection initializerem.
```
	l = [1,2];
```
jsou kompatibiln� i se spanem a readonly spanem

Kdy� m�m funkci s prom�nn�m po�tem parametr�, nahrad�m `params object()`, v .NET13 p�ibylo `params ReadOnlySpan(...)` p�ed�vaj� se collection initializerem, a� se funkce vr�t�, prost� se to dealokuje. U�et��me si zbyte�nou alokaci na hald�.

Nemus� v hranat�ch z�vork�ch b�t jen konstanty, mohou b�t i v�razy. Nap��klad **spread operator** 
```
	int[] source = {10,20,30};
	int[] ax = [ -1, .. source, -2, .. source, -3];
	// -1, 10, 20, 30, -2, 10, 20, 30, -3
	List<int> lx = [ -1, .. source, -2, .. source, -3];
```
list v tomto p��pad� m��e naalokovat m�n� pam�ti
**nev�hoda stackallocu** ka�d� vl�kno m� sv�j vlastn� z�sobn�k, nen� t�m p�dem neomezen�, m��e n�m p�et�ct z�sobn�k, pokud to pou��v�me neopatrn�. Mus�me nad pou�it�m p�em��let.

## COLLECTION PATTERN MATCHING
```
	int[a] {1,2,3,4,5,6,7};
	if (a is [< 2, > 1, .. int[] restArray, int x])
```
m� alespo� t�i polo�ky, prvn� je men�� ne� dva, druh� v�t�� ne� jedna, posledn� ��slo
Je d�le�it�, �e `restArray` d�l� kopii, alokuje se nov� pole/list
```
	Span<int> span = new int[] {1,2,3,4,5,6,7};
	if (span is [< 2, > 1, .. Span<int> restArray, var x])
```
v tomhle p��pad� se `restArray` nekop�ruje, pokud ho uprav�m, uprav� se p��mo p�edloha
```
	static int g1(Person) {
		int result = p switch {
			Person("Petr", var lastName) => lastName.Length,
			Person(var firstName, "Vesely") => firstName.Length,
			_ => 0
		};
		return result;
	}
```
m� to jen v�znam, jako by se to konstruovalo takto, ale nepracuje to s konstruktory. Jde o metodu **deconstruct**.  Dogenerov�v� se do record stuktur podobn� jako `.ToString()`
`.Deconstruct(out string, out string)`
op�t ducktyping, m��eme si napsat vlastn�.
# V�JIMKY
S�m o sob� je C# nedefinuje, je to d�no CommonLanguageRuntime, `():Exception`, jeden potomek je t�eba `IOException`, od toho je dal��, t�eba `FileNotFoundException`. Chceme zjistit, co nastalo �patn�, tak�e je dobr� h�zet co nejkonkr�tn�j�� v�jimky. Je to t��da, tedy referen�n� typ, m� message, stacktrace. Kdy� se v catchblocku namatchuje na filtr, vykon� se v�c, co jsme cht�li. Zkou�� se v po�ad�, v jak�m jsou napsan� => specifi�t�j�� mus� b�t prvn�. Kdy� se to nikde nenamatchuje, vy���� se to prost� ven. Pokud nen� v�jimka, p�esko�� se catchbloky prost� - ��dn� pam� nav�c. Kdy� ale je vyj�mka, mus� se vykonat stackTrace - t�k�, JIT na to nen� optimalizov�n, cheme, aby vol�n� prob�hala rychle. P�eskakuj� se p�i `throw` r�zn� dealokace prom�nn�ch atd, nesm�me si rozb�t volac� z�sobn�k, mus� skon�it po odchycen� v�jimce stejn�, jako po proveden� funkci. Je to zkr�tka *t�koton�n�*. **Nem�lo by se st�vat, �e na ka�d�m druh�m ��dku vznikne v�jimka.** Sc�n�� s v�jimkou je v�c ne� $100\times$ pomalej��.  Takhle je mo�n� ud�lat DOS �tok - mal� vstup zp�sob� drahou v�jimku. **V�jimky pou��v�me jen tehdy, vznikaj�-li v�jime�n�.**
```
	int <- int.Parse(string ...)
```
pokud v�m, �e to hod� v�jimku t�eba dvakr�t, OK, pokud v�m, �e p�lka z nich bude blb�, tohle nen� dobr� �e�en�
```
	int <- int.TryParse(string s, out int result)
```
vrac� to bool, jestli se povedlo, v `out` m�me v�sledek
**Nev�hoda TrySth v��i v�jimk�m** - kdy� u nich zapomenu na try block, program spadne. Zase neza�ne d�lat blbosti. V tomhle p��pad� se to jen zahod� a nic se neodhal�, program se pak m��e chovat divn�, i kdy� m�l spadnout. Nav�c nedostaneme detailn� informaci, co se stalo. Dozv�me se jen `false`, ne konkr�tn� v�jimku. Tak�e vlastn� jako bychom chytali jen obecnou v�jimku...
