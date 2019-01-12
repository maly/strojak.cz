---
id: 109
title: BCD a přerušení
date: 2013-12-24T21:46:09+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=109
permalink: /bcd-a-preruseni/
dsq_thread_id:
  - "2071334998"
categories:
  - "8080"
---
Už se blížíme ke konci, už vysvětluju to, co jsem průběžně odsouval&#8230;

<!--more-->

Kód BCD (Binary Coded Decimal) je způsob zápisu desítkových čísel v šestnáctkové soustavě tak, že se místo znaků 0-F používají jen znaky 0-9 a když k 09h přičtete jedničku, nedostanete 0Ah, ale 10h. [Odborné vysvětlení si můžete přečíst na Wikipedii (BCD)](http://cs.wikipedia.org/wiki/BCD), já se přiznám, že mi to moc jasné nebylo. Vezmu to tedy &#8222;vlastními slovy&#8220;.

Někdy se hodí vypsat číslice takovým způsobem, jakému rozumějí normální smrtelníci. Ti, jestli si ještě vzpomínáte, považují za číslice jen ty znaky od nuly do devítky, a kdybyste na ně vyrukovali s tvrzením, že A až F jsou taky číslice, tak vám nebudou rozumět. A ačkoli se někteří programátoři (pohříchu často vyrostlí na assembleru) domnívají, že člověk se má buď naučit rozumět počítačům tak, jak je přirozené strojům, nebo na ně nešahat a jít pracovat do marketingu, je převažující postoj opačný. Takže i v tom assembleru nezaškodí čas od času nasimulovat starou dobrou desítkovou soustavu.

Jestli jste si zkusili převést strojově osmibitové číslo na desítkovou hodnotu, dáte mi zapravdu, že to není úplně procházka růžovou zahradou. Dokonce ani počet bitů na číslici není vždy stejný&#8230; Proto vznikl kód BCD, kde se do jednoho osmibitového čísla vejde desítkové číslo z rozsahu 0 &#8211; 99. Pokud je číslo v kódu BCD, tak 99 (1001 1001) neznamená 153 desítkově, ale 99 desítkově.

Což, jako vyjádření čísla dobré, ale teď &#8211; jak se s tím pracuje? Procesor na to má nástroj.

Představte si, že sečtete dvě čísla, řekněme 13h a 28h. To máme 3Bh (je to totiž desítkově 19 + 40 = 59). A přesně to nám spočítá následující kód:

<pre>.ORG 0 
 MVI A, 13h
 ADI 28h</pre>

Pokud budeme obě čísla považovat za čísla v kódu BCD, tedy vlastně třináct a dvacet osm, budeme očekávat, že výsledek bude 41 &#8211; v BCD kódu 41h. Jak tedy sečíst dvě čísla v kódu BCD? Zcela normálně: sečteme je běžnou instrukcí ADD/ADI, a po ní použijeme instrukci DAA.

### DAA

znamená &#8222;Decadic Adjust after Addition&#8220;, neboli Desítková úprava po sčítání. Tahle instrukce vezme příznakové bity (zejména bit AC, který se nikde jinde nepoužívá) a pomocí nich upraví výsledek (3Bh) tak, aby byl v kódu BCD (41h).

Při odčítání platí

<h1 style="padding-left: 30px;">
  Požadavek na přerušení!
</h1>

<p style="padding-left: 30px;">
  V procesorových systémech se občas stane něco významného a důležitého, na co je potřeba okamžitě zareagovat. Třeba někdo zmáčkne klávesu na klávesnici. <em>(Aspoň vidíte, co je ve světě procesorů Opravdu Důležité &#8211; kdyby se náhodou vzňal a hořel, bylo by mu to jedno, ale zmáčknutý čudlík zasluhuje okamžitou reakci!)</em> Nebo přijdou nějaké údaje ze sériového rozhraní a je třeba je přečíst, než tam někdo pošle další a tenhle se ztratí. Nebo se stane to, že uplyne nějaký předem nadefinovaný čas &#8211; tedy něco jako minutka na vaření vajíček. V takovém případě požádá to zařízení, které má <em>výjimečnou událost</em>, procesor o přerušení. Ten všeho nechá, odskočí si do podprogramu, který obslouží, co je potřeba, a pak se zase hezky vrátí zpátky k tomu, co měl rozdělané.
</p>

<p style="padding-left: 30px;">
  <strong>KONEC!</strong>
</p>

&#8230; to samé jako při sčítání. Stačí použít instrukci DAA, která upraví výsledek tak, aby zase obsahoval číslo v kódu BCD.

Po pravdě řečeno &#8211; s instrukcí DAA se člověk moc nesetkává. Kupodivu i spousta algoritmů na převod čísel z desítkové do šestnáctkové soustavy a zpátky se bez téhle instrukce obejde. Proto je to takový docela otloukánek, většinou ho vysvětlují všichni až na konci&#8230; kromě špatných knih o programování v assembleru x86, které probírají instrukce ne podle jejich složitosti nebo funkce, ale podle abecedy. Tam se totiž ta samá instrukce jmenuje AAA! Představte si, co byste říkali, kdybych na vás jako první instrukci vytáhnul právě DAA a BCD kód!

<h1 style="padding-left: 30px;">
  Požadavek na přerušení!
</h1>

<p style="padding-left: 30px;">
  Přerušení je něco jako telefonát &#8211; taky vás zaskočí v nejnevhodnější chvíli a vy se mu musíte hned teď věnovat. Proto je dobré, aby jeho obsluha byla co nejkratší (&#8222;Ano, pane řediteli! &#8211; Jistě pane řediteli! &#8211; Tak to jste se asi pos*al, pane řediteli! &#8211; Ano, na personální si dojdu zítra ráno! Nashledanou!&#8220;) Vyřídit to nezbytné, ať se můžeme vrátit k poctivé práci.
</p>

<p style="padding-left: 30px;">
  <strong>KONEC!</strong>
</p>

BCD kód bych, s dovolením, považoval tímto za probraný &#8211; ještě se k němu vrátíme v nějakých ukázkách algoritmů, ale víc už asi moc ne. Namísto toho se vypořádáme s přerušením&#8230;

### DI, EI

Jak přerušení vypadá, to jste názorně mohli vidět v předchozím textu. Naštěstí existují instrukce DI (ta přerušení zakáže) a EI (ta ho opět povolí). DI použijeme ve chvíli, kdy procesor potřebuje dodělat svou práci a je to důležitější než obsloužit periferie &#8211; třeba něco časově kritického. V zásadě by mohl mít zakázané přerušení neustále (a některé systémy to tak mají), ale zase na druhou stranu, když zůstanu u alegorie s telefonem: Nikdo se vám nedovolá!

Teď tedy používám jedno virtuální DI, aby už další výklad žádné přerušení nerušilo, a řekneme si, jak to vlastně funguje.

Procesor má vstup INT. Za normálních okolností by na něm měla být nula, a taky tam je. Pokud některá část počítače (klávesnice, sériový port, časovač, &#8230;) požaduje přerušení, nastaví příslušné obvody tento vstup na hodnotu 1. Procesor dokončí aktuální instrukci a zkontroluje stav tohoto vstupu. Když je 0, pokračuje dál, pokud je ale 1, a zároveň je přerušení povolené, udělá následující kroky:

  1. Zakáže přerušení
  2. Potvrdí, že požadavek převzal (tj. ve stavovém slovu pošle informaci INTA &#8211; Interrupt Acknowledge)
  3. Přečte z datové sběrnice kód instrukce, kterou má vykonat. Je to jedna z instrukcí RST0-RST7, nebo instrukce CALL.
  4. Načtenou instrukci pak provede.

Všimněte si, že jsem nepsal, že ji čte &#8222;z paměti&#8220;, ale &#8222;ze sběrnice&#8220;. Je na systému, aby v takovém stavu připravil procesoru tu správnou instrukci.

Asi nejjednodušší způsob, který lze dosáhnout zapojením jednoho odporu, je ten, že vždy, když procesor vyšle INTA a čeká na instrukci, tak mu pomocný obvod 8228 vrátí RST 7. Vzpomeňte si, [co jsme si říkali o instrukci RST n](http://strojak.uelectronics.info/instrukce-8080-skoky/ "Instrukce 8080 – skoky"): Je to volání podprogramu na adrese 8*n. RST 7 tedy zavolá podprogram na adrese 38h a tam by měla proběhnout obsluha přerušení.

Složitější způsoby, které využívají třeba obvod 8214, dokážou obsloužit osm různých požadavků podle jejich priorit a adekvátně k nim zavolat různé instrukce RST. Specializované řadiče přerušení (8259) dokážou například cyklicky měnit priority nebo volat ne osm pevně daných instrukcí, ale pomocí instrukce CALL zavolat libovolnou adresu.

Ale u těch jednodušších systémů je většinou vše zapojeno tak, že INT znamená vykonání instrukce RST 7.

Obslužná rutina by měla být co nejkratší a nejrychlejší. Nezapomeňte na konci zase povolit přerušení instrukcí EI, jinak obsluha proběhne jen jednou. A pokud náhodou budete psát obsluhu přerušení pro různé RST, uvědomte si, že na to máte jen osm bajtů &#8211; nejčastěji ty první tři použijete pro JMP a pět zůstane volných.

### HLT

Víte, co dělá procesor v počítači většinu času? Čeká. Čeká, až mu něco řeknete, pak to chvíli zpracovává, a pak zase čeká. Mezi zmáčknutím kláves čeká. Když zmáčknete dvě klávesy za sekundu, tak za tu dobu provede 8080A rovné dva miliony taktů. Obsluha každého zmáčknutí zabere, no, ať nežeru, i třeba 500 taktů! Procesor tedy 1000 taktů pracoval a 1 999 000 taktů jen čekal, jestli se nestane něco zajímavého. Váš den, kdybyste byli procesor, by vypadal tak, že byste 43 sekund dělali něco zajímavého, a zbývajících 23 hodin 59 minut a 17 sekund by se nedělo nic. Vůbec nic. (A tohle jim děláme neustále, mimochodem!)

Instrukce HLT zastaví procesor. Stojí na místě, neděje se nic, žádné instrukce neprovádí, zkrátka stojí. Stojí a stojí. Nic nedělá. Šmitec, šlus, finito. Z téhle letargie ho probudí buď RESET, nebo právě požadavek na přerušení. Představte si, jak ten procesor zpracovává instrukci HLT, stojí stále na místě, jakoby v nekonečné smyčce, když v tom přijde požadavek na přerušení. Procesor udělá ty kroky, které jsem popsal a provede (třeba) instrukci RST 7. Ta vykoná nějaké operace a vrátí se za instrukci HLT. Program tak může pokračovat. Často se tenhle fígl používá např. k tomu, že si nastavíme časovač (onu &#8222;minutku&#8220;) a procesor pomocí HLT zastavíme. Až doběhne požadovaný časový interval, přijde přerušení, a to nás vyvede ze stavu HALT.

Sledujte: **HLT!**

.

.

_(Jo a taky je dobrý si před tím, než uděláme to HLT, zkontrolovat, jestli jsme nezapomněli povolit přerušení&#8230;)_