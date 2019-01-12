---
id: 128
title: Trocha assemblerové teorie
date: 2014-01-01T03:25:39+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=128
permalink: /trocha-assemblerove-teorie/
dsq_thread_id:
  - "2084618810"
categories:
  - "6502"
  - "8080"
  - Assembler
  - Obecně
  - Z80
---
Tuhle jsem do rozhovoru pro programátorský magazín (ve skutečnosti [Zdroják](http://www.zdrojak.cz/)) říkal: _[ASM80](http://www.asm80.com) je dvouprůchodový assembler_. Prý to mám vysvětlit, co to znamená&#8230;

<!--more-->

V dobách osmibitů byl &#8222;dvouprůchodový assembler&#8220; de facto standard. Ale co to znamená? Musíme si říct, jak vlastně překladač funguje.

Bere řádek po řádku a kouká se, jestli tam je instrukce nebo [pseudoinstrukce](http://strojak.uelectronics.info/pseudoinstrukce/ "Pseudoinstrukce"). Pokud ano, začlení si její kód do připravovaného výsledného útvaru, popř. provede to, co pseudoinstrukce nařizuje. Tím, že už v téhle fázi připravuje kód, tak vlastně ví, jak dlouhá která instrukce bude. Takže si může průběžně vytvářet i tabulku adres, kde má ke každému symbolickému jménu uloženou jeho adresu (nebo hodnotu). To je první průchod.

Ve druhém průchodu tohle všechno už má připravené, takže jede znovu, a jen na místech, kde je potřeba vyčíslit nějakou konkrétní hodnotu s odkazem, spočítá jeho hodnotu a doplní do kódu.

Takhle tedy funguje [ASM80](http://www.asm80.com). Fungoval stejně i GENS (např.) Něco podobného (podobného!) dělal i Prométheus, ale ten, nakolik jsem pochopil, dělal část prvního průchodu už při editaci textu, kdy si jednotlivé instrukce &#8222;předpřekládal&#8220;.

Co jiné počty průchodů? Tříprůchodový? Jasně, existují. Dokážou třeba vyřešit problém několikanásobné dopředné reference, navíc by mohly trošku optimalizovat (plné skoky nahradit relativním, dlouhé varianty instrukcí těmi se zero page u 6502 a podobně).

Šel by napsat jednoprůchodový assembler? No jasně, šel &#8211; představte si první průchod, při něm to vyhodnocování výrazů&#8230; Když narazí na adresu, kterou už zná, tak ji použije, když ji ale ještě nezná, zapíše si do tabulky symbolických názvů tenhle název s poznámkou: &#8222;Až na tohle návěští narazím, doplním ho tam a tam&#8220;.

Počet průchodů tedy dokáže ovlivnit některé aspekty chování překladače (dopředné reference by jednoprůchodový zvládnul).

A kolikaprůchodové se používají dneska?

Turbo assembler, poslední, který jsem na PC platformě používal, je &#8222;multiprůchodový&#8220;. Ale vzhledem k jeho komplexnosti to ani jinak nejde. Ten hlavní rozdíl proti &#8222;starým pákám&#8220; je v tom, že assembler dneska (ani včera) nepřipraví hotový binární kód pro spuštění. Sestaví něco, čemu se říká &#8222;objektkód&#8220;, což je v podstatě výsledek posledního průchodu, ale nevadí mu, když nějaké adresy nerozpoznal. Pravděpodobně patří nějaké knihovní rutině. Objektkód pak dostane do spárů program, kterému se říká &#8222;linker&#8220;, a ten spojí vše potřebné &#8211; všechny části kódu, knihovní rutiny, data, vše. Teprve když tady nějaký symbol chybí, tak je zle. Překladač oddělený od linkeru má tu výhodu, že program může být napsaný v různých jazycích, v jednom třeba výpočty, v druhém UI, knihovny ve třetím &#8211; a linker to poslepuje dohromady. Toto rozdělení samozřejmě není nijaká novinka. Není to dokonce ani výmysl šestnáctibitového světa. I osmibity měly překladače a linkery zvlášť, např. u CP/M. Tohle rozdělení funguje tam, kde není problém pracovat se soubory. U osmibitového domácího počítače s kazeťákem by to moc smysl nedávalo.