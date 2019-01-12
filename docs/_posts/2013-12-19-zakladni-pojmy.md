---
id: 13
title: Základní pojmy
date: 2013-12-19T19:12:53+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=13
permalink: /zakladni-pojmy/
dsq_thread_id:
  - "2063018065"
categories:
  - "6502"
  - "8080"
  - Z80
---
Na úvod bude dobré ujasnit si některé základní pojmy.

<!--more-->

**Mikroprocesor** se skládá z jednoho (nebo více) integrovaných obvodů. Tohle označení se používalo hlavně v 70. a 80. letech 20. století, aby se odlišily nové _mikroprocesory_ (většinou obvody vysoké a velmi vysoké integrace &#8211; LSI, VLSI) od starých _procesorů_, poskládaných z mnoha jednodušších obvodů. S masivním nástupem PC třídy 386 a vyšší se zase začalo používat slovo &#8222;procesor&#8220;. Slovo &#8222;mikroprocesor&#8220; tak zůstává spojeno hlavně s osmibitovou érou.

Mikroprocesor většinou obsahuje nějakou řídicí jednotku, která vykonává program, sadu registrů, v nichž jsou uložena data, se kterými se pracuje, a nějakou jednotku, ve které se provádějí matematické operace.

Ve světě mikroprocesorů nejsou žádné příkazy známé z vyšších programovacích jazyků. PRINT tu nenajdete. Jediné, co procesor umí, je vzít z paměti číslo &#8211; **operační (strojový) kód instrukce** &#8211; a podle něj provést nějakou činnost, třeba přesunout hodnotu odněkud někam nebo sečíst dvě čísla. Jediné, čemu mikroprocesor rozumí, je tedy _číslo_. A protože hovoříme o osmibitových mikroprocesorech, je přirozené, že ta nejčastěji používaná čísla budou právě osmibitová, tedy v rozsahu 0 až 255, hexadecimálně 00h až FFh.

Platí tedy. že každý osmibitový procesor zná přesně 256 instrukcí? Ani náhodou! Některé jich znají mnohem míň (třeba procesor 8080), takže některé operační kódy nemají žádnou přiřazenou instrukci. Jiné naopak mají mnohem víc instrukcí (Z80) a řeší to tím, že některé instrukce mají operační kód vícebajtový.

**Program** se skládá z instrukcí, které procesor vykonává po řadě tak, jak jdou po sobě v paměti, s výjimkou skoků. Program je zapsaný třeba takto:

21h 55h 3Ah

Tyto tři bajty jsou procesorem 8080 interpretovány tak, že do registrů H a L uloží hodnoty 3Ah a 55h. U procesoru Z80 mají tato tři čísla stejný efekt (protože Z80 je s 8080 na úrovni strojového kódu kompatibilní). U procesoru 6502 mají zcela jiný význam &#8211; vezme se obsah registru X, k němu se přičte číslo 55h, výsledek se ořízne na osm bitů a dostaneme adresu. Z paměti na téhle adrese se vezme jeden bajt, z paměti na adrese o jedničku vyšší druhý bajt. Tyto dva bajty dají dohromady 16bitovou adresu v paměti, kam procesor sáhne pro číslo, a nakonec provede operaci AND mezi hodnotou v registru A a tímto číslem (výsledek uloží do registru A). No a pak následuje prázdná instrukce (3Ah).

Různé procesory mají tedy odlišné vnímání stejných kódů, což je důvod, proč nelze vzít program pro jeden z nich a spustit bez úprav na druhém.

**Jazyk symbolických adres** (JSA) slouží k tomu, aby si programátor nemusel pamatovat tyhle číselné kódy (což o to, většinou si je stejně pamatují), a aby u složitějších programů nemusel počítat adresy, tj. na jaké adrese v paměti je která instrukce. Proto zavádí jednak symbolická návěští (takže se zapisuje nikoli JUMP 1234h, ale třeba JUMP START), a jednak zavádí symbolická jména pro instrukce. Takže třeba u procesoru 8080 nemusí programátor zapisovat zmíněný skok jako kód C3h, ale symbolicky: &#8222;JMP&#8220; (jako že _jump_, rozumíme si&#8230;)

**Assembler** je nástroj, který překládá program, zapsaný v těchto symbolických jménech, do konkrétních kódů jednotlivých instrukcí.

Co to znamená, když se řekne &#8222;Program je napsaný v assembleru&#8220;? Ve skutečnosti to, že program je napsaný v jazyku symbolických adres. Zkrátka se stalo, že se tomuhle jazyku začalo říkat &#8222;assembler&#8220;, i když správně je assembler vlastně ten překladač.

A když někdo řekne, že napsal program &#8222;ve strojáku&#8220;? Pravděpodobně ho napsal v tom symbolickém jazyce a přeložil assemblerem do strojového kódu. &#8222;Stroják&#8220; je takové označení, které se taky přeneslo na jazyk symbolických adres. _Ale mohlo se stát, že je dotyčný hardcore programátor a opravdu z hlavy nadiktoval instrukční kódy&#8230;_

Teď jsme si vymezili základní pojmy, co dál? V zásadě se můžeme pustit do popisu procesoru a jeho instrukcí. Můžete se podívat na procesor, který vás zajímá, ale pokud nevíte, jaký vás zajímá, začněte od toho nejstaršího, kterému se budu věnovat, a tím je Intel 8080.