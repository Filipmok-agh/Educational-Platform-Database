# Sekcja Webinars
## Tabela: `Webinar`
Przechowuje informacje o webinarach.

- `WebinarID` *(int, NOT NULL)*: Unikalny identyfikator webinaru.
- `WebinarName` *(varchar(50), NOT NULL)*: Nazwa webinaru.
- `Price` *(money, NOT NULL)*: Cena uczestnictwa w webinarze.
- `Webinar_date` *(datetime, NOT NULL)*: Data i godzina odbywania się webinaru.
- `LanguageID` *(int, NOT NULL)*: Identyfikator języka, w którym odbywa się webinar (klucz obcy).
- `TranslatorID` *(int, NULL)*: Identyfikator tłumacza (opcjonalnie), który tłumaczy webinar.
- `EmployeeID` *(int, NOT NULL)*: Identyfikator pracownika organizującego webinar (klucz obcy).
- `OnlineLink` *(varchar(max), NULL)*: Link do uczestnictwa w webinarze na żywo.
- `VideoLink` *(varchar(max), NULL)*: Link do nagrania webinaru.
- **Klucze i ograniczenia:**
  - `Webinar_pk`: Klucz główny na kolumnie `WebinarID`.
  - `chk_Webinar`: Ograniczenie sprawdzające, że:
    - Cena webinaru jest większa lub równa 0.
    - Nazwa webinaru ma co najmniej 1 znak.

```sql
CREATE TABLE Webinar (
    WebinarID int NOT NULL IDENTITY(1,1),
    WebinarName varchar(50) NOT NULL,
    Price money NOT NULL,
    Webinar_date datetime NOT NULL,
    LanguageID int NOT NULL,
    TranslatorID int NULL,
    EmployeeID int NOT NULL,
    OnlineLink varchar(max) NULL,
    VideoLink varchar(max) NULL,
    CONSTRAINT Webinar_pk PRIMARY KEY (WebinarID),
    CONSTRAINT chk_Webinar CHECK (Price >= 0 AND LEN(WebinarName) >= 1)
);
```

## Tabela: `WebinarExpirationDate`
Przechowuje informacje o dacie wygaśnięcia dostępu do webinaru dla studenta.

- `WebinarID` *(int, NOT NULL)*: Unikalny identyfikator webinaru (klucz obcy).
- `StudentID` *(int, NOT NULL)*: Unikalny identyfikator studenta (klucz obcy).
- `expr_date` *(date, NULL)*: Data wygaśnięcia dostępu do webinaru dla studenta.
- **Klucze i ograniczenia:**
  - `WebinarExpirationDate_pk`: Klucz główny na kombinacji kolumn `WebinarID` i `StudentID`.

```sql
CREATE TABLE WebinarExpirationDate (
    WebinarID int NOT NULL,
    StudentID int NOT NULL,
    expr_date date NULL,
    CONSTRAINT WebinarExpirationDate_pk PRIMARY KEY (WebinarID, StudentID)
);
```

```