## Kod do generowania bazy danych

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

-- Table: Orders
CREATE TABLE Orders (
    OrderID int NOT NULL,
    Paid money NULL,
    OrderDate datetime NOT NULL,
    StudentID int NOT NULL,
    CONSTRAINT OrderID_pk PRIMARY KEY (OrderID),
    CONSTRAINT chk_Orders CHECK (
        Paid >= 0
    )
);

-- Table: OrderCourse
CREATE TABLE OrderCourse (
    OrderDetailsID int NOT NULL,
    CourseID int NOT NULL,
    CONSTRAINT OrderCourse_pk PRIMARY KEY (OrderDetailsID)
);

-- Table: OrderDetails
CREATE TABLE OrderDetails (
    OrderDetailsID int NOT NULL,
    PaidDate datetime NULL,
    OrderID int NOT NULL,
    AccessGiven bit NOT NULL,
    CONSTRAINT OrderDetails_pk PRIMARY KEY (OrderDetailsID)
);

-- Table: OrderMeeting
CREATE TABLE OrderMeeting (
    OrderDetailsID int NOT NULL,
    MeetingID int NOT NULL,
    CONSTRAINT OrderMeeting_pk PRIMARY KEY (OrderDetailsID)
);

-- Table: OrderStudies
CREATE TABLE OrderStudies (
    OrderDetailsID int NOT NULL,
    FieldOfStudyID int NOT NULL,
    CONSTRAINT OrderStudies_pk PRIMARY KEY (OrderDetailsID)
);

-- Table: OrderWebinar
CREATE TABLE OrderWebinar (
    OrderDetailsID int NOT NULL,
    WebinarID int NOT NULL,
    CONSTRAINT OrderWebinar_pk PRIMARY KEY (OrderDetailsID)
);

-- Table: OrderStationaryWeek
CREATE TABLE OrderStationaryWeek (
    OrderDetailsID int NOT NULL,
    StationaryID int NOT NULL,
    CONSTRAINT OrderStationaryWeek_pk PRIMARY KEY (OrderDetailsID)
);
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
-- Table: Webinar
CREATE TABLE Webinar (
    WebinarID int NOT NULL,
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

-- Table: WebinarExpirationDate
CREATE TABLE WebinarExpirationDate (
    WebinarID int NOT NULL,
    StudentID int NOT NULL,
    expr_date date NULL,
    CONSTRAINT WebinarExpirationDate_pk PRIMARY KEY (WebinarID, StudentID)
);

-- Reference: CourseModulesProgress_Courses (table: CourseModulesProgress)
ALTER TABLE CourseModulesProgress ADD CONSTRAINT CourseModulesProgress_Courses
    FOREIGN KEY (CourseID)
    REFERENCES Courses (CourseID);

-- Reference: CourseModulesProgress_Modules (table: CourseModulesProgress)
ALTER TABLE CourseModulesProgress ADD CONSTRAINT CourseModulesProgress_Modules
    FOREIGN KEY (ModuleID)
    REFERENCES Modules (ModuleID);

-- Reference: CourseModulesProgress_Students (table: CourseModulesProgress)
ALTER TABLE CourseModulesProgress ADD CONSTRAINT CourseModulesProgress_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: CourseSchedule_LectureRoomDetails (table: CourseSchedule)
ALTER TABLE CourseSchedule ADD CONSTRAINT CourseSchedule_LectureRoomDetails
    FOREIGN KEY (RoomID)
    REFERENCES LectureRoomDetails (RoomID);

-- Reference: CourseSchedule_Modules (table: CourseSchedule)
ALTER TABLE CourseSchedule ADD CONSTRAINT CourseSchedule_Modules
    FOREIGN KEY (ModuleID)
    REFERENCES Modules (ModuleID);

-- Reference: Courses_AvailableLanguages (table: Courses)
ALTER TABLE Courses ADD CONSTRAINT Courses_AvailableLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvailableLanguages (LanguageID);

-- Reference: Courses_Employees (table: Courses)
ALTER TABLE Courses ADD CONSTRAINT Courses_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: Courses_Translator (table: Courses)
ALTER TABLE Courses ADD CONSTRAINT Courses_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);

-- Reference: EmployeeType_Employees (table: EmployeeType)
ALTER TABLE EmployeeType ADD CONSTRAINT EmployeeType_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: FieldOfStudyStudentList_FieldOfStudy (table: FieldOfStudyStudentList)
ALTER TABLE FieldOfStudyStudentList ADD CONSTRAINT FieldOfStudyStudentList_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: FieldOfStudyStudentList_Students (table: FieldOfStudyStudentList)
ALTER TABLE FieldOfStudyStudentList ADD CONSTRAINT FieldOfStudyStudentList_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: IntershipsAbsence_Interships (table: IntershipsAbsence)
ALTER TABLE IntershipsAbsence ADD CONSTRAINT IntershipsAbsence_Interships
    FOREIGN KEY (IntershipID)
    REFERENCES Interships (IntershipID);

-- Reference: IntershipsAbsence_Students (table: IntershipsAbsence)
ALTER TABLE IntershipsAbsence ADD CONSTRAINT IntershipsAbsence_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: Interships_FieldOfStudy (table: Interships)
ALTER TABLE Interships ADD CONSTRAINT Interships_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: Languages_AvailableLanguages (table: Languages)
ALTER TABLE Languages ADD CONSTRAINT Languages_AvailableLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvailableLanguages (LanguageID);

-- Reference: Languages_Translator (table: Languages)
ALTER TABLE Languages ADD CONSTRAINT Languages_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);

-- Reference: Meeting_LectureRoomDetails (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_LectureRoomDetails
    FOREIGN KEY (RoomID)
    REFERENCES LectureRoomDetails (RoomID);

-- Reference: Meeting_AvailableLanguages (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_AvailableLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvailableLanguages (LanguageID);

-- Reference: Meeting_MeetingType (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_MeetingType
    FOREIGN KEY (MeetingTypeID)
    REFERENCES MeetingType (MeetingTypeID);

-- Reference: Meeting_Subjects (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_Subjects
    FOREIGN KEY (SubjectID)
    REFERENCES Subjects (SubjectID);

-- Reference: Meeting_Translator (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);

-- Reference: Meeting_StationaryWeek (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_StationaryWeek
    FOREIGN KEY (StationaryID)
    REFERENCES StationaryWeek (StationaryID);

-- Reference: ModuleAbsence_Modules (table: ModuleAbsence)
ALTER TABLE ModuleAbsence ADD CONSTRAINT ModuleAbsence_Modules
    FOREIGN KEY (ModuleID)
    REFERENCES Modules (ModuleID);

-- Reference: ModuleAbsence_Students (table: ModuleAbsence)
ALTER TABLE ModuleAbsence ADD CONSTRAINT ModuleAbsence_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: Modules_Courses (table: Modules)
ALTER TABLE Modules ADD CONSTRAINT Modules_Courses
    FOREIGN KEY (CourseID)
    REFERENCES Courses (CourseID);

-- Reference: OrderCourse_Courses (table: OrderCourse)
ALTER TABLE OrderCourse ADD CONSTRAINT OrderCourse_Courses
    FOREIGN KEY (CourseID)
    REFERENCES Courses (CourseID);

-- Reference: OrderCourse_OrderDetails (table: OrderCourse)
ALTER TABLE OrderCourse ADD CONSTRAINT OrderCourse_OrderDetails
    FOREIGN KEY (OrderDetailsID)
    REFERENCES OrderDetails (OrderDetailsID);

-- Reference: OrderDetails_Orders (table: OrderDetails)
ALTER TABLE OrderDetails ADD CONSTRAINT OrderDetails_Orders
    FOREIGN KEY (OrderID)
    REFERENCES Orders (OrderID);

-- Reference: OrderMeeting_Meeting (table: OrderMeeting)
ALTER TABLE OrderMeeting ADD CONSTRAINT OrderMeeting_Meeting
    FOREIGN KEY (MeetingID)
    REFERENCES Meeting (MeetingID);

-- Reference: OrderMeeting_OrderDetails (table: OrderMeeting)
ALTER TABLE OrderMeeting ADD CONSTRAINT OrderMeeting_OrderDetails
    FOREIGN KEY (OrderDetailsID)
    REFERENCES OrderDetails (OrderDetailsID);

-- Reference: OrderStationaryWeek_OrderDetails (table: OrderStationaryWeek)
ALTER TABLE OrderStationaryWeek ADD CONSTRAINT OrderStationaryWeek_OrderDetails
    FOREIGN KEY (OrderDetailsID)
    REFERENCES OrderDetails (OrderDetailsID);

-- Reference: OrderStationaryWeek_StationaryWeek (table: OrderStationaryWeek)
ALTER TABLE OrderStationaryWeek ADD CONSTRAINT OrderStationaryWeek_StationaryWeek
    FOREIGN KEY (StationaryID)
    REFERENCES StationaryWeek (StationaryID);

-- Reference: OrderStudies_FieldOfStudy (table: OrderStudies)
ALTER TABLE OrderStudies ADD CONSTRAINT OrderStudies_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: OrderStudies_OrderDetails (table: OrderStudies)
ALTER TABLE OrderStudies ADD CONSTRAINT OrderStudies_OrderDetails
    FOREIGN KEY (OrderDetailsID)
    REFERENCES OrderDetails (OrderDetailsID);

-- Reference: OrderWebinar_OrderDetails (table: OrderWebinar)
ALTER TABLE OrderWebinar ADD CONSTRAINT OrderWebinar_OrderDetails
    FOREIGN KEY (OrderDetailsID)
    REFERENCES OrderDetails (OrderDetailsID);

-- Reference: OrderWebinar_Webinar (table: OrderWebinar)
ALTER TABLE OrderWebinar ADD CONSTRAINT OrderWebinar_Webinar
    FOREIGN KEY (WebinarID)
    REFERENCES Webinar (WebinarID);

-- Reference: Orders_Students (table: Orders)
ALTER TABLE Orders ADD CONSTRAINT Orders_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: StationaryWeek_FieldOfStudy (table: StationaryWeek)
ALTER TABLE StationaryWeek ADD CONSTRAINT StationaryWeek_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: StudentAbsence_Meeting (table: StudentAbsence)
ALTER TABLE StudentAbsence ADD CONSTRAINT StudentAbsence_Meeting
    FOREIGN KEY (MeetingID)
    REFERENCES Meeting (MeetingID);

-- Reference: StudentAbsence_Students (table: StudentAbsence)
ALTER TABLE StudentAbsence ADD CONSTRAINT StudentAbsence_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: SubjectGrades_Students (table: SubjectGrades)
ALTER TABLE SubjectGrades ADD CONSTRAINT SubjectGrades_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: SubjectGrades_Subjects (table: SubjectGrades)
ALTER TABLE SubjectGrades ADD CONSTRAINT SubjectGrades_Subjects
    FOREIGN KEY (SubjectID)
    REFERENCES Subjects (SubjectID);

-- Reference: Subjects_Employees (table: Subjects)
ALTER TABLE Subjects ADD CONSTRAINT Subjects_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: Subjects_FieldOfStudy (table: Subjects)
ALTER TABLE Subjects ADD CONSTRAINT Subjects_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: WebinarExpirationDate_Students (table: WebinarExpirationDate)
ALTER TABLE WebinarExpirationDate ADD CONSTRAINT WebinarExpirationDate_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: WebinarExpirationDate_Webinar (table: WebinarExpirationDate)
ALTER TABLE WebinarExpirationDate ADD CONSTRAINT WebinarExpirationDate_Webinar
    FOREIGN KEY (WebinarID)
    REFERENCES Webinar (WebinarID);

-- Reference: Webinar_AvailableLanguages (table: Webinar)
ALTER TABLE Webinar ADD CONSTRAINT Webinar_AvailableLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvailableLanguages (LanguageID);

-- Reference: Webinar_Employees (table: Webinar)
ALTER TABLE Webinar ADD CONSTRAINT Webinar_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: Webinar_Translator (table: Webinar)
ALTER TABLE Webinar ADD CONSTRAINT Webinar_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);

```