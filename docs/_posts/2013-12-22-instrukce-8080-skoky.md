---
id: 53
title: 'Instrukce 8080 &#8211; skoky'
date: 2013-12-22T22:00:45+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=53
permalink: /instrukce-8080-skoky/
dsq_thread_id:
  - "2067384135"
categories:
  - "8080"
---
Kdyby nebyly skoky, tak by program prostě jen jel, jel, jel&#8230; a na konci by jel zase od začátku. Něco jako flašinet.

<!--more-->

Předpokládám, že znáte nějaký vyšší jazyk. Pravděpodobně ten jazyk obsahuje příkaz, kterému se vyhýbáte jako čert kříži, i když tam z nějakého důvodu je. Pokud jste mladší ročníky a začali jste s nějakým moderním jazykem, tak před vámi možná dokonce existenci toho příkazu úplně zatajili. Každopádně vám řekli, že to je zlo, nesystémovost, vede to ke špatným věcem a vůbec &#8211; pokaždé, když použijete GOTO, tak Bůh zabije koťátko.

Vítejte ve světě, kde je GOTO tím hlavním nástrojem.

Totiž, aby bylo jasno: V assembleru nejsou objekty, třídy, metody&#8230; Dokonce tam nejsou ani řídicí struktury. Není tam cyklus FOR, WHILE, není ani IF / ELSE. Ba co hůř &#8211; nejsou ani datové typy. Zapomeňte na řetězce, zapomeňte na datové struktury, jediné, co máte, je číslo. Zapomeňte na lokální proměnné. Nic z toho v assembleru nedostanete. Je to tvrdý svět &#8211; člověk proti křemíku. Nic vám neodpustí, nic za vás nedomyslí. Ale zase má jiné výhody: můžete program optimalizovat do nejmenších podrobností a detailů a za týden práce máte vymazlený program, co byste ve vyšším jazyce psali jedno odpoledne. Možná je rychlejší, možná je kratší, každopádně v něm můžou číhat velmi zákeřné chyby, které nemáte ve vyšším jazyce šanci udělat.

&#8222;Ale jak tedy udělám smyčku? Jak cyklus? Jak vnořím podmínky? Vždyť bez těchto základních konstrukcí nelze programovat!&#8220; To si povíme za chvíli, teď si ukážeme, co máme k dispozici.

### JMP

To je ono, to zlé GOTO. Tady se jmenuje JMP (jako že &#8222;jump&#8220;) a má jeden parametr. Tím je šestnáctibitové číslo &#8211; adresa, na kterou se skočí, tj. ze které bude procesor číst další instrukci. Vnitřně funguje tak, že to šestnáctibitové číslo, které je součástí instrukce, uloží do registru PC. Ten obsahuje ([už jsme si to říkali](http://strojak.uelectronics.info/8080-architektura/ "8080: Architektura")) adresu, ze které se čte aktuální instrukce. Když tedy do PC uložíme číslo 0123h, bude se další instrukce číst z adresy 0123h.

Když takový program píšete, musíte znát adresu, kde bude ta cílová instrukce uložena. Naštěstí si to nemusíte počítat, to za vás dělá překladač. Vy jen překladači řeknete, kde bude ležet první instrukce (.org, vzpomínáte?), a zbytek adres si dopočítá za vás. Vy si můžete jednotlivé body pojmenovat symbolickými názvy a překladač je za vás správně vyhodnotí. Nějak takhle:



Což je samosebou zcela smyšlený příklad, ve skutečnosti by ho takhle nikdo nenapsal, ale je hezky vidět, jak instrukce JMP skáčou dopředu i dozadu. Zkuste si ho chvíli krokovat a uvidíte&#8230;

### CALL, RET

Víte, nebyl jsem tak úplně upřímný. GOTO není jediné, co máte k dispozici. Ještě můžete použít GOSUB / RETURN. Pokud znáte BASIC, víte, co tyto instrukce dělají &#8211; volají podprogram. Ve vyšších jazycích je to _něco jako procedura / funkce_, ale opravdu jen velmi vzdáleně: nejsou žádné lokální proměnné, nejsou parametry, nejsou návratové hodnoty. Je tedy konvence, která říká, že podprogram, pokud něco vrací, tak by to měl vracet v registru A nebo HL, ale není to vůbec nezbytné &#8211; když na to přijde, tak váš podprogram může vrátit údaje v registru E, B a návdavkem ještě v příznaku CY.

Pokud v podprogramu změníte hodnotu registru, zůstane změněná i po návratu do hlavního programu. Musíte s tím počítat, a pokud si nechcete přepisovat hodnoty v registrech, tak si nezapomeňte na začátku podprogramu potřebné hodnoty uložit na [zásobník](http://strojak.uelectronics.info/8080-priznaky-a-zasobnik/ "8080 – příznaky a zásobník") (PUSH) a před návratem zase ze zásobníku vybrat (POP). Ale pozor! Vždycky musíte vybrat přesně tolik údajů, kolik jste jich uložili, protože jinak se stanou strašlivé věci. Jaké? Hned si ukážeme.

Jak taková instrukce CALL funguje? CALL má jeden parametr, kterým je šestnáctibitová adresa, jako u instrukce JMP. CALL nejprve vezme adresu následující instrukce (říká se jí &#8222;návratová adresa&#8220;), tu uloží na zásobník, a pak provede skok na zadanou adresu, stejně jako JMP. Instrukce RET vezme hodnotu ze zásobníku (měla by tam být ta návratová adresa, kterou tam uložila instrukce CALL) a skočí na ni. provádění programu tak pokračuje za tou instrukcí CALL, která volala podprogram.

Pokud si v podprogramu uložíte obsah registru (třeba HL pomocí PUSH H), bude na zásobníku HL a pod ním návratová adresa. Před návratem tedy musíte HL zase odebrat (nejčastěji pomocí POP něco), aby se RET vrátila tam, kam má. Pokud byste hodnotu neodebrali, RET by obsah zásobníku, tj. uloženou hodnotu registrů HL, považovala za návratovou adresu a skočila by tam. Naopak pokud byste si ze zásobníku vyzvedli víc údajů, než jste do něj vložili, byla by mezi nima i návratová adresa, takže RET by se vrátila úplně někam jinam.

Ano, OBČAS se takové triky dělají, ale upozorňuju dopředu: dělají je programátoři, kteří vědí co dělají a proč. Mají své místo ve chvíli, kdy hledáte, jak ušetřit nějaký ten bajt / nějaký ten takt procesoru, ale v naprosté většině případů je přebývající POP nebo PUSH chyba, která povede k pádu celého systému.



### PCHL

Prosím, nečíst P-Ch-L (podle vzoru _Pche_!), ale P-C-H-L. Když to přečtete správně, napoví vám, jak funguje. Do registru PC uloží obsah HL (PC=HL). Je to tedy něco jako JMP na adresu, uloženou v HL.

### RST

RST je taková zkratka pro CALL na některé oblíbené adresy. Instrukcí RST je 8, číslovaných 0-7, a skáčou na adresu 8*N (kde N je to číslo). RST 0 je tedy totéž co CALL 0, RST 1 je CALL 8, RST 2 je CALL 16 (CALL 0010h) a tak dál&#8230; až RST 7 je totéž jako CALL 0038h. Oproti instrukci CALL, která zabírá 3 bajty (1 bajt instrukční kód 2 bajty adresa) má instrukce RST jen 1 bajt, navíc se provede rychleji než CALL. V mnoha systémech jsou proto na těchto adresách připravené často používané podprogramy. Namátkou ZX Spectrum používá RST 2 (v mnemonice procesoru Z80 označená jako RST 10h) pro vypsání znaku s ASCII kódem, který je v registru A, na výstup (obrazovku).

### Podmíněné skoky

Je hezké, že program skáče jako srnka, ale mnohem lepší a užitečnější by bylo, kdyby mohl nějak reagovat na to, co se počítá. K tomu slouží podmíněné instrukce Jcond, Ccond a Rcond. &#8222;Cond&#8220; je označení podmínky, která se má testovat. Vždy se testuje hodnota některého příznaku S, Z, CY nebo P.

| Cond | Provede se, pokud&#8230; |
| ---- | ------------------------ |
| C    | CY = 1                   |
| NC   | CY = 0                   |
| Z    | Z = 1                    |
| NZ   | Z = 0                    |
| M    | S = 1                    |
| P    | S = 0                    |
| PE   | P = 1                    |
| PO   | P = 0                    |

Podmínky mají i své mnemotechnické názvy: Carry, Not Carry, Zero, Not Zero, Minus, Plus, Parity Even, Parity Odd.

Podmíněné skoky, odvozené od JMP, jsou tedy JC, JNC, JZ, JNZ, JM, JP, JPE a JPO. Pracují tak, že otestují příslušný podmínkový bit. Pokud platí podmínka, provede se skok, pokud není splněna, pokračuje se dál. Například instrukce JZ 0123h zkontroluje nejprve, jestli je příznak Z=1. Pokud ano, skočí na adresu 0123h, pokud ne, pokračuje dál.

Ano, takhle nějak funguje IF cond THEN GOTO x. &#8222;Pokud je splněno, skoč tam a tam.&#8220; V praxi budete chtít často zapsat něco jako &#8222;Pokud je výsledek 0, udělej ještě to a to&#8230;&#8220; (tedy chování obdobné známé konstrukci IF) V assembleru to vyřešíte tak, že zapíšete &#8222;Pokud výsledek není 0, tak přeskoč až za blok příkazů&#8220;.

<pre class="lang:default decode:true ">; IF (CY) {INC A, DEC D}

 JNC navesti ; Pokud podmínka neplatí, přeskoč
 INC A       ; blok příkazů
 DEC D
navesti:     ; a tady se pokračuje dál.

; IF (NZ) {INC A, DEC D} else {DEC A, DEC E}

 JZ else    ; Pokud podmínka neplatí, přeskoč
 INC A       ; blok příkazů
 DEC D
 JMP endif   ; bez toho by se provedl i blok ELSE
else: 
 DEC A
 DEC E
endif:       ; a tady se pokračuje dál.</pre>

Takto tedy funguje konstrukce IF &#8211; THEN a IF &#8211; THEN &#8211; ELSE.

Podobně jako jsou podmíněné instrukce skoku JMP, tak můžeme vytvořit podmíněné skoky do podprogramu (Ccond &#8211; CZ, CNZ, CC, CNC, CM, CP, CPE, CPO) a podmíněné návraty (RZ, RNZ, RC, RNC, RM, RP, RPE, RPO). Fungují stejně jako CALL a RET, ale provedou se pouze pokud je splněná podmínka.

### While!

Dobře, ukážeme si jednu konstrukci z vyššího jazyka, přepsanou do assembleru. Představte si takový počítaný cyklus WHILE:

<pre class="lang:js decode:true">while (--B) {
  A = ctiKlav();
  pisObr(A+1);
}</pre>

Tedy dokud je hodnota B vyšší než 0, tak od něj odečti 1, zavolej funkci ctiKlav, výsledek ulož do A, a zavolej funkci pisObr s parametrem v A.

Řekněme si na rovinu &#8211; nic takového jako &#8222;výsledek ulož do registru A&#8220; nemáme. Je na autorovi podprogramu ctiKlav, aby vrátil požadované v registru A. Stejně tak nemůžeme podprogramu pisObr předávat nějaké parametry &#8211; pokud podprogram očekává nějakou hodnotu v registru A, je na nás, abychom ji do toho registru připravili. Nemluvě o tom, že budeme používat registr B jako počítadlo průchodů, takže pokud nám ho některý z podprogramů přepíše, tak algoritmus nebude fungovat.

Proto je, a to vám kladu na srdce, dobrým zvykem u každého podprogramu napsat do dokumentace, ve kterém registru očekává parametr, ve kterém vrací hodnotu, a zmínit, pokud nějaký registr přepíše, ať s tím může ten, kdo ten podprogram použije, správně naložit. (Kdysi jsem četl historku o tom, jak se kdosi v 80. letech bál použít podprogramy monitoru, protože v dokumentaci u nich psali poznámky jako: _Ničí registry D, E_. Čímž autor samosebou myslel, že přepíše hodnoty v nich uložené, ovšem nebohý juniorní programátor se bál toho výrazu _ničí _&#8211; počítač je tak drahý stroj, tak si přeci nevezme na svědomí, aby se vevnitř něco zničilo.)

Tak jak tedy to výše zmíněné přepsat do assembleru? Řešení:

<pre class="lang:asm decode:true">while:
    DCR B         ; --B
    JZ endwhile   ; je už 0? Skok na konec...
    CALL ctiKlav  ; volání
    INR A         ; A = A + 1
    CALL pisObr   ; volání
    JMP while     ; další průchod smyčkou
endwhile:</pre>

Budete používat nejrůznější varianty této konstrukce &#8211; s podmínkou na konci (do {&#8230;} while()) nebo s podmínkou uprostřed, ale vždy to bude plus mínus něco podobného.

Ony totiž všechny ty konstrukce, které znáte a máte rádi (for, while apod.), jsou nakonec často přeloženy do strojového jazyka, a to nějakým způsobem, který je velmi podobný tomuto. Tedy samé IF&#8230;THEN GOTO a GOTO.

GOTO!