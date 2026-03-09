## Cypoviny z testů
- Priorita operátorů
```C
if(0.1+0.2 == 0.3) // Vyjde false, protoze Cecko prioritizuje porovnani
``` 
- union -> Jedno místo v paměti, pro které máme více názvů (v union si definujeme víc proměnných a kompilátor naalokuje pouze datový typ co zabírá nejvíc místa)
- globalni promenne -> jdou pouzit od mista deklarace dal, inicializuje se na 0 a da se pouzit v jinem modulu pomoci extern
## Extra funkce

### Stringy
#include <ctype.h>
strchr(string, character) - hledá, zda je character ve stringu