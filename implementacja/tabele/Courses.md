# Sekcja Courses

## Tabela: `Courses`
Przechowuje informacje o kursach.

- `CourseID` *(int, NOT NULL)*: Unikalny identyfikator kursu.
- `CourseName` *(varchar(50), NOT NULL)*: Nazwa kursu.
- `EmployeeID` *(int, NOT NULL)*: Identyfikator pracownika odpowiedzialnego za kurs.
- `Price` *(money, NOT NULL)*: Cena kursu.
- `CourseType` *(varchar(50), NOT NULL)*: Typ kursu (np. online, stacjonarny).
- `Limit` *(int, NULL)*: Maksymalna liczba uczestników kursu.
- `LanguageID` *(int, NOT NULL)*: Identyfikator języka kursu.
- `TranslatorID` *(int, NULL)*: Identyfikator tłumacza (jeśli dotyczy).
- **Klucze i ograniczenia:**
  - `Courses_pk`: Klucz główny na kolumnie `CourseID`.
  - `chk_Courses`: Ograniczenia sprawdzające, że:
    - `CourseType` i `CourseName` mają co najmniej 1 znak.
    - `Price` jest nieujemne.
    - `Limit` jest nieujemne.
```sql

CREATE TABLE Courses (
    CourseID int IDENTITY(1,1) NOT NULL,
    CourseName varchar(50) NOT NULL,
    EmployeeID int NOT NULL,
    Price money NOT NULL,
    CourseType varchar(50) NOT NULL,
    Limit int NULL,
    LanguageID int NOT NULL,
    TranslatorID int NULL,
    CONSTRAINT Courses_pk PRIMARY KEY (CourseID),
    CONSTRAINT chk_Courses CHECK (
        LEN(CourseType) >= 1 AND
        Price >= 0 AND
        Limit >=0 AND
        LEN(CourseName)>=1
    )
);
```
## Tabela: `CourseModulesProgress`
Przechowuje informacje o postępach uczestników w modułach kursów.

- `CourseID` *(int, NOT NULL)*: Identyfikator kursu.
- `ModuleID` *(int, NOT NULL)*: Identyfikator modułu w kursie.
- `StudentID` *(int, NOT NULL)*: Identyfikator uczestnika.
- `Passed` *(bit, NOT NULL)*: Flaga określająca, czy moduł został zaliczony (1 = tak, 0 = nie).
- **Klucze i ograniczenia:**
  - `CourseModulesProgress_pk`: Klucz główny złożony z `CourseID`, `ModuleID` i `StudentID`.
```sql
CREATE TABLE CourseModulesProgress (
    CourseID int  NOT NULL,
    ModuleID int  NOT NULL,
    StudentID int  NOT NULL,
    Passed bit  NOT NULL,
    CONSTRAINT CourseModulesProgress_pk PRIMARY KEY  (CourseID,ModuleID,StudentID)
);
```
## Tabela: `CourseSchedule`
Przechowuje harmonogram modułów kursu.

- `ModuleID` *(int, NOT NULL)*: Identyfikator modułu.
- `RoomID` *(int, NULL)*: Identyfikator sali, w której odbywa się moduł (jeśli dotyczy).
- `LiveLink` *(varchar(max), NULL)*: Link do transmisji na żywo (jeśli dotyczy).
- `VideoLink` *(varchar(max), NULL)*: Link do nagrania wideo (jeśli dotyczy).
- `Course_date` *(datetime, NOT NULL)*: Data realizacji modułu.
- **Klucze i ograniczenia:**
  - `CourseSchedule_pk`: Klucz główny na kolumnie `ModuleID`.
```sql
CREATE TABLE CourseSchedule (
    ModuleID int  NOT NULL,
    RoomID int  NULL,
    LiveLink varchar(max)  NULL,
    VideoLink varchar(max)  NULL,
    Course_date datetime  NOT NULL,
    CONSTRAINT CourseSchedule_pk PRIMARY KEY  (ModuleID)
);
```
## Tabela: `Modules`
Przechowuje informacje o modułach wchodzących w skład kursów.

- `ModuleID` *(int, NOT NULL)*: Unikalny identyfikator modułu.
- `ModuleName` *(varchar(50), NOT NULL)*: Nazwa modułu.
- `CourseID` *(int, NOT NULL)*: Identyfikator kursu, do którego należy moduł.
- `ModuleType` *(varchar(50), NOT NULL)*: Typ modułu (np. teoretyczny, praktyczny).
- `MeetingsQuantity` *(int, NOT NULL)*: Liczba spotkań w ramach modułu.
- **Klucze i ograniczenia:**
  - `Modules_pk`: Klucz główny na kolumnie `ModuleID`.
  - `chk_Modules`: Ograniczenia sprawdzające, że:
    - `ModuleName` i `ModuleType` mają co najmniej 1 znak.
    - `MeetingsQuantity` jest większe od 0. 

```sql
CREATE TABLE Modules (
    ModuleID int NOT NULL IDENTITY(1,1),
    ModuleName varchar(50) NOT NULL,
    CourseID int NOT NULL,
    ModuleType varchar(50) NOT NULL,
    MeetingsQuantity int NOT NULL,
    CONSTRAINT Modules_pk PRIMARY KEY (ModuleID),
    CONSTRAINT chk_Modules CHECK (
        LEN(ModuleName) >= 1 AND
        MeetingsQuantity > 0 AND
        LEN(ModuleType) >= 1
    )
);
```
## Tabela: `ModuleAbsence`
Przechowuje informacje o nieobecnościach uczestników na spotkaniach modułów.

- `ModuleID` *(int, NOT NULL)*: Identyfikator modułu.
- `StudentID` *(int, NOT NULL)*: Identyfikator uczestnika.
- `Date` *(datetime, NOT NULL)*: Data nieobecności.
- **Klucze i ograniczenia:**
  - `ModuleAbsence_pk`: Klucz główny złożony z `ModuleID`, `StudentID` i `Date`.
```sql
CREATE TABLE ModuleAbsence (
    ModuleID int NOT NULL,
    StudentID int NOT NULL,
    Date datetime NOT NULL,
    CONSTRAINT ModuleAbsence_pk PRIMARY KEY (ModuleID, StudentID, Date)
);

```