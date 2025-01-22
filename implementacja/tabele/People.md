# Sekcja People
## Tabela: `Translator`
Przechowuje informacje o tłumaczach.

- `TranslatorID` *(int, NOT NULL)*: Unikalny identyfikator tłumacza.
- `FirstName` *(varchar(50), NOT NULL)*: Imię tłumacza.
- `LastName` *(varchar(50), NOT NULL)*: Nazwisko tłumacza.
- `DateOfBirth` *(date, NOT NULL)*: Data urodzenia tłumacza.
- `Country` *(varchar(50), NOT NULL)*: Kraj tłumacza.
- `City` *(varchar(50), NOT NULL)*: Miasto tłumacza.
- `Address` *(varchar(50), NOT NULL)*: Adres tłumacza.
- `Mail` *(varchar(50), NOT NULL)*: Adres e-mail tłumacza.
- `Phone` *(varchar(15), NOT NULL)*: Numer telefonu tłumacza.
- **Klucze i ograniczenia:**
  - `Translator_pk`: Klucz główny na kolumnie `TranslatorID`.
  - `chk_translator_validations`: Ograniczenia sprawdzające, że:
    - Tłumacz ma co najmniej 18 lat.
    - Wszystkie pola mają co najmniej 1 znak.

```sql
CREATE TABLE Translator (
    TranslatorID int NOT NULL,
    FirstName varchar(50) NOT NULL,
    LastName varchar(50) NOT NULL,
    DateOfBirth date NOT NULL,
    Country varchar(50) NOT NULL,
    City varchar(50) NOT NULL,
    Address varchar(50) NOT NULL,
    Mail varchar(50) NOT NULL,
    Phone varchar(15) NOT NULL,
    CONSTRAINT Translator_pk PRIMARY KEY (TranslatorID),
    CONSTRAINT chk_translator_validations CHECK (
        DATEDIFF(YEAR, DateOfBirth, GETDATE()) >= 18 AND
        LEN(FirstName) >= 1 AND
        LEN(LastName) >= 1 AND
        LEN(Country) >= 1 AND
        LEN(City) >= 1 AND
        LEN(Address) >= 1 AND
        LEN(Mail) >= 1 AND
        LEN(Phone) >= 1
    )
);
```
## Tabela: `Employees`
Przechowuje informacje o pracownikach.

- `EmployeeID` *(int, NOT NULL, IDENTITY)*: Unikalny identyfikator pracownika.
- `FirstName` *(varchar(50), NOT NULL)*: Imię pracownika.
- `LastName` *(varchar(50), NOT NULL)*: Nazwisko pracownika.
- `DateOfBirth` *(date, NOT NULL)*: Data urodzenia pracownika.
- `Country` *(varchar(50), NOT NULL)*: Kraj pracownika.
- `City` *(varchar(50), NOT NULL)*: Miasto pracownika.
- `Address` *(varchar(50), NOT NULL)*: Adres pracownika.
- `Mail` *(varchar(50), NOT NULL)*: Adres e-mail pracownika.
- `Phone` *(varchar(15), NOT NULL)*: Numer telefonu pracownika.
- **Klucze i ograniczenia:**
  - `Employees_pk`: Klucz główny na kolumnie `EmployeeID`.
  - `chk_Employees`: Ograniczenia sprawdzające, że:
    - Pracownik ma co najmniej 18 lat.
    - Wszystkie pola mają co najmniej 1 znak.

```sql
CREATE TABLE Employees (
    EmployeeID int NOT NULL IDENTITY,
    FirstName varchar(50) NOT NULL,
    LastName varchar(50) NOT NULL,
    DateOfBirth date NOT NULL,
    Country varchar(50) NOT NULL,
    City varchar(50) NOT NULL,
    Address varchar(50) NOT NULL,
    Mail varchar(50) NOT NULL,
    Phone varchar(15) NOT NULL,
    CONSTRAINT Employees_pk PRIMARY KEY (EmployeeID),
    CONSTRAINT chk_Employees CHECK (
        DATEDIFF(YEAR, DateOfBirth, GETDATE()) >= 18 AND
        LEN(FirstName) >= 1 AND
        LEN(LastName) >= 1 AND
        LEN(Country) >= 1 AND
        LEN(City) >= 1 AND
        LEN(Address) >= 1 AND
        LEN(Mail) >= 1 AND
        LEN(Phone) >= 1
    )
);
```

## Tabela: `EmployeeType`
Przechowuje informacje o typach pracowników, tj. ich stanowiskach.

- `EmployeeID` *(int, NOT NULL)*: Unikalny identyfikator pracownika (klucz obcy).
- `HeldPosition` *(varchar(50), NOT NULL)*: Stanowisko, które pracownik zajmuje.
- **Klucze i ograniczenia:**
  - `EmployeeType_pk`: Klucz główny na kolumnie `EmployeeID`.
  - `chk_EmployeeType`: Ograniczenie sprawdzające, że `HeldPosition` zawiera co najmniej 1 znak.

```sql
CREATE TABLE EmployeeType (
    EmployeeID int NOT NULL,
    HeldPosition varchar(50) NOT NULL,
    CONSTRAINT EmployeeType_pk PRIMARY KEY (EmployeeID),
    CONSTRAINT chk_EmployeeType CHECK (
        LEN(HeldPosition) >= 1
    )
);
```

## Tabela: `Students`
Przechowuje informacje o studentach.

- `StudentID` *(int, NOT NULL, IDENTITY)*: Unikalny identyfikator studenta.
- `FirstName` *(varchar(50), NOT NULL)*: Imię studenta.
- `LastName` *(varchar(50), NOT NULL)*: Nazwisko studenta.
- `DateOfBirth` *(date, NOT NULL)*: Data urodzenia studenta.
- `Country` *(varchar(50), NOT NULL)*: Kraj studenta.
- `City` *(varchar(50), NOT NULL)*: Miasto studenta.
- `Address` *(varchar(50), NOT NULL)*: Adres studenta.
- `Mail` *(varchar(50), NOT NULL)*: Adres e-mail studenta.
- `Phone` *(varchar(15), NOT NULL)*: Numer telefonu studenta.
- **Klucze i ograniczenia:**
  - `Students_pk`: Klucz główny na kolumnie `StudentID`.
  - `chk_Students`: Ograniczenia sprawdzające, że:
    - Student ma co najmniej 16 lat.
    - Wszystkie pola mają co najmniej 1 znak.

```sql
CREATE TABLE Students (
    StudentID int NOT NULL IDENTITY,
    FirstName varchar(50) NOT NULL,
    LastName varchar(50) NOT NULL,
    DateOfBirth date NOT NULL,
    Country varchar(50) NOT NULL,
    City varchar(50) NOT NULL,
    Address varchar(50) NOT NULL,
    Mail varchar(50) NOT NULL,
    Phone varchar(15) NOT NULL,
    CONSTRAINT Students_pk PRIMARY KEY (StudentID),
    CONSTRAINT chk_Students CHECK (
        DATEDIFF(YEAR, DateOfBirth, GETDATE()) >= 16 AND
        LEN(FirstName) >= 1 AND
        LEN(LastName) >= 1 AND
        LEN(Country) >= 1 AND
        LEN(City) >= 1 AND
        LEN(Address) >= 1 AND
        LEN(Mail) >= 1 AND
        LEN(Phone) >= 1
    )
);

```
## Tabela: `LectureRoomDetails`
Przechowuje szczegóły dotyczące sal wykładowych.

- `RoomID` *(int, NOT NULL)*: Unikalny identyfikator sali wykładowej.
- `BuildingNr` *(varchar(10), NOT NULL)*: Numer budynku, w którym znajduje się sala.
- `Floor` *(int, NOT NULL)*: Piętro, na którym znajduje się sala.
- `ClassNumber` *(int, NOT NULL)*: Numer klasy, w której odbywają się zajęcia.
- **Klucze i ograniczenia:**
  - `LectureRoomDetails_pk`: Klucz główny na kolumnie `RoomID`.
  - `chk_LectureRoomDetails`: Ograniczenie sprawdzające, że:
    - Numer budynku ma co najmniej 1 znak.
    - Numer klasy jest większy lub równy 0.

```sql
CREATE TABLE LectureRoomDetails (
    RoomID int NOT NULL,
    BuildingNr varchar(10) NOT NULL,
    Floor int NOT NULL,
    ClassNumber int NOT NULL,
    CONSTRAINT LectureRoomDetails_pk PRIMARY KEY (RoomID),
    CONSTRAINT chk_LectureRoomDetails CHECK (
        LEN(BuildingNr) >= 1 AND
        ClassNumber >= 0
    )
);
```

## Tabela: `Languages`
Przechowuje informacje o językach, które są przypisane do tłumaczy.

- `TranslatorID` *(int, NOT NULL)*: Unikalny identyfikator tłumacza (klucz obcy).
- `LanguageID` *(int, NOT NULL)*: Unikalny identyfikator języka.
- **Klucze i ograniczenia:**
  - `LanguageID_pk`: Klucz główny na kombinacji kolumn `TranslatorID` i `LanguageID`.

```sql
CREATE TABLE Languages (
    TranslatorID int NOT NULL,
    LanguageID int NOT NULL,
    CONSTRAINT LanguageID_pk PRIMARY KEY (TranslatorID, LanguageID)
);
```

## Tabela: `AvailableLanguages`
Przechowuje dostępne języki w systemie.

- `LanguageID` *(int, NOT NULL)*: Unikalny identyfikator języka.
- `Language` *(varchar(50), NOT NULL)*: Nazwa języka.
- **Klucze i ograniczenia:**
  - `AvailableLanguages_pk`: Klucz główny na kolumnie `LanguageID`.
  - `chk_AvailableLanguages`: Ograniczenie sprawdzające, że:
    - Nazwa języka ma co najmniej 1 znak.

```sql
CREATE TABLE AvailableLanguages (
    LanguageID int NOT NULL,
    Language varchar(50) NOT NULL,
    CONSTRAINT AvailableLanguages_pk PRIMARY KEY (LanguageID),
    CONSTRAINT chk_AvailableLanguages CHECK (
        LEN(Language) >= 1
    )
);
```