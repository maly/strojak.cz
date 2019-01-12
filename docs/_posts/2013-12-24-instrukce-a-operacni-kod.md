---
id: 83
title: Instrukce a operační kód
date: 2013-12-24T12:16:33+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=83
permalink: /instrukce-a-operacni-kod/
dsq_thread_id:
  - "2070508299"
categories:
  - "8080"
  - Z80
---
&#8230; a když na vás o půlnoci zakřičím: &#8222;C3h!&#8220;, tak vy vyskočíte!

<!--more-->

Jistě jste se v předchozích kapitolách dívali na to, jaké kódy patří k jakým instrukcím a zaujalo vás, že &#8222;MVI A, něco&#8220; má vždycky první bajt 3Eh. (_Cože? Nevšimli? Nezaujalo? A zvážili jste, že byste se místo assembleru učili třeba Javu?_)

Nebo taková instrukce MOV &#8211; ať už je jakákoli, je její kód vždycky mezi 40h a 7Fh. Má to svoji logiku. Schválně, podívejte se sami (ten program je naprosto nesmyslný, proto se nedívejte, co dělá, ale jak je přeložený!)



Dá se v tom vypozorovat určitá pravidelnost, že? Instrukce MOV obecně vypadá totiž po jednotlivých bitech takto:

<table>
  <tr>
    <td>
    </td>
    
    <td>
      1
    </td>
    
    <td>
      d
    </td>
    
    <td>
      d
    </td>
    
    <td>
      d
    </td>
    
    <td>
      s
    </td>
    
    <td>
      s
    </td>
    
    <td>
      s
    </td>
  </tr>
</table>

Trojice bitů D a S kódují jednotlivé registry (D = cílový, S = zdrojový). Registry jsou kódované následujícím způsobem:

| R2 | R1 | R0 | Registr |
| -- | -- | -- | ------- |
|    |    |    | B       |
|    |    | 1  | C       |
|    | 1  |    | D       |
|    | 1  | 1  | E       |
| 1  |    |    | H       |
| 1  |    | 1  | L       |
| 1  | 1  |    | M       |
| 1  | 1  | 1  | A       |

Dva nejvyšší bity jsou 0 a 1. Pokud má instrukce nejvyšší dva bity takto nastavené, ví dekodér instrukcí, že jde o MOV, že se bude přesouvat z registru do registru, a informaci o tom, ze kterého do kterého najde v bitech 5-3, resp. 2-0. (Ano, je tu výjimka &#8211; přesun z registru M do registru M není možný a operační kód, který by taková instrukce měla (76h) je vyhražen pro jinou instrukci.)

Co třeba instrukce s takovýmhle bitovým obrazem?

<table>
  <tr>
    <td>
    </td>
    
    <td>
    </td>
    
    <td>
      d
    </td>
    
    <td>
      d
    </td>
    
    <td>
      d
    </td>
    
    <td>
      1
    </td>
    
    <td>
      1
    </td>
    
    <td>
    </td>
  </tr>
</table>

Možné kódy jsou 06h, 0Eh, 16h, &#8230;, 3Eh. Tato bitová kombinace říká dekodéru, že se jedná o instrukci MVI, tedy přesun následujícího bajtu do některého z registrů. Který registr to bude, to je opět zakódované v operačním kódu &#8211; jsou to ty tři bity &#8222;d&#8220;.

A co takováhle instrukce?

<table>
  <tr>
    <td>
    </td>
    
    <td>
    </td>
    
    <td>
      d
    </td>
    
    <td>
      d
    </td>
    
    <td>
      d
    </td>
    
    <td>
    </td>
    
    <td>
      1
    </td>
    
    <td>
    </td>
  </tr>
</table>

Možné kódy jsou 04h, 0Ch, 14h, &#8230;, 3Ch. Tentokrát se jedná o instrukci INR a v operačním kódu je opět určeno, o jaký registr půjde.

Když se podíváte do [tabulky instrukcí](http://strojak.uelectronics.info/tabulka-instrukci-procesoru-8080/ "Tabulka instrukcí procesoru 8080"), zjistíte, že instrukce nejsou rozmístěny nahodile. V první čtvrtině (00-3Fh) jsou nejrůznější přesuny, rotace, inkrementace a dekrementace. Druhá čtvrtina (40h-7Fh) patří přesunům (MOV), třetí třetina (80h-BFh) obsahuje aritmetické instrukce, které pracují s registry (ADD, ADC, &#8230;) a poslední čtvrtina (C0h-FFh) obsahuje převážně skoky, operace s přímým operandem a operace se zásobníkem.

Takováto výstavba instrukcí je dána potřebou mít co nejjednodušší dekodér. Později, až se podíváme na operační kódy procesorů 6502 nebo 680x, zjistíme, že jsou v dodržování tohoto principu mnohem důslednější a že i přes své zdánlivé limity nabízejí programátorovi mnohem větší svobodu.