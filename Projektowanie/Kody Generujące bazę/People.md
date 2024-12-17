## Kod do generowania tabeli z sekcji People
```sql
-- Table: Students
CREATE TABLE Students (
    StudentID int  NOT NULL IDENTITY,
    FirstName varchar(50)  NOT NULL,
    LastName varchar(50)  NOT NULL,
    DateOfBirth date  NOT NULL,
    Country varchar(50)  NOT NULL,
    City varchar(50)  NOT NULL,
    Address varchar(50)  NOT NULL,
    Mail varchar(50)  NOT NULL,
    Phone varchar(15)  NOT NULL,
    CONSTRAINT Students_pk PRIMARY KEY  (StudentID)
);

-- Table: Employees
CREATE TABLE Employees (
    EmployeeID int  NOT NULL IDENTITY,
    FirstName varchar(50)  NOT NULL,
    LastName varchar(50)  NOT NULL,
    DateOfBirth date  NOT NULL,
    Country varchar(50)  NOT NULL,
    City varchar(50)  NOT NULL,
    Address varchar(50)  NOT NULL,
    Mail varchar(50)  NOT NULL,
    Phone varchar(15)  NOT NULL,
    CONSTRAINT Employees_pk PRIMARY KEY  (EmployeeID)
);

-- Table: EmployeeType
CREATE TABLE EmployeeType (
    EmployeeID int  NOT NULL,
    HeldPosition varchar(50)  NOT NULL,
    CONSTRAINT EmployeeType_pk PRIMARY KEY  (EmployeeID)
);

-- Table: Translator
CREATE TABLE Translator (
    TranslatorID int  NOT NULL,
    FirstName varchar(50)  NOT NULL,
    LastName varchar(50)  NOT NULL,
    DateOfBirth date  NOT NULL,
    Country varchar(50)  NOT NULL,
    City varchar(50)  NOT NULL,
    Address varchar(50)  NOT NULL,
    Mail varchar(50)  NOT NULL,
    Phone varchar(15)  NOT NULL,
    CONSTRAINT Translator_pk PRIMARY KEY  (TranslatorID)
);

-- Table: LectureRoomDetails
CREATE TABLE LectureRoomDetails (
    RoomID int  NOT NULL,
    BuildingNr varchar(10)  NOT NULL,
    Floor int  NOT NULL,
    ClassNumber int  NOT NULL,
    CONSTRAINT LectureRoomDetails_pk PRIMARY KEY  (RoomID)
);

-- Table: Languages
CREATE TABLE Languages (
    TranslatorID int  NOT NULL,
    LanguageID int  NOT NULL,
    CONSTRAINT LanguageID PRIMARY KEY  (TranslatorID,LanguageID)
);

-- Table: AvalibleLanguages
CREATE TABLE AvalibleLanguages (
    LanguageID int  NOT NULL,
    Language varchar(50)  NOT NULL,
    CONSTRAINT AvalibleLanguages_pk PRIMARY KEY  (LanguageID)
);

-- Table: ModuleAbsence
CREATE TABLE ModuleAbsence (
    ModuleID int  NOT NULL,
    StudentID int  NOT NULL,
    Absence datetime  NOT NULL,
    CONSTRAINT ModuleAbsence_pk PRIMARY KEY  (ModuleID,StudentID)
);

-- Table: IntershipsAbsence
CREATE TABLE IntershipsAbsence (
    IntershipID int  NOT NULL,
    StudentID int  NOT NULL,
    Absence datetime  NOT NULL,
    CONSTRAINT IntershipsAbsence_pk PRIMARY KEY  (IntershipID,StudentID)
);