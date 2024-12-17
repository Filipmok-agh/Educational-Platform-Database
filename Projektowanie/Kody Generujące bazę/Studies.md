## Kod do generowania tabeli z sekcji Studies

```sql
-- Table: FieldOfStudy
CREATE TABLE FieldOfStudy (
    FieldOfStudyID int  NOT NULL,
    Name varchar(50)  NOT NULL,
    Description varchar(50)   NOT NULL,
    Limit int  NOT NULL,
    EntryFee money  NOT NULL,
    CONSTRAINT FieldOfStudy_pk PRIMARY KEY  (FieldOfStudyID)
);

-- Table: FieldOfStudyStudentList
CREATE TABLE FieldOfStudyStudentList (
    FieldOfStudyID int  NOT NULL,
    StudentID int  NOT NULL,
    semester int  NOT NULL,
    CONSTRAINT FieldOfStudyStudentList_pk PRIMARY KEY  (StudentID,FieldOfStudyID)
);

-- Table: Subjects
CREATE TABLE Subjects (
    SubjectID int  NOT NULL,
    FieldOfStudyID int  NOT NULL,
    SubjectName varchar(50)  NOT NULL,
    Description varchar(50) NOT NULL,
    MeetingsQuantity int  NOT NULL,
    EmployeeID int  NOT NULL,
    semester int  NOT NULL,
    CONSTRAINT Subjects_pk PRIMARY KEY  (SubjectID)
);

-- Table: SubjectGrades
CREATE TABLE SubjectGrades (
    SubjectID int  NOT NULL,
    StudentID int  NOT NULL,
    Grade int  NOT NULL,
    CONSTRAINT SubjectGrades_pk PRIMARY KEY  (SubjectID,StudentID)
);

-- Table: Meeting
CREATE TABLE Meeting (
    MeetingID int  NOT NULL,
    MeetingTypeID int  NOT NULL,
    SubjectID int  NOT NULL,
    Meeting_date datetime  NOT NULL,
    Link varchar(max)  NULL,
    RoomID int  NULL,
    LanguageID int  NOT NULL,
    TranslatorID int  NULL,
    price money  NOT NULL,
    semester int  NOT NULL,
    CONSTRAINT Meeting_pk PRIMARY KEY  (MeetingID)
);


-- Table: MeetingType
CREATE TABLE MeetingType (
    MeetingTypeID int  NOT NULL,
    Description varchar(50)  NOT NULL,
    CONSTRAINT MeetingType_pk PRIMARY KEY  (MeetingTypeID)
);

-- Table: Interships
CREATE TABLE Interships (
    IntershipID int  NOT NULL,
    FieldOfStudyID int  NOT NULL,
    StartDate date  NOT NULL,
    EndDate date  NOT NULL,
    CONSTRAINT Interships_pk PRIMARY KEY  (IntershipID)
);

-- Table: StudentAbsence
CREATE TABLE StudentAbsence (
    MeetingID int  NOT NULL,
    StudentID int  NOT NULL,
    CONSTRAINT StudentAbsence_pk PRIMARY KEY  (MeetingID,StudentID)
);