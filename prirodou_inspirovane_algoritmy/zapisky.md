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
- čtveřice $(S, A, P, R)$
    - $S$ - množina stavů
    - $A$ - množina akcí (případně $A_s$, akce proveditelné ve stavu $s$)
    - $P_a(s, s')$ - pravděpodobnost, že po provedení akce $a$ ve stavu $s$, přejde do stavu $s'$
    - $R(s, s')$ - funkce odměny za přechod


