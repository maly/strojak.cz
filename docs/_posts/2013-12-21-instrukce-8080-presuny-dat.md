---
id: 34
title: 'Instrukce 8080 &#8211; přesuny dat'
date: 2013-12-21T17:32:55+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=34
permalink: /instrukce-8080-presuny-dat/
dsq_thread_id:
  - "2065173817"
categories:
  - "8080"
---
První skupina instrukcí, kterou si probereme, přesouvá data. Z registrů, do registrů, i z paměti a do paměti&#8230;

<!--more-->

Až budete psát programy v assembleru, zjistíte, že nejvíc kódu nezabírají nějaké hyper super složité výpočty, ale přesouvání dat odněkud někam. V podstatě většina činností, které mikroprocesorový systém navenek dělá, je z valné části _přesouvání dat_. Samotného počítání je minimum. Takže je jen logické, že začneme právě u těchto instrukcí.

### MOV

Univerzální instrukce pro kopírování obsahu z jednoho registru do druhého. Instrukce má dva operandy &#8211; názvy osmibitových registrů (z [minulého dílu](http://strojak.uelectronics.info/instrukce-8080-adresni-mody-a-registry/ "Instrukce 8080 – adresní módy a registry") víme, že to jsou A, B, C, D, E, H, L a M). Jako první se uvádí cílový, jako druhý zdrojový. MOV A,B tedy vezme obsah registru B a zkopíruje ho do registru A. MOV A,C zkopíruje obsah z registru C do registru A. MOV C,A přesně naopak &#8211; z registru A do registru C.

Ptáte se, kolik je kombinací? No všechny dostupné &#8211; tedy 8 x 8 = 64. Čímž neříkám, že jsou všechny smysluplné či funkční. Instrukce MOV A,A je samosebou platná, zkopíruje obsah z registru A do registru A, ale upřímně: k čemu to je?

Znovu připomínám, že M je &#8222;pseudoregistr&#8220;, který ve skutečnosti odkazuje na místo v paměti s adresou, která je uložená v dvojici registrů H, L. Takže MOV A,M znamená &#8222;vezmi obsah z paměti na adrese, která je v registrech H,L, a zkopíruj ho do registru A&#8220;. MOV M,E znamená &#8222;vezmi obsah registru E a zkopíruj ho do paměti na adresu, tkerá je v registrech H,L&#8220;. No a MOV M,M &#8211; to je výjimka. Taková instrukce neexistuje. Její operační kód zaujímá instrukce HLT, která zastaví procesor.

### MVI

Tato instrukce vloží do osmibitového registru přímo zadanou hodnotu.

### LXI

Tato instrukce vloží do registrového páru B, D, H nebo registru SP přímo zadanou šestnáctibitovou hodnotu.

### LDA

Instrukce má jeden parametr &#8211; šestnáctibitovou adresu. Obsah paměti na téhle adrese zkopíruje do registru A

### STA

Protipól předchozí instrukce &#8211; ukládá hodnotu z registru A do paměti na adresu zadanou jako operand.

_A nyní si prosvištime nektera sloviška a cele fěty_ &#8211; pardon, zkusíme si, jak tyhle instrukce pracují. Opět k tomu použijeme interaktivní techniku &#8211; assembler ASM80.



Kód přeložte a spusťte emulaci. První instrukce je MVI A, 10. Po provedení prvního kroku bude tedy v registru A hodnota&#8230; aha! Je tam 0Ah. Emulátor totiž ukazuje všechny hodnoty hexadecimálně, zatímco v kódu je zapsaná konstanta 10, bez H na konci, takže je to tedy desítková hodnota, a 10 desítkově je v šestnáctkové soustavě 0A. (A nestěžujte si, [různé způsoby zápisu konstant](http://strojak.uelectronics.info/zaklady-assembleru/ "Základy assembleru") jsme už brali!)

Takže tedy znovu: První instrukce uloží do registru A hodnotu 0Ah (=10), druhá instrukce uloží do registru B hodnotu 14h (=20). Třetí instrukce je LXI H, _číslo_ &#8211; mrkněte se o kousek výš&#8230; jasně! Tahle instrukce uloží zadané číslo do dvojice registrů HL. Tady je to 0040h, takže do H půjde vyšší (00) a do L nižší (40h) část čísla.

Na dalším řádku je instrukce MOV M,A. Z toho, co o ní víme, by se mělo stát následující: Obsah registru A (což je teď těch 0Ah) se zkopíruje do paměti na adresu, která je uložená v registrech HL. Tak schválně &#8211; v registrech HL je uložena hodnota 0040h, takže by se v paměti na adrese 0040h měla objevit hodnota 0Ah. Zkuste si sami &#8211; vidíte, že vlevo nahoře, v oblasti nadepsané MEMORY, se na adrese 0040h objeví právě ta hodnota 0Ah.

Bystřejší si všimli, že od adresy 0 jsou v paměti nějaká čísla. Ti, co jsou ještě bystřejší, už vědí: To je přeci ten náš program! A právě do té oblasti sáhne další instrukce &#8211; LDA 0003h vezme obsah paměti na adrese 0003h, což je zrovna shodou okolností číslo 14h (které je součástí instrukce MVI B, 20) a uloží ho do registru A.

Poslední instrukce pak uloží obsah registru A na adresu 0041h, takže od adresy 0040h budete mít vedle sebe hezky 0Ah a 14h. Máte? Skvělé, pojďme na další instrukce!

### LDAX

Tak jako LDA vezme hodnotu z paměti na adrese, kterou mu zadáme, a uloží ji do A, tak i LDAX vezme hodnotu z paměti a uloží ji do A. Adresu, se kterou se bude pracovat, najde v registrovém páru B nebo D (tedy v registrech B, C, resp. D, E).

### STAX

Jako má LDA svoje STA, tak má LDAX svůj STAX. Funguje stejně jako LDAX, jen přenos dat probíhá v opačném směru.

### LHLD

Tahle instrukce opět pracuje s dvojicí bajtů. Jako parametr dostane adresu a vykoná následující: Do registru L zkopíruje hodnotu z paměti na dané adrese a do registru H zkopíruje hodnotu z paměti na adrese o 1 vyšší.

### SHLD

Už to tak je. Instrukce L_něco_ mají svou obdobu v podobě S_něco_. Tam, kde L z paměti čte (load), tam S do paměti zapisuje (store). Takže SHLD uloží obsah registru L na zadanou adresu, obsah registru H na adresu o 1 vyšší.

Všimněte si, že LHLD i SHLD dodržují pravidlo &#8222;malého indiána&#8220; &#8211; na nižší adresu jde méně významný bajt.

### XCHG

Poslední instrukce pro přesun dat je tak trošku výjimka. Nemá žádné operandy &#8211; to, s čím pracuje, je dáno implicitně (jsou to dvojregistry D a H). A na rozdíl od předchozích instrukcí, které kopírovaly hodnoty, tato instrukce je vyměňuje. XCHG vymění obsah registrů D a E za obsah registrů H a L. Tedy to, co bylo v D, bude teď v H &#8211; a naopak. Kdybychom udělali třeba MOV H,D a MOV L,E, dosáhli bychom něčeho jiného obsah registrů D a E by se nevyměnil, ale ZKOPÍROVAL, takže by bylo v D totéž co v H a v E totéž co v L.

Pokud chceme vyměnit údaje ve dvou registrech, musíme použít pomocný registr. Například výměnu obsahu registrů B a C zařídíme takto:

<pre class="lang:default decode:true">MOV A,B
 MOV B,C
 MOV C,A</pre>

Tedy přes registr A. Jeho původní hodnotu samozřejmě ztratíme&#8230;

A opět drobná ukázka. Až si pohrajete, můžete si zkusit jedno cvičení. První samostatný program. Poslyšte zadání, je jednoduché: **Program prohodí obsah registrů BC a HL.**