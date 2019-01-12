---
id: 197
title: '6502 &#8211; sčítání a násobení'
date: 2014-02-19T11:17:28+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=197
permalink: /6502-scitani-a-nasobeni/
dsq_thread_id:
  - "2288042764"
categories:
  - "6502"
---
Několik užitečných algoritmů pro vaše procesory 6502.

<!--more-->

## 16bitový součet

U procesoru 8080 není sčítání 16bitových čísel problém &#8211; procesor má dostatek registrů a má k tomu i speciální instrukci DAD. U 6502 nemáme ani instrukci, ani registry. Proto se musí sčítání dvoubajtových čísel řešit algoritmem. Naštěstí není příliš složitý, využívá jen instrukci ADC.



Všimněte si, že obě čísla (P1 a P2) jsou uloženy v zero page (na adresách 60h a 62h) a do zero page se ukládá i součet (R, 64h). Nejprve se nuluje příznak přenosu, pak se sečtou dva nižší bajty a poté dva vyšší. Případný přenos z nižšího do vyššího bajtu zařídí příznak C.

Odčítání je pak naprosto analogické, jen na začátku je potřeba příznak C nastavit na 1, nikoli nulovat.

## Násobení

Vzpomínáte, jak jsme u procesoru [8080 násobili dvě osmibitová čísla](http://strojak.uelectronics.info/8080-nasobeni/ "8080 – násobení")? Můžeme podobný postup použít i u 6502? Tak v zásadě ano, ale se stejnými výhradami jako u sčítání: Nejsou registry.

Algoritmus rovněž využívá rotace do vyššího bajtu, jako u 8080, ale protože 6502 jaksi nemá nic jako &#8222;vyšší bajt&#8220;, jedná se zas o místo v paměti. A protože k paměti se nepřistupuje žádnou 16bitovou operací, není nezbytně nutné, aby &#8222;vyšší bajt&#8220; byl hned za &#8222;nižším bajtem&#8220;. A protože není 16bitové sčítání, viz výše, je nahrazeno rovněž algoritmem.



Výsledek násobení je v registrech X (vyšší bajt) a A (nižší bajt).

Všimněte si, že násobení dvojkou je zařízeno jako &#8222;šestnáctibitový shift&#8220; &#8211; pomocí ASL a ROL.