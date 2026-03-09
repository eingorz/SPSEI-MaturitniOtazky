---
tags: [maturita, das, normalization, database, design]
---
# 03. Fáze definování databáze, normalizace (DAS)

## 1. Životní cyklus databáze (Fáze návrhu)

Tvorba databáze není jednorázová akce, ale proces, který probíhá v několika fázích. Cílem je převést požadavky z reálného světa do funkčního databázového systému.

### A) Konceptuální návrh (Conceptual Design)
*   **Cíl:** Vytvořit model nezávislý na konkrétním softwaru (SŘBD) i hardwaru.
*   **Výstup:** **E-R diagram** (Entity-Relationship).
*   **Činnosti:**
    *   Sběr požadavků od uživatelů (analýza).
    *   Identifikace entit (objektů) a jejich atributů.
    *   Určení vztahů mezi entitami a jejich kardinality (1:1, 1:N, M:N).
*   *Poznámka:* V této fázi neřešíme datové typy ani cizí klíče, řešíme "co" tam bude, ne "jak".

### B) Logický návrh (Logical Design)
*   **Cíl:** Převést konceptuální model do struktury konkrétního databázového modelu (nejčastěji relačního).
*   **Výstup:** **Relační schéma** (sada tabulek).
*   **Činnosti:**
    *   Transformace entit na tabulky.
    *   Transformace atributů na sloupce (atributy).
    *   Určení **Primárních klíčů (PK)** a **Cizích klíčů (FK)** pro propojení tabulek.
    *   Rozklad vztahů M:N na vazební tabulky.
    *   **Normalizace** (odstranění redundancí).

### C) Fyzický návrh (Physical Design)
*   **Cíl:** Implementace logického modelu v konkrétním SŘBD (např. MySQL, Oracle, MS Access).
*   **Výstup:** SQL skript (`CREATE TABLE...`) a fyzické soubory na disku.
*   **Činnosti:**
    *   Volba konkrétních datových typů (`INT`, `VARCHAR(50)`, `DATE`).
    *   Vytvoření indexů pro zrychlení vyhledávání.
    *   Nastavení přístupových práv a pohledů (`VIEW`).
    *   Volba úložiště (Storage Engine - např. InnoDB vs MyISAM).

---

## 2. Normalizace databáze

Normalizace je proces organizování dat v databázi. Zahrnuje vytváření tabulek a navazování vztahů mezi nimi podle pravidel (normálních forem).

### Cíle normalizace:
1.  **Eliminace redundance:** Zamezení zbytečnému opakování dat (např. ukládat adresu zákazníka u každé jeho objednávky znovu a znovu).
2.  **Zajištění integrity dat:** Aby změna údaje na jednom místě (např. změna příjmení) nevyžadovala úpravu tisíců řádků.
3.  **Odstranění anomálií:**
    *   *Vkládací (Insert) anomálie:* Nelze vložit záznam, protože chybí doplňující údaj (např. nelze založit kurz, dokud se nepřihlásí student).
    *   *Aktualizační (Update) anomálie:* Při změně údaje se musí aktualizovat mnoho řádků, riziko chyby.
    *   *Mazací (Delete) anomálie:* Smazáním záznamu přijdeme o jinou informaci (např. smazáním posledního studenta kurzu smažeme i informace o kurzu samotném).

---

## 3. Normální formy (Normal Forms - NF)

Existuje jich více (až po 6NF), ale v praxi se pro relační databáze vyžaduje splnění **Třetí normální formy (3NF)**.

### 1. První normální forma (1NF) - Atomicita
*   **Pravidlo:** Každá buňka v tabulce musí obsahovat pouze **jednu (atomickou) hodnotu**. Nesmí obsahovat seznamy nebo opakující se skupiny.
*   *Příklad porušení:* Ve sloupci `Telefon` je napsáno "777123456, 608987654".
*   *Řešení:* Rozdělit do více řádků nebo (lépe) vytvořit samostatnou tabulku `Telefony`.

### 2. Druhá normální forma (2NF) - Úplná závislost
*   **Pravidlo:** Tabulka musí být v 1NF a všechny neklíčové atributy musí být plně závislé na **celém primárním klíči**.
*   *Týká se pouze tabulek se složeným primárním klíčem* (klíč tvoří více sloupců).
*   *Příklad porušení:* Tabulka `Znamky` má klíč (`ID_Student`, `ID_Predmet`). Obsahuje sloupec `Jmeno_Studenta`. Jméno závisí jen na `ID_Student` (část klíče), ne na předmětu.
*   *Řešení:* Přesunout `Jmeno_Studenta` do samostatné tabulky `Studenti`.

### 3. Třetí normální forma (3NF) - Žádná tranzitivní závislost
*   **Pravidlo:** Tabulka musí být ve 2NF a žádný neklíčový atribut nesmí záviset na jiném neklíčovém atributu.
*   *Lidsky:* "Všechny sloupce musí záviset na klíči, celém klíči a ničem jiném než na klíči."
*   *Příklad porušení:* Tabulka `Objednavky` obsahuje sloupce (`ID_Objednavky`, `Datum`, `ID_Zakaznika`, `Mesto_Zakaznika`).
    *   `Mesto_Zakaznika` závisí na `ID_Zakaznika` (což není primární klíč objednávky), ne přímo na `ID_Objednavky`.
*   *Řešení:* `Mesto_Zakaznika` přesunout do tabulky `Zakaznici`, v objednávce nechat jen `ID_Zakaznika` (cizí klíč).

---

## 4. Denormalizace

Někdy záměrně porušíme pravidla normalizace (vrátíme se z 3NF do nižší formy).
*   **Důvod:** Zvýšení výkonu (rychlosti) čtení.
*   **Princip:** Normalizace vede k mnoha tabulkám, které se musí spojovat (`JOIN`), což je výpočetně náročné. Denormalizace přidá redundanci (duplicitní data), ale umožní vybrat data z jedné tabulky bez spojování.
*   **Typické použití:** Datové sklady (Data Warehouses), analytické dotazy, počítaná pole (např. uložení `Celkova_Cena` přímo do objednávky, aby se nemusela pokaždé sčítat z položek).
