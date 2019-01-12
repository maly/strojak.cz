---
id: 113
title: 'Instrukce 8080 &#8211; práce s periferiemi'
date: 2013-12-25T16:35:11+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=113
permalink: /8080-instrukce-pro-praci-s-periferiemi/
dsq_thread_id:
  - "2072553229"
categories:
  - "8080"
---
Pracovat s pamětí je fajn, ale pro uživatele je paměť neviditelná. Uživatel ocení třeba nějaké to písmeno na displeji, nějaký ten zvuk, chce zmáčknout tlačítko, zakvrdlat joystickem či tak něco&#8230;

<!--more-->

Počítačové periferie jsou &#8222;všechno co není procesor nebo paměť&#8220;. Takže třeba ty klávesnice, výstupní zařízení, magnetofony, reproduktory, &#8230; Ty se nějakým způsobem připojují do systému (jakým přesně, to záleží na tvůrci systému) a procesor s nimi komunikuje.

Některé procesory (6502 například) nerozlišují periferie od paměti. Zkrátka a dobře určité adresy z adresního rozsahu neobsadí paměť, ale periferie. &#8222;Pošli znak 12 sériovou linkou&#8220; je pro ně stejná operace jako &#8222;ulož znak 12 do paměti na konkrétní adresu&#8220;. Má to svoje výhody, ale i nevýhody.

Procesor 8080 (nebo Z80) naproti tomu mají speciální vývody, které říkají: Teď se nepřistupuje do paměti, ale ke vstupně-výstupnímu zařízení, k periferii. Paměť může zůstat neaktivní a adresa čtení nebo zápisu se vztahuje na periferní zařízení (je pro to takové slovo &#8222;port&#8220; &#8211; &#8222;přistupujeme na porty&#8220; třeba). U procesoru 8080 může být až 256 periferních zařízení, protože pro tyto operace používá osmibitovou adresu.

### IN, OUT

Procesor 8080 má dvě instrukce pro práci s periferiemi. IN slouží ke čtení, OUT k zápisu. Obě instrukce mají jeden přímý parametr, a tím je osmibitová adresa periferie. Instrukce IN F8h přečte bajt z periferie na adrese F8h a uloží ho do registru A. OUT 23h vezme obsah registru A a pošle ho na periferii na adresu 23h.

Víc k těmto instrukcím asi nelze říct. Jsou opravdu tak jednoduché, jak vypadají. Konkrétní adresy už jsou na tvůrci toho kterého systému.

Například v počítači PMI-80 je na adresách F8h-FBh připojen obvod 8255, který slouží jako programovatelný obvod pro řízení vstupů a výstupů. Tentýž obvod v PMD-85 sídlí na adresách F4h-F7h (a tam obsluhuje klávesnici, LED a reproduktor), další stejného typu je na adresách F8h-FBh (a tam se stará o načítání dat z ROM modulu), no a na adresách 1Eh a 1Fh je připojený obvod 8251, který pro změnu slouží pro komunikaci s kazetovým magnetofonem.

Ostatně, když už jsem zmínil klávesnici&#8230;

## Klávesnice

Když jsem v minulém dílu psal o tom, že stisk klávesy vyvolá přerušení, tak, po pravdě řečeno, spíš ne&#8230; Totiž, ne že by to nešlo, ne že by to nebyl dobrý nápad, ale v dobách největšího rozmachu osmibitů snad žádný z nich nepoužíval přerušení při stisku klávesy (vzpomínám jen na jednu zvláštnost, a tou byla konstrukce klávesnice z nějakého Amatérského Rádia, která vyvolala přerušení při stisku klávesy). Naprostá většina systémů v té době používala zcela jiný princip.

Klávesy na klávesnici se zapojovaly do matice N x M, kdy každá klávesa sídlila v nějakém průsečíku. Jedno z těchto čísel bylo často 8, aby se líp připojovala k periferním obvodům. Například klávesnice u ZX Spectra byla organizovaná do 8 řádků po 5 sloupcích. Řádky byly připojeny na vstupní port (s klidovým stavem 1), sloupce na výstupní. Když chtěl programátor vědět, jaká klávesa je stisknuta, poslal postupně 0 na jednotlivé vodiče sloupců a pokaždé si přečetl hodnotu řádků. Pokud byly všude 1, znamenalo to, že v tom sloupci není žádná klávesa stisknuta, když našel 0, věděl, že našel nějakou stisknutou klávesu.

U jednodeskových počítačů platilo totéž. Stejně byla zapojena klávesnice PMI-80, PMD-85, IQ-151 a dalších. U Atari 800 třeba taky, ale tam se o tohle testování stisknuté klávesy nestaral procesor, ale specializovaný obvod POKEY. U Commodore C64 byla rovněž matice a specializovaný obvod CIA.

## Displej

Některé osmibitové počítače, zejména jednodeskové, měly jako displej nejrůznější sedmisegmentovky. Ty se většinou připojovaly přes nějaký port. U počítače BOB-85 měly dokonce i latche, tj. &#8222;paměti&#8220; aktuálního stavu; naopak u PMI-80 bylo potřeba postupně vysílat informace o tom, co se má na které sedmisegmentovce zobrazovat. Displej byl připojen podobně jako klávesnice a během čtení jednotlivých sloupců se také rozsvěcely jednotlivé sedmisegmentovky&#8230;

Displeje u &#8222;větších&#8220; počítačů byly řešeny buď jako znakové (IQ-151, SAPI-1), nebo grafické (PMD, Spectrum, &#8230;) &#8211; některé systémy měly inteligentní řadiče, které se dokázaly přepnout do různých módů. Ať tak či onak, většinou se k displeji přistupovalo jako k paměti (přesněji řečeno: řadič displeje si sahal do vyhrazené oblasti v paměti RAM a zobrazoval odtamtud data).

# Konec? Ani náhodou!

V tomto místě jsme skončili s přehledem instrukcí procesoru 8080. Jenže v assembleru víc než v jiném jazyce platí: mýlí se ten, kdo zaměňuje znalost jazyka za znalost jeho příkazů. Takže nebojte, zanedlouho se zase setkáme a budeme pokračovat. Ukážeme si, jak v assembleru řešit nějaké úlohy, a hlavně: máme před sebou ještě minimálně dva procesory!