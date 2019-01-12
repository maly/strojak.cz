---
id: 171
title: '6502 &#8211; příznaky a instrukce pro práci s nimi'
date: 2014-02-14T13:46:20+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=171
permalink: /6502-priznaky-a-instrukce-pro-praci-s-nimi/
dsq_thread_id:
  - "2265677126"
categories:
  - "6502"
---
Příznaky a stavový registr procesoru 6502.

<!--more-->

Podobnou roli, jakou má v [procesoru 8080 registr F](http://strojak.uelectronics.info/8080-priznaky-a-zasobnik/ "8080 – příznaky a zásobník"), zastává u 6502 registr P.

<table>
  <tr>
    <th>
      Bit
    </th>
    
    <td>
      7
    </td>
    
    <td>
      6
    </td>
    
    <td>
      5
    </td>
    
    <td>
      4
    </td>
    
    <td>
      3
    </td>
    
    <td>
      2
    </td>
    
    <td>
      1
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <th>
      Příznak
    </th>
    
    <td>
      N
    </td>
    
    <td>
      V
    </td>
    
    <td>
      1
    </td>
    
    <td>
      B
    </td>
    
    <td>
      D
    </td>
    
    <td>
      I
    </td>
    
    <td>
      Z
    </td>
    
    <td>
      C
    </td>
  </tr>
</table>

  * N (negative) informuje o znaménku výsledku (nebo přenesených dat, viz [popis instrukce LDA](http://strojak.uelectronics.info/instrukce-6502-presuny/ "Instrukce 6502 – přesuny")). Je-li kladný, je to 0, je-li záporný, je to 1
  * V (overflow) značí přetečení čísel se znaménkem (viz dál).
  * B (break) je nastaven na 1, pokud bylo přerušení vyvoláno instrukcí BRK
  * D (decimal) lze nastavit na 1, pak procesor zpracovává hodnoty v [kódu BCD](http://strojak.uelectronics.info/bcd-a-preruseni/ "BCD a přerušení").
  * I (interrupt) můžeme nastavit na 1, pokud chceme zakázat přerušení
  * Z (zero) je 1, pokud byl výsledek nebo načtený bajt nulový.
  * C (carry) se nastavuje na 1, jestliže došlo k přetečení ze 7. bitu

Pojem &#8222;přenosu&#8220; jsme si vysvětlovali v [kapitole o příznacích 8080](http://strojak.uelectronics.info/8080-priznaky-a-zasobnik/ "8080 – příznaky a zásobník") (doporučuju přečíst, i když jde o jiný procesor). U 6502 je potřeba věnovat pozornost příznaku V, který nemusí být zcela jasný.

Některé popisy se omezují na málo říkající a nepřesný &#8222;přenos ze 6. bitu&#8220;. Jiné popisy vysvětlují, že se jedná o XOR mezi přenosem ze 6. bitu a ze 7. bitu, což je technicky možná OK, ale neříká, co to vlastně znamená. Pojďme si to vysvětlit názorněji.

Příznak V říká, jestli došlo k přetečení čísla se znaménkem. Představme si, že sečteme dvě čísla &#8211; 127 a 1 (hexadecimálně 7Fh a 01h). Výsledek je 128 (tedy 80h). Pokud bychom ale používali aritmetiku se znaménkem, tak zjistíme, že 127 + 1 = -128, a to je špatně! Příznak V nás upozorňuje, že došlo k něčemu takovému, tj. že výsledek je mimo rozsah <-128;127>.

Při sčítání FFh a 01h sice dojde k normálnímu přetečení (C), ale z hlediska čísel se znaménkem se vlastně sčítalo &#8222;-1 + 1&#8220; a výsledek je 0, bez přetečení. V tedy bude 0.

Při sčítání 80h a FFh bude výsledek 7Fh. U čísel bez znaménka došlo k přetečení (128 + 255), u čísel se znaménkem (-128 + -1) taky. Budou tedy nastaveny příznaky C i V.

Demonstrační kód si můžete vyzkoušet opět v emulátoru. Instrukce CLC slouží k nulování příznaku C a ADC sčítá dvě čísla (6502 má pouze instrukci sčítání s příznakem C, proto je ho potřeba nejprve nulovat, ale k tomu se ještě dostaneme). Můžete si vyzkoušet chování; sledujte hlavně stav bitů V a C.

(Další podrobnosti o výpočtu příznaku V: [The 6502 overflow flag explained mathematically](http://www.righto.com/2012/12/the-6502-overflow-flag-explained.html))



Další bit v příznakovém registru, který zaslouží vysvětlení, je bit I. Pokud je tento bit roven 1, je zakázáno (&#8222;zamaskováno&#8220;) přerušení a procesor nereaguje na signál, přivedený na přerušovací vstup IRQ (6502 má dva druhy přerušení, maskovatelné IRQ a nemaskovatelné NMI, ale k nim se ještě dostaneme). Příznak může na hodnotu 1 nastavit programátor instrukcí SEI, popřípadě procesor poté, co přišel požadavek na přerušení &#8211; tím se zabrání, aby bylo vyvoláno přerušení dřív, než skončila obsluha předchozího.

Příznak B označuje, že obsluha přerušení byla vyvolána instrukcí BRK, nikoli vnějším signálem IRQ. Instrukce návratu z obsluhy přerušení jej opět nuluje.

Příznak D může programátor nastavit na 1 a tím vynutit, aby procesor pracoval v režimu BCD &#8211; tedy jako by po každé operaci sčítání a odčítání prováděl dekadickou korekci.

## Instrukce pro práci s příznakovým registrem

Příznakové bity nastavují různé instrukce v rámci své normální činnosti (většinou aritmetické, logické nebo instrukce přenosu dat), ale existuje i sada instrukcí pro nastavení či nulování konkrétních bitů.

### CLC, SEC

Instrukce nuluje (CLC &#8211; CLear Carry) nebo nastavuje (SEC &#8211; SEt Carry) příznak C

### CLD, SED

Instrukce nuluje (CLD) nebo nastavuje (SED) příznak D

### CLI, SEI

Instrukce nuluje (CLI) nebo nastavuje (SEI) příznak I

### CLV

Instrukce nuluje příznak V. (Vidíte správně, žádná instrukce SEV není.)

Tyto instrukce nemají žádný parametr (je tedy použit &#8222;implicitní mód&#8220;).