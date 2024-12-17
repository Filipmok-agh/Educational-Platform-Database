## Kod do generowania tabel z sekcji Courses
```sql
-- Table: Courses
CREATE TABLE Courses (
    CourseID int  NOT NULL,
    EmployeeID int  NOT NULL,
    Price money  NOT NULL,
    CourseType varchar(50)  NOT NULL,
    Limit int  NULL,
    LanguageID int  NOT NULL,
    TranslatorID int  NULL,
    ModulesQuantity int  NOT NULL,
    CONSTRAINT Courses_pk PRIMARY KEY  (CourseID)
);


-- Table: Modules
CREATE TABLE Modules (
    ModuleID int  NOT NULL,
    CourseID int  NOT NULL,
    ModuleTypeID int  NOT NULL,
    MettingsQuantity int  NOT NULL,
    CONSTRAINT Modules_pk PRIMARY KEY  (ModuleID)
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

-- Table: CourseModulesPassed
CREATE TABLE CourseModulesPassed (
    CourseID int  NOT NULL,
    ModuleID int  NOT NULL,
    StudentID int  NOT NULL,
    CONSTRAINT CourseModulesPassed_pk PRIMARY KEY  (CourseID,ModuleID,StudentID)
);

-- Table: ModuleAbsence
CREATE TABLE ModuleAbsence (
    ModuleID int  NOT NULL,
    StudentID int  NOT NULL,
    CONSTRAINT ModuleAbsence_pk PRIMARY KEY  (ModuleID,StudentID)
);

-- Table: ModuleType
CREATE TABLE ModuleType (
    ModuleTypeID int  NOT NULL,
    Description varchar(50)  NOT NULL,
    CONSTRAINT ModuleType_pk PRIMARY KEY  (ModuleTypeID)
);
