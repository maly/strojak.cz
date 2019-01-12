---
id: 20
title: Základy assembleru
date: 2013-12-20T16:59:05+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=20
permalink: /zaklady-assembleru/
dsq_thread_id:
  - "2063346554"
categories:
  - "6502"
  - "8080"
  - Z80
---
Ano, já vím, terminologicky to [není správné označení](http://strojak.cz/zakladni-pojmy/), ale _mí čtenáři mu rozumí_. Půjde o **Jazyk symbolických adres**, všeobecně (a nesprávně) nazývaný _assembler_.

<!--more-->

Procesor, jak jsme si už řekli, zpracovává instrukce tak, že čte z paměti jejich strojový kód po bajtech, ty dekóduje a podle nich provádí požadované činnosti.Zápis programů v číselných hodnotách by byl velmi nepohodlný, proto se používá zápis pomocí mnemotechnických názvů instrukcí. Takovému zápisu se říká &#8222;jazyk symbolických adres&#8220;, nebo (nesprávně, ale všeobecně srozumitelně) &#8222;zápis v assembleru&#8220; (anglicky _assembly language_).

> [Definice JSA na Wikipedii.](http://cs.wikipedia.org/wiki/Jazyk_symbolick%C3%BDch_adres)

Program se v JSA zapisuje jako v jiných jazycích do textového souboru po řádcích. Řádky mají přesně definovaný formát a skládají se z následujících prvků:

  1. **Návěští: **řetězec znaků [A-Z, 0-9, podtržítko], který nezačíná číslicí. Některé překladače rozlišují jen prvních 6 znaků, jiné prvních 8, další pak všechny. Návěští je zakončeno dvojtečkou.
  2. **Instrukce**: mnemotechnická zkratka instrukce, popřípadě pseudoinstrukce (direktivy)
  3. **Parametry**: Některé instrukce a direktivy vyžadují další údaje, se kterými musí instrukce pracovat. Zapisují se za mnemotechnické označení instrukce, od které se oddělí mezerou (nebo mezerami). Pokud je parametrů víc, oddělují se od sebe čárkami.
  4. **Poznámka:** Cokoli, co si potřebujete poznamenat. Na začátku je znak středník (;), vše, co je za tímto znakem, překladač ignoruje.

Ukažme si různé typy řádků.

  * **Prázdný řádek** překladač prostě ignoruje
  * **Řádek, kde je jen poznámka**. Pokud je prvním znakem na řádku (mezery nepočítáme) znak středník, bere se zbytek řádku jako poznámka a překladač jej ignoruje.
  * **Řádek s instrukcí:** Na řádku je uvedena existující (pseudo)instrukce, popřípadě její parametry, má-li nějaké. Překladač ji přeloží na odpovídající operační kód. Za parametry může být ještě poznámka, kterou překladač ignoruje
  * **Řádek s návěštím**: Libovolný typ výše uvedených řádků může ještě začínat návěštím, tj. řetězcem ukončeným dvojtečkou. V takovém případě překladač přiřadí tomuto řetězci adresu, která odpovídá internímu počítadlu adres. (K tomu se ještě vrátím.)

Podívejme se na ukázku kódu:

<pre class="lang:asm decode:true">; Program zacina
        .org 0100H    ; nastavime pocatek kodu

ZACATEK:
        nop
        nop
        nop
NAVEST: nop
        nop</pre>

V tomto kódu najdete řádek s komentářem (1), prázdný řádek (3), řádky s instrukcemi i řádky s návěštím.

Použil jsem jen jednu instrukci, totiž instrukci NOP, kterou znají všechny tři procesory a její význam je prostý: Nedělej nic. Možná se vám zdá zbytečné mít instrukci, která nedělá nic, ale ona se docela dobře hodí &#8211; když potřebujete někde &#8222;chvilku počkat&#8220;, nebo když si chcete někde nechat místo pro budoucí změnu. U procesorů 8080 / Z80 je její kód 0, u procesoru 6502 je to 0EAh.

Instrukce &#8222;.org&#8220; není instrukcí v pravém slova smyslu. Nemá totiž žádný operační kód. Pouze říká překladači: Teď se chovej tak a tak. Patří tedy mezi pseudoinstrukce (nebo taky &#8222;direktivy&#8220;). Org je zkratka pro &#8222;origin&#8220;, tedy počátek. Tady říká překladači: _Ber to tak, že tady, jak jsem já, bude nějaká konkrétní adresa, a následující instrukce tedy ukládej od té adresy dál._

Pseudoinstrukce .org má jeden parametr &#8211; totiž tu adresu. Tady je zapsaná v hexadecimální podobě.

### Jak lze zapsat konstanty v assembleru?

  * Hexadecimální zápis 
      * Pomocí číslic 0-9 a znaků A-F. Číslo začíná vždycky číslicí a je ukončeno znakem H (h). Takže: 1234h, 00AAH, 23beh, 0cfh, 18H, &#8230;
      * Pomocí číslic 0-9 a znaků A-F, před kterým je znak $. Takže: $1234, $00AA, $23be, $cf, $18, &#8230;
      * Některé překladače umožňují i zápis v &#8222;céčkové&#8220; syntaxi &#8222;0x&#8230;&#8220;
  * Binární zápis 
      * Řetězec znaků 0,1 ukončený písmenem &#8222;b&#8220;
      * Řetězec znaků 0,1, před kterým je znak &#8222;%&#8220;
  * Desítkové číslo 
      * Zapsáno tak, jak jsme zvyklí.
  * Znaková konstanta 
      * Znak zapsaný v apostrofech nebo uvozovkách. Hodnotou je ASCII kód daného znaku. Např. &#8222;@&#8220; = 64 = 40h

Tady je na místě připomenout, že konkrétní syntaxe jednotlivých překladačů se může lišit. V dalším popisu budu vycházet z tvaru, jaký jsem implementoval v překladači ASM80. Totéž platí i pro pseudoinstrukce &#8211; někde je správný zápis &#8222;org&#8220;, u jiného překladače je to důsledně &#8222;.org&#8220; s tečkou. ASM80 povoluje oba tvary. Původní assemblery braly zápis bez tečky.

### Listing

Když si vezmete výše zapsaný kód a zkusíte ho přeložit překladačem &#8211; např. výše zmíněným ASM80 &#8211; získáte na výstupu jednak binární podobu (která nebude moc zajímavá), jednak takzvaný _listing_, což je výpis programu spolu s adresami a instrukčními kódy.

<pre class="lang:default decode:true">0000                          ; Program zacina   
0100                          .ORG   0100H   ; nastavime pocatek kodu   
0100                ZACATEK:   
0100   00                     NOP   
0101   00                     NOP   
0102   00                     NOP   
0103   00           NAVEST:   NOP   
0104   00                     NOP   

_PC         0104
ZACATEK     0100
NAVEST      0103</pre>

Každý řádek začíná adresou, na jaké je daná instrukce uložena. Vidíte, že na prvním řádku ještě překladač neví, kde chceme program mít, a tak počítá s tím, že bude uložený od adresy 0. Na druhém řádku pseudoinstrukcí .org říkáme, že program bude v paměti uložen od adresy 0100h. Překladač si proto nastaví interní počítadlo adres (_PC) na hodnotu 0100h. Třetí řádek je &#8222;prázdný řádek s návěštím&#8220;. To znamená, že do tabulky návěští je zařazeno návěští se jménem ZACATEK a je mu přiřazena hodnota interního počítadla adres, tedy 0100h.

Čtvrtý řádek obsahuje konečně nějakou reálnou instrukci. Je to instrukce NOP, je přeložena na operační kód (00) a ten bude uložen na adrese 0100h. Počítadlo se posune na následující adresu, tedy 0101h.

Pátý řádek: NOP, kód je zase 00, je uložen na adrese 0101h a počítadlo se posouvá&#8230;

Šestý řádek: NOP, kód je zase 00, je uložen na adrese 0102h a počítadlo se posouvá&#8230;

Na sedmém řádku je návěští NAVEST. Je mu tedy přiřazena hodnota interního počítadla adres (0103h). Na tomtéž řádku je i instrukce. Její operační kód je uložen na tuto adresu a pokračujeme dál&#8230;

Na samotném konci listingu vidíte výpis všech návěští. _PC je interní počítadlo adres (které skončilo na hodnotě 0104h), no a návěští ZACATEK a NAVEST mají ty hodnoty, jaké jsme si popsali výše.

Základy jsou tedy jasné, pojďme na skutečné programování v assembleru!