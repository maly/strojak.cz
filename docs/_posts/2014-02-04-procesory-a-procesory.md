---
id: 151
title: Procesory a procesory
date: 2014-02-04T11:25:57+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=151
permalink: /procesory-a-procesory/
dsq_thread_id:
  - "2219206017"
categories:
  - Obecně
---
Už jsem zmiňoval, že mezi mikroprocesory jsou poměrně zásadní rozdíly &#8211; čímž samozřejmě nemyslím to, že je každý jiný, ale mám na mysli rozdíly v přístupu, takříkajíc _filosofické_.

Ukázali jsme si procesor Intel 8080. Tento procesor vychází ideově z předchozího procesoru téže firmy, totiž 8008, a ten svou architekturou zase vyšel z procesoru terminálu [Datapoint 2200](http://en.wikipedia.org/wiki/Datapoint_2200). Původně měl být v tomto terminálu použit, ale Intel ho nestihl vyvinout a vyrobit včas, takže nakonec v Datapointu byl procesor sestavený z SSI a MSI obvodů a Intel navrhl univerzální procesor 8008.

Jeho nejznámější rival byl vyvinut firmou Motorola a byl označen 6800. Motorola při návrhu vyšla z procesoru počítačů PDP-11. Některé rysy měl procesor 6800 shodné s procesorem 8080 (třeba 16bitovou adresovou sběrnici, osmibitovou datovou sběrnici), ale ve spoustě věcí se lišil.

6800 používá zápis vícebajtových čísel stylem Big Endian (8080 stylem Little Endian). Pokud máte na adresy 0000 a 0001 zapsat hexadecimální číslo 1234h, pro procesor 8080 ho zapíšete tak, že na nižší adresu zapíšete nižší bajt (34h), na vyšší adresu vyšší (12h). U Motoroly 6800 to bude přesně naopak. Ve výpisu paměti na jednom řádku to pak bude vypadat takto:

<pre>Intel:    0000: 34 12 xx xx
Motorola: 0000: 12 34 xx xx</pre>

Procesor od Motoroly nerozlišuje paměť a porty a pracuje s obojím stejně. Nemá tedy pro čtení a zápis z/do portů speciální instrukce, ale používá standardní instrukce pro práci s pamětí, které jsou mnohem flexibilnější. Nevýhodou je, že se tak návrháři připravují o souvislý prostor 64kB pro paměť, protože část musí vyhradit pro porty.

Motorola použila v procesoru 6800 pouhé dva osmibitové registry (A a B) a jeden šestnáctibitový indexový registr X. (Ukazatel zásobníku SP a programový čítač PC jsou i zde, oba šestnáctibitové).

Na první pohled to může vypadat jako nedostatečné &#8211; vždy´t některé algoritmy, co jsme si ukazovali u procesoru 8080, využily klidně šest, sedm registrů. Jak to tedy řešit u 6800?

Procesor 6800 přišel s jednoduchým trikem, který po něm převzali i jeho následníci. Paměť si rozdělil na 256 stránek po 256 bajtech (tj. horní byte adresy je &#8222;číslo stránky&#8220; a dolní &#8222;adresa ve stránce&#8220;). Stránka 0 pak má speciální roli, protože je snadno adresovatelná. Co to znamená?

Procesor 8080 měl u spousty instrukcí pevně dáno, s jakými daty pracuje. Buď s registrem, s přímo zadanou konstantou, nebo s obsahem paměti, adresovaným registry HL nebo 16bitovou adresou. 6800 naproti tomu měl instrukční sadu mnohem víc _ortogonální_.

> Ortogonální instrukční sada je taková, kdy instrukce může pracovat s různými daty naprosto stejně, bez ohledu na to, jestli jsou v registru, nebo v paměti.

Procesor 6800 proto u instrukcí nabízel několik adresních módů. Operand mohl být určen:

  1. Implicitně. Například instrukce INCA pracuje s registrem A
  2. Bezprostředně (Immediate). LDAA #$02 nahraje do registru A (LoaD A) konstantu &#8222;bezprostředně zapsanou&#8220; za operačním kódem &#8211; tedy 02h
  3. Přímou adresou (Direct). LDAA $02 nahraje do registru A hodnotu, která je uložená na adrese, co je zapsaná za operačním kódem. Adresa je pouze osmibitová, vyšších osm bitů se doplní nulami, adresa je tedy 0002h. Instrukce zabírá dva bajty, první je operační kód, druhý je zkrácená adresa &#8211; tedy vlastně &#8222;adresa ve stránce 0&#8220;
  4. Rozšířenou adresou (Extended). Obdoba předchozího zápisu, ale adresa je dvojbajtová, lze tedy adresovat paměť v celém rozsahu. LDAA $1234 nahraje do registru A hodnotu z adresy 1234h
  5. Indexovanou adresou, tedy adresou, která vznikne součtem hodnoty z indexového registru X a osmibitové konstanty.
  6. Relativně. V takovém případě se k adrese PC přičetla hodnota konstanty v rozsahu -128..+127

Pokud programátor chce k obsahu akumulátoru (tedy registru A nebo B) přičíst nějaké číslo, použije instrukci sčítání a podle adresního módu určí, jestli se bude zadávat přímo nebo z paměti, a pokud z paměti, tak jestli plnou adresou (výhoda: může být kdekoli, nevýhoda: instrukce je o bajt delší a časově náročnější), nebo ze stránky 0, nebo pomocí indexového registru&#8230; Hodnota může být přitom klidně načtena z portu, protože, jak jsme si už řekli, procesor 6800 nerozlišuje mezi porty a pamětí a ke všemu přistupuje stejně.

Z popisu adresních módů vyplývá, že stránka 0 je snadno dostupná a je to takový kompromis mezi plnou adresou a registrem v procesoru. Přístup k těmto 256 buňkám paměti je rychlý a instrukce jsou i kratší, lze je tedy považovat za určitý ekvivalent vnitřních registrů procesoru.

Assemblerem procesoru 6800 se zabývat nebudu, přesto jsem ho tu uvedl. Z architektury 6800 totiž vyšel nejen legendární procesor 6502 (kterému se věnovat budu), ale i další procesory &#8211; třeba &#8222;poslední osmibitový procesor&#8220; 6809 (samozřejmě v uvozovkách &#8211; tento procesor měl na tehdejší dobu ojedinělé možnosti, ale přišel bohužel pozdě) a celá řada procesorů počínaje procesorem MC68000 (což nebyl &#8222;osmibit roztáhnutý na 16 bitů&#8220;, ale regulerní 32bitový procesor, ořezaný na 16 bitů, s vysoce ortogonální instrukční sadou, dvěma režimy procesoru apod.) přes další procesory téže řady až k MC68060 a dál k PowerPC. Tato řada se v běžných domácích a kancelářských počítačích už nevyskytuje (od přechodu Apple na Intel), ale dodneška se udržuje v embedded zařízeních apod.

Dovolte mi nostalgickou odbočku. Po 8080 jsem přešel na Z80 (byl jsem Spectrista) a tenhle procesor mi učaroval. Stovky instrukcí, mnoho registrů, šestnáctibitová aritmetika&#8230; Na 6502 jsem se díval s totálním nepochopením: vždyť to má jen tři registry a je to pomalé (=má to malou frekvenci). ve skutečnosti byla &#8222;datová propustnost&#8220; srovnatelná. Ostatně operace s pamětí v zero page zabraly 3 takty procesoru, nejrychlejší operace s pamětí u Z80 trvala 7 taktů. Krásu téhle architektury jsem docenil až později, když jsem se naučil assembler 68000 (Atari ST, Amiga). V čerstvé paměti jsem měl tehdy naučený asembler 8086, se všemi jeho &#8222;segmenty&#8220; a &#8222;instrukcemi, co používaly vždy přesně určené registry&#8220; &#8211; a najednou máte k dispozici ortogonální sadu a osm adresních + osm datových registrů. Třeba instrukce PUSH (která tam vlastně nebyla) fungovala jako obyčejný přesun hodnoty z registru do paměti s adresním módem &#8222;přenes do paměti na adresu danou některým adresním registrem, ale předtím hodnotu sniž&#8220; (predekrement).

A po obecném úvodu pojďme už na assembler 6502!