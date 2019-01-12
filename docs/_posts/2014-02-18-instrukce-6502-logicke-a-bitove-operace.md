---
id: 193
title: 'Instrukce 6502 &#8211; logické a bitové operace'
date: 2014-02-18T14:06:18+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=193
permalink: /instrukce-6502-logicke-a-bitove-operace/
dsq_thread_id:
  - "2283517402"
categories:
  - "6502"
---
Blížíme se ke konci, zbývá doprobrat už jen pár instrukcí, konkrétně logické operace a manipulace s bity.

<!--more-->

### AND, ORA, EOR

Trojice instrukcí pro základní bitové operace &#8211; and, or, xor (exclusive or) se u procesoru 6502 jmenují AND, ORA a EOR. Provedou danou logickou operaci s obsahem registru A, výsledek uloží do A a nastaví příznaky N a Z.

Všechny tři instrukce mají poměrně bohaté možnosti adresování:

  * imm &#8211; přímý operand: AND #1 provede operaci A = A & 01
  * abs, zp &#8211; přímo zadaná adresa, buď plná, nebo v zero page
  * abx,aby &#8211; absolutní adresa, zvýšená o obsah registru X či Y
  * zpx &#8211; adresa v zero page, indexovaná přes registr X
  * izx, izy &#8211; nepřímo adresovaný operand (viz [adresní módy](http://strojak.uelectronics.info/adresni-mody-6502/ "Adresní módy 6502"))



### Rotace &#8211; ROL, ROR

Instrukce ROL a ROR [rotují bitově](http://strojak.uelectronics.info/instrukce-8080-rotace/ "Instrukce 8080 – rotace") obsah registru A nebo paměti (ROL doleva, ROR doprava). Při rotaci se rotuje přes příznak C.

ROL posune bity o 1 doleva. To znamená, že bit 0 se přesune na pozici 1, bit 1 na pozici 2, bit 2 na pozici 3 a tak dál, a bit 7, který nám vypadne zleva ven, je zapsán do příznaku C, a původní hodnota z C je přesunuta do pozice 0 v registru A. ROR funguje stejně, jen obráceným směrem. Graficky to vypadá nějak takto:

<table>
  <tr>
    <th rowspan="2">
      Operace
    </th>
    
    <th colspan="8">
      Pozice v operandu
    </th>
    
    <th>
      Příznak
    </th>
  </tr>
  
  <tr>
    <th>
      7
    </th>
    
    <th>
      6
    </th>
    
    <th>
      5
    </th>
    
    <th>
      4
    </th>
    
    <th>
      3
    </th>
    
    <th>
      2
    </th>
    
    <th>
      1
    </th>
    
    <th>
    </th>
    
    <th>
      C
    </th>
  </tr>
  
  <tr>
    <th>
      Výchozí stav
    </th>
    
    <td style="background: #fff;">
      Bit 7
    </td>
    
    <td style="background: #eee;">
      Bit 6
    </td>
    
    <td style="background: #ddd;">
      Bit 5
    </td>
    
    <td style="background: #ccc;">
      Bit 4
    </td>
    
    <td style="background: #bbb;">
      Bit 3
    </td>
    
    <td style="background: #aaa;">
      Bit 2
    </td>
    
    <td style="background: #999;">
      Bit 1
    </td>
    
    <td style="background: #888;">
      Bit 0
    </td>
    
    <td style="background: #ff8;">
      C
    </td>
  </tr>
  
  <tr>
    <th>
      ROL
    </th>
    
    <td style="background: #eee;">
      Bit 6
    </td>
    
    <td style="background: #ddd;">
      Bit 5
    </td>
    
    <td style="background: #ccc;">
      Bit 4
    </td>
    
    <td style="background: #bbb;">
      Bit 3
    </td>
    
    <td style="background: #aaa;">
      Bit 2
    </td>
    
    <td style="background: #999;">
      Bit 1
    </td>
    
    <td style="background: #888;">
      Bit 0
    </td>
    
    <td style="background: #ff8;">
      C
    </td>
    
    <td style="background: #fff;">
      Bit 7
    </td>
  </tr>
  
  <tr>
    <th>
      ROR
    </th>
    
    <td style="background: #ff8;">
      C
    </td>
    
    <td style="background: #fff;">
      Bit 7
    </td>
    
    <td style="background: #eee;">
      Bit 6
    </td>
    
    <td style="background: #ddd;">
      Bit 5
    </td>
    
    <td style="background: #ccc;">
      Bit 4
    </td>
    
    <td style="background: #bbb;">
      Bit 3
    </td>
    
    <td style="background: #aaa;">
      Bit 2
    </td>
    
    <td style="background: #999;">
      Bit 1
    </td>
    
    <td style="background: #888;">
      Bit 0
    </td>
  </tr>
</table>

Instrukce ROL a ROR nabízejí opět několik adresních módů. Bez operandu pracuje s obsahem registru A. Pokud chcete pracovat s obsahem paměti, můžete buňku adresovat buď absolutně (abs, 2 bajty adresa), v nulové stránce (zp, 1 bajt), nebo indexovaně (abx nebo zpx).

### Posuny &#8211; ASL a LSR

Posuny se od rotací liší v tom, že &#8222;vypadnuvší&#8220; bit je přesunut do příznaku C, ale původní hodnota tohoto příznaku je zahozena a místo ní vstoupí zpět hodnota 0. ASL posouvá doleva, zprava doplní 0, LSR posouvá doprava, zleva doplní 0. Adresní módy jsou stejné jako u rotací &#8211; bez operandů se pracuje s registrem A, pokud chcete pracovat s pamětí, můžete použít abs, zp, abx nebo zpx.

Matematicky odpovídá posun doleva vynásobení hodnoty dvojkou, posun doprava pak celočíselnému dělení 2 (zbytek je v příznaku C). Grafické znázornění zde:

<table>
  <tr>
    <th rowspan="2">
      Operace
    </th>
    
    <th colspan="8">
      Pozice v operandu
    </th>
    
    <th>
      Příznak
    </th>
  </tr>
  
  <tr>
    <th>
      7
    </th>
    
    <th>
      6
    </th>
    
    <th>
      5
    </th>
    
    <th>
      4
    </th>
    
    <th>
      3
    </th>
    
    <th>
      2
    </th>
    
    <th>
      1
    </th>
    
    <th>
    </th>
    
    <th>
      C
    </th>
  </tr>
  
  <tr>
    <th>
      Výchozí stav
    </th>
    
    <td style="background: #fff;">
      Bit 7
    </td>
    
    <td style="background: #eee;">
      Bit 6
    </td>
    
    <td style="background: #ddd;">
      Bit 5
    </td>
    
    <td style="background: #ccc;">
      Bit 4
    </td>
    
    <td style="background: #bbb;">
      Bit 3
    </td>
    
    <td style="background: #aaa;">
      Bit 2
    </td>
    
    <td style="background: #999;">
      Bit 1
    </td>
    
    <td style="background: #888;">
      Bit 0
    </td>
    
    <td style="background: #ff8;">
      C
    </td>
  </tr>
  
  <tr>
    <th>
      ASL
    </th>
    
    <td style="background: #eee;">
      Bit 6
    </td>
    
    <td style="background: #ddd;">
      Bit 5
    </td>
    
    <td style="background: #ccc;">
      Bit 4
    </td>
    
    <td style="background: #bbb;">
      Bit 3
    </td>
    
    <td style="background: #aaa;">
      Bit 2
    </td>
    
    <td style="background: #999;">
      Bit 1
    </td>
    
    <td style="background: #888;">
      Bit 0
    </td>
    
    <td style="background: #eef;">
    </td>
    
    <td style="background: #fff;">
      Bit 7
    </td>
  </tr>
  
  <tr>
    <th>
      LSR
    </th>
    
    <td style="background: #eef;">
    </td>
    
    <td style="background: #fff;">
      Bit 7
    </td>
    
    <td style="background: #eee;">
      Bit 6
    </td>
    
    <td style="background: #ddd;">
      Bit 5
    </td>
    
    <td style="background: #ccc;">
      Bit 4
    </td>
    
    <td style="background: #bbb;">
      Bit 3
    </td>
    
    <td style="background: #aaa;">
      Bit 2
    </td>
    
    <td style="background: #999;">
      Bit 1
    </td>
    
    <td style="background: #888;">
      Bit 0
    </td>
  </tr>
</table>



### Instrukce BIT

Instrukce BIT provede logický součin (AND) obsahu registru A a operandu, který je adresován buď absolutní adresou (abs), nebo nulovou stránkou (zp). Výsledek je zahozen, ale předtím je podle něj nastaven stav příznaku Z. Je-li tedy výsledek 0, je Z=1.

Instrukce BIT ještě nastaví příznaky N a V &#8211; zkopíruje do nich šestý a sedmý bit operandu (bit 7 do příznaku N, bit 6 do příznaku V).