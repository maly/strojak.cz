---
id: 86
title: Tabulka instrukcí procesoru 8080
date: 2013-12-24T12:06:01+00:00
author: Martin Maly
layout: page
guid: http://strojak.uelectronics.info/?page_id=86
---
[<img class="alignnone size-large wp-image-89" alt="Intel 8080 OPCODES" src="http://strojak.uelectronics.info/wp-content/uploads/sites/7/2013/12/Intel-8080-OPCODES-1024x599.png" srcset="https://strojak.cz/wp-content/uploads/sites/7/2013/12/Intel-8080-OPCODES-1024x599.png 1024w, https://strojak.cz/wp-content/uploads/sites/7/2013/12/Intel-8080-OPCODES-300x175.png 300w, https://strojak.cz/wp-content/uploads/sites/7/2013/12/Intel-8080-OPCODES.png 1200w" sizes="(max-width: 1024px) 100vw, 1024px" />](http://pastraiser.com/cpu/i8080/i8080_opcodes.html)

<table style="font-family: monospace; font-size: 8pt;" width="1200" cellspacing="0" cellpadding="0">
  <tr>
    <td>
      <table style="font-family: monospace; font-size: 8pt;" cellspacing="0" cellpadding="0">
        <tr>
          <td style="background: #ff99cc;">
          </td>
          
          <td>
          </td>
          
          <td>
            Řídicí instrukce
          </td>
        </tr>
        
        <tr>
          <td style="background: #ffcc99;">
          </td>
          
          <td>
          </td>
          
          <td>
            Skoky a podprogramy
          </td>
        </tr>
        
        <tr>
          <td style="background: #ccccff;">
          </td>
          
          <td>
          </td>
          
          <td>
            Osmibitové přesuny dat
          </td>
        </tr>
        
        <tr>
          <td style="background: #ccffcc;">
          </td>
          
          <td>
          </td>
          
          <td>
            Šestnáctibitové přesuny
          </td>
        </tr>
        
        <tr>
          <td style="background: #ffff99;">
          </td>
          
          <td>
          </td>
          
          <td>
            Osmibitové aritmeticko-logické instrukce
          </td>
        </tr>
        
        <tr>
          <td style="background: #ffcccc;">
          </td>
          
          <td>
          </td>
          
          <td>
            Šestnáctibitové aritmeticko-logické
          </td>
        </tr>
      </table>
    </td>
    
    <td width="25">
    </td>
    
    <td>
      <table cellspacing="0" cellpadding="0">
        <tr style="font-family: monospace; font-size: 8pt;" align="center">
          <td align="right">
            Délka instrukce v bajtech →
          </td>
          
          <td>
          </td>
          
          <td class="withborder" style="width: 7em;">
            INS reg</p> 
            
            <p>
              2  7
            </p>
            
            <p>
              S Z A P C</td> 
              
              <td>
              </td>
              
              <td align="left">
                ← Označení instrukce<br /> ← Trvání v T cyklech<br /> ← Mění příznaky
              </td></tr> </tbody> </table> </td> 
              
              <td width="25">
              </td>
              
              <td style="width: 300px;">
                Trvání podmíněných instrukcí skoků v taktech procesoru (T) je různé, podle toho, jestli se skok provede, nebo ne. V tabulce je to označeno tak, že pro trvání instrukce jsou zapsána dvě čísla s lomítkem. To první, vyšší, před lomítkem udává trvání instrukce v případě, že podmínka byla splněna a procesor provádí skok. To druhé číslo je počet taktů v případě, že podmínka splněna není a procesor neskáče.</p> 
                
                <p>
                  Instrukce označené hvězdičkou jsou neoficiální a nedokumentované instrukce, které by neměly být používány.</td> </tr> </tbody> </table> 
                  
                  <p>
                    (Zdroj tabulky: <a href="http://pastraiser.com/cpu/i8080/i8080_opcodes.html">http://pastraiser.com/cpu/i8080/i8080_opcodes.html</a>)
                  </p>