# Sekcji Studies


## Tabela: `FieldOfStudy`
Przechowuje informacje o dziedzinach nauki.

- `FieldOfStudyID` *(int, NOT NULL)*: Unikalny identyfikator dziedziny nauki.
- `Name` *(varchar(50), NOT NULL)*: Nazwa dziedziny nauki.
- `Description` *(varchar(50), NOT NULL)*: Opis dziedziny nauki.
- `Limit` *(int, NOT NULL)*: Limit liczby studentów w danej dziedzinie.
- `EntryFee` *(money, NOT NULL)*: Opłata rejestracyjna za udział w dziedzinie.
- **Klucze i ograniczenia:**
  - `FieldOfStudy_pk`: Klucz główny na kolumnie `FieldOfStudyID`.
  - `chk_FieldOfStudy`: Ograniczenie sprawdzające, że:
    - Nazwa i opis dziedziny mają co najmniej 1 znak.
    - Opłata rejestracyjna oraz limit są większe lub równe 0.

```sql
CREATE TABLE FieldOfStudy (
    FieldOfStudyID int NOT NULL,
    Name varchar(50) NOT NULL,
    Description varchar(50) NOT NULL,
    Limit int NOT NULL,
    EntryFee money NOT NULL,
    CONSTRAINT FieldOfStudy_pk PRIMARY KEY (FieldOfStudyID),
    CONSTRAINT chk_FieldOfStudy CHECK (
        LEN(Name) >= 1 AND
        LEN(Description) >= 1 AND
        EntryFee >= 0 AND
        Limit >= 0
    )
);
```

## Tabela: `FieldOfStudyStudentList`
Przechowuje informacje o studentach zapisanych na określoną dziedzinę nauki oraz szczegóły dotyczące ich semestru.

- `FieldOfStudyID` *(int, NOT NULL)*: Unikalny identyfikator dziedziny nauki (klucz obcy).
- `StudentID` *(int, NOT NULL)*: Unikalny identyfikator studenta (klucz obcy).
- `Semester` *(int, NOT NULL)*: Numer semestru, na którym student jest zapisany.
- `StartDate` *(date, NOT NULL)*: Data rozpoczęcia nauki w danej dziedzinie.
- `EndDate` *(date, NULL)*: Data zakończenia nauki w danej dziedzinie (opcjonalnie).
- **Klucze i ograniczenia:**
  - `FieldOfStudyStudentList_pk`: Klucz główny na kombinacji kolumn `StudentID` i `FieldOfStudyID`.
  - `chk_FieldOfStudyStudentList`: Ograniczenie sprawdzające, że:
    - Numer semestru jest większy lub równy 0.
    - Jeśli `EndDate` jest określona, to `StartDate` musi być wcześniejsze niż `EndDate`.

```sql
CREATE TABLE FieldOfStudyStudentList (
    FieldOfStudyID int NOT NULL,
    StudentID int NOT NULL,
    Semester int NOT NULL,
    StartDate date NOT NULL,
    EndDate date NULL,
    CONSTRAINT FieldOfStudyStudentList_pk PRIMARY KEY (StudentID, FieldOfStudyID),
    CONSTRAINT chk_FieldOfStudyStudentList CHECK (
        Semester >= 0 AND
        (EndDate IS NULL OR StartDate < EndDate)
    )
);
```

## Tabela: `Subjects`
Przechowuje informacje o przedmiotach w danej dziedzinie nauki.

- `SubjectID` *(int, NOT NULL)*: Unikalny identyfikator przedmiotu.
- `FieldOfStudyID` *(int, NOT NULL)*: Unikalny identyfikator dziedziny nauki, do której należy przedmiot (klucz obcy).
- `SubjectName` *(varchar(50), NOT NULL)*: Nazwa przedmiotu.
- `Description` *(varchar(50), NOT NULL)*: Opis przedmiotu.
- `MeetingsQuantity` *(int, NOT NULL)*: Liczba spotkań związanych z danym przedmiotem.
- `EmployeeID` *(int, NOT NULL)*: Identyfikator pracownika odpowiedzialnego za przedmiot (klucz obcy).
- `Semester` *(int, NOT NULL)*: Numer semestru, w którym przedmiot jest realizowany.
- **Klucze i ograniczenia:**
  - `Subjects_pk`: Klucz główny na kolumnie `SubjectID`.
  - `chk_Subjects`: Ograniczenie sprawdzające, że:
    - Nazwa i opis przedmiotu mają co najmniej 1 znak.
    - Liczba spotkań jest większa niż 0.
    - Numer semestru jest większy lub równy 0.

```sql
-- Table: Subjects
CREATE TABLE Subjects (
    SubjectID int NOT NULL,
    FieldOfStudyID int NOT NULL,
    SubjectName varchar(50) NOT NULL,
    Description varchar(50) NOT NULL,
    MeetingsQuantity int NOT NULL,
    EmployeeID int NOT NULL,
    Semester int NOT NULL,
    CONSTRAINT Subjects_pk PRIMARY KEY (SubjectID),
    CONSTRAINT chk_Subjects CHECK (
        LEN(SubjectName) >= 1 AND
        LEN(Description) >= 1 AND
        MeetingsQuantity > 0 AND
        Semester >= 0
    )
);
```

## Tabela: `SubjectGrades`
Przechowuje informacje o ocenach studentów z przedmiotów.

- `SubjectID` *(int, NOT NULL)*: Unikalny identyfikator przedmiotu (klucz obcy).
- `StudentID` *(int, NOT NULL)*: Unikalny identyfikator studenta (klucz obcy).
- `Grade` *(int, NOT NULL)*: Ocena przypisana studentowi z przedmiotu.
- **Klucze i ograniczenia:**
  - `SubjectGrades_pk`: Klucz główny na kombinacji kolumn `SubjectID` i `StudentID`.
  - `chk_SubjectGrades`: Ograniczenie sprawdzające, że:
    - Ocena jest większa lub równa 0.

```sql
CREATE TABLE SubjectGrades (
    SubjectID int NOT NULL,
    StudentID int NOT NULL,
    Grade int NOT NULL,
    CONSTRAINT SubjectGrades_pk PRIMARY KEY (SubjectID, StudentID),
    CONSTRAINT chk_SubjectGrades CHECK (
        Grade >= 0
    )
);
```

## Tabela: `Meeting`
Przechowuje informacje o spotkaniach związanych z przedmiotami.

- `MeetingID` *(int, NOT NULL)*: Unikalny identyfikator spotkania.
- `MeetingTypeID` *(int, NOT NULL)*: Typ spotkania (np. wykład, ćwiczenia).
- `SubjectID` *(int, NOT NULL)*: Unikalny identyfikator przedmiotu, z którym związane jest spotkanie (klucz obcy).
- `Meeting_date` *(datetime, NOT NULL)*: Data i godzina spotkania.
- `Link` *(varchar(max), NULL)*: Link do spotkania online (opcjonalnie).
- `RoomID` *(int, NULL)*: Identyfikator sali, w której odbywa się spotkanie (opcjonalnie).
- `LanguageID` *(int, NOT NULL)*: Identyfikator języka, w którym odbywa się spotkanie (klucz obcy).
- `TranslatorID` *(int, NULL)*: Identyfikator tłumacza (opcjonalnie).
- `Price` *(money, NOT NULL)*: Cena za uczestnictwo w spotkaniu.
- `StationaryID` *(int, NULL)*: Identyfikator stacjonarnego narzędzia (opcjonalnie).
- **Klucze i ograniczenia:**
  - `Meeting_pk`: Klucz główny na kolumnie `MeetingID`.
  - `chk_Meeting`: Ograniczenie sprawdzające, że:
    - Cena spotkania jest większa lub równa 0.

```sql
CREATE TABLE Meeting (
    MeetingID int NOT NULL,
    MeetingTypeID int NOT NULL,
    SubjectID int NOT NULL,
    Meeting_date datetime NOT NULL,
    Link varchar(max) NULL,
    RoomID int NULL,
    LanguageID int NOT NULL,
    TranslatorID int NULL,
    Price money NOT NULL,
    StationaryID int NULL,
    CONSTRAINT Meeting_pk PRIMARY KEY (MeetingID),
    CONSTRAINT chk_Meeting CHECK (
        Price >= 0
    )
);
```

## Tabela: `StudentAbsence`
Przechowuje informacje o nieobecnościach studentów na spotkaniach.

- `MeetingID` *(int, NOT NULL)*: Unikalny identyfikator spotkania, na którym student był nieobecny (klucz obcy).
- `StudentID` *(int, NOT NULL)*: Unikalny identyfikator studenta, który był nieobecny (klucz obcy).
- `ClassRetakeID` *(int, NULL)*: Identyfikator egzaminu poprawkowego lub innego rozwiązania w przypadku nieobecności (opcjonalnie).
- **Klucze i ograniczenia:**
  - `StudentAbsence_pk`: Klucz główny na kombinacji kolumn `MeetingID` i `StudentID`.

```sql
CREATE TABLE StudentAbsence (
    MeetingID int NOT NULL,
    StudentID int NOT NULL,
    ClassRetakeID int NULL,
    CONSTRAINT StudentAbsence_pk PRIMARY KEY (MeetingID, StudentID)
);
```

## Tabela: `Interships`
Przechowuje informacje o praktykach związanych z dziedzinami nauki.

- `IntershipID` *(int, NOT NULL)*: Unikalny identyfikator praktyk.
- `FieldOfStudyID` *(int, NOT NULL)*: Unikalny identyfikator dziedziny nauki, do której przypisane są praktyki (klucz obcy).
- `IntershipName` *(varchar(50), NOT NULL)*: Nazwa praktyk.
- `StartDate` *(date, NOT NULL)*: Data rozpoczęcia praktyk.
- `EndDate` *(date, NOT NULL)*: Data zakończenia praktyk.
- **Klucze i ograniczenia:**
  - `Interships_pk`: Klucz główny na kolumnie `IntershipID`.
  - `chk_Interships`: Ograniczenie sprawdzające, że:
    - Nazwa praktyk ma co najmniej 1 znak.
    - Data rozpoczęcia jest wcześniejsza niż data zakończenia.

```sql
CREATE TABLE Interships (
    IntershipID int NOT NULL,
    FieldOfStudyID int NOT NULL,
    IntershipName varchar(50) NOT NULL,
    StartDate date NOT NULL,
    EndDate date NOT NULL,
    CONSTRAINT Interships_pk PRIMARY KEY (IntershipID),
    CONSTRAINT chk_Interships CHECK (
        LEN(IntershipName) >= 1 AND
        StartDate < EndDate
    )
);
```

## Tabela: `IntershipsAbsence`
Przechowuje informacje o nieobecnościach studentów na praktykach.

- `IntershipID` *(int, NOT NULL)*: Unikalny identyfikator praktyk, na których student był nieobecny (klucz obcy).
- `StudentID` *(int, NOT NULL)*: Unikalny identyfikator studenta, który był nieobecny (klucz obcy).
- `Absence` *(datetime, NOT NULL)*: Data i godzina nieobecności studenta.
- **Klucze i ograniczenia:**
  - `IntershipsAbsence_pk`: Klucz główny na kombinacji kolumn `IntershipID`, `StudentID` i `Absence`.

```sql
CREATE TABLE IntershipsAbsence (
    IntershipID int NOT NULL,
    StudentID int NOT NULL,
    Absence datetime NOT NULL,
    CONSTRAINT IntershipsAbsence_pk PRIMARY KEY (IntershipID, StudentID, Absence)
);
```

## Tabela: `StationaryWeek`
Przechowuje informacje o tygodniu stacjonarnym związanym z dziedziną nauki.

- `Semester` *(int, NOT NULL)*: Numer semestru, w którym odbywa się tydzień stacjonarny.
- `StartDate` *(date, NOT NULL)*: Data rozpoczęcia tygodnia stacjonarnego.
- `EndDate` *(date, NOT NULL)*: Data zakończenia tygodnia stacjonarnego.
- `FieldOfStudyID` *(int, NOT NULL)*: Unikalny identyfikator dziedziny nauki, do której przypisany jest tydzień stacjonarny (klucz obcy).
- `StationaryID` *(int, NOT NULL)*: Unikalny identyfikator tygodnia stacjonarnego.
- `Price` *(money, NOT NULL)*: Cena związana z tygodniem stacjonarnym.
- `Quantity` *(int, NOT NULL)*: Ilość dostępnych miejsc na tydzień stacjonarny.
- **Klucze i ograniczenia:**
  - `StationaryWeek_pk`: Klucz główny na kolumnie `StationaryID`.
  - `chk_StationaryWeek`: Ograniczenie sprawdzające, że:
    - Data rozpoczęcia jest wcześniejsza niż data zakończenia.
    - Numer semestru jest większy lub równy 0.

```sql
CREATE TABLE StationaryWeek (
    Semester int NOT NULL,
    StartDate date NOT NULL,
    EndDate date NOT NULL,
    FieldOfStudyID int NOT NULL,
    StationaryID int NOT NULL,
    Price money NOT NULL,
    Quantity int NOT NULL,
    CONSTRAINT StationaryWeek_pk PRIMARY KEY (StationaryID),
    CONSTRAINT chk_StationaryWeek CHECK (
        StartDate < EndDate AND
        Semester >= 0
    )
);
```

## Tabela: `MeetingType`
Przechowuje informacje o typach spotkań.

- `MeetingTypeID` *(int, NOT NULL)*: Unikalny identyfikator typu spotkania.
- `Description` *(varchar(50), NOT NULL)*: Opis typu spotkania (np. wykład, ćwiczenia).
- **Klucze i ograniczenia:**
  - `MeetingType_pk`: Klucz główny na kolumnie `MeetingTypeID`.
  - `chk_MeetingType`: Ograniczenie sprawdzające, że:
    - Opis ma co najmniej 1 znak.

```sql
CREATE TABLE MeetingType (
    MeetingTypeID int NOT NULL,
    Description varchar(50) NOT NULL,
    CONSTRAINT MeetingType_pk PRIMARY KEY (MeetingTypeID),
    CONSTRAINT chk_MeetingType CHECK (
        LEN(Description) >= 1
    )
);
```

```