---
id: 79
title: 'Instrukce 8080 &#8211; logické operace'
date: 2013-12-24T11:26:01+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=79
permalink: /instrukce-8080-logicke-operace/
dsq_thread_id:
  - "2070589493"
categories:
  - "8080"
---
&#8222;Milý autore, tvůj seriál je zmatený a nemá žádnou logiku! Logika v něm chybí! Váš čtenář.&#8220; &#8211; &#8222;Milý čtenáři, logika tu nechybí. Logika je tu právě teď!&#8220;

<!--more-->

Protože předpokládám, že všichni, co to čtete, jste už nějaký programovací jazyk zvládli, tak si nemusíme, doufám, vysvětlovat, co je to AND, OR a XOR. Některým tužším borcům naznačím: &, | a ^. Už jo, už to berou, tak pojďme na to.

### ANA, ANI

Provede operaci AND mezi registry A a vybraným registrem (ANA), nebo mezi registrem A a zadanou osmibitovou konstantou (ANI). Hezky bit po bitu, a výsledek jde zpátky do A. Pokud se na parametr budeme dívat jako na masku, kterou je třeba aplikovat na registr A, tak vězte, že kde má maska 1, tam zůstane A nedotčeno, kde má 0, tam bude vynulováno. Instrukce ovlivňují všechny příznaky.

### ORA, ORI

Provede operaci OR mezi registry A a vybraným registrem (ORA), nebo mezi registrem A a zadanou osmibitovou konstantou (ORI). Hezky bit po bitu, a výsledek jde zpátky do A. Pokud se na parametr budeme dívat jako na masku, kterou je třeba aplikovat na registr A, tak vězte, že kde má maska 0, tam zůstane A nedotčeno, kde má 1, tam bude nastaveno na 1. Instrukce ovlivňují všechny příznaky.

### XRA, XRI

Provede operaci XOR mezi registry A a vybraným registrem (XRA), nebo mezi registrem A a zadanou osmibitovou konstantou (XRI). Hezky bit po bitu, a výsledek jde zpátky do A. Pokud se na parametr budeme dívat jako na masku, která se bude aplikovat na registr A, tak vězte, že kde má maska 0, tam zůstane A nedotčeno, kde má 1, tam bude změněna hodnota příslušného bitu v A. Instrukce ovlivňují všechny příznaky.

### CMA

Vezme obsah registru A a zneguje ho, tj. všechny 1 prohodí za 0 (a obráceně). Instrukce nijak neovlivní příznaky.

### Praktické použití

Takhle odsud to možná vypadá jako nějaká zajímavost pro nerdy, ale věřte, že v assembleru budete tyhle instrukce používat často. Například pro nastavení konkrétního bitu použijete ORI 01h (02h, 04h, 08h, 10h, 20h, 40h, 80h). Pro vynulování bitu použijete AND, ale v negované podobě &#8211; tam, kde chcete bit nastavit na 0, nechte 0, všude jinde 1. Chcete-li vynulovat bit 3 (To je on: &#8222;&#8230;.3&#8230;&#8220;), použijete konstantu, která má všude 1, s výjimkou pozice 3: &#8222;11110111&#8220;. Tedy F7h.

Operace XOR slouží krom změny hodnot bitů i k jednomu hezkému fíglu: pokud potřebujete vynulovat registr A a nezáleží vám na tom, že se změní hodnota příznaků, použijte XRA A. Taková instrukce zabere jen jeden bajt a je i proti MVI A,0 rychlejší.

Instrukce CMA má efekt stejný jako XRI FFh &#8211; ale nemění příznaky a zabere jen jeden bajt.

Teď jsme se dostali do bodu, kdy zbývá probrat už jen šest instrukcí procesoru 8080, takže je opět vhodná chvíle na drobné odbočení, ať to máme pestřejší&#8230;