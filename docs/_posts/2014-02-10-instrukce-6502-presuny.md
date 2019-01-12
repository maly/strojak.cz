---
id: 164
title: 'Instrukce 6502 &#8211; přesuny'
date: 2014-02-10T14:18:33+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=164
permalink: /instrukce-6502-presuny/
dsq_thread_id:
  - "2246800766"
categories:
  - "6502"
---
Instrukční soubor 6502 začneme probírat od instrukcí, které přesouvají data.

<!--more-->

Už jsem zmiňoval, že na rozdíl od 8080, kde se liší mnemotechnický zápis instrukcí pro přesuny mezi registry od instrukcí pro přesun z/do paměti (a tam se navíc rozlišuje, zda je adresa uložená v registrech, nebo zapsaná přímo), vystačí si 6502 s několika málo instrukcemi, které přesunou data z/do registru, a to, odkud (nebo kam) se přesouvá, je určeno [adresním módem](http://strojak.uelectronics.info/adresni-mody-6502/ "Adresní módy 6502"). Základních instrukcí je šest: LDA, LDX, LDY, STA, STX, STY. Trojice LD\* (LOAD) přesouvá do registrů (A, X, resp. Y), instrukce ST\* (STORE) naopak data z registrů ukládá.

### LDA

přesune operand do registru A. Na této instrukci jsme si [minule ukazovali adresní módy](http://strojak.uelectronics.info/adresni-mody-6502/ "Adresní módy 6502"), její funkce je tedy jasná. Můžeme ji použít s následujícími módy:

  * imm: LDA #nn &#8211; přesune do registru A přímo hodnotu nn (jednobajtové číslo)
  * zp: LDA nn (nebo LDA *nn) &#8211; přesune do registru A hodnotu na adrese 00nn
  * zpx: LDA nn,X (nebo LDA *nn,X) &#8211; dtto jako předchozí, ale k nn je přičten obsah registru X
  * abs: LDA nnnn &#8211; přesune do registru A hodnotu na adrese nnnn (adresa je 16bitové číslo)
  * abx: LDA nnnn,X &#8211; dtto jako předchozí, ale k adrese se přičítá obsah registru X
  * aby: LDA nnnn,Y &#8211; dtto jako předchozí, ale k adrese se přičítá obsah registru Y
  * izx: LDA (nn,X) &#8211; nepřímé adresování operandu, viz [adresní módy](http://strojak.uelectronics.info/adresni-mody-6502/ "Adresní módy 6502")
  * izy: LDA (nn),Y &#8211; nepřímé adresování operandu, viz [adresní módy](http://strojak.uelectronics.info/adresni-mody-6502/ "Adresní módy 6502")

Všimněte si, že nelze použít mód zpy (tedy zero page s indexací přes Y). Zde je malá ukázka chování těchto instrukcí (pokud jste se ještě s embedovanou verzí assembleru ASM80 nesetkali, tak vězte, že kód kliknutím na COMPILE přeložíte, kliknutím na EMULATOR se otevře emulační okno, kde je možné kód procházet a sledovat, jak se mění obsah paměti či hodnoty v registrech)



(V kódu jsem použil instrukci LDX #2, která &#8211; analogicky &#8211; naplní registr X hodnotou 2)

Jeden zajímavý detail, který může člověka, co přechází ze světa procesorů 8080, zarazit, splést a škaredě překvapit: instrukce LDA (i LDX, LDY a další) mění příznaky Z a N, tedy pokud je přenášená hodnota nulová, nastaví Z, pokud je záporná (=nejvyšší bit je 1), nastaví příznak N. O příznacích budeme teprve mluvit, ale tato zvláštnost, tj. že příznaky ovlivní i některé instrukce pro přenos dat, je natolik důležitá, že ji zmiňuju už teď.

### LDX

Analogicky k LDA pracuje tato instrukce s registrem X. Oproti LDA můžete použít jen následující adresní módy:

  * imm: LDX #nn &#8211; přesune do registru X přímo hodnotu nn (jednobajtové číslo)
  * zp: LDX nn (nebo LDX *nn) &#8211; přesune do registru X hodnotu na adrese 00nn
  * zpy: LDX nn,Y (nebo LDX *nn,Y) &#8211; dtto jako předchozí, ale k nn je přičten obsah registru Y. _POZOR &#8211; nelze použít mód zpx!_
  * abs: LDX nnnn &#8211; přesune do registru X hodnotu na adrese nnnn (adresa je 16bitové číslo)
  * aby: LDX nnnn,Y &#8211; dtto jako předchozí, ale k adrese se přičítá obsah registru Y. _POZOR &#8211; nelze použít mód abx!_

### LDY

Instrukce je obdobná předchozí, ale u indexovaného přístupu zase nedokáže použít obsah registru Y, tj. naopak umožňuje pouze zpx a abx.

  * imm: LDY #nn &#8211; přesune do registru X přímo hodnotu nn (jednobajtové číslo)
  * zp: LDY nn (nebo LDY *nn) &#8211; přesune do registru X hodnotu na adrese 00nn
  * zpx: LDY nn,X (nebo LDY *nn,X) &#8211; dtto jako předchozí, ale k nn je přičten obsah registru X. _POZOR &#8211; nelze použít mód zpy!_
  * abs: LDY nnnn &#8211; přesune do registru Y hodnotu na adrese nnnn (adresa je 16bitové číslo)
  * abx: LDY nnnn,X &#8211; dtto jako předchozí, ale k adrese se přičítá obsah registru X. _POZOR &#8211; nelze použít mód aby!_

### STA, STX, STY

Ukládací instrukce, které přenášejí data v opačném směru než jejich LD\* obdoby. Dovolují stejné adresní módy jako odpovídající LD instrukce, s jednou výjimkou: nelze použít mód imm. Dává to smysl, protože nelze uložit hodnotu z registru do konstanty. Instrukce STX a STY navíc neumožňují použít mód &#8222;absolutní indexovaný&#8220; (abx,aby). Instrukce ST\* navíc nemění stav příznaků. Pro úplnost jen doplním seznam možných adresovacích módů:

  * STA: zp, zpx, abs, abx, aby, izx, izy
  * STX: zp, zpy, abs
  * STY: zp, zpx, abs

## Přesuny

Tím jsme si prošli šest základních instrukcí pro přesun mezi registry a pamětí. Ve světě 8080 by jim zhruba odpovídaly instrukce MVI, MOV r,M, MOV M,r. Pro přesun mezi jednotlivými registry je k dispozici série instrukcí T** (Transfer)

### TAX, TXA, TAY, TYA

Čtveřice instrukcí, která kopíruje hodnotu mezi akumulátorem a registry X,Y. Adresní mód je implicitní, tj. instrukce sama ví, odkud se má kam co přesouvat a nepotřebuje žádné další informace. TAX přesouvá hodnotu z registru A do X, TAY analogicky do registru Y, TXA přesouvá z registru X do registru A, TYA z registru Y do registru A. Na přímé přesuny mezi registry X a Y instrukce nejsou. Všechny čtyři navíc, podobně jako LD*, ovlivňují příznaky N a Z.

### TSX, TXS

TSX vezme hodnotu registru SP (ukazatel zásobníku) a zkopíruje ji do registru X. Přitom nastaví příznaky N a Z podle přenášené hodnoty. TXS naopak přesune hodnotu z registru X do registru S a příznaky nemění.

## Zásobník

Už jsem [zmiňoval](http://strojak.uelectronics.info/architektura-6502/ "Architektura 6502"), že procesor 6502 má ukazatel [zásobníku](http://strojak.uelectronics.info/8080-priznaky-a-zasobnik/ "8080 – příznaky a zásobník") (SP) pouze osmibitový. Adresa v paměti je napevno v první stránce, tedy na adresách 0100h &#8211; 01FFh. Zásobník stejně jako u 8080 roste směrem k nižším adresám. Pokud je ukazatel roven 0 a vy uložíte další hodnotu, zapíše se na adresu 0100h a SP se sníží o 1, tedy na FFh. Což znamená, že další ukládání přepíše hodnotu na adrese 01FFh!

### PHA, PLA

PUSH A, resp. POP A &#8211; PHA uloží hodnotu z registru A do zásobníku, tj. na adresu (0100h+SP) a sníží hodnotu SP o 1. PLA funguje analogicky v opačném směru, tj. zvýší hodnotu SP o 1 a do registru A uloží obsah z adresy (0100h+SP). Navíc nastaví příznaky N a Z.

### PHP, PLP

Obdoba předchozích dvou instrukcí, ale nepracuje se s hodnotou registru A, ale s registrem P (příznakový registr). PHP uloží na zásobník obsah příznakového registru, PLP naopak ze zásobníku takovou hodnotu přečte (a, logicky, změní hodnoty všech příznaků).

## Shrnutí

Probrali jsme instrukce, které u procesoru 6502 přenášejí data. Na jednu stranu mají poměrně bohaté možnosti, na druhou stranu &#8222;ne všechno lze použít se vším&#8220; (LDX například dokáže použít absolutní adresu s indexem Y, STX ne), adresní mód zpy funguje jen u instrukcí LDX, STX (ano, jen u těchto dvou, u žádných jiných se s tímto módem už nesetkáme)&#8230; Navíc je potřeba mít na paměti, že instrukce, které přenášejí hodnotu do registru A, X, Y, taky nastavují příznaky N a Z. No a v neposlední řadě dostává ortogonalita na zadek u instrukcí přesunů &#8211; hodnotu z registru X do registru Y nepřesunete napřímo, do zásobníku můžete uložit jen registr A (a příznak), pokud chcete uložit X, Y, musíte přes registr A, a pokud chcete nastavit hodnotu ukazatele zásobníku, musíte k tomu zase využít registr X, nemůžete použít A (tady bych si tipnul historický vliv předchůdce 6800, kde SP a X byby oba šestnáctibitové).