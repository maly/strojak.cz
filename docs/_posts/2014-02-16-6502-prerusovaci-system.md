---
id: 177
title: '6502 &#8211; přerušovací systém'
date: 2014-02-16T14:49:32+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=177
permalink: /6502-prerusovaci-system/
dsq_thread_id:
  - "2275124541"
categories:
  - "6502"
---
Nejen o přerušení, ale také o tom, co se děje, když zapnete napájení.

<!--more-->

Napětí je připojeno, hodinový takt běží, procesor 6502 začíná pracovat. Co udělá ze všeho nejdřív? _Správnou odpověď do vzkazů, pokud neuhodnete, musíte si dát tuto hádanku na svůj Facebook!_

Promiňte, samozřejmě to není hádanka a nic si na Facebook dávat nemusíte. Řekneme si to hned teď. Ale začneme přerušením.

Procesor 6502 má, stejně jako jiné procesory, k dispozici přerušovací systém. [Princip přerušení](http://strojak.uelectronics.info/bcd-a-preruseni/ "BCD a přerušení") jsme si už popisovali u procesoru 8080: v situaci, kdy je potřeba zareagovat (např. přišel kompletní načtený znak z terminálu) si vyžádají okolní obvody pozornost procesoru přerušovacím signálem. Procesor k takovému účelu má extra přerušovací vstup (někdy víc), a zareaguje tak, že uloží svůj stav na zásobník a provede určitou instrukci, která většinou způsobí skok do podprogramu.

Přerušení u 8080 je maskovatelné, to znamená, že pomocí instrukce může programátor zakázat, aby procesor na přerušení reagoval (obvykle v časově kritických místech).

U 6502 jsou dva přerušovací vstupy. Jeden z nich je maskovatelný (IRQ) &#8211; signál na tomto vstupu vyvolá přerušení pouze v případě, že není nastaven [příznak I](http://strojak.uelectronics.info/6502-priznaky-a-instrukce-pro-praci-s-nimi/ "6502 – příznaky a instrukce pro práci s nimi"). Druhý přerušovací vstup je nemaskovatelný (NMI, Non-Maskable Interrupt). Signál na tomto vstupu vyvolá přerušení vždy.

Co se stane, když systém vyvolá přerušení? Procesor v tu chvíli uloží na zásobník hodnotu registru PC (nejprve vyšší, potom nižší bajt), pak uloží rovněž na zásobník hodnotu příznakového registru P a pak skočí na adresu obsluhy přerušení.

A tu zjistí kde přesně? Správná otázka. Pokud šlo o maskovatelné přerušení IRQ, tak si ji přečte na adresách FFFEh a FFFFh, tedy na posledních dvou adresách adresního prostoru. Pokud vás trápí otázka &#8222;a jak se tam ta adresa dostane?&#8220;, odpověď zní: To záleží na návrháři systému. Pokud je tam pevná paměť (ROM/PROM/EPROM/atd.), je v ní adresa obsluhy přerušení (&#8222;přerušovací vektor&#8220;) uložená napevno. Když je tam RAM, zapsal si ji tam programátor.

Pokud šlo o nemaskovatelné přerušení NMI, přečte si adresu obslužné rutiny na adresách FFFAh a FFFBh.

Zajímá vás, co je mezi tím? Na FFFAh a FFFBh je adresa obsluhy NMI, adresa obsluhy IRQ je na FFFEh a FFFFh, zbývá volný prostor FFFCh a FFFDh&#8230; Tam je adresa RESETu.

Ano, čtete dobře. Po zapnutí napájení nebo po přivedení signálu RESET se procesor nenastavuje do nějakého definovaného stavu, on prostě jen skočí na adresu, která je zapsaná v buňkách FFFCh a FFFDh. Jediný rozdíl proti přerušení (viz výše) je v tom, že RESET neukládá PC a P na zásobník.

Vzhledem k tomu je potřeba, aby na těchto adresách byla při startu systému smysluplná adresa. Nemůžeme spoléhat na to, že ji tam zapíše programátor, takže je to potřeba buď vyřešit tím, že na konci paměťového rozsahu je paměť ROM, nebo nějakým obvodovým hackem, který po startu &#8222;podvrhne&#8220; procesoru tu správnou adresu.

### Instrukce RTI

Instrukce RTI &#8211; Return from Interrupt doplňuje přerušení, jak jsme si popsali výše. Provádí přesně opačné kroky, tj. ze zásobníku načte obsah registru P, pak nižší a vyšší bajt registru PC. Postará se tedy o správný návrat z rutiny přerušení.

Jednoduchý příklad v assembleru: program uloží do registru A hodnotu 3 a tu pak zapíše do nulté stránky na adresu 0. Zápis pak probíhá stále dokola &#8211; můžete si ověřit pomocí krokování. Pokud kliknete na IRQ, procesor si odskočí do obsluhy přerušení, která změní obsah registru A. Po návratu do nekonečné zapisovací smyčky se už tedy bude zapisovat jiná hodnota.


  
Všimněte si, že tentokrát program začíná na adrese 0200h a od adresy FFFCh jsou zadány hodnoty startovací adresy a přerušovací rutiny.

Při krokování si všimněte, že při provádění obsluhy přerušení je nastaven příznak I.

### Instrukce BRK

Co se stane, když procesor provede instrukci BRK? Uloží na zásobník hodnotu registru PC (nejprve vyšší, potom nižší bajt), pak uloží na zásobník hodnotu příznakového registru P a pak skočí na adresu obsluhy přerušení IRQ, kterou si přečte na adresách FFFEh a FFFFh.

Možná vám to připadá povědomé&#8230; Ano, přesně totéž se děje při maskovatelném přerušení IRQ! Není to náhoda.

Ve skutečnosti je procesor 6502 zapojen tak, že přerušovací požadavek po kontrole příznaku I uloží do registru, kam si načítá kód další instrukce, operační kód instrukce BRK (který je, čistě pro zajímavost, roven 00h). Takže se opravdu provádí to samé &#8211; s jedinou výjimkou: instrukce BRK před skokem na obsluhu ještě nastaví [příznakový bit B](http://strojak.uelectronics.info/6502-priznaky-a-instrukce-pro-praci-s-nimi/ "6502 – příznaky a instrukce pro práci s nimi"). Podle něj lze poznat, jestli obsluhu přerušení vyvolal vnější systém (B=0) nebo instrukce BRK (B=1).

Pro zájemce jen dodám, že stejně funguje i obsluha NMI, která rovněž podvrhne BRK, ale změní i adresy vektoru, a v zásadě i signál RESET, který ale místo &#8222;ukládání do paměti&#8220; při práci se zásobníkem aktivuje signál &#8222;čtení z paměti&#8220; &#8211; více o těchto vnitřních zajímavostech naleznete v článku <a href="http://www.pagetable.com/?p=410" rel="next">Internals of BRK/IRQ/NMI/RESET on a MOS 6502</a>. Z tohoto článku ocituju i souhrnnou tabulku, co se děje v procesoru 6502 při přerušení a instrukci BRK:

<table border="1">
  <tr>
    <th>
      Příčina
    </th>
    
    <th>
      Vektor
    </th>
    
    <th>
      Ukládá PC a P na zásobník?
    </th>
    
    <th>
      Nastavuje příznak B?
    </th>
  </tr>
  
  <tr>
    <td>
      signál NMI
    </td>
    
    <td>
      $FFFA/$FFFB
    </td>
    
    <td align="center">
      ano
    </td>
    
    <td align="center">
      ne
    </td>
  </tr>
  
  <tr>
    <td>
      signál RESET
    </td>
    
    <td>
      $FFFC/$FFFD
    </td>
    
    <td align="center">
      ne
    </td>
    
    <td align="center">
      ne
    </td>
  </tr>
  
  <tr>
    <td>
      signál IRQ
    </td>
    
    <td>
      $FFFE/$FFFF
    </td>
    
    <td align="center">
      ano
    </td>
    
    <td align="center">
      ne
    </td>
  </tr>
  
  <tr>
    <td>
      instrukce BRK
    </td>
    
    <td>
      $FFFE/$FFFF
    </td>
    
    <td align="center">
      ano
    </td>
    
    <td align="center">
      ano
    </td>
  </tr>
</table>

(K dalšímu studiu: [6502 interrupts](http://www.6502.org/tutorials/interrupts.html))