---
id: 106
title: Pseudoinstrukce
date: 2013-12-24T20:17:08+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=106
permalink: /pseudoinstrukce/
dsq_thread_id:
  - "2071225723"
categories:
  - "6502"
  - "8080"
  - Z80
---
A jestli vám vadí to &#8222;pseudo-&#8222;, nemáte rádi věci, co jsou jen &#8222;jako&#8220; a raději chcete mít vše jasně nařízené, tak to nazývejte třeba &#8222;direktivy&#8220;.

<!--more-->

Překladač (správně nazývaný assembler, ale už jsme si vysvětlili, že tohle slovo se taknějak přemigrovalo i na Jazyk Symbolických Adres, a já tentokrát potřebuju zdůraznit, že mám na mysli ne ten jazyk, ale opravdu překladač, _přičemž jaksi mimochodem píšu asi nejdelší vsuvku na celém tomhle webu a doufám, že si vzpomenu, jak jsem chtěl tu větu dokončit&#8230;_) plní hlavně dva úkoly:

  1. Překládá instrukce, zapsané v mnemotechnickém tvaru, tj. v podobě nějakých snadno zapamatovatelných názvů, na jejich instrukční kódy (MOV B, C => 41h).
  2. Počítá pozici v paměti a dovoluje je nazvat nějakým lidským jménem, abychom mohli napsat &#8222;CALL podprogram&#8220; a nemuseli přemýšlet, na jaké že adrese ten podprogram je.

Kromě těchto instrukcí (&#8222;pravých&#8220; instrukcí) pracuje překladač ještě s takzvanými pseudoinstrukcemi. &#8222;Pseudo-&#8220; proto, že se zapisují jako instrukce, ale ve skutečnosti je procesor nezná. Zná je překladač a nějak se podle nich zachová. Některé už jsme si ukázali.

### ORG adresa

Oznámí prohlížeči, že toto místo programu bude na dané adrese a další instrukce se uloží za něj. Díky tomu si překladač správně spočítá adresy.

### DB číslo [,číslo, &#8230;]

Uloží do výsledného kódu bajt (nebo bajty). Místo čísla můžeme zapsat i řetězec do uvozovek &#8211; překladač s ním naloží jako se seznamem ASCII kódů.

### DW číslo [,číslo, &#8230;]

Uloží do výsledného kódu šestnáctibitové číslo jako dva bajty. Pokud je víc parametrů, uloží víc dvojbajtových hodnot.

### DS počet

Řekne překladači, že má vynechat určitý počet bajtů. Používá se v případech, že chceme nechat nějaký prostor volný (např. pro buffer) a je nám jedno, jaký bude jeho obsah na začátku, stačí, když bude N bajtů dlouhý.

### návěští EQU hodnota

Přiřadí jménu návěští nějakou hodnotu. Třeba z [minulé lekce](http://strojak.uelectronics.info/8080-ahoj-svete/ "8080 – Ahoj světe!"):

<pre>PRTOUT EQU 8500h</pre>

Od této chvíle už nemusíme řešit, jestli jsme nespletli adresu &#8211; prostě napíšeme CALL PRTOUT a překladač zjistí, že jsme PRTOUT nadefinovali takovouhle hodnotu, tak ji použije.

#### Poznámka k návěštím

Každé jméno návěští je unikátní. To znamená, že pokud použijete stejné jméno znovu k označení nějaké pozice v paměti, nebo pokud už nadefinovanému jménu budete přiřazovat hodnotu pomocí EQU, překladač vám vyhodí chybové hlášení. Je to logické &#8211; kdybyste si definovali dvakrát stejný název, překladač by nevěděl, jaká adresa platí&#8230;

**Následující pseudoinstrukce jsou popsané tak, jak je implementuje překladač [ASM80](http://www.asm80.com). V jiných překladačích se jejich syntax může lišit.**

### .INCLUDE jméno-souboru

Na místo této instrukce vloží obsah zadaného souboru, jako byste ho tam vložili pomocí copy-paste. Ale asi není potřeba vysvětlovat víc. Všimněte si tečky na začátku pseudoinstrukce&#8230; Správně by se měla psát u všech pseudoinstrukcí (je to taková docela užitečná konvence), ale z historických důvodů se u těch výše zmíněných nepoužívá.

### .IF výraz &#8211; .ENDIF

Překladač vyhodnotí výraz, a pokud je nenulový, pokračuje v překladu. Pokud je nula, vynechá všechny řádky až do nejbližšího .ENDIF &#8211; obdoba podmíněných bloků třeba u preprocesoru jazyka C.

### .IFN výraz &#8211; .ENDIF

Stejná konstrukce, jen obrácená podmínka: následující instrukce až do .ENDIF se překládají, pokud je výraz nulový!

Aktuální seznam pseudoinstrukcí naleznete v [dokumentaci k ASM80](http://retrocip.cz/asm80/).

&nbsp;

&nbsp;