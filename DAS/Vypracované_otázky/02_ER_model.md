---
tags: [maturita, das, er-model, database, design]
---

# 02. E-R model (Entity-Relationship Model)

## 1. Definice a účel

E-R model (Entity-Relationship Model) je **konceptuální datový model**, který slouží k grafickému znázornění struktury databáze na vysoké úrovni abstrakce.

**Cíle E-R modelu:**
*   Zachytit **entity** (objekty reálného světa), o kterých chceme uchovávat informace.
*   Popsat **vztahy** (relace) mezi těmito entitami.
*   Definovat **atributy** (vlastnosti) entit a vztahů.
*   Slouží jako podklad pro tvorbu **relačního schématu** (fyzické databáze).

**Notace:**
*   **Chenova notace:** Entity (obdélníky), Vztahy (kosočtverce), Atributy (elipsy). Klasická akademická notace.
*   **Crow's Foot (Vraní noha):** Entity (obdélníky), Vztahy (čáry se symboly kardinality). Standard v praxi (MySQL Workbench, Visio).

---

## 2. Základní stavební kameny

### A) Entita (Entity)
Reálný nebo abstraktní objekt, který je jednoznačně identifikovatelný a o kterém chceme uchovávat data.
*   **Příklad:** `Student`, `Kniha`, `Objednávka`, `Zákazník`.
*   **Entitní množina (Entity Set):** Skupina všech entit stejného typu (např. tabulka `Studenti`).
*   **Slabá entita (Weak Entity):** Nemá vlastní primární klíč a její existence závisí na jiné entitě (např. `Položka_Objednávky` závisí na `Objednávce`). V diagramu mívá dvojitý obrys.

### B) Atribut (Attribute)
Vlastnost, která popisuje entitu.
*   **Jednoduchý atribut:** Nedělitelný (např. `Věk`, `Cena`).
*   **Složený atribut (Composite):** Lze rozdělit na menší části (např. `Adresa` -> `Ulice`, `Město`, `PSČ`). V DB se obvykle rozpadne na samostatné sloupce.
*   **Vícehodnotový atribut (Multi-valued):** Může nabývat více hodnot pro jednu entitu (např. `Telefon`, `Email`). V relačním modelu se řeší samostatnou tabulkou (1:N).
*   **Odvozený atribut (Derived):** Hodnota se neukládá, ale vypočítává z jiných atributů (např. `Věk` z `Datum_narození`). V diagramu čárkovaně.
*   **Klíčový atribut (Key):** Jednoznačně identifikuje entitu (podtržený).

### C) Vztah (Relationship)
Vazba mezi dvěma nebo více entitami.
*   **Stupeň vztahu (Degree):**
    *   **Unární (Rekurzivní):** Vztah entity sama se sebou (např. `Zaměstnanec` je nadřízený jinému `Zaměstnanci`).
    *   **Binární:** Vztah mezi dvěma entitami (nejčastější).
    *   **Ternární:** Vztah mezi třemi entitami.

---

## 3. Kardinalita a Parcialita vztahů

### Kardinalita (Cardinality)
Určuje maximální počet instancí jedné entity, které mohou být ve vztahu s instancí druhé entity.

1.  **1:1 (One-to-One):** Jedné entitě A odpovídá právě jedna entita B a naopak.
    *   *Příklad:* `Ředitel` řídí `Školu` (jeden ředitel jednu školu).
    *   *Implementace:* Primární klíč jedné tabulky je cizím klíčem v druhé (často unikátním).

2.  **1:N (One-to-Many):** Jedné entitě A odpovídá více entit B, ale entitě B odpovídá jen jedna entita A.
    *   *Příklad:* `Třída` má `Žáky` (jedna třída má N žáků, žák patří do jedné třídy).
    *   *Implementace:* Primární klíč strany "1" se vloží jako cizí klíč do tabulky strany "N".

3.  **M:N (Many-to-Many):** Jedné entitě A odpovídá více entit B a entitě B odpovídá více entit A.
    *   *Příklad:* `Student` navštěvuje `Předměty` (student má N předmětů, předmět má M studentů).
    *   *Implementace:* Nelze realizovat přímo v relační DB. Musí se rozložit pomocí **vazební (asociační) tabulky** na dva vztahy 1:N.

### Parcialita (Participation / Modality)
Určuje, zda je účast entity ve vztahu povinná.
*   **Povinná (Total / Mandatory):** Každá instance entity A musí mít vztah s B (např. `Objednávka` musí mít `Zákazníka`). Značí se dvojitou čarou nebo svislou čárkou `|`.
*   **Nepovinná (Partial / Optional):** Instance entity A nemusí mít vztah s B (např. `Zaměstnanec` nemusí mít přiděleno `Služební auto`). Značí se jednou čarou nebo kroužkem `O`.

---

## 4. Převod E-R diagramu do relačního schématu

Proces transformace konceptuálního modelu (diagram) do logického modelu (tabulky).

1.  **Entity -> Tabulky:** Každá silná entita se stane tabulkou.
2.  **Atributy -> Sloupce:** Jednoduché atributy se stanou sloupci tabulky.
    *   *Složené atributy:* Rozpadnou se na jednotlivé sloupce.
    *   *Vícehodnotové atributy:* Vytvoří se nová tabulka s cizím klíčem.
3.  **Identifikátory -> Primární klíče (PK):** Klíčový atribut se stane PK.
4.  **Vztahy 1:N:** PK ze strany "1" se přidá jako cizí klíč (FK) do tabulky na straně "N".
5.  **Vztahy M:N:** Vytvoří se nová vazební tabulka.
    *   Její PK je složený z PK obou původních entit.
    *   Obsahuje případné atributy vztahu (např. `Známka` u vztahu `Student`-`Předmět`).
6.  **Slabé entity:** Převezmou PK své nadřazené entity jako část svého složeného PK.

---

## 5. Příklad (Škola)

**Zadání:** Evidujeme studenty, učitele a předměty.
*   Student chodí do jedné třídy (1:N).
*   Učitel učí více předmětů, předmět učí více učitelů (M:N).

**Entity:**
*   `Student` (ID, Jméno, *ID_Tridy*)
*   `Trida` (ID, Nazev)
*   `Ucitel` (ID, Jmeno)
*   `Predmet` (ID, Nazev)

**Vazební tabulka (pro M:N):**
*   `Rozvrh` (*ID_Ucitel*, *ID_Predmet*, Den, Hodina) - Zde se rozpadl vztah M:N na dva vztahy 1:N.
