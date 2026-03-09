---
tags: [maturita, das, mysql, database, theory]
---

# 01. Databázový systém (DAS)

## 1. Definice a základní pojmy

Databázový systém (DBS) je integrovaný soubor dat a softwarových nástrojů, které umožňují efektivní správu, ukládání, vyhledávání a manipulaci s těmito daty.

**DBS se skládá ze dvou hlavních komponent:**
1.  **Databáze (DB):** Samotná data uložená na trvalém paměťovém médiu (HDD, SSD). V relačním modelu jsou organizována do tabulek.
2.  **Systém řízení báze dat (SŘBD / DBMS - Database Management System):** Softwarová vrstva, která zajišťuje rozhraní mezi uživatelem (nebo aplikací) a daty.

### Klíčové vlastnosti SŘBD (DBMS)
SŘBD musí zajišťovat:
*   **Abstrakci dat:** Oddělení fyzického uložení dat od logického pohledu uživatele.
*   **Integritu dat:** Dodržování definovaných pravidel a omezení (např. unikátnost klíčů, datové typy).+
*   **Bezpečnost:** Řízení přístupových práv a autentizaci uživatelů.
*   **Souběžný přístup (Concurrency):** Správné zpracování požadavků od více uživatelů najednou bez kolizí.
*   **Zotavení (Recovery):** Schopnost obnovit data po havárii (výpadek proudu, chyba disku).
*   **Transakční zpracování:** Zajištění atomicity operací (ACID).

---

## 2. Architektura databázových systémů

### A) Architektura File-Server (Desktopové databáze)
*   **Příklad:** Microsoft Access, dBase, FoxPro.
*   **Princip:** Celý databázový soubor se přenáší po síti ke klientovi, kde probíhá zpracování.
*   **Nevýhody:** Vysoká zátěž sítě, riziko poškození dat při výpadku klienta, slabší zabezpečení.

### B) Architektura Client-Server (SQL servery)
*   **Příklad:** MySQL, PostgreSQL, Oracle, MS SQL Server.
*   **Princip:**
    *   **Server:** Běží na něm SŘBD, spravuje data, vykonává dotazy, zajišťuje bezpečnost a integritu.
    *   **Klient:** Posílá požadavky (SQL dotazy) a přijímá pouze výsledná data.
*   **Výhody:** Nízká zátěž sítě (přenáší se jen dotaz a výsledek), vysoká bezpečnost, centralizovaná správa, transakční zpracování.

### C) Třívrstvá architektura (Multi-tier)
*   **Klient (Prezentační vrstva):** Webový prohlížeč nebo aplikace.
*   **Aplikační server (Logická vrstva):** Zpracovává byznys logiku (např. PHP, Java, Python skripty), komunikuje s DB.
*   **Databázový server (Datová vrstva):** Ukládá data (MySQL).

---

## 3. Transakční zpracování a vlastnosti ACID

Transakce je posloupnost databázových operací, která je z hlediska systému považována za **jeden nedělitelný celek**. Aby byl databázový systém považován za spolehlivý, musí splňovat vlastnosti **ACID**:

### 1. Atomicity (Atomicita - Nedělitelnost)
Transakce proběhne buď celá, nebo vůbec. Pokud nastane chyba v polovině transakce, všechny změny se musí vrátit zpět (ROLLBACK).
*   *MySQL příkaz:* `START TRANSACTION`, `COMMIT` (potvrzení), `ROLLBACK` (vrácení změn).

### 2. Consistency (Konzistence - Správnost)
Transakce převádí databázi z jednoho validního (konzistentního) stavu do druhého. Musí být splněna všechna integritní omezení (cizí klíče, unikátnost, datové typy, triggery).

### 3. Isolation (Izolace - Nezávislost)
Transakce běžící současně se nesmí vzájemně ovlivňovat. Výsledky transakce nejsou viditelné pro ostatní transakce, dokud není potvrzena (`COMMIT`).
*   *MySQL Isolation Levels:*
    *   `READ UNCOMMITTED` (nejnižší, "dirty reads")
    *   `READ COMMITTED` (běžné)
    *   `REPEATABLE READ` (výchozí v MySQL/InnoDB - zaručuje konzistentní čtení v rámci transakce)
    *   `SERIALIZABLE` (nejvyšší, zamyká řádky, pomalé)

### 4. Durability (Trvalost)
Jakmile je transakce potvrzena (`COMMIT`), změny jsou trvale uloženy a nesmí být ztraceny ani při výpadku napájení nebo havárii systému. To se zajišťuje pomocí transakčních logů (Write-Ahead Logging).

---

## 4. MySQL Specifika: Storage Engines (Úložiště)

MySQL je unikátní svou architekturou **Pluggable Storage Engines**. Uživatel si může vybrat, jakým způsobem se data pro konkrétní tabulku ukládají.

### A) InnoDB (Výchozí a doporučený)
*   **Podpora transakcí:** ANO (plná podpora ACID).
*   **Cizí klíče (Foreign Keys):** ANO (referenční integrita).
*   **Zamykání:** Row-level locking (zamyká pouze konkrétní řádky, ne celou tabulku -> vysoká propustnost při zápisu).
*   **Crash Recovery:** Automatická obnova po pádu díky logům.
*   **Použití:** Všechny moderní aplikace vyžadující spolehlivost a relace.

### B) MyISAM (Starší, dnes již méně používaný)
*   **Podpora transakcí:** NE (operace jsou atomické pouze na úrovni jednoho příkazu).
*   **Cizí klíče:** NE (ignoruje definice cizích klíčů).
*   **Zamykání:** Table-level locking (při zápisu zamkne celou tabulku -> nevhodné pro časté zápisy).
*   **Výhody:** Rychlejší čtení v specifických případech, fulltextové vyhledávání (ve starších verzích MySQL, InnoDB už to umí také).
*   **Použití:** Archivní data, read-only tabulky, logovací tabulky.

### C) Memory (HEAP)
*   Data jsou uložena pouze v RAM. Při restartu serveru se data ztratí (struktura tabulky zůstane).
*   Extrémně rychlé, vhodné pro dočasné výpočty nebo cache.

---

## 5. Uživatelé databázového systému

1.  **Databázový administrátor (DBA):** Odpovídá za instalaci, konfiguraci, zálohování, optimalizaci výkonu a správu uživatelských práv (`GRANT`, `REVOKE`).
2.  **Databázový návrhář (Designer):** Navrhuje logické schéma (ER diagramy), definuje tabulky, relace a integritní omezení.
3.  **Aplikační programátor:** Píše aplikace komunikující s DB (používá SQL v kódu - PHP, Java, C#).
4.  **Koncový uživatel:** Pracuje s daty prostřednictvím aplikace (nevidí SQL, nezná strukturu).

---

## 6. Jazyk SQL (Structured Query Language)

Standardizovaný jazyk pro komunikaci s relačními databázemi. Dělí se na:

*   **DDL (Data Definition Language):** Definice struktury (`CREATE`, `ALTER`, `DROP`, `TRUNCATE`).
*   **DML (Data Manipulation Language):** Práce s daty (`SELECT`, `INSERT`, `UPDATE`, `DELETE`).
*   **DCL (Data Control Language):** Práva (`GRANT`, `REVOKE`).
*   **TCL (Transaction Control Language):** Řízení transakcí (`COMMIT`, `ROLLBACK`, `SAVEPOINT`).
