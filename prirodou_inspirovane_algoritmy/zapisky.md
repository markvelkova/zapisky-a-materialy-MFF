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


