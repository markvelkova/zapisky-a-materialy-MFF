# úvod
## dělení AI
### symbolická AI
- logika
- plánování
- prohledávání

### výpočetní AI
- strojové učení
    - lineární modely
    - rozhodovací stromy
    - neuronové sítě
        - deep learning -> v tom někde LLM

- evoluční algoritmy
- fuzzy logika
    - hodnoty nejsou 0,1 ale na škále
    - konjukce je minimum, disjuknce maximum 
    - nebo konjunkce násobení, disjunkce sčítání

## dělení strojového učení
### učení s učitelem (supervised)
- dostanu vstup, vím, co má být výstup
- **regrese** pokud je výstup číslo
- **klasifikace** pokud je výstup rozdělení do tříd


### učení bez učitele (unsupervised)
- dostanu vstup, ne výstup
- **clusterování** snažím se v datech najít nějké vzory a rozdělit
- **generativní** modely - ze 100 obrázků koček to bude genrovat další
- **hledání anomálií**

### zpětnovazební učení (reinforcement)
- máme agenta v prostředí, musí se tam naučit chovat, aby maximalizoval odměny
- prostředí nám dává zpětnou vazbu


## jak si poradit s různorodými daty
**velikosti**
- škálování na 0-1
- odečíst průměr a vydělit odchylkou

**kategorie**
- očíslovat? potom říkám, že hodnoty spolu souvisí (Praha, Brno, Ostrava - dvě Prahy jsou Brno)
- nejaký encoding, **příznaky** `jePraha`, `jeBrno`...
- uspořádané (**ordinální**) příznaky - kategorie věku... je třeba s rozhodnout, jak s tím pracovat

**cyklické údaje**
- třeba čas během dne
- 23:59 a 00:00 jsou blízko, ale jako čísla daleko
- vezmu 24hodiny a čas je sin a cos ručičky

## evoluční algoritmy
- pracují s populací kandidátů na řešení
- na tom se simuluje přirozená evoluce

## simple genetic algorithm
- jedinci kódování jako sekvence nul a jedniček
- na SAT nebo problém batohu
    - 011010 - 0 beru předmět, 1 neberu předmět
- **fitness** funkce
- křížení 
    - například rozseknout a prohodit konce
**průběh**
```
dostaneme náhodnou populaci
spustíme cyklus - iterace je generace
 vyhodnotit fitnessy
 v cyklu  - vyber rodiče 
          - udělej předpotomka
          - spusť mutace
 nahraď původní populaci novou
```

## neuronové sítě
- **neuron**
- perceptron 
    - dělá velmi jednoduchou operaci, lineární funkce
    - několik vstupů, jeden výstup


# 2. přenáška - zpětnovazební učení
## agenti obecně
- i vypínač - akce-reakce, termostat
- mnoho různých definic
## agenti ve zpěnovazebním učení
- maximalizuje odměnu
## Markovský rozhodovací proces (MDP)
- **čtveřice $(S, A, P, R)$**
    - $S$ - množina stavů
    - $A$ - množina akcí (případně $A_s$, akce proveditelné ve stavu $s$)
    - $P_a(s, s')$ - pravděpodobnost, že po provedení akce $a$ ve stavu $s$, přejde do stavu $s'$ - nedeterministické
        - může se psátt též jako $P(s,a,s')$ nebo $P(s,a)$ to ale vrací stav, pokud máme determinismus 
    - $R(s, s')$ - funkce odměny za přechod - může být záporná i kladná
        - též $R(s)$, nebo $R_{s}(a)$, nebo $R(s,a)$
- agent začne v $s_0$, udělá akci $a_0$, přejde do $s_1$, dostane odměnu $r_0$ etc
- **celková odměna**
    - $R = \sum(r_t) = \sum{R_{a_t} (s_t, s_{t+1})}$ 
    - nekonverguje, je to nekonečná suma, potřebujeme $\gamma$ diskontní faktor (*discount factor*), ten je menší než 0, takže se pořád zmenšuje a suma konverguje, pak:
    $R = \sum \gamma^t R_{a_t} (s_t, s_{t+1})$
    - problém je, že $\gamma$ je třeba vybrat správně, příliš malá omezuje rozhodování na moc malém počtu kroků
- **pravděpodobnost provedení akce**
    - $\pi: S \times A \to [0,1]$
    - $\pi (s,a)$ - pravděpodobnost, že ve stavu $s$ provede akce $a$
        - nebo $\pi (s) = a$ u deterministického agenta
- **jak dobrý je pro mě stav**
    - $V^\pi(s) = E[\sum \gamma^t R_{a_t} (s_t, s_{t+1})]$
    - střední hodnota přes akce ve daném stavu, stední hodnota je potřeba, protože to není deterministické
- **hodnota akce při stavu**, jak dobrá je akce pi stavu
    - $Q^\pi (s,a) = E_{\pi}[R + \gamma V^\pi(s')]$ - odměna za akci plus jak dobrý je stav, kam se dostanu
    - $Q^\pi (s,a) = E[R_0 | a_t \~{} \pi(s_t), s_0 = s, a_0 = a]$
    - kdybychom perfektně znali Q hodnoty, může prostě vybírat $argmax_{a\in A} Q^{\pi *} (s,a)$
## základní rovnice
- $T^* = argmax_\pi V^\pi(s)$
- $argmax_{a\in A} Q^{\pi *} (s,a)$
## explorace, exploitace
- průzkum nových, braní nejlepší známé akce
- je pořeba to vyvážit
### $\epsilon$-greedy 
- s pravděpodobností $\epsilon$ vyberu náhodnou akci
- nejdřív vysoké $\epsilon$, postupně snižujeme, takže konvergujeme k používání nejlepší známé
### co nám dá $Q(s_t,a_t)$?
- $Q(s_t,a_t) = E [ R_{a_t}(s_t, s{t+1}) + \gamma V^\pi(s{t+1}) ]$
- můžeme vyjádřit V

nebo

- $V(s_t) = \sum {\pi(s_t, a)} * \sum{P_{a_t}(s_t, s_{t+1}} = E[R_{a_t}(s_t, s_{t+1}) + \gamma V(s_{t+1})]$

## Q učení - algoritmus
- snažíme se naučit Q, je to matice, řádky stavy, sloupce akce
- pustíme agenta a přepočítáváme Q
- inicializuju všechno na nula
- agent provede první akce, dostane první odměnu
- zapíšeme si do matice, využiju přiom rovnice $Q(s_t,a_t)$
- vyberu přitom jen nejepší akci pokračující ze stavu, kam jsme se dostali naší akcí
- $Q(s_t,a_t) = r_t + \gamma * max_a Q(s_{t+1}, a)$
- ale to bychom si přepsali historii, takže udělám vážený součet nového s tím, co už jsem věděl
### **první varianta udržení historie**
- $Q^{new}(s_{t},a_{t}) \leftarrow (1-\alpha) \cdot Q(s_{t},a_{t}) + \alpha \cdot  \bigg( r_{t} + \gamma\cdot \max_{a}Q(s_{t+1}, a) \bigg)$
    - první je historie, druhé je teď
    - nechceme to přepsat kvůli nedeterminismu
    - nemusíme pak udělat tu akci $a$, která byla maximální vpravo
    - propisují se hlavně ty nejnovjší samozřejmě, ale pořát to udržuje nějakou historii
### **druhá varianta**
- $Q^{new}(s_{t},a_{t}) \leftarrow \alpha \cdot \bigg( r_{t} + \gamma\cdot \max_{a}Q(s_{t+1}, a) - Q(s_{t},a_{t})\bigg)$
### **co když máme jen V**
- přejdu do akce s největším rewardem
- maximalizujeme přes $a$ sumou přes $s'$ - $\sum {P_a (s, s') * V(s')}$

### nevýhody Q-učení
- pokud budu vždycky nadhodnocovat nějakou akci, pak ji budu pořád vykonávat a nezkusím jinou

## SARSA (state-action-reward-state-action)
- učí se to Q hodnotu současné strategie, současného chování
- například některé akce jsou pro robota nebezpečné, to nechceme
- Q učení se naučí rychleji, nakonec se naučí optimum i kdybychom používali úplně náhodné kroky
- sarsa ho nutí, aby se vyhýbal dírám, za to dostane hodně zápornou odměnu, výrazně víc, než v Q učení
- Q učení má nastavené explorativní chování, stejně by tam vlezlo znova
- učí se pomaleji, je stabilnější

# 3. přednáška - evoluční algoritmy
- genetické algoritmy - jedinci vektory 0,1
- genetické programování - jedinci třeba stromy výrazů
- evoluční programování - evoluce konečných automatů
- evoluční strategie - vektory reálných čísel...
- typicky je problémově specifický algoritmus lepší než genetický
## simple genetic algorithm
- chceme maximalizovat fitness funkci
### fitness funkce
- $f: D \rightarrow \mathbf{R}$, kde $D$ je ${0,1}^n$
- $f(x) = \sum x_i \space xor \space p_i$ nebo $f(x) = \sum I(x_i == p_i)$, kde $I$ je indikátor

- problémy řešitelné - součet podmnožiny, problém batohu
### součet podmožiny
- $min |k-\sum A|$, kde $A$ je množina, $k$ je cílový součet
- ale algoritmus chce maximalizovat...
    - záporné hodnoty? 
    - $\frac {1}{|k-\sum A|}$
    - přičtení velkého čísla
```
pop <- náhodná populace
while not happy:
    fits <- vyhodnoť pop (f(ind) for ind in pop)
    //making_pool je seznam jedinců, lepší víckrát, horší chybí, beru dvojice, každá má dva potomky
    mp <- select (pop, fits)
    off <- crossover(mp)
    off <- mutation(off) 
    pop = off
return pop (nebo jen nejlepšího)
```
### jak udělat selekci
#### ruletová selekce
- pravděpodobnost výběru jedince odpovídá fitness
- ruleta má tolik políček, kolik je součet fitnessů
- $p_{ind} = \frac{f_{ind}}{\sum f_j}$
- vadí záporná fitness, vadí některá škálování
- můžeme ji udělat silnější než turnaj
#### turnajová selekce
- nevadí škálování, nevadí záporná fitness
```
i1, i2 <- vyber uniformně náhodně z populace
if f(i1) > f(i2) and rand() < 0.8 //tím and oslabujeme selekci
    return i1
else
    return i2
```
### křížení
- bere dvojice rodičů
#### jednobodové křížení
- náhodně vyberu bod a prohodím konce
#### dvoubodové křížení
- vyberu dva body, prohodí, neprohodí, prohodí etc.
#### uniformní křížení
- na každou pozici hodíme korunou, od koho to vezmeme
- druhý potomek buď komplementárně, nebo znovu
- může být víc rodičů klidně
### mutace
- pokud znám problém, můžu tady zapojit heuristiky
- můžu mít víc mutací klidně
#### bit-flip
- s určitou pravděpodobností změním bit
### jak nepřijít o nejlepší řešení
#### elitismus
- automaticky nahradíme náhodého potomka nejlepším rodičem
#### jiné počty v generacích
- nebo obecně upravit změnu generace `pop = off` - $(\mu, \mu)$
- $(\mu, \lambda)$ kde $\lambda > \mu$
- $(\mu + \lambda)$ - zajistí, že nepřijdeme o nejlepší řešení, třeba $100+1$ nebo $1+100$

# 4. přednáška - evoluční algoritmy poračování
## jak zobecnit evoluční algoritmy
- čísla od $0$ do $k$
- barvení grafu - počet správných hran
## jedinec jako permutace
- permutace viz **problém obchodního cestujícího**
- jedinec je permutace od $1$ do $k$
### mutace
- `reverse` části jedince - `1,2,3,4,5 -> 1,4,3,2,5`
- `swap` `1,2,3,4,5,6 -> `1,5,3,4,2,6`
- `split and swap` `1,2,3,4,5,6 -> 3,4,5,6,1,2,3`, vlastně rotace - to u TSP nedává smysl, je to furt ta stejná kružnice
- `přesun úseku` `1,2,3,4,5,6 -> 1,4,5,2,3,6`
### kde ještě se dají použít permutace
- **bin packing** - umístit předměty do přihrádek (binů) tak, že se minimalizuje počet binů
    - permutace je pořadí, v jakém máme brát předměty
### křížení u TSP
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
#### order crossover (OX)
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
## jedinec jako vektor reálných čísel
- spojitá optimalizace $min (f: \mathbf{R} ^ n \rightarrow \mathbf{R})$
- tvar křídla u letadla
- rozmisťování větrníků ve větrných elektrárnách
### křížení
- jedno, více bodové
- aritmetická
    - $w * p_1 + (1-w) * p_2$ z váhy a rodičů
    - $p_1 + w*(p_2 - p_1)$ kde $w$ náleží $(-\alpha , 1 + ,\alpha)$
- rekombinace
    - vyrobí se váženým průměrem superrodič a z něj pak mutacemi potomci
### mutace
- nezatížená - vygeneruje nové číslo
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
