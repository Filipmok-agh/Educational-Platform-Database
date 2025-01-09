## Kod do generowania tabeli z sekcji Studies

```sql

-- Table: FieldOfStudy
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

-- Table: FieldOfStudyStudentList
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

-- Table: SubjectGrades
CREATE TABLE SubjectGrades (
    SubjectID int NOT NULL,
    StudentID int NOT NULL,
    Grade int NOT NULL,
    CONSTRAINT SubjectGrades_pk PRIMARY KEY (SubjectID, StudentID),
    CONSTRAINT chk_SubjectGrades CHECK (
        Grade >= 0
    )
);

-- Table: Meeting
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

-- Table: StudentAbsence
CREATE TABLE StudentAbsence (
    MeetingID int NOT NULL,
    StudentID int NOT NULL,
    ClassRetakeID int NULL,
    CONSTRAINT StudentAbsence_pk PRIMARY KEY (MeetingID, StudentID)
);

-- Table: Interships
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

-- Table: IntershipsAbsence
CREATE TABLE IntershipsAbsence (
    IntershipID int NOT NULL,
    StudentID int NOT NULL,
    Absence datetime NOT NULL,
    CONSTRAINT IntershipsAbsence_pk PRIMARY KEY (IntershipID, StudentID, Absence)
);

-- Table: StationaryWeek
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

-- Table: MeetingType
CREATE TABLE MeetingType (
    MeetingTypeID int NOT NULL,
    Description varchar(50) NOT NULL,
    CONSTRAINT MeetingType_pk PRIMARY KEY (MeetingTypeID),
    CONSTRAINT chk_MeetingType CHECK (
        LEN(Description) >= 1
    )
);

```