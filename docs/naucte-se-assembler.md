---
id: 30
title: Naučte se assembler
date: 2013-12-21T10:38:05+00:00
author: Martin Maly
layout: page
guid: http://strojak.uelectronics.info/?page_id=30
---
## <span style="font-family: BitstreamVeraSansRoman, Verdana, Geneva, sans-serif; font-size: 14px; line-height: 16px;">Koho ještě v roce 2014 zajímá assembler starých osmibitových procesorů Z80, I8080, 6502 a podobných?</span>

Pokud mezi ně patříte, jste na správném místě. Postupem času tady vznikne tutoriál pro jednotlivé procesory. Pokud se ujme, přidám i další, novější (PIC, ARM, AVR), ale zatím bych rád zůstal u té klasiky. Předpokládám, že víte, co je bit, bajt, co je šestnáctková soustava a podobné věci. To tady opravdu probírat nechci a nebudu. Začneme až od architektury mikroprocesoru. Stranou nechám i hardware, problematiku připojení do systému, časování a další věci, spojené s fungováním procesoru (třeba přerušovací systém) a budu se věnovat téměř výhradně programování v assembleru a strojovému kódu.

  1. <a title="Permalink to Základní pojmy" href="http://strojak.cz/zakladni-pojmy/" rel="bookmark">Základní pojmy</a>
  2. <a title="Permalink to 8080: Architektura" href="http://strojak.cz/8080-architektura/" rel="bookmark">8080 &#8211; architektura</a>
  3. <a title="Permalink to Základy assembleru" href="http://strojak.cz/zaklady-assembleru/" rel="bookmark">Základy assembleru</a>
  4. <a title="Permalink to 8080: První program" href="http://strojak.cz/8080-prvni-program/" rel="bookmark">8080 &#8211; první program</a>
  5. <a title="Permalink to Instrukce 8080 – adresní módy a registry" href="http://strojak.cz/instrukce-8080-adresni-mody-a-registry/" rel="bookmark">Instrukce 8080 – adresní módy a registry</a>
  6. <a title="Permalink to Instrukce 8080 – přesuny dat" href="http://strojak.cz/instrukce-8080-presuny-dat/" rel="bookmark">Instrukce 8080 – přesuny dat</a>
  7. <a title="Permalink to 8080 – Příznaky a zásobník" href="http://strojak.cz/8080-priznaky-a-zasobnik/" rel="bookmark">8080 – příznaky a zásobník</a>
  8. <a title="Permalink to Instrukce 8080 – aritmetika" href="http://strojak.cz/instrukce-8080-aritmetika/" rel="bookmark">Instrukce 8080 – aritmetika</a>
  9. <a title="Permalink to Instrukce 8080 – skoky" href="http://strojak.cz/instrukce-8080-skoky/" rel="bookmark">Instrukce 8080 – skoky</a>
 10. [Instrukce 8080 – rotace](http://strojak.cz/instrukce-8080-rotace/)
 11. [8080 &#8211; násobení](http://strojak.cz/8080-nasobeni/)
 12. [Instrukce 8080 &#8211; logické operace](http://strojak.cz/instrukce-8080-logicke-operace/)
 13. [Instrukce a operační kód](http://strojak.cz/instrukce-a-operacni-kod/)
 14. [8080 &#8211; Ahoj světe](http://strojak.cz/8080-ahoj-svete/)
 15. <a title="Permalink to Pseudoinstrukce" href="http://strojak.cz/pseudoinstrukce/" rel="bookmark">Pseudoinstrukce</a>
 16. [BCD a přerušení](http://strojak.cz/bcd-a-preruseni/)
 17. [Instrukce 8080 &#8211; práce s periferiemi](http://strojak.cz/8080-instrukce-pro-praci-s-periferiemi/)
 18. <a title="Permalink to Trocha assemblerové teorie" href="http://strojak.cz/trocha-assemblerove-teorie/" rel="bookmark">Trocha assemblerové teorie</a>
 19. [8080 – algoritmy 1](http://strojak.cz/8080-algoritmy-1/)
 20. [Srovnání architektur 8080 a 6800](http://strojak.cz/procesory-a-procesory/)
 21. [Architektura 6502](http://strojak.cz/architektura-6502/)
 22. [Adresní módy 6502](http://strojak.cz/adresni-mody-6502/)
 23. [Instrukce 6502 – přesuny](http://strojak.cz/instrukce-6502-presuny/)
 24. [6502 – příznaky a instrukce pro práci s nimi](http://strojak.cz/6502-priznaky-a-instrukce-pro-praci-s-nimi/)
 25. [6502 – přerušovací systém](http://strojak.cz/6502-prerusovaci-system/)
 26. [Instrukce 6502 – skoky a podprogramy](http://strojak.cz/instrukce-6502-skoky-a-podprogramy/)
 27. [Instrukce 6502 – aritmetika](http://strojak.cz/instrukce-6502-aritmetika/)
 28. [Instrukce 6502 – logické a bitové operace](http://strojak.cz/instrukce-6502-logicke-a-bitove-operace/)
 29. [6502 – algoritmy sčítání a násobení](http://strojak.cz/6502-scitani-a-nasobeni/)
 30. [6502 – Ahoj, světe…](http://strojak.cz/6502-ahoj-svete/)
 31. [6502 – !etěvs ,johA](http://strojak.cz/6502-etevs-joha/)
 32. [6502 &#8211; čtení kláves a pořádek v kódu](http://strojak.cz/6502-cteni-klaves-a-poradek-v-kodu/)

**Autor děkuje všem, kdo přispěli k sepsání tohoto kurzu radou, námětem, připomínkou nebo faktickou opravou.** Jmenovitě pak především [Romanu Bórikovi](http://pmd85.borik.net/wiki/Intro), který pečlivě pročetl jednotlivé kapitoly, upozornil na překlepy, nejasnosti a chyby a přispěl mnoha cennými radami!