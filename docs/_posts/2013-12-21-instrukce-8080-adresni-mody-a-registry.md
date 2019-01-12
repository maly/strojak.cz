---
id: 32
title: 'Instrukce 8080 &#8211; adresní módy a registry'
date: 2013-12-21T16:31:32+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=32
permalink: /instrukce-8080-adresni-mody-a-registry/
dsq_thread_id:
  - "2065087348"
categories:
  - "8080"
---
Jak instrukce ví, kde má vzít data, se kterými pracuje?

<!--more-->

Některé instrukce (jako už zmíněný NOP) nevyžadují žádné parametry a nepracují s žádnými daty. Tam je to jasné. Ovšem většina instrukcí _něco_ dělá, to znamená, že potřebuje odněkud vzít nějaké údaje, někam je uložit atd. Procesor 8080 má čtyři různé způsoby, jak říct, kde se data nacházejí.

### Přímý operand

Tímto způsobem zapíšeme přímo hodnotu do kódu a procesor ji použije. S přímým operandem pracuje např. instrukce MVI, kterou jsme si ukázali v [minulé kapitole](http://strojak.uelectronics.info/8080-prvni-program/ "8080: První program"), Součástí instrukce je přímo hodnota, která je použita.

### Přímé adresování

Je podobné předchozímu. I v tomto případě je součástí instrukce hodnota, která ale není použita tak, jak je, ale je brána jako adresa (v paměti nebo vstupně-výstupní brány). Například instrukce &#8222;LDA 554Ah&#8220; používá právě tento typ adresování. Procesor udělá to, že hodnotu 554Ah vezme jako adresu místa v paměti, přečte z něj jeden bajt a ten uloží do registru A.

### Adresování registrem

Součástí instrukce je jméno registru, registrového páru nebo dvojice registrů, se kterými se operace provádí.

### Nepřímé adresování registrem

Instrukce bere hodnotu v dvojici registrů (BC, DE, HL) nebo v registru SP jako adresu do paměti. Pracuje pak s obsahem na této adrese.

Některé instrukce pracují s tzv. &#8222;implicitním operandem&#8220;, což znamená, že operand (jeden z operandů) je daný už samotnou instrukcí. Například zmíněná instrukce ADD používá dva operandy &#8211; jeden je vždy A a druhý je určen parametrem.

### Registry

U osmibitových instrukcí můžeme použít libovolný osmibitový registr &#8211; tedy A (akumulátor), B, C, D, E, H, L a M.

Moment! Registr M? Nikde jsem se o něm nezmínil&#8230; Není to nějaký renonc?

Není. &#8222;Registr M&#8220; (memory) je ve skutečnosti buňka v paměti na adrese, která je uložena v registrech H a L. Pokud je v registru H hodnota 12h a v registru L hodnota 34h, bude instrukce, která pracuje s &#8222;registrem M&#8220;, pracovat ve skutečnosti s obsahem paměti na adrese 1234h. Instrukce ADD M přičte k obsahu registru A hodnotu z paměti na adrese 1234h.

Všimněte si, že nelze přímo přistupovat k registru F. Ono to většinou není k ničemu dobré, ale kdybyste opravdu potřebovali znát jeho hodnotu, dá se to obejít takovým trikem, který si popíšeme později.

Pokud instrukce pracuje s šestnáctibitovou hodnotou, využívá šestnáctibitový registr SP nebo dvojice registrů B, D, H. &#8222;Dvojregistr&#8220; B  jsou vlastně registry B a C, v B je vyšší bajt, v C nižší. Obdobně pro D a H. Instrukce pro práci se zásobníkem používají &#8222;šestnáctibitový registr&#8220; PSW, což je ve skutečnosti dvojice registrů A a F (A vyšší byte, F nižší).

### Malý Indián

Procesory Intel, Zilog Z80 i 6502 používají zápis vícebytových čísel ve formátu &#8222;Little Endian&#8220;. Znamená to, že na nižší adrese je méně významná část čísla. Příklad: Na adresu 0100h uložíme naši obligátní hodnotu 1234h. Paměť je organizovaná po bajtech, hodnota je dvoubajtová (16 bitů), jak to tedy uložit? Použijeme dvě buňky paměti, 0100h a 0101h. Na tu nižší přijde nižší část čísla (34h), na tu vyšší zase vyšší část (12h). V paměti to tedy bude vypadat nějak takto:

0100h: 34h
  
0101h: 12h

Některé procesory (z těch známějších především procesory od Motoroly) používají opačný způsob (&#8222;Big Endian&#8220;), kdy se části čísla zapisují do paměti od nejvýznamnějšího bajtu.