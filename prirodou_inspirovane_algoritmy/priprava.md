# Základy strojového učení
### vysvětlit rozdíly mezi jednotlivými typy strojového učení (učení s učitelem, bez učitele, zpětnovazební učení)
#### s učitelem
- zadané vstupy i výstupy
#### bez učitele
- zadané jen vstupy - generuj podobné, poznávej, rozděl do skupin
#### zpětnovazební
- maximlalizuj odměnu v prostředí
### vysvětlit rozdíly mezi jednotlivými úlohami strojového učení s učitelem (regrese, klasifikace)
- skupiny vs číselná hodnota
### popsat typy příznaků (kategorické, ordinální, číselné) a jejich předzpracování (kódování kategorických a ordinálních příznaků, škálování číselných příznaků)
#### číselné příznaky 
- mohou být použity přímo jako vstupy většiny modelů
- bývá lepší data nějakým způsobem přeškálovat
- škálování každého příznaku do intervalu [0,1]
- normalizace, tj. odečtení průměru a vydělení směrodatnou odchylkou
#### Kategorické příznaky (např. textové popisy) 
- mohou mít hodnotu z nějaké konečné množiny a mezi hodnotami není žádné uspořádání
- převést na číselné
- one-hot encoding
#### uspořádané (**ordinální**) příznaky 
- kategorie věku... je třeba s rozhodnout, jak s tím pracovat
- **cyklické údaje**
- třeba čas během dne
- 23:59 a 00:00 jsou blízko, ale jako čísla daleko
- vezmu 24hodiny a čas je sin a cos ručičky

# Zpětnovazební učení
### popsat problém zpětnovazebního učení intuitivně (agent, prostředí, hledání strategie)
Cílem zpětnovazebního učení je naučit se takové chování agenta, které maximalizuje jeho celkovou odměnu, kterou získá z prostředí, pokud bude dané chování používat. Předpokládáme, že agent se vyskytuje v nějakém prostředí, jehož stav může pozorovat a ovlivňovat. Agent běží v cyklech, v každé iteraci $t$ pozoruje stav prostředí $s_t$, na základě pozorování provede akci $a_t$ a tím převede prostředí do nového stavu $s_{t+1}$. Za provedení akce dostane od prostředí odměnu $r_t$.
### popsat prostředí jako Markovský rozhodovací proces
- **čtveřice $(S, A, P, R)$**
- **Stavy, Akce, Přechody**(pravděpodobnosti)**, Odměny**
- pechodová funkce nezávisí na historii jen na aktuálním stavu a akci
-  agent má policy, která říká pravděpodobnost, že ve stavu udělá akci
-  celková suma se počítá s diskontním faktorem $\gamma^t$ aby klesala
### definovat zpětnovazební učení jako problém hledání strategie maximalizující celkovou odměnu agenta
### definovat pojmy hodnota stavu a hodnota akce
-  $V$ je střední hodnota oděny, pokud začínáme v tomhle stavu a jdeme dál
-  ![image](https://github.com/user-attachments/assets/812165cf-7112-41b7-ac86-b45f0e752783)
- navíc su budeme udržovat ![image](https://github.com/user-attachments/assets/065a3735-aa40-4227-8b9b-7b681fbc8a35) což je hodnota akce v tom stavu, pokud budeme dál udržovat policy pí
- **agent hledá optimální strategii maximalizující užitek stavů**
### popsat základní myšlenku Monte-Carlo metod (v rozsahu podle textu na webu)
- udělám akci a ve stavu s a dál pokračuju podle policy, zaznamenám odměnu a spočítám novou policy pro stav s, je to pomalé
### vysvětlit Q-učení (včetně vzorce pro aktualizaci matice Q)
- Bellmanovy rovnice
- ![image](https://github.com/user-attachments/assets/7854f96d-ea83-44ae-a2f0-8515fcc12422)
- propagace užitku z cílových stavů do předcházejících
- ![image](https://github.com/user-attachments/assets/1e53ce3f-42dd-48f0-b8a8-e8eae0b4bed0)
- mám na začátku samé nuly v amtici. Pozoruju stav $s_t$ porvedu akci $a_t$, dostanu odměnu $r_t$ a posunu se do $s_{t+1}$.
- alfa je parametr učení
- nové políčko je to, co mám + reward + maximum z toho, co můžu získat z nového políčka
### popsat algoritmus SARSA
![image](https://github.com/user-attachments/assets/1125d017-db1d-409d-87e5-75e89c14ad10)
- máme policy a víme, co budeme dělat v dalším tahu, odpadá tedy maximalizace, minus v závorce je, protože na začátku není 1-alfa
### vysvětlit, jak použít Q-učení v prostorech se spojitými stavy a akcemi
- rozdělit do intervalů
# Jednoduchý genetický algoritmus
### popsat základní verzi evolučního algoritmu s binárním kódováním
```
P_0 <- náhodně inicializuj populaci
t <- 0
dokud není konec
    vyhodnoť fitness jedinců v P_t
    M_t <- vyber rodiče pro aplikaci operátorů pomocí ruletové selekce z P_t
    O_t <- vytvoř populaci potomků aplikováním genetických operátorů na M_t
    P_{t+1} <- O_t
    t <- t+1
```
### popsat základní genetické operátory (jednobodové, n-bodové křížení, mutace)
- do bodu z jednoho, od bodu z druheho
### popsat ruletovou a turnajovou selekci
- pravěpodobnosti vs zápas
### vysvětlit výhody a nevýhody turnajové a ruletové selekce
- záleží přímo na hodnotách fitness, pokud tedy fitness změníme tak, že k ní přičteme nějakou velkou konstantu, rozdíly mezi jedinci se zmenší a selekce se začne chovat náhodně
- nefunguje pro záporné fitness funkce
- turnajová záleží jen na pořadí jedinců
- můžeme ruletu udělat silnější než turnaj
### vysvětlit, co je fitness funkce
- funkce z jedince na číslo, určje kvalitu jedince
### popsat elitismus
- malá část nejlepších se rovnou kopíruje
### popsat metody pro environmentální selekci (přenos jedinců do další populace), (μ,λ) a (μ+λ)
- mí jsou rodiče, lambda jsou potomci
- mi, lambda - vybírá se lambda nejlepších z potomků
- mi+lambda - vybírá se lambda nejlepších z rodičů i potomků
### popsat rozšíření jednoduchého GA na problémy s celočíselným kódováním
- přirozená čísla, je potřeba změnit mutace
# Evoluční algoritmy pro kombinatorickou optimalizaci
### popsat mutace pro permutační kódování
- `reverse` části jedince - `1,2,3,4,5 -> 1,4,3,2,5`
- `swap` `1,2,3,4,5,6 -> `1,5,3,4,2,6`
- `split and swap` `1,2,3,4,5,6 -> 3,4,5,6,1,2,3`, vlastně rotace - to u TSP nedává smysl, je to furt ta stejná kružnice
- `přesun úseku` `1,2,3,4,5,6 -> 1,4,5,2,3,6`
### popsat křížení používaná při permutačním kódování (OX, PMX, ER)
#### order crossover (OX)
```
1,2|3,4,5,6|7,8
4,7|2,8,5,3|1,6
xxxxxxxxxxxxxxx
prohodím středy
zbytek beru ze svého původního, pokud tam ještě není
4,6|2,8,5,3|7,1
2,8|3,4,5,6|1,7
```
#### partially mapped crossover (PMX)
```
1,2|3,4,5,6|7,8
4,7|2,8,5,3|1,6
xxxxxxxxxxxxxxx
prohodím středy
zkopíruju ze sebe, co jde
8 je namapovaná na 4, místo 8 napíšu 4
etc
1,6|2,8,5,3|7,4
8,7|3,4,5,6|1,2
```
#### edge recombination (ER)
```
1,2,3,4,5,6,7,8
4,7,2,8,5,3,1,6
sousedi:
1 | 2 8 3 6
2 | 3 1 7 8
3 | 4 2 1 5
4 | 5 3 6 7
5 | 6 4 3 8
6 | 7 5 1 4
7 | 8 6 2 4
8 | 1 7 2 5
kouknu na seznamy, vyberu nejkratší příp random
vybrali jsme 1, vyberu si následníka s nejméně záznamy
1,6
2 | 3 x 7 8
3 | 4 2 x 5
4 | 5 3 6 7
5 | 6 4 3 8
6 | 7 5 x 4  <<<
7 | 8 6 2 4
8 | x 7 2 5
----
pokračujeme z 6, vybíráme 5
2 | 3 x 7 8
3 | 4 2 x 5
4 | 5 3 x 7
5 | x 4 3 8 <<<
7 | 8 x 2 4
8 | x 7 2 5
----
1,6,5
2 | 3 x 7 8
3 | 4 2 x x
4 | x 3 x 7 <<<
7 | 8 x 2 4
8 | x 7 2 x
----
1,6,5,4 - trojka se vybírá nenáhodně konečně
2 | 3 x 7 8
3 | x 2 x x <<<
7 | 8 x 2 x
8 | x 7 2 x
----
1,6,5,4,3
2 | x x 7 8 <<<
7 | 8 x 2 x
8 | x 7 2 x
----
1,6,5,4,3,2
7 | 8 x x x
8 | x 7 x x <<<
----
1,6,5,4,3,2,8
7 | x x x x
----
1,6,5,4,3,2,8,7
```
- ale pozor, nemusí mi vyjít hrana z posledního do prvního
### vysvětlit použití evolučních algoritmů pro problém obchodního cestujícího
- viz o nadpis výš, ten příklad je tsp

# Evoluční algoritmy pro spojitou optimalizaci
### definovat problém spojité optimalizace
- spojitá optimalizace $min (f: \mathbf{R} ^ n \rightarrow \mathbf{R})$
- tvar křídla u letadla
- rozmisťování větrníků ve větrných elektrárnách
### popsat genetické operátory používané ve spojité optimalizaci - aritmetické křížení, zatížené a nezatížené mutace
#### křížení
- jedno, více bodové
- aritmetická
    - $w * p_1 + (1-w) * p_2$ z váhy a rodičů
    - $p_1 + w*(p_2 - p_1)$ kde $w$ náleží $(-\alpha , 1 + ,\alpha)$
- rekombinace
    - vyrobí se váženým průměrem superrodič a z něj pak mutacemi potomci
#### mutace
- **nezatížená** - vygeneruje nové číslo
- zatížená - bere pozici rodiče plus $\sigma$ násobek čísla kolem nuly
- jako koeficient násobení můžeme brát třeba i $e^{číslo-z-normálního-rozdělení-0-1}$
- vliv mutace $\sigma$ se hodí postupem času snižovat, je lepší exponenciálně než lineárně
#### pravidlo 1/5
- $\sigma$ velká - skáču víceméně kamkoli
    - pravděpodobnost, že skočíme správně, je délka části, kde jsme lepší než rodič, oproti celé délce
    - čím lepšího máme jedince, tím menší pravděpodobnost, že náhodně vyrobíme lepšího jedince
- $\sigma$ malá
    - pravděpodobnost lepšího je jedna polovina - buď je funkce rostoucí nebo klesající
    - můžeme se posunout jenom kousek
- sledujeme pravděpodobnost zlepšení, když je větší než $1/5$, zvětším $\sigma$, jinak zmenším $\sigma$
- jedna z prvních evolučních strategií
- je to ze 70. let, dnes už se moc nepoužívá
### vysvětlit rozdíl mezi separabilními a neseparabilními funkcemi
- separabilní lze optimalizovat po složkách (zafixujeme všechny proměnné kromě jedné, přes tu najdeme optimum a máme jednu souřadnici optima)
### popsat algoritmus diferenciální evoluce a jeho výhody
#### motivace
nezávislé na rotacích funkce, škálování
#### průběh
- vyber $p_1$ až $p_4$ uniformně náhodně
- $o' = p_1 + F*(p_2 - p_3)$, dobrá počáteční hodnota $0.8$
- $o = uniformXover(p_4, o', CR)$
- $f(o) > f(p_4) ->$ vyber do další generace jen lepšího
- $CR$ je pravděpodobnos, že se vybírá z konkréního z těch jedinců, není to 50:50
- V diferenciální evoluci se provádí mutace, která k danému jedinci přičte rozdíl dvou náhodně zvolených jedinců z populace. Díky tomu je tato mutace invariantní vůči libovolným rotacím a škálováním prohledávaného prostoru. Nevadí jí tedy ani neseparabilní nebo vysoce podmíněné funkce. Kromě této mutace se ještě provádí v zásadě uniformní křížení s dalším náhodným jedincem. V rámci selekce se potom potomek porovná s rodičem a v populaci se nechá jen lepší z nich.

# Lineární a kartézské GP, gramatická evoluce
### popsat lineární genetické programování (kódování jedince, operátory)
- jedinec zapsaný jako posloupnost instrukcí
- například mutace, které změní instrukci za nějakou jinou
- křížení tak, že zkombinujeme části z jednoho programu s částmi z jiného programu
### popsat kartézské genetické programování (kódování jedince, operátory)
- program zadaný na mřížce r×l, která má r řádek a l vrstev (sloupců)
- Jedinec je potom vektor r×l genů
- každý gen se skládá ze dvou částí - kódu (jména) funkce, kterou počítá, a indexů do předchozích vrstev, ze kterých bere vstupy
- v první vrstvě indexy ukazují do pole vstupů
- navíc pro každý požadovaný výstup jedinec obsahuje index, kde se tento výstup v něm počítá
- mutace, která změní funkci, její vstupy, nebo výstup programu.
### popsat gramatickou evoluci (kódování jedince, operátory)
- jedinec **posloupnost čísel**, která určuje, **jaké pravidlo z gramatiky se má použít pro přepis aktuálně prvního neterminálu**
- varianty známých n-bodových operátorů a mutací, které náhodně mění některé geny v jedinci
- křížení, které vybere z každého jedince gen, kde se začíná zpracovávat nějaký neterminál a prohodí ho s pozicí ve druhém jedinci, kde se objeví ten samý neterminál- kromě této pozice se prohazují i další geny, které se týkají zpracování tohoto neterminálu (tj. dokud se nepřejde na další neterminál v jedinci před zpracováním tohoto).
### vysvětlit, jak řešit problém s příliš krátkým nebo dlouhým jedincem v gramatické evoluci
- snažíme generovat delší jedince - pokud jedinec nepoužije některé své geny, tak to až tak nevadí
# Stromové genetické programování
### popsat stromové genetické programování a jeho genetické operátory
- jedinec je strom, který **v uzlech obsahuje funkce (neterminály)** a **v listech vstupy nebo konstanty (terminály)**
- vyhodnocuje se od listů ke kořeni, hodnota kořene je potom výstup programu
- křížení, která prohazují podstromy
- mutace, které změní terminál/neterminál na nějaký jiný
- mutace, ketré nahradí podstrom novým, náhodně vygenerovaným, případě rovnou terminálem
### popsat práci s konstantami ve stromovém genetickém programování
- zadávají mezi terminály jen základní konstanty a předpokládá se, že si program další hodnoty spočítá sám z nich
- mít speciální operátory, které mění hodnoty konstant podobně jako se to dělá ve spojité optimalizaci
### popsat typované genetické programování
- stačí k jednotlivým neterminálům připsat, jaké mají typy vstupů a výstupů
- operátory s tím potom musí umět pracovat
### popsat automaticky definované funkce
- dáme algoritmu možnost vytvořit si vlastní definice některých neterminálů
### vysvětlit, jak u automaticky definovaných funkcí zabránit zacyklení/nekonečné rekurzi
- omezuje množina vstupů, nebo možných neterminálů
### $\color{red}{\text{porovnat jednotlivé typy genetického programování a jejich výhody/nevýhody}}$


# Perceptronové neuronové sítě
### popsat, jakým způsobem počítá jeden perceptron
- f je funkce, která pro x<0 vrací 0 a jinak 1 **signum**
- ![image](https://github.com/user-attachments/assets/ce6a39df-1ba9-4a84-a336-57f9e306cc61)
### popsat algoritmus pro trénování perceptronu a vysvětlit, kdy konverguje
![image](https://github.com/user-attachments/assets/9226035a-2c99-401d-a78d-e740c5e53874)
- lineárně separabilní třídy, pak konverguje
### popsat strukturu vícevrstvých perceptronových sítí
- první vrstva na vstup, další skryté na sebe, výstupní 
### popsat aktivační funkce používané ve vícevrstvých perceptronech (sigmoida, tanh, ReLU)
- **relu** ![image](https://github.com/user-attachments/assets/80b33ea9-1f47-49c2-8d38-16b0f213072e)
- **sigmoida** ![image](https://github.com/user-attachments/assets/f192e808-4cf1-4782-9269-5ffed7c2ed05) - dává od 0 do 1
- **hyperbolický tangens** - dává od -1 k 1
### popsat metodu pro trénování neuronových sítí (backpropagation)
- ![image](https://github.com/user-attachments/assets/5773ce0a-61af-4791-ab22-374244938bdc)
- L je chybová fuknce
- alfa je parametr učení
### odvodit pravidla pro adaptaci vah v metodě zpětného šíření (alespoň pro výstupní vrstvu + ideu pro skryté vrstvy)

# RBF sítě - Radial Basis Functions
### popsat RBF jednotku a architekturu RBF sítě
- neurony ve vstupní vrstvě místo skalárního součinu počítají nějakou funkci závislou na vzdálenosti od svého “váhového” vektoru - středu
- ![image](https://github.com/user-attachments/assets/0a2de7ba-b2c3-4cce-a8e8-150e960f0623)
### vysvětlit princip trénování RBF sítí
- napřed se pomocí nějakého shlukovacího algoritmu nastaví středy neuronů ve vstupní vrstvě
- potom se spočítá parametr β v každém neuronu jako ![image](https://github.com/user-attachments/assets/66fdcce5-757f-4566-ae0a-042e3acddf83)
- sigma je průměrná vzdálenost od středu
### popsat algoritmus k-means
- náhodně zvolí k středů shluků mj (často jako k vzorků ze vstupních dat)
- potom opakuje fáze přiřazování a přepočítávání středů
- ve fázi přiřazování je každý vzorek ze vstupních dat přiřazen do toho shluku, jehož střed je k němu nejblíž
- ve fázi přepočítání středů se každý střed přepočítá jako střed shluku dat, které k němu patří
- tyto dvě fáze se opakují, dokud algoritmus nezkonverguje, nebo po zadaný počet iterací.
### porovnat geometrické vlastnosti RBF sítí a vícevrstvých perceptronů

# Konvoluční sítě
### popsat funkci konvolučního filtru
### popsat funkci pooling vrstev v konvoluční síti
### popsat architekturu konvoluční neuronové sítě
### vysvětlit, co jsou matoucí vzory a jak ovlivňují praktické nasazení neuronových sítí
### popsat algoritmus FGSM pro vytváření matoucích vzorů
### vysvětlit myšlenku přenosu uměleckého stylu
### vysvětlit základní fungovaní generative adversarial networks (podle rozsahu v textu na webu)

# Rekurentní neuronové sítě
### definovat, co jsou rekurentní neuronové sítě
### vysvětlit, kde se rekurentní sítě používají
### vysvětlit trénování rekurentních sítí pomoci algoritmu zpětného šíření v čase
### vysvětlit problém s explodujícími a mizejícími gradienty
### popsat princip Echo State sítí
- ![image](https://github.com/user-attachments/assets/3e57f1fe-f08e-4cbe-8189-07bedbb7a68b)
- na vstup dostane vektor délny $n$, přidá k tomu vnitřní stav délky $m$, hodí to do vnitřní vrstvy a dostane nový vnitřní stav
### popsat trénování Echo State sítí
- netrénuje se rekurentní část
- trénuje se výstupní pomocí gradientní metody třeba
### popsat LSTM buňku a architekturu LSTM sítě
- ![image](https://github.com/user-attachments/assets/b9131d7c-e801-4688-8dee-de1daa3ea14b)
- místo neuronů LSTM buňky
### vysvětlit výhody Echo State sítí a LSMT sítí v porovnání se základními rekurentními sítěmi
