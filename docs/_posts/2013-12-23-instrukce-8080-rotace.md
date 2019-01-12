---
id: 63
title: 'Instrukce 8080 &#8211; rotace'
date: 2013-12-23T14:56:40+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=63
permalink: /instrukce-8080-rotace/
dsq_thread_id:
  - "2068700394"
categories:
  - "8080"
---
&#8222;Cože? Rotace? Proč ne něco Opravdu Důležitého?&#8220; &#8211; ale ony jsou docela důležité, věřte mi, a když je správně použijete, tak ušetří spoustu času.

<!--more-->

Procesor 8080 oplývá sadou instrukcí, která dokáže provádět takzvané bitové rotace registru A. Co to znamená?

Představme si registr A jako osm bitů, očíslovaných (od nejnižšího) 0-7. Představme si je napsané vedle sebe.

<table>
  <tr>
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

To je on. S ním budeme teď pracovat a jako první si ukážeme operaci &#8222;rotace vlevo&#8220;:

### RLC

Instrukce RLC posune bity o 1 doleva. To znamená, že bit 0 se přesune na pozici 1, bit 1 na pozici 2, bit 2 na pozici 3 a tak dál, a bit 7, který nám vypadne zleva ven, se zase vrátí zprava na pozici 0. (A právě proto, že takhle &#8222;krouží&#8220;, se té operaci říká &#8222;rotace&#8220;. Kdyby vypadl a byl zahozen, byl by to &#8222;posuv&#8220; &#8211; ale takové instrukce 8080 nemá). Bit 7 je zároveň zkopírován do příznaku CY.

<table>
  <tr>
    <th rowspan="2">
      Operace
    </th>
    
    <th colspan="8">
      Pozice v registru A
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
      CY
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
      CY
    </td>
  </tr>
  
  <tr>
    <th>
      RLC
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
    
    <td style="background: #fff;">
      Bit 7
    </td>
    
    <td style="background: #fff;">
      Bit 7
    </td>
  </tr>
</table>

### RRC

Instrukce RRC posune bity o 1 doprava. To znamená, že bit 7 se přesune na pozici 6, bit 6 na pozici 5, bit 5 na pozici 4 a tak dál, a bit 0, který tentokrát vypadne vpravo, se vrátí zleva na pozici 7, a návdavkem je zkopírován do příznaku CY.

<table>
  <tr>
    <th rowspan="2">
      Operace
    </th>
    
    <th colspan="8">
      Pozice v registru A
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
      CY
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
      CY
    </td>
  </tr>
  
  <tr>
    <th>
      RRC
    </th>
    
    <td style="background: #888;">
      Bit 0
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

### RAL

Další dvě instrukce berou v úvahu i obsah příznaku CY. RAL funguje stejně jako RLC, ale s jedním rozdílem &#8211; bit 7, který zleva vypadne, je zapsán do příznaku CY, a hodnota z CY je přesunuta do pozice 0 v registru A. Graficky to vypadá nějak takto:

<table>
  <tr>
    <th rowspan="2">
      Operace
    </th>
    
    <th colspan="8">
      Pozice v registru A
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
      CY
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
      CY
    </td>
  </tr>
  
  <tr>
    <th>
      RAL
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
      CY
    </td>
    
    <td style="background: #fff;">
      Bit 7
    </td>
  </tr>
</table>

### RAR

RAR je obdoba předchozí funkce RAL, jen směr je opačný &#8211; bity se posouvají doprava, bit 0 jde do CY a &#8222;staré CY&#8220; jde na pozici 7.

<table>
  <tr>
    <th rowspan="2">
      Operace
    </th>
    
    <th colspan="8">
      Pozice v registru A
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
      CY
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
      CY
    </td>
  </tr>
  
  <tr>
    <th>
      RAR
    </th>
    
    <td style="background: #ff8;">
      CY
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

Víc rotací procesor 8080 nemá.

_Jaké mají tyto operace smysl?_ Když se nad tím zamyslíte, je to analogické jako u desítkové soustavy: když máte číslo (123) a posunete ho o jednu pozici doleva (a zprava doplníte nulou), dostanete jeho desetinásobek (1230). Když ho posunete doprava, dostanete celočíselný výsledek dělení deseti. U dvojkové soustavy platí totéž &#8211; posun doleva je totéž jako vynásobit dvěma, posun doprava je dělení dvěma.

Trochu tam hapruje to rotování kolem dokola, ale to lze obejít pomocí vhodného domaskování nebo tím, že si předem nastavíte příznak CY na požadovanou hodnotu.

Dají se použít ještě na spoustě dalších míst &#8211; ale to si ještě ukážeme.

### STC, CMC a jak nastavit příznak CY?

Přiznám se, že jsem váhal, kam zařadit instrukce STC a CMC, a nakonec je zařadím sem. Instrukce STC nastaví CY na 1, instrukce CMC neguje jeho hodnotu. Pokud chcete CY nastavit na nulu, musíte buď udělat STC a CMC, nebo (pokud vám nevadí, že přijdete o hodnotu v příznacích S, Z, P) zkusit třeba [CMP A &#8211; tato instrukce porovná registr A se sebou samotným](http://strojak.uelectronics.info/instrukce-8080-aritmetika/ "Instrukce 8080 – aritmetika"). Výsledek je, nepřekvapivě, &#8222;hodnota se sobě rovná&#8220;, takže instrukce nastaví Z na 1 a S a CY nuluje.

#### Rotace 8080 &#8211; souhrn

<table>
  <tr>
    <th rowspan="2">
      Operace
    </th>
    
    <th colspan="8">
      Pozice v registru A
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
      CY
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
      CY
    </td>
  </tr>
  
  <tr>
    <th>
      RLC
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
    
    <td style="background: #fff;">
      Bit 7
    </td>
    
    <td style="background: #fff;">
      Bit 7
    </td>
  </tr>
  
  <tr>
    <th>
      RRC
    </th>
    
    <td style="background: #888;">
      Bit 0
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
  
  <tr>
    <th>
      RAL
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
      CY
    </td>
    
    <td style="background: #fff;">
      Bit 7
    </td>
  </tr>
  
  <tr>
    <th>
      RAR
    </th>
    
    <td style="background: #ff8;">
      CY
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