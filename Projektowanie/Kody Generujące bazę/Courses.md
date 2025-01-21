## Kod do generowania tabel z sekcji Courses
```sql

-- Table: Courses
CREATE TABLE Courses (
    CourseID int NOT NULL,
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

-- Table: CourseModulesProgress
CREATE TABLE CourseModulesProgress (
    CourseID int  NOT NULL,
    ModuleID int  NOT NULL,
    StudentID int  NOT NULL,
    Passed bit  NOT NULL,
    CONSTRAINT CourseModulesProgress_pk PRIMARY KEY  (CourseID,ModuleID,StudentID)
);

-- Table: CourseSchedule
CREATE TABLE CourseSchedule (
    ModuleID int  NOT NULL,
    RoomID int  NULL,
    LiveLink varchar(max)  NULL,
    VideoLink varchar(max)  NULL,
    Course_date datetime  NOT NULL,
    CONSTRAINT CourseSchedule_pk PRIMARY KEY  (ModuleID)
);

-- Table: Modules
CREATE TABLE Modules (
    ModuleID int NOT NULL,
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

-- Table: ModuleAbsence
CREATE TABLE ModuleAbsence (
    ModuleID int NOT NULL,
    StudentID int NOT NULL,
    Date datetime NOT NULL,
    CONSTRAINT ModuleAbsence_pk PRIMARY KEY (ModuleID, StudentID, Date)
);

```