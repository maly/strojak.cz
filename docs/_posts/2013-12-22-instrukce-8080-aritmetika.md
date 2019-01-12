---
id: 47
title: 'Instrukce 8080 &#8211; aritmetika'
date: 2013-12-22T20:41:04+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=47
permalink: /instrukce-8080-aritmetika/
dsq_thread_id:
  - "2067253825"
categories:
  - "8080"
---
Je to počítač, ne? Tak to má taky počítat!

<!--more-->

Procesor samosebou není živ jen přesouváním dat tam a zpátky. Občas taky musí něco spočítat. K tomu mu slouží aritmetické instrukce pro sčítání, odčítání a porovnání. (Kde je násobení, dělení a hyperbolický sinus? Dozvíte se později!)

### ADD

Instrukce ADD r vezme obsah v osmibitovém registru r (tedy A, B, C, D, E, H, L nebo M, což je ve skutečnosti obsah paměti, ale to už jsem psal asi pětkrát&#8230;) a přičte ho k registru A. Výsledek sčítání uloží do registru A. Pokud vám připadá, že se registr A používá nějak často, tak se vám to nezdá, je to opravdu tak, a protože se k němu vztahují skoro všechny aritmetické i logické instrukce, tak se mu říká &#8222;akumulátor&#8220;.

Po sčítání nastaví patřičně příznaky S, Z, AC, P a CY &#8211; viz [popis funkce příznaků v minulé lekci](http://strojak.uelectronics.info/8080-priznaky-a-zasobnik/ "8080 – příznaky a zásobník").

Pojďme se podívat na pár příkladů sčítání a toho, jak ovlivní příznaky. Všechna čísla budou hexadecimálně.

| Sčítanec 1 | Sčítanec 2 | Výsledek | S | Z | AC | CY | Poznámka                                                                                          |
| ---------- | ---------- | -------- | - | - | -- | -- | ------------------------------------------------------------------------------------------------- |
| 03         | 05         | 08       |   |   |    |    | Nic zvláštního, vešli jsme se dokonce do jedné pozice                                             |
| 09         | 08         | 11       |   |   | 1  |    | Při sčítání bylo třeba přenést jedničku z nejnižší pozice do vyšší                                |
| FF         | 01         | 00       |   | 1 | 1  | 1  | Výsledek je 0 (Z), přetekl rozsah registru (CY) a přenášela se jednička mezi čtveřicemi bitů (AC) |
| F0         | 05         | F5       | 1 |   |    |    | Pokud používáme aritmetiku se znaménkem, tak je výsledek záporný (S)                              |

#### Se znaménkem&#8230;?

Jak to vlastně je, když chceme použít místo čísel bez znaménka (0-255) čísla se znaménkem (-128 až 127)? Kde to dáme procesoru najevo? Odpověď zní: Nikde! Procesoru to je úplně jedno. Vezměme si poslední řádek z předchozí tabulky. Pokud se bude jednat o čísla bez znaménka, tak sčítáme hodnotu 240 (F0h) a 5. To je dohromady 245, tedy hexadecimálně F5h. Když je budeme vnímat jako čísla se znaménkem, tak sčítáme -16 (F0h) a +5. Výsledek je -11, což je hexadecimálně F5h.

Předposlední řádek je podobný: při sčítání bez znaménka je FFh+01h=100h, což se do osmi bitů nevejde, máme tedy výsledek 00 a nastavený přenos (1). Pokud sčítáme se znaménkem, sčítáme -1 (FFh) a +1 a výsledek je &#8211; opět nula!

To, jestli pracujeme s čísly se znaménkem nebo bez znaménka, si musíme určit coby programátoři sami. Procesor je zpracovává stále stejně a je jen na nás, jak je interpretujeme. Jen je důležité je interpretovat vždy stejně.

### ADI

Často potřebujeme přičíst nějakou předem známou konstantu. Pomocí ADD bychom si ji museli nejprve uložit do některého registru, a ten pak přičíst. Naštěstí to jde rychleji, pomocí jedné instrukce ADI. Ta má jediný parametr, a tím je osmibitová konstanta. Tu přičte k registru A. Zbytek, tedy nastavování příznaků apod., platí stejně jako u instrukce ADD.

### ADC

Funguje stejně jako instrukce ADD, ale k výsledku přičte ještě hodnotu příznaku CY. Hodí se to například při sčítání větších čísel. Představme si, že máme dvě šestnáctibitová čísla, jedno v registrech BC, druhé v registrech DE, a chceme je sečíst a výsledek uložit do registrů BC. Takhle jednoduše to nejde, žádnou instrukci na to nemáme, takže to musíme udělat krok po kroku. Nejprve si sečteme čísla na nižších pozicích (C + E) a výsledek uložíme do C. Ovšem pokud součet těchto číslic bude větší než 255, tak musíme při sčítání vyšší pozice přičíst (přenesenou) jedničku! Což je bez problémů, protože, jak víme: ADD nastaví správně příznak CY, a ADC ho dokáže započítat. Součet tedy naprogramujeme třeba takto:

<pre class="lang:default decode:true">mov a,c
 add e
 mov c,a
 mov a,b
 adc d
 mov b,a</pre>

Naštěstí instrukce MOV nijak neovlivňuje příznaky, takže stav příznaku CY tak, jak ho nastaví instrukce ADD, se až do provedení instrukce ADC nezmění.

Funguje to? Ověříme v emulátoru! Sečteme si čísla 1000 a 2000 (03E8h a 07D0h), výsledek by měl být 3000 (0BB8h)&#8230; Všimněte si, že při součtu nižších řádů (E8h+D0h) je nastaven bit přenosu, protože výsledek je větší než FFh.

### 
  
ACI

Jako má ADD své ADI, tak má ADC své ACI. K reistru A přičte zadanou konstantu a hodnotu příznaku CY.

### SUB, SUI

Instrukce SUB od hodnoty v registru A odečte hodnotu zadaného registru a výsledek uloží do A. SUI dělá totéž, ale s osmibitovou konstantou. Pokud je výsledek menší než 0, tak se pokračuje od FFh dolů (-1 = FFh, -2 = FEh, &#8230;) a je nastaven příznak CY, který má tentokrát význam &#8222;výpůjčky&#8220; (borrow).

### SBB, SBI

Totéž jako SUB, SUI, ale tentokrát od výsledku odečte 1, pokud je nastaven příznak CY.

### CMP, CPI

Tyto instrukce porovnají obsah registru A s jiným registrem (CMP) nebo konstantou (CPI) a nastaví příznakové bity takto:

| Porovnání | S | Z | CY |
| --------- | - | - | -- |
| A=n       |   | 1 |    |
| A>n       |   |   |    |
| A<n       | 1 |   | 1  |

Podmíněné instrukce skoku se pak mohou podle těchto hodnot rozhodnout.

Zajímavost: Instrukce CMP a CPI vnitřně dělají totéž jako instrukce SUB, SUI, tedy odečtou od A hodnotu parametru, nastaví příznaky, ale výsledek neuloží do A, ale zahodí ho.

### INR, DCR

Velmi častá operace je zvýšení hodnoty o 1 nebo naopak odečtení 1. Vyšší jazyky, které vyšly z jazyka C, na to mají i operátory &#8222;++&#8220; a &#8222;&#8211;&#8222;. INR přičte k registru 1, DCR odečte 1. Instrukce ovlivní příznaky S, Z, AC a P, CY ponechá tak jak je (v tom se liší od ADI 1 / SUI 1)

### INX, DCX

INR a DCR pracují s osmibitovým registrem, INX a DCX pracují s dvojicí registrů, jako by byl jeden šestnáctibitový. Parametrem může být B, D, H nebo SP.

### DAD

Sčítání v šestnácti bitech. K obsahu registrů HL přičte BC, DE, HL nebo SP.

### DAA

Tahle instrukce souvisí s čísly ve formátu BCD. Jen ji tu takhle zmíním, že existuje, a její popis si nechám na jindy, adekvátně jejímu významu&#8230;

A jak se tedy násobí a dělí? Dostaneme se k tomu, nebojte, ale teď si odskočíme. Jump!