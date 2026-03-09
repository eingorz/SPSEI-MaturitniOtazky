# Java
## Třída File
- Ukládá jen path atd.
## Čtení a psaní do souboru
### Třídy
- Pro Text:
	- FileReader
	- FileWriter
- Pro Binary
	- FileInputStream
	- FileOutputStream
# C
## Struktura FILE
- Struktura ukládající název, path atd.
- fclose() - musí se používat kvůli bufferu
``` C
FILE *f;
f = fopen("C:\\dokumenty\\I4C\\nazev.txt", "r");
//Může být aj relativní cesta obv
// fopen() vrací adresu na soubor
fputs(); // oba berou co napsat a pointer na soubor
fputc();
fprintf();

fscanf();
fgets();



fwrite(); //co vkladam (adresa), size, how many to write, file to write to) 
fread(); //v podstatě to samé
fflush();
// maže buffer
fseek();
//movuje ukazatel

``` 