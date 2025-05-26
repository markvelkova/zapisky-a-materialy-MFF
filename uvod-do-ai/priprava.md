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
- když je fronta prázdná, vrátím false, jinak si vyberu vrchol, poud goal..., pokud ne, dám jeho syny do fronty
- může prozkoumáva jeden stav víckrát, pokud do nj dojde různými cestami
#### graph search
- přidává syny do fronty jen poud nejsou v navštívených

### Explain and compare node selection strategies (depth-first, breadth-first, uniform-cost).
| **Vlastnost**                   | **Prohledávání do šířky (BFS)**                               | **Prohledávání do hloubky (DFS)**                                                                                       |
|--------------------------------|----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| **Pořadí rozšiřování uzlů**     | Nejmenší neprozkoumaná hloubka (FIFO)                         | Nejhlubší neprozkoumaný uzel (LIFO)                                                                                      |
| **Úplnost**                     | Úplné (při konečném faktoru větvení)                          | Úplné pro prohledávání grafu<br>Neúplné pro prohledávání stromu                                                          |
| **Optimálnost**                | Optimální (pokud cena cesty neklesá s hloubkou)                | Neoptimální (lze upravit na optimální pomocí „branch-and-bound“)                                                        |
| **Časová složitost**           | $O(b^d)$, kde b je faktor větvení a d je hloubka cílového uzlu   | $O(b^m)$, kde m je maximální hloubka jakéhokoliv uzlu (m může být výrazně větší než d)                                     |
| **Paměťová složitost**         | $O(b^d)$                                                        | $O(bm)$                                                                                                                 |
| **Paměťové nároky**            | Paměť je větší problém než samotný výpočet                     | Zpětné prohledávání: generuje se vždy jen jeden nástupce, paměť = $O(m)$                                                   |
<br>**uniform cost je dijkstra**<br>
### Informed (heuristic) search: explain evaluation function f and heuristic functionh, define admissible and monotonous heuristics and prove their relation.
![image](https://github.com/user-attachments/assets/8b30ca36-0fde-473e-b2e2-d737e26df4a5)
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
![image](https://github.com/user-attachments/assets/50fe079a-c67c-4688-af27-e69bd26aa154)
#### normalization
- máme evdence a počítáme pravděpodobnos jevu
![image](https://github.com/user-attachments/assets/fde97654-75f7-438f-9f1b-fa10f54bd69f)
- kdž to spočítám pro všechny jevy, musí se to sečíst na jedničku
![image](https://github.com/user-attachments/assets/fe419baa-4f7d-4685-82a8-1a603c833960)
- takže $P(E-e)$ nemusím počítat, hodím tam alfu
![image](https://github.com/user-attachments/assets/178c36bf-5133-4ae8-8625-410d11da9a03)
![image](https://github.com/user-attachments/assets/0927cbfe-c734-4277-90b6-b39a224c7c12)
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
- 
![image](https://github.com/user-attachments/assets/54c41e18-cee6-40a5-9038-4f9b6730b6c5)
#### explain chain rule
![image](https://github.com/user-attachments/assets/b6704e93-6750-405f-9ab5-d261551a7ec6)
### describe inference techniques for Bayesian networks 
- o je to s hidden proměnnýma
![image](https://github.com/user-attachments/assets/da910934-79ca-404c-aa11-83f5124fa951)
- $P(X,e,y)$:
![image](https://github.com/user-attachments/assets/323e66d9-358d-4755-ae12-3096786a20d9)
![image](https://github.com/user-attachments/assets/81a37805-448a-4eaf-bed8-3e060d31a196)
#### enumeration
- to o jeden nadpis výš, opakuje stejné části víckrát, není efektivní
- když so to uložíme, máme dynamické programování
#### variable elimination
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
- **stav závisí jen na předchozím stavu**
#### observation model
![image](https://github.com/user-attachments/assets/46dfed73-dae4-4f42-b75e-adbb34c657da)
- **pozorování závisí jen na akuálním stavu**
### Define basic inference tasks and show how they are solved.
#### filtering
![image](https://github.com/user-attachments/assets/8e811c2e-355d-42a4-aef7-1962424a71c0)
- je potřeba jít od začátku
- ![image](https://github.com/user-attachments/assets/e6d9ffd2-db61-4477-9563-3c617668f92f)
- ![image](https://github.com/user-attachments/assets/344365fc-8606-4199-8ed9-b9b709b56c20)
- ![image](https://github.com/user-attachments/assets/a6c80f2f-97cc-4236-8ba3-05ca06d31266)

#### prediction
![image](https://github.com/user-attachments/assets/ce3e2b04-4d7c-4f3e-b984-ee4eeab460cf)
- filering bez přidání nové evidence
- ![image](https://github.com/user-attachments/assets/bbdc5c2e-f25d-4c0b-af65-dbd0f319b98f)

#### smoothing
![image](https://github.com/user-attachments/assets/8a17c07a-2d52-4119-879c-840795b8b1ba)
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
- $U([s_0,s_1,s_2...]) = R(s_0) + \gamma R(s_1) + \gamma ^2 R(s_2)...
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

## Adversarial search and games:
- Explain core properties of environment and information needed to apply
adversarial search.
- Explain and compare mini-max and alpha-beta search.
- Define an evaluation function and give some examples.
- Describe how stochastic games are handled (expected mini-max).
- Define single-move games, explain the notions of strategy, Nash equilibrium,
Pareto dominance, explain Prisoner’s dilemma; define maximin technique and
show some strategies for repeated games.
- Mechanism design: explain classical auctions (English, Dutch, sealed bid) and
tragedy of commons (and how it can be solved).

## Machine learning:
- Define and compare types of learning (supervised, unsupervised, reinforcement),
explain Ockham’s Razor principle and diYerence between classification and
regression.
- Define decision trees and show how to lean them (including the definition of
entropy and information gain).
- Describe principles and methods for learning logical models (explain false
negative and false positive notions, describe current-best-hypothesis and
version-space learning).
- Describe linear regression, show its relation to linear classification (define
linearly separable examples) and artificial neural networks (describe
backpropagation technique).
- Explain parametric and non-parametric models, describe k-nearest neighbor
methods and the core principles of Support Vector Machines (maximum margin
separator, kernel function, support vector).
- Describe and compare Bayesian learning and maximum-likelihood learning;
describe parameter learning for Bayesian networks including expectationmaximization (EM) algorithm (explain the notion of hidden variable).
- Formulate reinforcement learning problem, describe and compare passive and
active learning; describe and compare methods of direct utility estimation,
adaptive dynamic programming (ADP), and temporal diYerence (TD) for passive
learning; explain the diYerence between model-based and model-free
approaches; explain active version of ADP and the notions of greedy agent and
exploration vs exploitation problem; describe exploration policies (random vs
exploration function); describe and compare methods Q-learning (including Qvalue and its relation to utility) and SARSA.
