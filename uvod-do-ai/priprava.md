# Expected knowledge from course NAIL120 Introduction to Artificial Intelligence
## Intelligent agents, environment, and structure of agents:
### Define an agent and a rational agent.
- agent je cokoli, co vnímá sensory okolí a provádí akce
- racionální agent vybírá takovou akci, o které předpokládá, že bude maximalizovat jeho odměnu
![image](https://github.com/user-attachments/assets/81461212-4ffe-4025-8985-289aab5cb8a8)
### Explain properties of environment 
- **partial/full observability**
    - sensory vidí ompletní stav prostředí
- **deterministic/stochastic**
    - příští stav prostředí je určen předchozím stavem a provedenou akcí
- **episodic/sequential**
    - atomické epizody, další nezávisí na akcích v minulých epizodách
- **static/dynamic**
    - prostředí se nemění 
- **discrete/continuous**
     - stav prostředí, zpracování času, akce agenta
- **single/multi-agent**
    - agenti jsou ty entity, které můžeme popsat rovnicí výše
### Explain and compare reflex and goal-based agent architectures
#### reflex
- na základě stavu světa vybere akci
- ![image](https://github.com/user-attachments/assets/5e1426ea-4ea5-41a0-b99f-e20e7ba8e34e)
#### goal based
- zvažuje ještě cíl
- ![image](https://github.com/user-attachments/assets/a548de3b-c206-402b-b66a-7f910ba3f75b)
### possible representations of states (atomic, factored, structured).
#### atomický stav
- nedělitelný, bez vnitřní struktury, třeba v prohledávání
#### factored
- vektor hodnot - constrainy, výroková logika, plánování
#### strukturovaný stav
- množina objektů, které mají mezi sebou různé vztahy
- predikátová logika

## Problem solving and search:
### Formulate a well-defined problem and its (optimal) solution, give some examples.
- atomická reprezentace stavů
- cíl je množina cílových stavů
- akce popisují přechody mezi stavy
- **úkol je najít sekvenci akcí, které vedou k cíli**
- prohledávání
- **agent je open loop**
- **prostředí je celepozorovaelné, dirskrétní, statické a deterministické**
![image](https://github.com/user-attachments/assets/4e0beddc-973d-42a6-b719-93b9dd1774b7)
### Explain and compare tree search and graph search, discuss memory vs time.
#### tree search
- když je fronta prázdná, vrátím false, jinak si vyberu vrchol, pokud goal..., pokud ne, dám jeho syny do fronty
- může prozkoumáva jeden stav víckrát, pokud do nj dojde různými cestami
#### graph search
- přidává syny do fronty jen poud nejsou v navštívených

### Explain and compare node selection strategies (depth-first, breadth-first, uniform-cost).
| **Vlastnost**                   | **Prohledávání do šířky (BFS)**                               | **Prohledávání do hloubky (DFS)**                                                                                       |
|--------------------------------|----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| **Pořadí rozšiřování uzlů**     | Nejmenší neprozkoumaná hloubka (FIFO)                         | Nejhlubší neprozkoumaný uzel (LIFO)                                                                                      |
| **Úplnost**                     | **Úplné** (při konečném faktoru větvení)                          | **Úplné pro graph search**<br>**Neúplné pro tree search**                                                          |
| **Optimálnost**                | Optimální (pokud cena cesty neklesá s hloubkou)                | Neoptimální (lze upravit na optimální pomocí „branch-and-bound“)                                                        |
| **Časová složitost**           | $O(b^d)$, kde b je faktor větvení a d je hloubka cílového uzlu   | $O(b^m)$, kde m je maximální hloubka jakéhokoliv uzlu (m může být výrazně větší než d)                                     |
| **Paměťová složitost**         | $O(b^d)$                                                        | $O(bm)$                                                                                                                 |
| **Paměťové nároky**            | Paměť je větší problém než samotný výpočet                     | Zpětné prohledávání: generuje se vždy jen jeden nástupce, paměť = $O(m)$                                                   |
<br>**uniform cost je dijkstra**<br>
### Informed (heuristic) search: explain evaluation function f and heuristic functionh, define admissible and monotonous heuristics and prove their relation.
![image](https://github.com/user-attachments/assets/8b30ca36-0fde-473e-b2e2-d737e26df4a5)
- g je vzálenost od startu zjevně, tu totiž známe
#### Přípustná heuristika (h(n))
- heuristika mensi nebo rovna „ceny nejlevnější cesty z uzlu n do cíle“
- optimistický pohled (algoritmus předpokládá lepší náklady, než jaké ve skutečnosti jsou)
- funkce (f(n)) v algoritmu A* je dolní odhad nákladů cesty přes uzel (n)
#### Monotonní heuristika (h(n))
- nechť $n'$ je následník uzlu $n$ přes akci $a$ a $c(n, a, n')$ je cena přechodu
- h(n) <= c(n, a, n') + h(n')
- trojúhelníková nerovnost
#### Monotonní heuristika je přípustná - důkaziště.

Nechť $n_1, n_2, \ldots, n_k$ je optimální cesta z $n_1$ do cílového uzlu $n_k$, potom:

$$
h(n_j) - h(n_{j+1}) \leq c(n_j, a_j, n_{j+1}) \quad \text{(protože monotonie)}
$$

$$
h(n_1) \leq \sum_{i=1}^{k-1} c(n_i, a_i, n_{i+1}) \quad
$$

#### při monotónní heuristice hodnoty f(n) jsou neklesající po cestě
Nechť $n'$ je následník $n$, tedy z $g(n') = g(n) + c(n,a,n')$, potom: <br>
$f(n') = g(n') + h(n') = g(n) + c(n,a,n') + h(n') \leq g(n) + h(n) = f(n)$


### Describe A* algorithm and prove its properties (optimality for tree search and graph search).
- dijkstra s heuristikou
#### optimalita ve stromoprohledu - přípustnost
- **první expandovaný cíl je optimální**
- nechť $G2$ je neoptimální cíl objevený první a $C*$ je optimální cena

$$
f(G2) = g(G2) + h(G2) = g(G2) > C\*  \quad \text{(protože heurisitka cíle je 0)}
$$

- nechť $n$ je libovolný vrchol na optimální cestě

$$
f(n) = g(n) + h(n) = \leq C\*  \text{(protože přípustnost)}
$$

$$
f(n)\leq C\*  < f(G2) 
$$

#### optimalita v grafoprohledu - monotonie
- problém by mohl být, že bychom přišli do vrcholu podruhé lépe, ale to by se ignorovalo
- s monotónní heuristikou nám ale $f(n)$ nikdy neklesá podél  jakékoli cesty
- a* vybere ten s nejmenším $f$, ze všech otevřených cest nemůže být kratší než tato

## Constraint satisfaction:
### Define a constraint satisfaction problem (including the notion of constraint) and give some examples of CSPs.
- **konečná množina proměnných**
- **konečná množina hodnot pro každou proměnou - doména**
- **konečná množina podmínek**, kde podmínka je relace přes podmnožinu proměnných, specifikuje povolené tupples například
- **řešení je úplné konzistentní ohodnocení proměných**

### Apply problem solving techniques to CSPs, explain why a given technique is appropriate for CSPs.
#### backtracking
- přiřaď hodnotu, když se to rozbije, vrať se a přiřaď jinak
- dá se nsadno rozšířit, je korektní
### Explain principles of variable and value ordering and give examples of heuristics.
- při backtrackingu zkouší hodnoty a vybírá proměnné v nějakém pořadí
#### FAIL-FIRST - VARIABLE ORD
- **dom** - nejmenší doména
- **deg** - nejvíc podmínek

#### SUCCEED-FIRST - VALUE ORD
- například aby omezovala conejmene ostatnich proměnných
- heuristiky specifické pro problém, obecně nelehký problém
### Define arc consistency and show an algorithm to achieve it (AC-3).
- hrana je konzistentní právě tehdy, když pro každou hodnotu ve výchozím, najdu  následujícím vrcholu hodnotu, která splňuje podmínku
![image](https://github.com/user-attachments/assets/d43224ec-fdcd-49f3-aa35-9c5f26226d83)
#### AC-3 algoritmus
- na počátku bylo slovo, teda fronta všech hran
- dokud nneí prázdná, vyber hranu, zkontroluj, jesli obsahuje něco nekonzistentního, pokud ano a my jsme to odstranili z výchozího vrcholu, musíme přidat do front všechny sousedy výchozího vrcholu
- čekáme, až bude fronta prázdná
### Define k-consistency and explain its relation to backtrack-free search; give an example of a global constraint.
![image](https://github.com/user-attachments/assets/77b88411-b0ac-4ae1-a17a-417f62d00352)
- **pro konzistentní ohodnocení k-1 proměnných garantujeme konzistenní ohodnocení k-té**
![image](https://github.com/user-attachments/assets/f5137c3e-58d6-41a2-8fcd-43fa661a59df)
- *Pokud je problém pro všechna i do n i-konzistentní, pak se dá vyřešit bez backtracku*
- ale časová komplexita k-konzistence je exponenciální v k
### Explain forward checking and look ahead techniques.
#### forward checking
- když přiřadím hodnotu, odtsraním všechny varianty, které to zablokovalo - u královen disable všechna políčka, kam poslední přidaná útočí
#### look ahead
- po každém přiřazení obnov konzistenci
- nedívá se jen na přímé sousedy jako forward

## Knowledge representation and propositional logic:
### Define a knowledge-based agent.
- má knowledge base (dále studnice vědomostí), a counter
```
TELL (studnice, make_percept_sentence(percept, t))
action = ASK (studnice, make_action_query(t))
TELL (studnice, make_action_sentence(action)
t++
return action
```
### Define a formula in propositional logic, describe conjunctive and disjunctive normal forms (and how to obtain them), define Horn clauses.
- netřeba
### Explain the notions of model, entailment, satisfiability, and their relations.
- netřeba
### Explain DPLL algorithm (including the notions of pure symbol and unit clause).
- netřeba
### Explain resolution algorithm (and how it proves entailment) and explain forward and backward chaining as its special cases.
- když mám hornovské klauzule, můžu udělat resoluci v lineárním čase
#### forward chaining
![image](https://github.com/user-attachments/assets/8af946ce-a5c3-4bd9-bf4c-743dad25b789)
#### backward chaining
![image](https://github.com/user-attachments/assets/aa08b0cb-d2c0-4ca5-b64c-eebf4a688e2d)

## Automated planning:
### Define planning domain and problem (representation of states, planning operator vs action, applicability and relevance of action, transition function, regression set).
#### problém
- $(doména, s_0, goal)$, kde doména jsou stavy, akce, tranziční funkce
- ![image](https://github.com/user-attachments/assets/8ceac722-4373-4d4e-9eb8-469e584317c5)
#### stav
- **stav je set instancí atomů**, stavů je konečné množství
- stav splňuje cíl, pokud splňuje všechny pozizivní atomy cíle a nesplňuje žádný negativní axon cíle

#### transition funkce
- γ(s, a) jak se zmeni stav aplikaci akce
- a je applicable
    – γ(s, a) = (s − effects−(a)) ∪ effects+(a)
    – effects+(a) pozitiví efekty a
    – effects−(a) negativní efeky a
- a není applicable - γ(s, a) nedefinovaná

#### operátor
- trojice $(jmeno(o), předpoklady(o), efekty(o))$
#### akce 
- operátor, kde je za proměnné explicitně dosazeno
#### applicable akce
- poziivní předpoklady akce jsou podmnožinou stavu, s negativními není průnik
#### relevatní k cíli
- průnik cíle a efektů je neprázdný
- efekty nejsou v konfliktu:
![image](https://github.com/user-attachments/assets/9733aebc-acdd-4b19-9ff0-7befb40b9966)
#### regression set
![image](https://github.com/user-attachments/assets/1116da1d-4fbc-4b1b-8798-2f1cf1f0f44b)
### Explain progression/forward planning and regression/backward planning.
![image](https://github.com/user-attachments/assets/7b13e603-a3c7-4e2e-9907-0fbaf138d1e6)
- můžeme to dělat ideáln+ A*
![image](https://github.com/user-attachments/assets/b394bbcf-17c7-4c4f-bea4-2eca12b857fe)
- prohledává jen relevantní stavy
- je těžší najít heuristiku
### Describe how planning can be realized by logical reasoning (situation calculus).
- předpoklady implikují possibility akce
- vlastnosti dalšího savu:
![image](https://github.com/user-attachments/assets/f8544fa6-8068-481b-90ae-ef26bf9b0dab)
- ptáme se ,zda existuje takový stav s, že jsou v něm splněny goal condiions



## Knowledge representation and probabilistic reasoning:
### Define the core notions 
#### event - jev
- množina elementaárních jevů
#### random variable
- má doménu, třeba kostka 1 má doménu {1,2,3,4,5,6}
#### conditional probability
![image](https://github.com/user-attachments/assets/78880fee-3473-4284-8706-8672d31ca82b)
#### full joint probability distribution
- tabulka se všemi možnostmi, jak se to může nakombit
- ![image](https://github.com/user-attachments/assets/406e4300-d01e-4a62-bb2f-2068e51ada0f)
#### independence
### Explain probabilistic inference 
![image](https://github.com/user-attachments/assets/fa5d173d-6ed8-4dc6-ae6a-975c43a36c9e)
- Y nás zajímají, E víme, H nás nezajímají a musíme to projet přes všechny jejich možnosti
#### Bayes’rule 
![image](https://github.com/user-attachments/assets/d9e75365-5631-4c73-9378-8a7d4a0f6ac6)
#### marginalization
- vysčítání přes elementární jevy
- ![image](https://github.com/user-attachments/assets/50fe079a-c67c-4688-af27-e69bd26aa154)
#### normalization
- máme evdence a počítáme pravděpodobnos jevu
- ![image](https://github.com/user-attachments/assets/fde97654-75f7-438f-9f1b-fa10f54bd69f)
- kdž to spočítám pro všechny jevy, musí se to sečíst na jedničku
![image](https://github.com/user-attachments/assets/fe419baa-4f7d-4685-82a8-1a603c833960)
- takže $P(E-e)$ nemusím počítat, hodím tam alfu
- ![image](https://github.com/user-attachments/assets/178c36bf-5133-4ae8-8625-410d11da9a03)
- ![image](https://github.com/user-attachments/assets/0927cbfe-c734-4277-90b6-b39a224c7c12)
#### causal direction vs diagnostic direction
![image](https://github.com/user-attachments/assets/7368094d-a228-4d6e-8c2a-8e0e9828e234)

### Define Bayesian network etc
#### bayesovska síť
- využívá podmíněné nezávislosti
- je to DAG
- vrcholy jsou random vars, každý vrchol má svoji tabulku podmíněné pravděpodonosti na základě rodičů
- beru vrcholy a cokoli, co s nimi souvisí, do nich zapojím
#### explain its relation to full joint probability distribution
- reprezentuje ji
- nemusíme uchovávat celou tu tabulku, protože všecho můžeem dopočítat
- ![image](https://github.com/user-attachments/assets/54c41e18-cee6-40a5-9038-4f9b6730b6c5)
#### explain chain rule
- ![image](https://github.com/user-attachments/assets/b6704e93-6750-405f-9ab5-d261551a7ec6)
### describe inference techniques for Bayesian networks 
- to je to s hidden proměnnýma
- marginalizace
- ![image](https://github.com/user-attachments/assets/e892fb89-8018-4081-95cb-6d8eb1b9efc6)
- faktorizace
- ![image](https://github.com/user-attachments/assets/323e66d9-358d-4755-ae12-3096786a20d9)
- například vloupání za předpokladu, že volá Marry i John
- na vloupání (b) a zemětřesení (e) závisí alarm (a)
- na alarmu závisí, jestli volají John (j) a Mary (m)
- hidden jsou alarm a zemětřeseni
1. marginalizace přes $a$ a $e$
- $P(b|j,m) = \alpha \sum_{e} \sum_{a} P(b,e,a,j,m)$
2. faktorizace přes strukturu sítě
-  pravidlo **$P(x_1,...,x_n) = \prod_{i}P(x_i | parents(X_i)$**
- $P(b|j,m) = \alpha \sum_{e} \sum_{a} P(b) * P(e) * P(a|b,e) * P(j|a) * P(m|a)$
#### enumeration
- to o jeden nadpis výš, opakuje stejné části víckrát, není efektivní
- když so to uložíme, máme dynamické programování
- uděláme si faktory, což jsou matice popisující ty tabulky
- postupně se vyplňují
#### variable elimination
- násobením faktorů získáme tabulku pro sjednocení
- sčítáním můžeme eliminovat proměnnou
![image](https://github.com/user-attachments/assets/d5f1ceb5-aec0-42af-929c-4268042da7a4)
#### rejection sampling
- vyhodíme nekonzstentní s evidence
#### likelihood weighting
- zafixujeme evidence
- výsledek má váhu podle pravděpodobnosti evidence vůči vzorku
![image](https://github.com/user-attachments/assets/71dd8bf7-cc77-47b9-a1be-074104f71e68)

## Probabilistic reasoning over time:
### Define transition and observation models and explain their assumptions
#### transition model
![image](https://github.com/user-attachments/assets/3e71c6dd-3263-4fe1-a198-0f23868f8e2c)
- **stav závisí jen na předchozím stavu** tedy $=P(X_t | X_{t-1}$
#### observation model
![image](https://github.com/user-attachments/assets/46dfed73-dae4-4f42-b75e-adbb34c657da)
- **pozorování závisí jen na akuálním stavu** tedy $=P(E_t | X_t)$
### Define basic inference tasks and show how they are solved.
#### filtering
![image](https://github.com/user-attachments/assets/8e811c2e-355d-42a4-aef7-1962424a71c0)
- je potřeba jít od začátku
- $P(X_{t+1}|e_{1:t+1}) = P(X_{t+1}|e_{1:t},e_{t+1})$
> Bayes: $P(A|B,C) = (P(B|A,C)*P(A|C)) / P(B|C)$ <br>
> ale jmenovatel se nám vleze do alfy, takže
- $P(X_{t+1}|e_{1:t},e_{t+1}) = \alpha P(e_{t+1}|X_{t+1},e_{1:t})*P(X_{t+1}|e_{1:t})$
> pozorování závisí jen na aktuálním stavu
- $= \alpha P(e_{t+1}|X_{t+1})*P(X_{t+1}|e_{1:t})$
> uděláme si vpravo sumu přes všechny možné stavy minulé
- $= \alpha P(e_{t+1}|X_{t+1})*\sum_{x_t}P(X_{t+1}|x_t,e_{1:t})*P(x_t|e_{1:t})$
> stav závisí jen na předchozím stavu
- $= \alpha P(e_{t+1}|X_{t+1})*\sum_{x_t}P(X_{t+1}|x_t)*P(x_t|e_{1:t})$
> $P(x_t|e_{1:t})$ rekurzivní část<br>
> počítáme stejně, dokud nenarazíme na čas 0, kde jsou přímo hodntoy pravděpodobností pro výchozí stav
- ![image](https://github.com/user-attachments/assets/a6c80f2f-97cc-4236-8ba3-05ca06d31266)
#### prediction
![image](https://github.com/user-attachments/assets/ce3e2b04-4d7c-4f3e-b984-ee4eeab460cf)
- filtering bez přidání nové evidence
- $P(X_{t+k+1}|e_{1:t}) = \sum_{x_{t+k}}P(X_{t+k+1}|x_{t+k})P(x_{t+k}|e_{1:t})$
- když půjdu pořád do budoucnosti, ustálí se to na nějaké standartní distribuci a zůstane konstantní
#### smoothing
- dopočítáváme nějaký okamžik minulosti, když víme evidence za celou dobu
- vlastně filtering s evidencí navíc asi
![image](https://github.com/user-attachments/assets/8a17c07a-2d52-4119-879c-840795b8b1ba)
- $P(X_k|e_{1:t}) = P(X_k|e_{1:k},e_{k+1:t})$
> Bayes: $P(A|B,C) = (P(B|A,C)*P(A|C)) / P(B|C)$ <br>
> ale jmenovatel se nám vleze do alfy, takže
- $P(X_k|e_{1:k},e_{k+1:t}) = \alpha P(e_{k+1:t}|X_k,e_{1:k})*P(X_k|e_{1:k})$
> z podmíněné nezávislosti můžeme $P(e_{k+1:t}|X_k,e_{1:k}) = P(e_{k+1:t}|X_k)$
- $= \alpha P(e_{k+1:t}|X_k)*P(X_k|e_{1:k})$
- $= \alpha f_{1:k} (filtering) x b_{k+1:t}
![image](https://github.com/user-attachments/assets/b1603553-e73d-4cb6-8aa7-6ecce0b828d5)

#### most likely explanation 
![image](https://github.com/user-attachments/assets/0e3b63ed-5358-48d0-b857-bbdad8fcdf55)
![image](https://github.com/user-attachments/assets/a5de903d-889b-4b4c-bee2-9358c2f20b8b)

### Define and compare Hidden Markov Model and Dynamic Bayesian Network.
#### hidden Markov model
- stav je jedna proměnná $X_t$ a evidence jedna proměnná $E_t$
- pak je všechno matice
- **transition model**
- ![image](https://github.com/user-attachments/assets/a3cfd9ec-3e4c-4df4-ae1b-3d4887f2b7ec)
- **sensor model**
- ![image](https://github.com/user-attachments/assets/f241fa9c-8f7c-4e6b-83d8-2cbc7680e6a4)
![image](https://github.com/user-attachments/assets/1b6b8e8d-fc97-48c7-be77-4da8794a93f3)
#### dynamic Bayesion network
- kopie pro kazdy sttav
![image](https://github.com/user-attachments/assets/19edd33a-2642-4890-b681-0102e437ca64)
![image](https://github.com/user-attachments/assets/779a83a0-71c1-47ff-88cf-16191f74a198)
- převod hmm na dbn
- **je lepší** výrazně méně pravděpodobností
- ![image](https://github.com/user-attachments/assets/965233cf-fe8b-447b-a150-b0dc047060e7)

## Decision making:
### Formalize rationality via maximum expected utility principle 
#### expected utility
- máme nějakou funkci užitku
- předpokládaný užitek akce na základě evidence je vážený součet užitků stavů, kam nás ta akce může dostat
- ![image](https://github.com/user-attachments/assets/6186624d-0439-477c-b07b-e716b6ac6ded)
#### relation between utility and preferences
- často nevíme pesně číslo užitku, ale umíme porovnat dvě akce
- nastavíme nejlepší na možnosti užitek 1 a nejhorší užitek 0
- necháme agenta, aby si vybral mezi S a stadatrní loterií ,kde nejlepší má pravděpodobnost $p$, nejhorší $1-p$
- v momentě, kdy je agent nerozhodný nastavím užitek S na $p$
### Define decision networks
- je to Baysovská síť, kde jsou navíc rozhodovcí vrcholy
![image](https://github.com/user-attachments/assets/13d8713b-569d-4a1e-b44a-1f71c3d0a6c4)
#### show how the rational decision is done
- vyzkouším všechny varianty těch rozhodovacích a vyberu kombinaci, která dá nejlepší užitek
### Define a sequential decision problem 
- máme plně pozorovatelné, ale nedeterministické prostředí
- hledáme plán, který nám přinese maximální užitek
#### Markov Decision Process its assumptions
- **plně pozorovatelné stochastické prostředí s Markovovým přechodovým modelem a odměnou**
- $P(s'|s,a)$ pravděpodobnost, že mě akce přeese z s do s'
- $R(s)$ odměna za stav
- $U([s_0,s_1,s_2...]) = R(s_0) + \gamma R(s_1) + \gamma ^2 R(s_2)...$
- $\gamma$ je diskontní faktor, vdálená budoucnost je nejistá a nezajímá nás tolik. Je tam taky proto, aby konvergovaly iteration algoritmy
##### its solution (policy)
- funkce $\pi (s)$, která vrací akci pro každý stav
- optimální je taková, která dává největší **expected** užitek
- ![image](https://github.com/user-attachments/assets/ee41ba2b-1be8-4b5e-8c1d-bc21320347c1)
![image](https://github.com/user-attachments/assets/4f3c57a1-1448-46dc-8315-b278f33c45de)
- nám dává pravý užitek stavu
#### describe Bellman equation
![image](https://github.com/user-attachments/assets/cbc0722b-9f65-41e9-94e4-b8c1b8d858df)
- odměna za stav a to nejlepší rozložení odměn sousedů
#### techniques to solve MDP (value and policy iteration)
![image](https://github.com/user-attachments/assets/b00f649a-d3e4-48d3-9836-326ad79ea8c1)
![image](https://github.com/user-attachments/assets/a7deff11-9cb8-4ad4-9e83-99392a9e2e08)
#### formulate Partially Observable MDP and show how to solve it.
- **přechodový model $P(s'|s,a)$**
- **akce aplikovatelné ve stavu $A(s)$**
- **odměna $R(s)$**
- **sensorový model $P(s|e)$**
- můžeme použít belief states místo normálních (pravděpodobostní distribuce přes všechny stavy)
- ![image](https://github.com/user-attachments/assets/fe8abe48-9d22-44ab-96e0-d837287bf77b)
- použijeme dynamickou rozhodovací síť
- počítáme belief state filteringem
- podobné jako ExpectedMiniMax


## Adversarial search and games:
### Explain core properties of environment and information needed to apply adversarial search
- nejčastěji máme deterministické hry s úplnou informací pro dva hráče
- hledáme strategii - počáteční tah a pak tahy pro všechny nadcházející stavy, kam nás dostane oponent
- optimální strategie je nejlepší možná ve hře s bezchybným oponentem
### Explain and compare mini-max and alpha-beta search.
- minimax je nepraktický pro reálné hry
- prohledává příliš velký prostor
- ![image](https://github.com/user-attachments/assets/03042c26-19a6-4916-b25a-0cb9bfd29ab6)
- typek nahore maximalizuje, pod korenem minimalizuje
- $\alpha$ je nejlepší zatím maximum, $\beta$ je nejlepší zatím minimum
### Define an evaluation function and give some examples.
- vrací pro stav předpokládaný užitek
- něco jako heuristika, když nemůžeme dopočíat hru do konce (třeba počet cenných figur v šachách)
### Describe how stochastic games are handled (expected mini-max).
![image](https://github.com/user-attachments/assets/02bf12c3-6c55-4e78-91dd-d8def4911b20)
- ve stromečku s námi hraje ještě šance
### Define single-move games, explain the notions of strategy
- **máme hráče**, kteří dělají rozhodnutí
- **akce**, prováděné hráči
- **payoff** funkci, která na základě kombinace všech akcí dá odměnu hráům
- **strategie** - může být **pure** (deterministická) nebo **mixed** (pravděpodobnostní)
- řešení je přiřazení racionální strategie každému hráči
#### Nash equilibrium
- pokud všichni zůstanou tak, jak jsou, nikomu se nevyplatí samotnému změnit strategii
#### Pareto dominance
- jeden výsledek je dominovaný druhým, pokud by všichni preferovali ten druhý
- pareto optimalita je, pokud není žádný výsledek, který by všichni preferovali
#### explain Prisoner’s dilemma 
![image](https://github.com/user-attachments/assets/afceb4a4-418e-4ddc-9508-6221f368e643)
- dominantní ekvilibrium (přiznat PŘIZNAT!!!)
- je paterodominované variantou  (ZATLOUKAT, ZATLOUKAT)
#### define maximin technique
- najdeme si pro kždou volbu nejhorší variantu, jak to může dopadnout a vybereme si opatrně
- 

#### show some strategies for repeated games
- máme vězňovo dilema stokrát za sebou - pořád je racionální se přiznat
- ale, pokud hráči mají 99% že spolu budou hrát znovu, je lepší **perpetual punishment neboli věčné utrpení** (alias zkouškové) - hráč zatlouká, dokud druhý poprvé nezahraje testify
- **tit-for-tat** začni se zaloukáním a opakuj potom tahy druhého hráče
### Mechanism design
#### classical auctions (English, Dutch, sealed bid) 
- chceme, aby všichni měli domainantní strategii a tedy, aby to bylo rychlé
- maximalizujeme očekávaný prospěch prodávajícího a global utility, teda aby vyhrál ten, kdo si tu věc cení nejvýš
##### anglická aukce
- začíná na minimu a jde nahoru
- jednoduchá dominantní strategie - dokud to není nad tvvoji value, tak přihazuj
- může potlačovat konkurenci (vidím Muska) a má vysoké nároky na rychlost připojení například
##### holandská aukce
- jde s cenou dolů v čase - kytky, ovoce
##### sealed-bid
- obálková
- nemá dominantní strategii
- varianta, kde se platí druhá nejvyšší cena má dominantní strategii - vsaď svoji value
#### tragedy of commons (and how it can be solved).
- vzduch, obecní louka
- musíme je zdanit
- algoritmus
    - každý agent nahlásí svou cenu $b_i$
    - centrum alokuje prostředky podmnožině agentů a chce maximalizovat, kolik za ně dostane
    - každý agent zaplatí daň $W - B$, kde W je součet ostatních z podmnožiny bez tohoto hráče, a B součet všech ostatních podmnožině i mimo, bez tohoto hráče
    - tedy každý v podmnožině platí nejvyšší cenu mimo podmnožinu
    - lidi mimo podmnožinu nic
    - dominantní strategie je vrazit tam naši pravou hodnotu

## Machine learning:
### Define and compare types of learning (supervised, unsupervised, reinforcement), explain Ockham’s Razor principle and diYerence between classification and regression.
#### s učitelem
- zadané vstupy i výstupy
- učí se funkci **hypotézy**, která aproximuje reálnou funkci
- Occamova břitvička
- *Entia non sunt multiplicanda praeter necessitatem.
tj. Entity se nemají zmnožovat více, než je nutné.*
#### bez učitele
- zadané jen vstupy - generuj podobné, poznávej, rozděl do skupin
#### zpětnovazební
- maximlalizuj odměnu v prostředí
#### regrese vs klasifikace
- číselná hodnota vs kategorie

### Define decision trees and show how to learn them (including the definition of entropy and information gain).
- učení s učitelem
- sežere vektor vstupu a vrátí jeden výstup
- udělá sekvenci testů a dospěje k řešení
![image](https://github.com/user-attachments/assets/a9dbac10-c665-4153-b3fd-efa470928124)
- při stavění chceme vždy větvit podle toho atributu, který na základě **entropie** přinese největší **information gain**, podstromy rekurzivně
- ![image](https://github.com/user-attachments/assets/3eb0880a-5896-4aac-a14e-15094db26d01)
![image](https://github.com/user-attachments/assets/e94bf9b6-7be7-4f99-a324-2bd9300b11d9)
- p je počet pozitivních, n je počet negativních zástupců
- remainder je vysčítání přes druhy, B je pro všechno na jedné hromadě

### Describe principles and methods for learning logical models (explain false negative and false positive notions, describe current-best-hypothesis and version-space learning).
- hledáme ultimátní logickou formuli místo stromu
- attributy jsou unární predikáty, výsledek taky
- můžeme mít obr disjunkci všech variant, ale spíš cheme jednu hypotézu
#### falešně negativní
- musíme pustit podmínku nebo přidat disjunkci
#### falešně pozitivní
- přidat podmínku nebo odebrat disjunkci
![image](https://github.com/user-attachments/assets/c3793be2-ac67-4c74-9a83-865da00ec4db)
#### version space
- začnu s množinou všech hypotéz a jenom vyhazuju nekonzistentní věci
- ![image](https://github.com/user-attachments/assets/6136f137-0caa-4fb3-b865-febe63e2f932)

### Describe linear regression, show its relation to linear classification (define linearly separable examples) and artificial neural networks (describe backpropagation technique).
- hledáme lineární funkci, která má nejmenší chybu
- ![image](https://github.com/user-attachments/assets/cfe0b7d9-6113-4ad8-be84-127eac0aaabd)
- kde $y = w_1*x + w_0$
#### jakým způsobem počítá jeden perceptron
- f je funkce, která pro x<0 vrací 0 a jinak 1 **signum**
- ![image](https://github.com/user-attachments/assets/ce6a39df-1ba9-4a84-a336-57f9e306cc61)
#### algoritmus pro trénování perceptronu
![image](https://github.com/user-attachments/assets/9226035a-2c99-401d-a78d-e740c5e53874)
- lineárně separabilní třídy, pak konverguje
#### struktura vícevrstvých perceptronových sítí
- první vrstva na vstup, další skryté na sebe, výstupní 
#### aktivační funkce používané ve vícevrstvých perceptronech (sigmoida, tanh, ReLU)
- **relu** ![image](https://github.com/user-attachments/assets/80b33ea9-1f47-49c2-8d38-16b0f213072e)
- **sigmoida** ![image](https://github.com/user-attachments/assets/f192e808-4cf1-4782-9269-5ffed7c2ed05) - dává od 0 do 1
- **hyperbolický tangens** - dává od -1 k 1
#### backpropagation
- ![image](https://github.com/user-attachments/assets/5773ce0a-61af-4791-ab22-374244938bdc)
- L je chybová fuknce
- alfa je parametr učení

### Explain parametric and non-parametric models, describe k-nearest neighbor methods and the core principles of Support Vector Machines (maximum margin separator, kernel function, support vector).
#### parametric model
- když máme natrénovanou síť, můžeme zahodit trénovací data, protože jsou reprezentována váhami
#### non-parametric
- budeme si hypoézu reprezentovat daty
#### k-nearest neighbours
![image](https://github.com/user-attachments/assets/705998d6-6c52-4a9c-927e-e87adbfdbf17)
- rozhodne se podle nejbližších sousedů, buď to určí třídu, nebo se nějak vyprůměruje hodnota
- Minkowskeho vzdálenost
- ![image](https://github.com/user-attachments/assets/ead0c5b9-ddf0-4422-a290-0407a0f227ae)
#### support vector machine
- maximum margin separator - umístit dělící čáru tak, aby kolem byl co nejširší pruh - lepší generalizace, **support vectors jsou vzorky blízko hranice**
![image](https://github.com/user-attachments/assets/47b26c89-6bc5-468f-8ba2-341472641512)
- když nejsou lineárně separabilní, můžu si je kernel funkcí namapovaz do další dimenze, kde jsou
![image](https://github.com/user-attachments/assets/18579d89-3947-45e1-b193-110b1cad4e65)

### Describe and compare Bayesian learning and maximum-likelihood learning
- mám víc hypotéz
- **vím, že existují právě tyhle typy sáčků**
- ![image](https://github.com/user-attachments/assets/5146664a-db56-4e48-b3d0-39c45ec7bdbc)
- používám všchny, přijdou data, spočítám pravdpodonbnost hypotézy na základě dat
![image](https://github.com/user-attachments/assets/a03f32b7-4b11-4367-b759-a03f0b74dcc7)
- udělám predikci dalšího ujako vážený průměr predikcí podle hypotéz
![image](https://github.com/user-attachments/assets/77c6fd5b-88ba-4cd3-9df1-f83a9e22a0a0)
#### describe parameter learning for Bayesian networks
- neznáme procenta, **neznámý typ sáčků**
- ![image](https://github.com/user-attachments/assets/72971682-64c1-4456-8f0c-8091cfd92b81)
- vlastně maximlizuju tuhle funkci
- udělám derivaci podle $\phi$ a hledám, kde je rovna nule
#### expectation maximization (EM) algorithm (explain the notion of hidden variable)
![image](https://github.com/user-attachments/assets/3d195656-7979-40a5-bc16-b355a79bcb00)
- chceme se naučit podmíněnou distribuci té skryté proměnné
- budeme předsírat, že ji známe
- EXPECTATION - hodíme tam naši tipnutou hodnotu
- MAXIMALIZATION - upravíme parametry tak, aby se zvýšila pravděpodobnost modelu
- opakuj do konvergence
### Formulate reinforcement learning problem
#### intuitivně
Cílem zpětnovazebního učení je naučit se takové chování agenta, které maximalizuje jeho celkovou odměnu, kterou získá z prostředí, pokud bude dané chování používat. Předpokládáme, že agent se vyskytuje v nějakém prostředí, jehož stav může pozorovat a ovlivňovat. Agent běží v cyklech, v každé iteraci $t$ pozoruje stav prostředí $s_t$, na základě pozorování provede akci $a_t$ a tím převede prostředí do nového stavu $s_{t+1}$. Za provedení akce dostane od prostředí odměnu $r_t$.
#### prostředí jako Markovský rozhodovací proces
- **čtveřice $(S, A, P, R)$**
- **Stavy, Akce, Přechody**(pravděpodobnosti)**, Odměny**
- pechodová funkce nezávisí na historii jen na aktuálním stavu a akci
-  agent má policy, která říká pravděpodobnost, že ve stavu udělá akci
-  celková suma se počítá s diskontním faktorem $\gamma^t$ aby klesala
#### hodnota stavu a hodnota akce
-  $V$ je střední hodnota oděny, pokud začínáme v tomhle stavu a jdeme dál
-  ![image](https://github.com/user-attachments/assets/812165cf-7112-41b7-ac86-b45f0e752783)
- navíc su budeme udržovat ![image](https://github.com/user-attachments/assets/065a3735-aa40-4227-8b9b-7b681fbc8a35) což je hodnota akce v tom stavu, pokud budeme dál udržovat policy pí
- **agent hledá optimální strategii maximalizující užitek stavů**

### describe and compare passive and active learning
- passive má fixed policy ajen se učí hodnoty stavů (SARSA)
- active se učí i policy (Q-učení)

### describe and compare methods of direct utility estimation, adaptive dynamic programming (ADP), and temporal diYerence (TD) for passive learning
#### direct utiliy estimation
- doběhnu do konce a každý stav má hodnotu součet odměn od sebe dál
- udržuju si průměr, protože to může vyjít různě pro různé testy
- **koverguje pomalu** prozkoumává i varianty, které nejsou v souladu s Bellmanem
- ![image](https://github.com/user-attachments/assets/d1254909-3c87-426f-a439-87aba7efd3f1)
- **utility stavů totiž nejsou nezávislé**
#### ADP
- z pozorování se učí přechodovou funkci (prostě počítá frekvenci, co kdy následovalo) a odměny
- užitek počítá z Bellmanových rovnic, třeba value iteration

#### temporal difference
- můžeme sipoupravovat utility tak, aby splňovaly rovnice pomocí pozorovaných přechodů
- předpoklad, že $U(1,3) = 0.84$ a $U(2,3) = 0.92$
- pokud vždycky je právě tenhle přechod, můžeme předpokládat, že by mělo platit $U(1,3) = -0.04 + U(2,3)$, což by dávalo $U(1,3) = 0.88$
- můžeme tedy zvůšit $U(1,3)$
- ![image](https://github.com/user-attachments/assets/ffe96514-9dbb-47e3-be4e-e28ffd3923f7)

### explain the difference between model-based and model-free approaches
- model free je Q-učení - nepotřebuje přechodový model, stačí jen Q hodnoty
- model based potebuje přechodový model - třeba temporal difference
### explain active version of ADP and the notions of greedy agent and exploration vs exploitation problem
- nemá ani policy, bere nejvýhodnější akci - to je jeho policy
- ale měl by vždycky brát to aktuálně nejlepší?
- to, co je nejlpší v našem modelu nemusí bý nejlepší v prostředí

### describe exploration policies (random vs exploration function)
- jednou za čas udělá random akci - pomalu konverguje
- exploration funkce - $f(u,n)$  určuje poměr hamižnosti a zvědavosti
      - n je kolikrát jsme tam byli, u je užitek
![image](https://github.com/user-attachments/assets/b43831d4-2f6f-42b4-85d8-6767b6a618ec)


### describe and compare methods Q-learning (including Qvalue and its relation to utility) and SARSA.
#### Q-učení
- Bellmanovy rovnice
- ![image](https://github.com/user-attachments/assets/38d62e48-98b9-47f3-8981-78f481fd05f3)

- ![image](https://github.com/user-attachments/assets/7854f96d-ea83-44ae-a2f0-8515fcc12422)
- propagace užitku z cílových stavů do předcházejících
- ![image](https://github.com/user-attachments/assets/1e53ce3f-42dd-48f0-b8a8-e8eae0b4bed0)
- mám na začátku samé nuly v amtici. Pozoruju stav $s_t$ porvedu akci $a_t$, dostanu odměnu $r_t$ a posunu se do $s_{t+1}$.
- alfa je parametr učení
- nové políčko je to, co mám + reward + maximum z toho, co můžu získat z nového políčka
#### popsat algoritmus SARSA
![image](https://github.com/user-attachments/assets/1125d017-db1d-409d-87e5-75e89c14ad10)
- máme policy a víme, co budeme dělat v dalším tahu, odpadá tedy maximalizace, minus v závorce je, protože na začátku není 1-alfa

