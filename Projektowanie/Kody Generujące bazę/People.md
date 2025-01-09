## Kod do generowania tabeli z sekcji People
```sql

-- Table: Translator
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

-- Table: Employees
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

-- Table: EmployeeType
CREATE TABLE EmployeeType (
    EmployeeID int NOT NULL,
    HeldPosition varchar(50) NOT NULL,
    CONSTRAINT EmployeeType_pk PRIMARY KEY (EmployeeID),
    CONSTRAINT chk_EmployeeType CHECK (
        LEN(HeldPosition) >= 1
    )
);

-- Table: Students
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

-- Table: LectureRoomDetails
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

-- Table: Languages
CREATE TABLE Languages (
    TranslatorID int NOT NULL,
    LanguageID int NOT NULL,
    CONSTRAINT LanguageID_pk PRIMARY KEY (TranslatorID, LanguageID)
);

-- Table: AvailableLanguages
CREATE TABLE AvailableLanguages (
    LanguageID int NOT NULL,
    Language varchar(50) NOT NULL,
    CONSTRAINT AvailableLanguages_pk PRIMARY KEY (LanguageID),
    CONSTRAINT chk_AvailableLanguages CHECK (
        LEN(Language) >= 1
    )
);

```