
# C# zápisky 19.12.2024
## SPAN

```
readonly ref struct Span<T> //bezpeènı pohled do pamìti
	private ref T; //je to první, za ním jsou další T
	public indexer
		this[int index] //range check jako u klasického pole
	...
```
vypadá to jako silnì typovanı C pointer, ale to jsme nechtìli - mohli bychom narazit na zápornı index 
```
	...
	Lenght
```
zajišuje, e se nám span vejde, nemùeme vyrobit moc velkı do malé pamìti atd

### rozdíl oproti normálnímu poli
normální pole má pamì, tohle jen nìkam ukazuje
mùu mí pole intù a pøiøadit ho do spanu - na haldì se v tom pøípadì nealokuje ádné nové místo, span sám je na zásobníku.
```
	int[] a1 = new int[4];
	Span<int> s1 = a1;
```
Kdy pak zmìním nìco ve spanu, zmìní to pøiøazené pole:
```
	s1[0] = 22; //-> a1[0] = 22;
```
mùeme udìlat jen span `.Slice` která vyrobí vıøez
```
	Span<int> s2 = a1.AsSpan().Slice(2); //kam
	Span<int> s3 = a1.AsSpan().Slice(1,2); //odkud, kam
```
lze i readonly span
```
	ReadOnlySpan<int> s4 = a1.AsSpan().Slice(2); //kam
```
 hodí se obecnì v situaci, kdy chceme zpracovávat velké vìci po kouscích
```
	private static void f(int count) {
		ReadOnlySpan<int> s5 = stackalloc int[count];
		for (int i = 0; i < s1.Length; i++)
			s1[i] = 0;
	}
	
```
Jen se posune vršek zásobníku o urèitı poèet, alokace vìtšího poètu lokálních promìnnıch najednou. Je to jen doèasná pamì po dobu bìhu funkce. Mùe dojít k pøeteèení zásobníku.
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
 ^^ **DUCKTYPING**, funguje na jakıkoli typ, kterı umí udìlat `.Add`, kam pasuje daná hodnota v závorce. Tohle i první je sjednoceno collection initializerem.
```
	l = [1,2];
```
jsou kompatibilní i se spanem a readonly spanem

Kdy mám funkci s promìnnım poètem parametrù, nahradím `params object()`, v .NET13 pøibylo `params ReadOnlySpan(...)` pøedávají se collection initializerem, a se funkce vrátí, prostì se to dealokuje. Ušetøíme si zbyteènou alokaci na haldì.

Nemusí v hranatıch závorkách bıt jen konstanty, mohou bıt i vırazy. Napøíklad **spread operator** 
```
	int[] source = {10,20,30};
	int[] ax = [ -1, .. source, -2, .. source, -3];
	// -1, 10, 20, 30, -2, 10, 20, 30, -3
	List<int> lx = [ -1, .. source, -2, .. source, -3];
```
list v tomto pøípadì mùe naalokovat ménì pamìti
**nevıhoda stackallocu** kadé vlákno má svùj vlastní zásobník, není tím pádem neomezenı, mùe nám pøetéct zásobník, pokud to pouíváme neopatrnì. Musíme nad pouitím pøemıšlet.

## COLLECTION PATTERN MATCHING
```
	int[a] {1,2,3,4,5,6,7};
	if (a is [< 2, > 1, .. int[] restArray, int x])
```
má alespoò tøi poloky, první je menší ne dva, druhá vìtší ne jedna, poslední èíslo
Je dùleité, e `restArray` dìlá kopii, alokuje se nové pole/list
```
	Span<int> span = new int[] {1,2,3,4,5,6,7};
	if (span is [< 2, > 1, .. Span<int> restArray, var x])
```
v tomhle pøípadì se `restArray` nekopíruje, pokud ho upravím, upraví se pøímo pøedloha
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
má to jen vıznam, jako by se to konstruovalo takto, ale nepracuje to s konstruktory. Jde o metodu **deconstruct**.  Dogenerovává se do record stuktur podobnì jako `.ToString()`
`.Deconstruct(out string, out string)`
opìt ducktyping, mùeme si napsat vlastní.
# VİJIMKY
Sám o sobì je C# nedefinuje, je to dáno CommonLanguageRuntime, `():Exception`, jeden potomek je tøeba `IOException`, od toho je další, tøeba `FileNotFoundException`. Chceme zjistit, co nastalo špatnì, take je dobré házet co nejkonkrétnìjší vıjimky. Je to tøída, tedy referenèní typ, má message, stacktrace. Kdy se v catchblocku namatchuje na filtr, vykoná se vìc, co jsme chtìli. Zkouší se v poøadí, v jakém jsou napsané => specifiètìjší musí bıt první. Kdy se to nikde nenamatchuje, vyšíøí se to prostì ven. Pokud není vıjimka, pøeskoèí se catchbloky prostì - ádná pamì navíc. Kdy ale je vyjímka, musí se vykonat stackTrace - tìké, JIT na to není optimalizován, cheme, aby volání probíhala rychle. Pøeskakují se pøi `throw` rùzné dealokace promìnnıch atd, nesmíme si rozbít volací zásobník, musí skonèit po odchycené vıjimce stejnı, jako po provedené funkci. Je to zkrátka *tìkotonání*. **Nemìlo by se stávat, e na kadém druhém øádku vznikne vıjimka.** Scénáø s vıjimkou je víc ne $100\times$ pomalejší.  Takhle je moné udìlat DOS útok - malı vstup zpùsobí drahou vıjimku. **Vıjimky pouíváme jen tehdy, vznikají-li vıjimeènì.**
```
	int <- int.Parse(string ...)
```
pokud vím, e to hodí vıjimku tøeba dvakrát, OK, pokud vím, e pùlka z nich bude blbì, tohle není dobré øešení
```
	int <- int.TryParse(string s, out int result)
```
vrací to bool, jestli se povedlo, v `out` máme vısledek
**Nevıhoda TrySth vùèi vıjimkám** - kdy u nich zapomenu na try block, program spadne. Zase nezaène dìlat blbosti. V tomhle pøípadì se to jen zahodí a nic se neodhalí, program se pak mùe chovat divnì, i kdy mìl spadnout. Navíc nedostaneme detailní informaci, co se stalo. Dozvíme se jen `false`, ne konkrétní vıjimku. Take vlastnì jako bychom chytali jen obecnou vıjimku...
