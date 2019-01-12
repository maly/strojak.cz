---
id: 180
title: 'Instrukce 6502 &#8211; skoky a podprogramy'
date: 2014-02-17T11:07:48+00:00
author: Martin Maly
layout: post
guid: http://strojak.uelectronics.info/?p=180
permalink: /instrukce-6502-skoky-a-podprogramy/
dsq_thread_id:
  - "2278771323"
categories:
  - "6502"
---
V minulé lekci jsem použil v kódu instrukci skoku a popisoval jsem návrat z přerušení. Pojďme si tedy doplnit sérii a probrat zbývající instrukce skoků.

<!--more-->

### Nepodmíněný skok &#8211; JMP

Instrukce nepodmíněného skoku děljá přesně to, co u jiných procesorů &#8211; tedy to, co se označuje známým &#8222;GOTO&#8220;. Skočí se na jinou adresu a pokračuje se odtamtud. Pokud pracujete s assemblerem, nemáte žádné programátorské struktury, žádné smyčky ani bloky IF-ELSE-ENDIF, všechno musíte řešit pomocí skoků a podmíněných skoků.

Instrukce JMP používá dva adresní módy &#8211; buď absolutní adresování, nebo nepřímé. Absolutní znamená, že se skáče přímo na zadanou adresu:



Nepřímé adresování (viz [díl o adresních módech](http://strojak.uelectronics.info/adresni-mody-6502/ "Adresní módy 6502")) pracuje tak, že ze zadané adresy (a z adresy o 1 vyšší) se načtou dva bajty, které dohromady dají dvoubajtovou _efektivní adresu_ a skáče se na ni.


  
Všimněte si, že v tomto druhém případě neskáče JMP přímo na adresu LOOP, ale na (ind). ind je návěští, na kterém jsou uložené dva bajty (viz výpis přeloženého programu).

### Podmíněné skoky &#8211; Bxx

Podmíněných skoků je osm pro osm různých podmínek &#8211; podle čtyř příznakových bitů, vždy 0 nebo 1. Zde jsou v přehledné tabulce:

<table border="1" cellspacing="0" cellpadding="2">
  <tr>
    <th>
      Příznak
    </th>
    
    <th colspan="2">
      Stav
    </th>
  </tr>
  
  <tr>
    <th>
    </th>
    
    <th>
    </th>
    
    <th>
      1
    </th>
  </tr>
  
  <tr>
    <td>
      N
    </td>
    
    <td>
      BPL
    </td>
    
    <td>
      BMI
    </td>
  </tr>
  
  <tr>
    <td>
      V
    </td>
    
    <td>
      BVC
    </td>
    
    <td>
      BVS
    </td>
  </tr>
  
  <tr>
    <td>
      C
    </td>
    
    <td>
      BCC
    </td>
    
    <td>
      BCS
    </td>
  </tr>
  
  <tr>
    <td>
      Z
    </td>
    
    <td>
      BNE
    </td>
    
    <td>
      BEQ
    </td>
  </tr>
</table>

Mnemotechnika těchto názvů je prostá. Instrukce jsou skoky (Branch), z toho je písmeno B. Další dvě písmena jsou název podmínky &#8211; u příznaku N (Negative), který říká, jestli je číslo kladné nebo záporné, je to BPL (Branch if PLus) a BMI (Branch if MInus). U příznaku Z, který udává, jestli je číslo nula, to není Zero-Nonzero, ale využívá se toho, že tento skok bývá často prováděn po testu na rovnost (který interně probíhá jako odčítání). Při nerovnosti, nenulovém výsledku (Z=0) se skáče instrukcí BNE (Branch if Not Equal), analogicky při rovnosti, a tedy Z=1, se skáče instrukcí BEQ (Branch if EQual).

U příznaků C a V, které nemají takhle jednoznačné &#8222;vysvětlení&#8220;, se používá mnemotechnika &#8222;Branch if V is Clear&#8220; (BVC), &#8222;Branch if C is Set&#8220; (BCS) apod.

Adresní mód těchto instrukcí je vždy relativní. To znamená, že se skáče v rozmezí -128..+127. Využívá se toho, že podobné podmíněné skoky jsou většinou součástí krátkých smyček. Pokud tomu tak není, musíte použít &#8222;náhražkovou&#8220; konstrukci:

<pre class="lang:asm decode:true">BCC NekamDaleko ; nelze, pokud je adresa vzdálená víc než 128 pozic
                ; Místo toho je třeba použít:

BCS Skip        ; Obrácená podmínka, která přeskočí následující instrukci
JMP NekamDaleko ; tady se provede samotný skok
Skip: ....      ; a tady se pokračuje</pre>

Překladač naštěstí za vás spočítá správnou hodnotu odskoku z aktuální adresy návěští a cílové adresy. Hodnota 0 znamená následující adresu (tj. žádný efekt), hodnota 0FEh skáče o dvě místa zpátky, tj. na tu samou adresu, kde je instrukce (je to hodnota -2, a Bxx jsou dvoubajtové).

### BRK, RTI

Tyto instrukce jsme probrali v [minulém díle](http://strojak.uelectronics.info/6502-prerusovaci-system/ "6502 – přerušovací systém") &#8211; slouží pro vyvolání přerušení a pro návrat z obslužné rutiny přerušení.

### Podprogramy &#8211; JSR, RTS

To, k čemu u 8080 sloužily instrukce CALL a RET, zajišťují u 6502 instrukce JSR a RTS (Jump to Subroutine / Return from Subroutine). JSR používá pouze absolutní adresní mód, tj. za instrukcí jsou nižší a vyšší bajty cílové adresy. JSR uloží na zásobník vyšší a nižší bajt návratové adresy, a pak do PC nahraje přečtenou adresu. Čímž se vlastně provede skok na nějakou adresu (jako u JMP), s tím rozdílem, že na zásobníku je adresa, kam se má program vrátit (ve skutečnosti je o 1 menší, s čímž počítá instrukce RTS).

K návratu slouží instrukce RTS. Ta přečte ze zásobníku dva bajty, z nich složí adresu, přičte 1 (viz výše) a na ni skočí. Pokud podprogram zanechal zásobník v takovém stavu, v jakém ho našel, tak se skočí na instrukci, následující za příslušnou instrukcí JSR.

Rozdíl mezi RTS a RTI je v tom, že RTI načítá ze zásobníku i uloženou hodnotu příznakového registru P a k návratové adrese nepřičítá 1 (instrukce BRK i přerušení ukládají pravou návratovou adresu).

**Úhrnem** lze o instrukční sadě procesoru 6502 v souvislosti se skoky říct, že je hodně omezená. Podmíněné skoky pouze relativní, skoky do podprogramu a návraty pouze nepodmíněné a s absolutní adresou, jen nepodmíněný skok lze adresovat i nepřímou adresou (ale nelze relativně).