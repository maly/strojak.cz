---
id: 221
title: '6502 &#8211; čtení kláves a pořádek v kódu'
date: 2014-03-07T12:49:51+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=221
permalink: /6502-cteni-klaves-a-poradek-v-kodu/
dsq_thread_id:
  - "2379887946"
categories:
  - "6502"
  - Obecně
---
Dnes nás čeká druhý krok při oživování počítačů. Už nás pozdravil, tak ještě aby nás poslouchal. A až bude poslouchat, tak si řekneme něco o štábní kultuře.

<!--more-->

Když jsme začali experimentovat se SBC6502, tak jsem [popisoval obvod ACIA](http://strojak.uelectronics.info/6502-ahoj-svete/ "6502 – Ahoj, světe…"). V počítači SBC6502 je k tomuto obvodu připojen terminál. Terminál ale není jen obrazovka, která vypisuje znaky. Je tam i klávesnice, kterou se s počítačem komunikuje.

Klávesnice je připojená úplně stejně jako obrazovka, jen komunikace probíhá opačným směrem, tj. do počítače. Když se podíváte do popisu obvodu ACIA, zjistíte dvě užitečné informace:

  * Přijatý znak si můžete přečíst z datového registru, pokud tedy byl nějaký znak přijatý.
  * O tom, jestli byl nějaký znak už přijatý, informuje bit 0 stavového registru.

Takže teoreticky by mělo stačit kontrolovat bit 0 stavového registru, a pokud bude nastavený, tak to znamená, že přišel nějaký znak z klávesnice. V takovém případě ho můžeme přečíst z datového registru.

Co s ním uděláme? Hmmm&#8230; co ho třeba zase vypsat? To by šlo:

<pre class="lang:asm decode:true">; Nastavení adres pro komunikační obvod ACIA 6850
ACIA        equ   $A000 
ACIACONTROL equ   ACIA+0 
ACIASTATUS  equ   ACIA+0 
ACIADATA    equ   ACIA+1 

                  ; program začíná na adrese C000h, tedy tam, kde začíná ROM
          .ORG    $C000 
                  ; Emulátor má začít odsud
          .ENT    $ 
                  ; K testu použij emulátor počítače SBC6502 - pouze pro IDE ASM80.com
          .ENGINE sbc6502 
                  ; Vstupní adresa
RESET:            
                  ; Nastavíme si ukazatel zásobníku
          LDX     #$FF 
          TXS     
                  ; Nastavení řídicího registru ACIA
          LDA     #$15 
          STA     ACIAControl 

                  ; Začínáme vypisovat znaky, Y je ukazatel
          LDY     #0 
LOOP:             
          LDA     Message,Y ; Načti znak ze zprávy na pozici Y
          BEQ     key ; Jestli je to 0, tak hop!
          JSR     SEROUT ; Jinak zavolej podprogram pro vyslání znaku
          INY     ; Y++ - abychom adresovali další bajt
          BNE     LOOP ; a jestli toho ještě nebylo dost, tak hop na začátek
KEY:      LDA     ACIAStatus ; Přišel nějaký znak?
          AND     #1 ; Bit 0 nám to řekne
          BEQ     KEY ; Nepřišel? Tak to zkusíme znovu, dokud nějaký nepřijde
          LDA     ACIAData 
          JSR     serout 

          JMP     KEY ; TO JE KONEC!!! 🙁

MESSAGE:          
          DB      $0C,"My hovercraft is full of eels!",$0D,$0A,$00 

                  ; podprogram pro vyslání hodnoty z registru A
                  ; přes sériový obvod 6850 na terminál
SEROUT:   PHA     ; Uschováme hodnotu, protože registr A potřebujeme
SO_WAIT:  LDA     ACIAStatus ; Je volno?
          AND     #2 ; Bit 1 nám to řekne
          BEQ     SO_WAIT ; Není? Tak to zkusíme znovu, dokud nebude
          PLA     ; Už je, takže si vrátíme zpět hodnotu z registru A
          STA     ACIAData ; a pošleme ji do 6850
          RTS     ; už není co na práci, tak se můžeme vrátit

                  ; Nastavíme vektory, které 6502 potřebuje, aby věděl, kam
                  ; má po resetu systému skočit.
          .ORG    $FFFC 
          DW      reset 
          DW      reset</pre>

Použil jsem první příklad s vypisováním znaků a dopsal právě kontrolu klávesnice a výpis znaků. Zkuste si tenhle příklad přeložit v [ASM80.com](http://www.asm80.com) a spustit v emulátoru SBC6502. Program vypíše hlášku a po ní čeká na stisknutí klávesy. Jakmile je nějaká stisknuta, pošle terminál její ASCII kód po sériovém rozhraní do počítače, tam jej zpracuje procesor a vypíše zpátky.

SBC6502 nepoužívá přerušení, které by systém upozornilo na to, že přišel znak a je možno ho zpracovat. Proto procesor musí pravidelně kontrolovat, jestli se už něco neděje, a během té kontroly nedělá nic jiného. V nejjednodušších single task systémech je to přijatelné řešení.

Složitější systémy mohou použít přerušení, buď od obvodů klávesnice, které signalizují, že přišel znak, nebo třeba přerušení od časovače (ZX Spectrum takhle testovalo klávesnici každou padesátinu sekundy při systémovém přerušení). V obsluze přerušení pak načtou znak a uloží ho do nějakého bufferu k dalšímu zpracování.

## Organizace kódu

Protože vy, čtenáři, snad všichni znáte nějaké vyšší jazyky, tak nemusím moc složitě představovat koncepty modulů a lokálních proměnných. Ano, i tyhle věci v assembleru máme, ale není to tak úplně prosté&#8230;

### Moduly

No, říkejme tomu tak. Ve skutečnosti se jedná jen o jednoduchý INCLUDE &#8222;jméno&#8220;, který na to místo načte obsah externího souboru.

> Některé staré assemblery vůbec žádný include neměly. Ono by to třeba u ZX Spectra 48 s páskou nebylo moc pohodlné. Čímž neříkám, že takové kompilery nebyly, třeba HiSoft C měl #include, jak se na céčko sluší a patří, a při překladu jste spustili magnetofon, kde byly soubory ke slinkování, překladač si je prošel, načetl, přeložil&#8230; Ano, bylo to tak děsivé, jak to zní.

Většina modernějších assemblerů include samozřejmě má, jen se liší jeho syntax. Některé assemblery používají .INC, některé INCLUDE, některé .INCLUDE, takže nezbývá než si přečíst manuál k tomu kterému kousku. Já ve svém překladači používám tvar _.INCLUDE název souboru_.

Řekněme, že mi připadá jako dobrý nápad (a on to dobrý nápad je) přesunout tyhle rutiny pro výpis znaku a načtení znaku někam stranou, do nějaké společné (common) knihovny (library), kterou si důvtipně nazvu &#8222;comlib.a65&#8220;. V hlavním programu tak budu moci vesele tyhle rutiny používat, aniž by mi překážely ve zdrojáku, stačí jen, když je vhodně includuju.

Tím se nám zdroják rozštípnul na dva soubory: comlib.a65 (knihovna) a vlastní zdrojový kód.

<pre class="lang:default decode:true">; COMLIB.A65 - základní komunikační knihovny

; Nastavení adres pro komunikační obvod ACIA 6850
ACIA equ       $A000 
ACIACONTROL equ     ACIA+0 
ACIASTATUS equ    ACIA+0 
ACIADATA  equ     ACIA+1 

ACIAINIT:      ; Nastavení řídicího registru ACIA
          LDA     #$15 
          STA     ACIAControl 
          RTS

                  ; podprogram pro vyslání hodnoty z registru A
                  ; přes sériový obvod 6850 na terminál
SEROUT:   PHA     ; Uschováme hodnotu, protože registr A potřebujeme
SO_WAIT:  LDA     ACIAStatus ; Je volno?
          AND     #2 ; Bit 1 nám to řekne
          BEQ     SO_WAIT ; Není? Tak to zkusíme znovu, dokud nebude
          PLA     ; Už je, takže si vrátíme zpět hodnotu z registru A
          STA     ACIAData ; a pošleme ji do 6850
          RTS     ; už není co na práci, tak se můžeme vrátit

                  ; podprogram pro načtení stisknuté klávesy do registru A
                  ; přes sériový obvod 6850
                  ; Podprogram čeká na stisk klávesy!
SERIN:    LDA     ACIAStatus ; Je klávesa?
          AND     #1 ; Bit 0 nám to řekne
          BEQ     SERIN ; Není? Tak to zkusíme znovu, dokud nebude
          LDA     ACIAData ; a přečteme z 6850
          RTS     ; máme hotovo, tak se můžeme vrátit</pre>

Tenhle soubor pak elegantně načteme v hlavním programu:

<pre class="lang:default decode:true">; program začíná na adrese C000h, tedy tam, kde začíná ROM
          .ORG    $C000 
                  ; Emulátor má začít odsud
          .ENT    $ 
                  ; K testu použij emulátor počítače SBC6502 - pouze pro IDE ASM80.com
          .ENGINE sbc6502 
                  ; Vstupní adresa
RESET:            
                  ; Nastavíme si ukazatel zásobníku
          LDX     #$ff 
          TXS     

	  JSR ACIAINIT

LOOP:     JSR SERIN
	  JSR SEROUT

          JMP     LOOP ; Stále dokola...

; ještě někam musíme vložit tu knihovnu...
; třeba sem, sem se hlavní program nedostane
.include comlib.a65

			.ORG    $FFFC 
          DW      reset 
          DW      reset</pre>

Do comlib.a65 si klidně můžeme přihodit i [rutinu PRIMM z minulé lekce](http://strojak.uelectronics.info/6502-etevs-joha/ "6502 – !etěvs ,johA"). K tomu ale až na konci. Teď si musíme ukázat ještě jednu důležitou vlastnost assemblerů&#8230;

Assembler totiž sám o sobě nemá lokální jména. Jakmile jednou nadefinujete konstantu, návěští, něco, tak to je vidět v celém kódu. Což je docela problém, protože u složitějšího programu vám brzy dojde fantazie při pojmenovávání např. smyček. &#8222;LOOP1&#8220;, &#8222;LOOP2&#8220;, &#8230; to není moc elegantní.

Nemluvě o tom, že třeba použijete návěští &#8222;LOOP&#8220; v nějaké knihovní funkci. V hlavním programu na to zapomenete (nebo o tom ani nevíte, protože tu knihovnu dělal někdo jiný), a překladač &#8211; logicky &#8211; zařve, že návěští bylo už použité. Co s tím?

### Lokální návěští

V téhle situaci se hodí lokální návěští. Špatná zpráva je, že ne každý assembler je podporuje, a pokud ano, tak má svou konvenci, které bude pravděpodobně odlišná od všech ostatních konvencí všech ostatních assemblerů.

Některé mocnější assemblery zavádějí pseudokonstrukce &#8222;procedure&#8220; a deklaraci &#8222;local&#8220; apod., jiné se staví k problému z druhé strany a dovolují pro drobné smyčky a návěští používat jakási &#8222;pseudonávěští&#8220; a odkazovat se na ně zápisem &#8222;skoč na předchozí pseudonávěští&#8220;, &#8222;skoč na následující pseudonávěští&#8220;, &#8222;skoč o dvě pseudonávěští zpátky&#8220;&#8230;

Já jsem v ASM80 zvolil cestu bloků. Blok začíná direktivou &#8222;.block&#8220; a končí direktivou &#8222;.endblock&#8220;. Všechna návěští, co jsou v něm definována, jsou lokální. To znamená že v bloku na ně můžete odkazovat, mimo něj nejsou vidět. Pokud chcete, aby bylo návěští vidět i mimo blok, dejte před jeho název znak @ &#8211; ten se nestane součástí jména, jen říká, že toto návěští bude globální. &#8222;@SEROUT:&#8220; říká &#8222;Definuj globální návěští se jménem SEROUT&#8220;.

Díky tomu můžu jako první řádek knihovny comlib napsat .block, na poslední .endblock, a vím, že pokud takovou knihovnu includuju, tak mi z ní nic &#8222;nevyteče&#8220; ven, pokud explicitně neřeknu, co má být vidět zvenčí. Takže nová, _šetrná_ verze comlib vypadá takto:

<pre class="lang:default decode:true">; COMLIB.A65 - základní komunikační knihovny

.block
; díky deklaraci BLOCK nebudou následující návěští vidět 
; ve zbytku kódu, kromě těch, před kterými je @

; Nastavení adres pro komunikační obvod ACIA 6850
ACIA equ       $A000 
ACIACONTROL equ     ACIA+0 
ACIASTATUS equ    ACIA+0 
ACIADATA  equ     ACIA+1 

@ACIAINIT:      ; Nastavení řídicího registru ACIA
          LDA     #$15 
          STA     ACIAControl 
          RTS

                  ; podprogram pro vyslání hodnoty z registru A
                  ; přes sériový obvod 6850 na terminál
@SEROUT:   PHA     ; Uschováme hodnotu, protože registr A potřebujeme
SO_WAIT:  LDA     ACIAStatus ; Je volno?
          AND     #2 ; Bit 1 nám to řekne
          BEQ     SO_WAIT ; Není? Tak to zkusíme znovu, dokud nebude
          PLA     ; Už je, takže si vrátíme zpět hodnotu z registru A
          STA     ACIAData ; a pošleme ji do 6850
          RTS     ; už není co na práci, tak se můžeme vrátit

                  ; podprogram pro načtení stisknuté klávesy do registru A
                  ; přes sériový obvod 6850
                  ; Podprogram čeká na stisk klávesy!
@SERIN:    LDA     ACIAStatus ; Je klávesa?
          AND     #1 ; Bit 0 nám to řekne
          BEQ     SERIN ; Není? Tak to zkusíme znovu, dokud nebude
          LDA     ACIAData ; a přečteme z 6850
          RTS     ; máme hotovo, tak se můžeme vrátit
.endblock</pre>

Díky uzavření, &#8222;zapouzdření&#8220; zdrojáku můžu v hlavním programu použít klidně návěští SO_WAIT, a nedojde k chybě, protože to, které jsem použil v comlib.a65, bude vidět pouze v comlib.a65, nikde jinde. Stejně tak názvy jako ACIAData, ACIAStatus apod. Jediné, co bude vidět zvenčí, je SERIN, SEROUT a ACIAINIT.

## Vylepšený PRIMM

[Minule jsem tu ukazoval funkci PRIMM](http://strojak.uelectronics.info/6502-etevs-joha/ "6502 – !etěvs ,johA"), která pomocí jednoduchého triku dokáže vytisknout konstantní řetězec znaků, zapsaný přímo v kódu, přímo za voláním JSR PRIMM. Kód funguje většinou dobře, ale jsou dva stavy, ve kterých fatálně selže.

První případ je, že je počet znaků větší než 255. Registr Y u posledního znaku &#8222;přeteče&#8220; do nuly, rutina tím končí, ale bohužel se tím návratová adresa ocitne někde uprostřed textu a výsledek bude katastrofální. Řešení existuje &#8211; nechat přetočit Y, ale přitom si uloženou adresu zvýšit o 0100h, jak [navrhnul v komentářích Roman Bórik](http://strojak.cz/6502-etevs-joha/#comment-1265536202).

Druhý problém nastane v případě, že ukazatel zásobníku SP je nízko. V takovém případě přeteče přes nulu, dostane se opět do vysokých hodnot FDh-FFh, a v takovém případě jednoduchý přepočet pomocí vzorce &#8222;SP + 104h&#8220; selže, protože se ocitneme mimo zásobník, na adresách 0200h a vyšších, a nejen že načteme nesmysly, ale taky nesmysly uložíme. I tuto situaci by bylo možné ošetřit, ale v tomto případě to není asi úplně potřeba, pokud inicializujeme zásobník standardně, tj. na hodnotu FFh. Pokud se totiž za takové situace stane, že se zásobník protočí přes nulu, tak máme zásadnější problém někde jinde&#8230;