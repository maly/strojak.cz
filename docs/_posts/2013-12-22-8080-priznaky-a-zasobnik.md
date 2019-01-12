---
id: 41
title: '8080 &#8211; příznaky a zásobník'
date: 2013-12-22T04:22:50+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=41
permalink: /8080-priznaky-a-zasobnik/
dsq_thread_id:
  - "2065953400"
categories:
  - "8080"
---
Procesor potřebuje vědět, jak dopadla ta která instrukce, a občas si potřebuje nějaká data odložit na později.

<!--more-->

## Příznaky

O registru F jsem se už zmiňoval. Je to takový lehce mystický registr, do kterého se nedá přímo zapisovat a ze kterého se nedá přímo číst. Psal jsem, že v něm jsou uložené takzvané _příznaky_. Co to je?

Některé instrukce, převážně ty, co něco počítají, mohou dát najevo, že výsledek je v určitém formátu. Například že je nulový, záporný, jakou má paritu, nebo že při výpočtu přetekl výsledek z rozsahu 0-255. A přesně tyto informace ukládá jako _příznakové bity_ do registru F.

U procesoru 8080 je příznakových bitů 5 a jsou v registru uloženy takto:

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
      S
    </td>
    
    <td>
      Z
    </td>
    
    <td>
    </td>
    
    <td>
      AC
    </td>
    
    <td>
    </td>
    
    <td>
      P
    </td>
    
    <td>
      1
    </td>
    
    <td>
      CY
    </td>
  </tr>
</table>

  * S (sign) informuje o znaménku výsledku. Je-li výsledek kladný, je to 0, je-li výsledek záporný, je to 1
  * Z (zero) je roven 1 v případě, že výsledek je nula. Pokud je nenulový, je Z = 0
  * AC (auxiliary carry) je roven 1, pokud při operaci došlo k přenosu přes polovinu bajtu (mezi nižší a vyšší čtveřicí)
  * P (parity) je nastaven vždy tak, aby doplnil počet jedniček ve výsledku na lichý (tedy 0, je-li lichý, 1, je-li sudý) &#8211; viz též [paritní bit](https://cs.wikipedia.org/wiki/Paritn%C3%AD_bit).
  * CY (carry) je 1, pokud dojde k přenosu z nejvyššího bitu.

Termín &#8222;přenos&#8220; je možná nejasný, pojďme si ho upřesnit. Co se stane, když budu sčítat čísla 0FAh a 0Ah (250 a 10) v osmibitovém registru? Výsledek je 260, hexadecimálně 104h &#8211; a to je číslo, které má devět bitů. Devátý (nejvyšší) bit se do registru nevejde, tam zůstanou jen bity 0-7 (tedy osm bitů). Pokud taková situace nastane, je příznak CY nastaven na jedničku.

Tento příznak je velmi užitečný, pokud sčítáte vícebajtová čísla. Sečtete nejprve čísla na nižších řádech, a pokud je náhodou větší než možný rozsah, _přenesete si_ jedničku do vyššího řádu.

Tentýž příznak funguje i pro odčítání &#8211; zde zase funguje jako označení &#8222;výpůjčky&#8220; do nejvyššího řádu v případě, že výsledek je menší než 0. Když od čísla 04h odečteme číslo 05h, bude výsledek 0FFh (nezapomeňte, že hodnoty jdou za sebou a po nule je 0FFh). Příznak CY bude nastaven (&#8222;půjčili&#8220; jsme si jedničku), příznak S bude nastaven (výsledek je záporný).

Ve skutečnosti je -1 a 255 jedno a totéž číslo a je jen na nás, jestli se na něj díváme jako na číslo se znaménkem, nebo na číslo bez znaménka. -2 je 254, -3 je 253 atd. Pokud chceme v binární soustavě převést číslo na jeho zápornou hodnotu, uděláme bitovou negaci (zaměníme 0 za 1 a opačně) a přičteme jedničku (procesor na to samosebou má vhodnou instrukci).



A k čemu jsou příznaky dobré? Jednak pomáhají s výpočty, a jednak se můžete podle výsledků rozhodovat a provádět podmíněné skoky a volání podprogramů.

### Zásobník

Občas procesor potřebuje, jak jsem už psal, nějaká data &#8222;odložit&#8220; na později. Ať už to jsou data, co si potřebuje uložit programátor, nebo třeba adresa, kam se má program vrátit z podprogramu. Používá k tomu strukturu zvanou zásobník (stack). Stack je struktura typu LIFO (Last In &#8211; First Out, tedy co se poslední uloží, to se první vrátí). Implementace zásobníku je jednoduchá: V paměti si vyhradíte oblast, a adresu jejího konce +1 uložíte do registru SP (Stack Pointer). Zásobník je připravený.

Do zásobníku se **vždy ukládají dva bajty**, tedy buď adresa, nebo celý registrový pár. Pokud na zásobník uložíme pár HL, stane se toto: Procesor sníží SP o 1, na tuto adresu uloží obsah registru H, pak zase sníží SP o 1 a na tuto adresu uloží obsah registru L. Teď uložíme třeba pár BC: Procesor sníží SP o 1, na tuto adresu uloží obsah registru B, pak zase sníží SP o 1 a na tuto adresu uloží obsah registru C.

Když teď vyzvedneme hodnotu ze zásobníku &#8211; třeba do registrů DE &#8211; procesor vezme obsah z adresy SP, uloží ho do registru E, SP zvýší o 1, vezme obsah z adresy SP, uloží ho do registru D a opět zvýší SP o 1. SP teď ukazuje na předchozí položku a v DE máme tu poslední uloženou. Což byla, shodou okolností, zrovna hodnota z registrů B a C &#8211; nikde není psáno, že se musí údaje vybrat do téhož registru, z jakého byly uloženy.

Zásobník se tedy posouvá od vyšších adres k nižším. Proto se umisťuje na konec RAM, aby měl dost prostoru kam růst. Což s sebou nese riziko, že pokud uložíte do zásobníku příliš mnoho hodnot, klesne SP až tak, že další uložená data můžou přepsat váš program (tomuto stavu se říká _přetečení zásobníku_, stack overflow). Pokud umístíte zásobník POD program, může se zase stát, že poklesne až třeba do oblasti, kde je ROM. Oba stavy jsou chybné, znamenají téměř stoprocentně pád programu a většinou nastávají, když se program dostane do nekonečné smyčky, ve které se odskakuje nebo ukládá.

### PUSH a POP

K uložení obsahu registrů do zásobníku slouží instrukce PUSH s jedním parametrem, a tím je jméno dvojice registrů (B, D, H), nebo &#8222;PSW&#8220; &#8211; o kterém jsem se už zmiňoval. PSW (Program Status Word) je dvojice registrů A a F. Pro vybrání hodnot ze zásobníku slouží instrukce POP (a stejné parametry). Pojďme si zaexperimentovat: nastavíme si zásobník do paměti a budeme do něj ukládat a zase vybírat data.



Všimněte si nejprve toho, jak se do paměti ukládají data, když se vykonává instrukce PUSH. Později, při načítání zpět, je načítáme v jiném pořadí, takže registry nemají původní obsah.

Na konci [minulé lekce](https://strojak.cz/instrukce-8080-presuny-dat/ "Instrukce 8080 – přesuny dat") jsem zadal úkol: vymyslet způsob, jak prohodit obsah registrových párů BC a HL. Předpokládám, že jste napsali něco takového:

<pre class="lang:default decode:true">MOV A,B
MOV B,H
MOV H,A
MOV A,C
MOV C,L
MOV L,A</pre>

Což je správně, ale přijdeme tím o obsah, který je v registru A. Pokud nám to nevadí, není co řešit. Ale pokud by nám to vadilo, dáme na začátek PUSH PSW a na konec POP PSW. Samozřejmě, spoléháme na to, že zásobník je správně nastavený, ale vzhledem k tomu, jak často je používaný, tak je to jedna z prvních věcí, co slušný programátor udělá.

No a s informacemi z této lekce můžete stejné zadání vyřešit třeba takto:

<pre class="lang:default decode:true">PUSH B
PUSH H
POP B
POP H</pre>

### SPHL

Tato instrukce naplní registr SP obsahem dvojice registrů HL. Tedy SP = HL. Ptáte se, jak se zařídí opak, tedy jak zjistíte hodnotu registru SP a uložíte ji do HL? Tipujete instrukci HLSP? Ne, žádná taková není. Musíte použít konstrukci LXI H,0 a DAD SP (o instrukci DAD si řekneme víc v [další lekci](https://strojak.cz/instrukce-8080-aritmetika/ "Instrukce 8080 – aritmetika")).

### XTHL

Další instrukce, která vyměňuje hodnoty dvojic registrů. XTHL vymění hodnotu dvojice registrů HL s &#8222;poslední uloženou hodnotou na zásobníku&#8220;. Tedy: poslední hodnota, uložená na zásobníku, se ocitne v HL, a hodnota HL bude poslední hodnota na zásobníku.