---
id: 186
title: 'Instrukce 6502 &#8211; aritmetika'
date: 2014-02-17T16:13:59+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=186
permalink: /instrukce-6502-aritmetika/
dsq_thread_id:
  - "2279521683"
categories:
  - "6502"
---
V tomto díle konečně donutíme procesor 6502 něco spočítat.

<!--more-->

Když jsem v minulých dílech naznačoval, že to s ortogonalitou instrukční sady procesoru 6502 není, ani přes velké množství adresních módů, moc slavné, tak věřte, že jsem si to nejhorší šetřil až na závěr.

### INC, DEC

Nejjednodušší aritmetické instrukce jsou inkrement a dekrement, tedy přičtení jedničky a odečtení jedničky. 6502 má k tomu účelu instrukce INC a DEC. Tyto instrukce zvýší (INC) nebo sníží (DEC) obsah paměťové buňky o 1. Můžete je použít s následujícími adresními módy:

  * abs: INC 1234h &#8211; zvýší obsah buňky na adrese 1234h o 1
  * zp: INC 12h &#8211; zvýší obsah buňky na adrese 0012h o 1
  * abx: INC 1234h,X &#8211; zvýší obsah buňky na adrese (1234h + X) o 1
  * zpx: INC 12h,X &#8211; zvýší obsah buňky v nulté stránce paměti na adrese (12h+X) o 1 (nezapomeňte, že nultá stránka má vždycky horní byte adresy rovný 0, pokud tedy bude v X hodnota FFh, nebude se pracovat s adresou 0111h, ale 0011h!)

Totéž pro instrukci DEC.

### INX, INY, DEX, DEY

Obdoba instrukcí INC, DEC, ale místo obsahu paměti se pracuje s registry X (INX, DEX) a Y (INY, DEY).

Instrukce inkrementu a dekrementu nastavují podle výsledku operace příznaky N a Z.

Možná jste si všimli, že jsem v seznamu neuvedl instrukce, které inkrementují/dekrementují obsah akumulátoru A. Neuvedl jsem je, protože je procesor 6502 nemá.

### ADC, SBC

Sčítání a odčítání 6502 samozřejmě obsahuje. ADC (Addition with Carry) přičte parametr a hodnotu příznaku C k registru A a výsledek ponechá v registru A. SBC (Subtraction with Carry) odečte od obsahu registru A parametr a negaci příznaku C a výsledek uloží do registru A.

Co z toho vyplývá? Zaprvé: 6502 vždycky uvažuje stav příznaku C (přenos). Neexistuje instrukce pro sčítání nebo odčítání, která by jeho stav ignorovala. Pokud chceme &#8222;jen&#8220; sčítat dvě čísla, je potřeba předtím nastavit C na nulu instrukcí CLC, jinak bude výsledek o 1 vyšší. Pokud výsledek sčítání přeteče 255, bude C=1, jinak zůstane nulový.

U instrukce pro odčítání platí přesný opak &#8211; pokud chceme zanedbat přenos, musíme příznak nastavit na 1 (instrukcí SEC). Pokud výsledek při odčítání podteče nulu (výsledkem je záporné číslo), bude příznak C roven 0, jinak 1.

A tak se může stát, pokud neošetříte příznaky správně, že dvojice instrukcí ADC #1, SBC #1 ve skutečnosti dělají věci nečekané. Viz následující kód &#8211; sledujte instrukce a výsledek v registru A:


  
Adresní módy těchto instrukcí jsou stejné jako např. u instrukce LDA, tedy:

  * imm &#8211; přímý operand: ADC #1 přičte 1
  * abs, zp &#8211; přímo zadaná adresa, buď plná, nebo v zero page
  * abx,aby &#8211; absolutní adresa, zvýšená o obsah registru X či Y
  * zpx &#8211; adresa v zero page, indexovaná přes registr X
  * izx, izy &#8211; nepřímo adresovaný operand (viz [adresní módy](http://strojak.uelectronics.info/adresni-mody-6502/ "Adresní módy 6502"))

### Porovnání &#8211; CMP

Instrukce CMP porovná hodnotu v registru A s operandem. Vnitřně funguje tak, že od hodnoty v registru A odečte hodnotu operandu, podle výsledku nastaví příznaky N, Z a C a výsledek zahodí.

Mohou nastat tři situace, které si ukážeme v následující tabulce:

| Situace     | N | Z | C |
| ----------- | - | - | - |
| A = operand |   | 1 | 1 |
| A > operand |   |   | 1 |
| A < operand | 1 |   |   |

(Hodnoty uvažujeme jako čísla bez znaménka)

Instrukce CMP nabízí stejné adresační možnosti jako instrukce ADC či SBC.



CPX, CPY

Podobně jako existují obdoby instrukcí INC a DEC pro práci s registry X a Y, tak i CMP má obdoby CPX a CPY. Liší se od CMP tím, že neporovnávají operand s hodnotou registru A, ale s registrem X, resp. Y. CPX a CPY mají jen tři adresní módy: přímý operand (imm), absolutní adresa (abs) nebo adresa v nulté stránce (zp).