# Funkcjonalności systemu – lista użytkowników i ich uprawnienia

<p style="text-align: left; font-size: medium;"><br>Mokrzycki Filip,<br> Mateusz Wójcik,<br> Piotr Kacprzak </p>

## Uczestnik webinarów
1. Możliwość usunięcia swojego konta.  
2. Dostęp do listy dostępnych webinarów (cena, język, krótki opis).  
3. Możliwość zakupienia dostępu do płatnych webinarów.  
4. Informacja zwrotna o pomyślnym przeprowadzeniu płatności.  
5. Dostęp do opłaconych webinarów.  
6. Dostęp do linku z nagraniami webinarów.  
7. Możliwość złożenia wniosku do Dyrektora Szkoły w sprawie odstępstwa od regulaminu płatności.  

## Uczestnik kursów
1. Dostęp do informacji o godzinie, dacie i miejscu odbywania się kursu.  
2. Dostęp do listy kursów (ceny, język, typ, krótki opis).  
3. Wgląd do frekwencji, aktualnych postępów oraz wyników.  
4. Dostęp do nagrań kursów synchronicznych oraz asynchronicznych.  
5. Możliwość opłacenia kursu.  
6. Informacja zwrotna o pomyślnym przeprowadzeniu płatności.  
7. Możliwość złożenia wniosku do Dyrektora Szkoły w sprawie odstępstwa od regulaminu płatności.  

## Student
1. Dostęp do sylabusa.  
2. Dostęp do harmonogramu spotkań.  
3. Dostęp do informacji o godzinie, dacie i miejscu odbywania się zajęć.  
4. Wgląd do frekwencji, aktualnych postępów oraz wyników.  
5. Dostęp do informacji na temat praktyk.  
6. Informacja o sposobie odrobienia danej nieobecności.  
7. Możliwość opłacenia studium.  
8. Informacja zwrotna o pomyślnym przeprowadzeniu płatności.  
9. Możliwość złożenia wniosku do Dyrektora Szkoły w sprawie odstępstwa od regulaminu płatności.  

## Administrator
1. Możliwość usuwania webinarów.  
2. Możliwość tworzenia webinarów oraz kursów.  

## Dyrektor Szkoły
1. Wgląd do historii płatności użytkowników.  
2. Możliwość rozpatrzenia wniosków złożonych przez uczestników.  
3. Możliwość układania planu i dobierania wykładowców.  
4. Możliwość ustalania limitu miejsc dla kursów stacjonarnych oraz studiów.  

## Wykładowca
1. Dostęp do harmonogramu własnych zajęć.  
2. Wgląd do listy studentów.  
3. Możliwość sprawdzania obecności.  

## Potencjalny klient
1. Możliwość założenia konta na platformie.  
2. Dostęp do sylabusa.  
3. Dostęp do harmonogramu spotkań.  
4. Wgląd do kosztu pojedynczego spotkania studyjnego.  
5. Wgląd do kosztu webinaru, kursu, studium.  
6. Dostęp do listy webinarów (cena, język, krótki opis).  
7. Dostęp do listy kursów (ceny, język, typ, krótki opis).  

## Funkcjonalność bazy danych dla osób uprawnionych przez zleceniodawcę
1. Raporty finansowe – zestawienie przychodów dla każdego webinaru/kursu/studium.  
2. Lista „dłużników” – osoby, które skorzystały z usług, ale nie uiściły opłat.  
3. Raport dotyczący liczby zapisanych osób na przyszłe wydarzenia (z informacją, czy wydarzenie jest stacjonarne, czy zdalne).  
4. Raport dotyczący frekwencji na zakończonych już wydarzeniach.  
5. Lista obecności dla każdego szkolenia z datą, imieniem, nazwiskiem i informacją, czy uczestnik był obecny, czy nie.  
6. Raport bilokacji – lista osób, które są zapisane na co najmniej dwa przyszłe szkolenia, które kolidują czasowo.  


## Schemat bazy danych

<div style="text-align: center;">
  <img src="Projektowanie/schemat.svg" alt="Opis obrazka">
</div>


## Kod do generowania bazy danych

-- Table: Courses
```sql

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
        LENGTH(CourseType) >= 1 AND
        Price >= 0 AND
        Limit >=0 AND
        LENGTH(CourseName) >= 1
    )
);
```
-- Reference: Courses_AvalibleLanguages (table: Courses)
```sql
ALTER TABLE Courses ADD CONSTRAINT Courses_AvalibleLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvalibleLanguages (LanguageID);
```
-- Reference: Courses_Employees (table: Courses)
```sql

ALTER TABLE Courses ADD CONSTRAINT Courses_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);
```
-- Reference: Courses_Translator (table: Courses)
```sql

ALTER TABLE Courses ADD CONSTRAINT Courses_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);
```
-- Table: CourseModulesProgress
```sql

CREATE TABLE CourseModulesProgress (
    CourseID int  NOT NULL,
    ModuleID int  NOT NULL,
    StudentID int  NOT NULL,
    Passed bit  NOT NULL,
    CONSTRAINT CourseModulesProgress_pk PRIMARY KEY  (CourseID,ModuleID,StudentID)
);
```
-- Reference: CourseModulesProgress_Courses (table: CourseModulesProgress)
```sql

ALTER TABLE CourseModulesProgress ADD CONSTRAINT CourseModulesProgress_Courses
    FOREIGN KEY (CourseID)
    REFERENCES Courses (CourseID);
```
-- Reference: CourseModulesProgress_Modules (table: CourseModulesProgress)
```sql

ALTER TABLE CourseModulesProgress ADD CONSTRAINT CourseModulesProgress_Modules
    FOREIGN KEY (ModuleID)
    REFERENCES Modules (ModuleID);
```
-- Reference: CourseModulesProgress_Students (table: CourseModulesProgress)
```sql

ALTER TABLE CourseModulesProgress ADD CONSTRAINT CourseModulesProgress_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);
```
-- Table: CourseSchedule
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
-- Reference: CourseSchedule_LectureRoomDetails (table: CourseSchedule)
```sql

ALTER TABLE CourseSchedule ADD CONSTRAINT CourseSchedule_LectureRoomDetails
    FOREIGN KEY (RoomID)
    REFERENCES LectureRoomDetails (RoomID);
```

-- Reference: CourseSchedule_Modules (table: CourseSchedule)
```sql

ALTER TABLE CourseSchedule ADD CONSTRAINT CourseSchedule_Modules
    FOREIGN KEY (ModuleID)
    REFERENCES Modules (ModuleID);
```

-- Table: Modules
```sql

CREATE TABLE Modules (
    ModuleID int NOT NULL,
    ModuleName varchar(50) NOT NULL,
    CourseID int NOT NULL,
    ModuleType varchar(50) NOT NULL,
    MettingsQuantity int NOT NULL,
    CONSTRAINT Modules_pk PRIMARY KEY (ModuleID),
    CONSTRAINT chk_Modules CHECK (
        LENGTH(ModuleName) >= 1 AND
        MettingsQuantity > 0 AND
        LENGTH(ModuleType) >= 1
    )
);
```

-- Reference: Modules_Courses (table: Modules)
```sql

ALTER TABLE Modules ADD CONSTRAINT Modules_Courses
    FOREIGN KEY (CourseID)
    REFERENCES Courses (CourseID);
```

-- Table: ModuleAbsence
```sql

CREATE TABLE ModuleAbsence (
    ModuleID int  NOT NULL,
    StudentID int  NOT NULL,
    Date datetime  NOT NULL,
    CONSTRAINT ModuleAbsence_pk PRIMARY KEY  (ModuleID,StudentID)
);
```

-- Reference: ModuleAbsence_Modules (table: ModuleAbsence)
```sql

ALTER TABLE ModuleAbsence ADD CONSTRAINT ModuleAbsence_Modules
    FOREIGN KEY (ModuleID)
    REFERENCES Modules (ModuleID);
```

-- Reference: ModuleAbsence_Students (table: ModuleAbsence)
```sql

ALTER TABLE ModuleAbsence ADD CONSTRAINT ModuleAbsence_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);
```

-- Table: Orders
```sql

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
```

-- Reference: Orders_Students (table: Orders)
```sql

ALTER TABLE Orders ADD CONSTRAINT Orders_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);
```

-- Table: OrderCourse
```sql

CREATE TABLE OrderCourse (
    OrderDetailsID int  NOT NULL,
    CourseID int  NOT NULL,
    CONSTRAINT OrderCourse_pk PRIMARY KEY  (OrderDetailsID)
);
```

-- Reference: OrderCourse_Courses (table: OrderCourse)
```sql

ALTER TABLE OrderCourse ADD CONSTRAINT OrderCourse_Courses
    FOREIGN KEY (CourseID)
    REFERENCES Courses (CourseID);
```

-- Reference: OrderCourse_OrderDetails (table: OrderCourse)
```sql

ALTER TABLE OrderCourse ADD CONSTRAINT OrderCourse_OrderDetails
    FOREIGN KEY (OrderDetailsID)
    REFERENCES OrderDetails (OrderDetailsID);
```

-- Table: OrderDetails
```sql

CREATE TABLE OrderDetails (
    OrderDetailsID int  NOT NULL,
    PaidDate datetime  NULL,
    OrderID int  NOT NULL,
    AccesGiven bit  NOT NULL,
    CONSTRAINT OrderDetails_pk PRIMARY KEY  (OrderDetailsID)
);
```

-- Reference: OrderDetails_Orders (table: OrderDetails)
```sql

ALTER TABLE OrderDetails ADD CONSTRAINT OrderDetails_Orders
    FOREIGN KEY (OrderID)
    REFERENCES Orders (OrderID);
```

-- Table: OrderMeeting
```sql

CREATE TABLE OrderMeeting (
    OrderDetailsID int  NOT NULL,
    MeetingID int  NOT NULL,
    CONSTRAINT OrderMeeting_pk PRIMARY KEY  (OrderDetailsID)
);
```

-- Reference: OrderMeeting_Meeting (table: OrderMeeting)
```sql

ALTER TABLE OrderMeeting ADD CONSTRAINT OrderMeeting_Meeting
    FOREIGN KEY (MeetingID)
    REFERENCES Meeting (MeetingID);
```

-- Reference: OrderMeeting_OrderDetails (table: OrderMeeting)
```sql

ALTER TABLE OrderMeeting ADD CONSTRAINT OrderMeeting_OrderDetails
    FOREIGN KEY (OrderDetailsID)
    REFERENCES OrderDetails (OrderDetailsID);
```

-- Table: OrderStudies
```sql

CREATE TABLE OrderStudies (
    OrderDetailsID int  NOT NULL,
    FieldOfStudyID int  NOT NULL,
    CONSTRAINT OrderStudies_pk PRIMARY KEY  (OrderDetailsID)
);
```

-- Reference: OrderStudies_FieldOfStudy (table: OrderStudies)
```sql

ALTER TABLE OrderStudies ADD CONSTRAINT OrderStudies_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);
```

-- Reference: OrderStudies_OrderDetails (table: OrderStudies)
```sql

ALTER TABLE OrderStudies ADD CONSTRAINT OrderStudies_OrderDetails
    FOREIGN KEY (OrderDetailsID)
    REFERENCES OrderDetails (OrderDetailsID);
```

-- Table: OrderWebinar
```sql

CREATE TABLE OrderWebinar (
    OrderDetailsID int  NOT NULL,
    WebinarID int  NOT NULL,
    CONSTRAINT OrderWebinar_pk PRIMARY KEY  (OrderDetailsID)
);
```

-- Reference: OrderWebinar_OrderDetails (table: OrderWebinar)
```sql

ALTER TABLE OrderWebinar ADD CONSTRAINT OrderWebinar_OrderDetails
    FOREIGN KEY (OrderDetailsID)
    REFERENCES OrderDetails (OrderDetailsID);
```

-- Reference: OrderWebinar_Webinar (table: OrderWebinar)
```sql

ALTER TABLE OrderWebinar ADD CONSTRAINT OrderWebinar_Webinar
    FOREIGN KEY (WebinarID)
    REFERENCES Webinar (WebinarID);
```

-- Table: OrderSessionWeek
```sql

CREATE TABLE OrderSessionWeek (
    OrderDetailsID int  NOT NULL,
    SessionWeekID int  NOT NULL,
    CONSTRAINT OrderSessionWeek_pk PRIMARY KEY  (OrderDetailsID)
);
```

-- Reference: OrderSessionWeek_OrderDetails (table: OrderSessionWeek)
```sql

ALTER TABLE OrderSessionWeek ADD CONSTRAINT OrderSessionWeek_OrderDetails
    FOREIGN KEY (OrderDetailsID)
    REFERENCES OrderDetails (OrderDetailsID);
```

-- Reference: OrderSessionWeek_SessionWeek (table: OrderSessionWeek)
```sql

ALTER TABLE OrderSessionWeek ADD CONSTRAINT OrderSessionWeek_SessionWeek
    FOREIGN KEY (SessionWeekID)
    REFERENCES SessionWeek (SessionWeekID);
```

-- Table: Translator
```sql

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
    CONSTRAINT chk_translator_validations CHECK (
        DATEDIFF(CURDATE(), DateOfBirth) / 365.25 >= 18 AND
        LENGTH(FirstName) >= 1 AND
        LENGTH(LastName) >= 1 AND
        LENGTH(Country) >= 1 AND
        LENGTH(City) >= 1 AND
        LENGTH(Address) >= 1 AND
        LENGTH(Mail) >= 1 AND
        LENGTH(Phone) >= 1
    )
);
```

-- Table: Employees
```sql

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
    CONSTRAINT Employees_pk PRIMARY KEY  (EmployeeID),
    CONSTRAINT chk_Employees CHECK (
        DATEDIFF(CURDATE(), DateOfBirth) / 365.25 >= 18 AND
        LENGTH(FirstName) >= 1 AND
        LENGTH(LastName) >= 1 AND
        LENGTH(Country) >= 1 AND
        LENGTH(City) >= 1 AND
        LENGTH(Address) >= 1 AND
        LENGTH(Mail) >= 1 AND
        LENGTH(Phone) >= 1
    )
);
```

-- Table: EmployeeType
```sql

CREATE TABLE EmployeeType (
    EmployeeID int NOT NULL,
    HeldPosition varchar(50) NOT NULL,
    CONSTRAINT EmployeeType_pk PRIMARY KEY (EmployeeID),
    CONSTRAINT chk_EmployeeType CHECK (
        LENGTH(HeldPosition) >= 1
    )
);
```

-- Reference: EmployeeType_Employees (table: EmployeeType)
```sql

ALTER TABLE EmployeeType ADD CONSTRAINT EmployeeType_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);
```

-- Table: Students
```sql

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
    CONSTRAINT Students_pk PRIMARY KEY  (StudentID),
    CONSTRAINT chk_Students CHECK (
        DATEDIFF(CURDATE(), DateOfBirth) / 365.25 >= 16 AND
        LENGTH(FirstName) >= 1 AND
        LENGTH(LastName) >= 1 AND
        LENGTH(Country) >= 1 AND
        LENGTH(City) >= 1 AND
        LENGTH(Address) >= 1 AND
        LENGTH(Mail) >= 1 AND
        LENGTH(Phone) >= 1
    )
);

```

-- Table: LectureRoomDetails
```sql

CREATE TABLE LectureRoomDetails (
    RoomID int NOT NULL,
    BuildingNr varchar(10) NOT NULL,
    Floor int NOT NULL,
    ClassNumber int NOT NULL,
    CONSTRAINT LectureRoomDetails_pk PRIMARY KEY (RoomID),
    CONSTRAINT chk_LectureRoomDetails CHECK (
        LENGTH(BuildingNr) >= 1 AND
        ClassNumber >= 0
    )
);
```

-- Table: Languages
```sql

CREATE TABLE Languages (
    TranslatorID int  NOT NULL,
    LanguageID int  NOT NULL,
    CONSTRAINT LanguageID_pk PRIMARY KEY  (TranslatorID,LanguageID)
);
```

-- Reference: Languages_AvalibleLanguages (table: Languages)
```sql

ALTER TABLE Languages ADD CONSTRAINT Languages_AvalibleLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvalibleLanguages (LanguageID);
```

-- Reference: Languages_Translator (table: Languages)
```sql

ALTER TABLE Languages ADD CONSTRAINT Languages_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);
```

-- Table: AvalibleLanguages
```sql

CREATE TABLE AvalibleLanguages (
    LanguageID int NOT NULL,
    Language varchar(50) NOT NULL,
    CONSTRAINT AvalibleLanguages_pk PRIMARY KEY (LanguageID),
    CONSTRAINT chk_AvalibleLanguages CHECK (
        LENGTH(Language) >= 1
    )
);
```

-- Table: FieldOfStudy
```sql

CREATE TABLE FieldOfStudy (
    FieldOfStudyID int NOT NULL,
    Name varchar(50) NOT NULL,
    Description varchar(50) NOT NULL,
    Limit int NOT NULL,
    EntryFee money NOT NULL,
    CONSTRAINT FieldOfStudy_pk PRIMARY KEY (FieldOfStudyID),
    CONSTRAINT chk_FieldOfStudy CHECK (
        LENGTH(Name) >= 1 AND
        LENGTH(Description) >= 1 AND
        EntryFee >= 0 AND
        Limit >= 0
    )
);
```

-- Table: FieldOfStudyStudentList
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

-- Reference: FieldOfStudyStudentList_FieldOfStudy (table: FieldOfStudyStudentList)
```sql

ALTER TABLE FieldOfStudyStudentList ADD CONSTRAINT FieldOfStudyStudentList_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);
```

-- Reference: FieldOfStudyStudentList_Students (table: FieldOfStudyStudentList)
```sql

ALTER TABLE FieldOfStudyStudentList ADD CONSTRAINT FieldOfStudyStudentList_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);
```

-- Table: Subjects
```sql

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
        LENGTH(SubjectName) >= 1 AND
        LENGTH(Description) >= 1 AND
        MeetingsQuantity > 0 AND
        Semester >= 0
    )
);
```

-- Reference: Subjects_Employees (table: Subjects)
```sql

ALTER TABLE Subjects ADD CONSTRAINT Subjects_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);
```

-- Reference: Subjects_FieldOfStudy (table: Subjects)
```sql

ALTER TABLE Subjects ADD CONSTRAINT Subjects_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

```

-- Table: SubjectGrades
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

-- Reference: SubjectGrades_Students (table: SubjectGrades)
```sql

ALTER TABLE SubjectGrades ADD CONSTRAINT SubjectGrades_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);
```

-- Reference: SubjectGrades_Subjects (table: SubjectGrades)
```sql

ALTER TABLE SubjectGrades ADD CONSTRAINT SubjectGrades_Subjects
    FOREIGN KEY (SubjectID)
    REFERENCES Subjects (SubjectID);
```

-- Table: Meeting
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
    CONSTRAINT Meeting_pk PRIMARY KEY (MeetingID),
    CONSTRAINT chk_Meeting CHECK (
        Price >= 0
    )
);
```

-- Reference: Meeting_LectureRoomDetails (table: Meeting)
```sql

ALTER TABLE Meeting ADD CONSTRAINT Meeting_LectureRoomDetails
    FOREIGN KEY (RoomID)
    REFERENCES LectureRoomDetails (RoomID);
```

-- Reference: Meeting_AvalibleLanguages (table: Meeting)
```sql

ALTER TABLE Meeting ADD CONSTRAINT Meeting_AvalibleLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvalibleLanguages (LanguageID);
```

-- Reference: Meeting_MeetingType (table: Meeting)
```sql

ALTER TABLE Meeting ADD CONSTRAINT Meeting_MeetingType
    FOREIGN KEY (MeetingTypeID)
    REFERENCES MeetingType (MeetingTypeID);
```

-- Reference: Meeting_Subjects (table: Meeting)
```sql

ALTER TABLE Meeting ADD CONSTRAINT Meeting_Subjects
    FOREIGN KEY (SubjectID)
    REFERENCES Subjects (SubjectID);
```

-- Reference: Meeting_Translator (table: Meeting)
```sql

ALTER TABLE Meeting ADD CONSTRAINT Meeting_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);
```

-- Table: StudentAbsence
```sql

CREATE TABLE StudentAbsence (
    MeetingID int  NOT NULL,
    StudentID int  NOT NULL,
    MakeupClassID int  NULL,
    CONSTRAINT StudentAbsence_pk PRIMARY KEY  (MeetingID,StudentID)
);
```

-- Reference: StudentAbsence_Meeting (table: StudentAbsence)
```sql

ALTER TABLE StudentAbsence ADD CONSTRAINT StudentAbsence_Meeting
    FOREIGN KEY (MeetingID)
    REFERENCES Meeting (MeetingID);
```

-- Reference: StudentAbsence_Meeting (table: StudentAbsence)
```sql

ALTER TABLE StudentAbsence ADD CONSTRAINT StudentAbsence_Meeting
    FOREIGN KEY (MakeupClassID)
    REFERENCES Meeting (MeetingID);
```

-- Reference: StudentAbsence_Students (table: StudentAbsence)
```sql

ALTER TABLE StudentAbsence ADD CONSTRAINT StudentAbsence_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);
```

-- Table: Interships
```sql

CREATE TABLE Interships (
    IntershipID int NOT NULL,
    FieldOfStudyID int NOT NULL,
    IntershipName varchar(50) NOT NULL,
    StartDate date NOT NULL,
    EndDate date NOT NULL,
    CONSTRAINT Interships_pk PRIMARY KEY (IntershipID),
    CONSTRAINT chk_Interships CHECK (
        LENGTH(IntershipName) >= 1 AND
        StartDate < EndDate
    )
);
```

-- Reference: Interships_FieldOfStudy (table: Interships)
```sql

ALTER TABLE Interships ADD CONSTRAINT Interships_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);
```

-- Table: IntershipsAbsence
```sql

CREATE TABLE IntershipsAbsence (
    IntershipID int  NOT NULL,
    StudentID int  NOT NULL,
    Absence datetime  NOT NULL,
    CONSTRAINT IntershipsAbsence_pk PRIMARY KEY  (IntershipID,StudentID,Absence)
);
```

-- Reference: IntershipsAbsence_Interships (table: IntershipsAbsence)
```sql

ALTER TABLE IntershipsAbsence ADD CONSTRAINT IIntershipsAbsence_Interships
    FOREIGN KEY (IntershipID)
    REFERENCES Interships (IntershipID);
```

-- Reference: IntershipsAbsence_Students (table: IntershipsAbsence)
```sql

ALTER TABLE IntershipsAbsence ADD CONSTRAINT IntershipsAbsence_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);
```

-- Table: SessionWeek
```sql

CREATE TABLE SessionWeek (
    Semester int NOT NULL,
    RoomID int NOT NULL,
    StartDate date NOT NULL,
    EndDate date NOT NULL,
    FieldOfStudyID int NOT NULL,
    MeetingID int NOT NULL,
    SessionWeekID int NOT NULL,
    CONSTRAINT SessionWeek_pk PRIMARY KEY (SessionWeekID),
    CONSTRAINT chk_SessionWeek CHECK (
        StartDate < EndDate AND
        Semester >= 0
    )
);
```
-- Reference: SessionWeek_FieldOfStudy (table: SessionWeek)
```sql

ALTER TABLE SessionWeek ADD CONSTRAINT SessionWeek_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);
```

-- Reference: SessionWeek_Meeting (table: SessionWeek)
```sql

ALTER TABLE SessionWeek ADD CONSTRAINT SessionWeek_Meeting
    FOREIGN KEY (MeetingID)
    REFERENCES Meeting (MeetingID);
```

-- Reference: SessionWeek_LectureRoomDetails (table: SessionWeek)
```sql

ALTER TABLE SessionWeek ADD CONSTRAINT SessionWeek_LectureRoomDetails
    FOREIGN KEY (RoomID)
    REFERENCES LectureRoomDetails (RoomID);
```

-- Table: MeetingType
```sql

CREATE TABLE MeetingType (
    MeetingTypeID int NOT NULL,
    Description varchar(50) NOT NULL,
    CONSTRAINT MeetingType_pk PRIMARY KEY (MeetingTypeID),
    CONSTRAINT chk_MeetingType CHECK (
        LENGTH(Description) >= 1
    )
);
```

-- Table: Webinar
```sql

CREATE TABLE Webinar (
    WebinarID int  NOT NULL,
    WebinarName varchar(50)  NOT NULL,
    Price money  NOT NULL,
    Webinar_date datetime  NOT NULL,
    LanguageID int  NOT NULL,
    TranslatorID int  NULL,
    EmployeeID int  NOT NULL,
    OnlineLink varchar(max)  NULL,
    VideoLink varchar(max)  NULL,
    CONSTRAINT Webinar_pk PRIMARY KEY  (WebinarID)
    CONSTRAINT chk_Webinar CHECK (Price >= 0 and LENGTH(WebinarName) >= 1)
);
```

-- Reference: Webinar_AvalibleLanguages (table: Webinar)
```sql

ALTER TABLE Webinar ADD CONSTRAINT Webinar_AvalibleLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvalibleLanguages (LanguageID);
```

-- Reference: Webinar_Employees (table: Webinar)
```sql

ALTER TABLE Webinar ADD CONSTRAINT Webinar_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);
```

-- Reference: Webinar_Translator (table: Webinar)
```sql

ALTER TABLE Webinar ADD CONSTRAINT Webinar_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);
```

-- Table: WebinarExpirationDate
```sql

CREATE TABLE WebinarExpirationDate (
    WebinarID int  NOT NULL,
    StudentID int  NOT NULL,
    expr_date date  NULL,
    CONSTRAINT WebinarExpirationDate_pk PRIMARY KEY  (WebinarID,StudentID)
);
```

-- Reference: WebinarExpirationDate_Students (table: WebinarExpirationDate)
```sql

ALTER TABLE WebinarExpirationDate ADD CONSTRAINT WebinarExpirationDate_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);
```

-- Reference: WebinarExpirationDate_Webinar (table: WebinarExpirationDate)
```sql

ALTER TABLE WebinarExpirationDate ADD CONSTRAINT WebinarExpirationDate_Webinar
    FOREIGN KEY (WebinarID)
    REFERENCES Webinar (WebinarID);

```
## Widoki
-- Zestawienie przychodów dla każdego szkolenia
```sql

CREATE VIEW FinancialReport AS
SELECT w.WebinarID AS ID, w.WebinarName AS Name, 'Webinar' AS Type, w.Price *
                                                                    (SELECT count(*)
                                                                     FROM OrderWebinar ow JOIN
                                                                          OrderDetails od ON ow.OrderDetailsID = od.OrderDetailsID JOIN
                                                                          Orders o ON od.OrderID = o.OrderID
                                                                     WHERE ow.WebinarID = w.WebinarID) AS Income
FROM Webinar w
UNION
SELECT c.CourseID AS ID, c.CourseName AS Name, 'Course' AS Type, c.Price *
                                                                 (SELECT count(*)
                                                                  FROM OrderCourse oc JOIN
                                                                       OrderDetails od ON oc.OrderDetailsID = od.OrderDetailsID JOIN
                                                                       Orders o ON od.OrderID = o.OrderID
                                                                  WHERE oc.CourseID = c.CourseID) AS Income
FROM Courses c
UNION
SELECT s.StudiesID AS ID, s.StudiesName AS Name, 'Study' AS Type, s.EntryFee *
                                                                  (SELECT count(*)
                                                                   FROM OrderStudies os JOIN
                                                                        OrderDetails od ON os.OrderDetailsID = od.OrderDetailsID JOIN
                                                                        Orders o ON od.OrderID = o.OrderID
                                                                   WHERE os.FieldOfStudyID = s.FieldOfStudyID) +
                                                                  (SELECT sum(m.MeetingPrice)
                                                                   FROM Meeting m JOIN
                                                                        Subject sb ON sm.SubjectID = sb.SubjectID
                                                                        JOIN OrderMeeting om ON om.MeetingID = m.MeetingID
                                                                   WHERE sb.StudiesID = s.StudiesID) AS Income
FROM FieldOfStudy s
```
-- Zestawienie przychodów dla każdego webinaru
```sql
CREATE VIEW WebinarsFinancialReport AS
SELECT ID AS 'Webinar ID', Name, Income
FROM FinancialReport
WHERE Type = 'Webinar'
```
-- Zestawienie przychodów dla każdego kursu
```sql

CREATE VIEW CoursesFinancialReport AS
SELECT ID AS 'Course ID', Name, Income
FROM FinancialReport
WHERE Type = 'Course'
```

-- Zestawienie przychodów dla każdego studium
```sql

CREATE VIEW StudiesFinancialReport AS
SELECT ID AS 'FieldOfStudy ID', Name, Income
FROM FinancialReport
WHERE Type = 'Study'
```

-- Lista dłużników
```sql

CREATE VIEW DebtorsList AS
SELECT
    S.StudentID,
    S.FirstName,
    S.LastName,
    S.Mail,
    S.Phone
FROM
    Students S
        JOIN Orders O ON S.StudentID = O.StudentID
        LEFT JOIN OrderDetails OD ON O.OrderID = OD.OrderID
WHERE
    OD.PaidDate IS NULL;
```

-- Raport o liczbie zapisanych osób na przyszłe wydarzenia
```sql

CREATE VIEW NumberOfPeopleRegisteredForFutureEvents AS
SELECT w.WebinarID AS ID, w.WebinarName AS Name,
       count(*) AS NumberOfParticipants, 'Webinar' AS Type, 'Online' AS Location
FROM Webinar w JOIN
     WebinarExpirationDate wd ON w.WebinarID = wd.WebinarID
WHERE Webinar_date > getdate()
GROUP BY w.WebinarID, w.WebinarName
UNION
SELECT
    M.ModuleID AS ID,
    M.ModuleName AS Name,
    CS.Course_date AS ModuleDate,
    COUNT(CMP.StudentID) AS TotalAttendees
FROM
    CourseSchedule CS
        JOIN Modules M ON CS.ModuleID = M.ModuleID
        LEFT JOIN CourseModulesProgress CMP ON M.ModuleID = CMP.ModuleID
WHERE
    CS.Course_date > GETDATE()
GROUP BY
    M.ModuleID, M.ModuleName, CS.Course_date
UNION
SELECT m.MeetingID AS ID, s.SubjectName AS Name,
       count(*) AS NumberOfParticipants, 'Study Meeting' AS Type, mt.Description AS Location
FROM Meeting m JOIN
     MeetingType mt ON m.MeetingTypeID = mt.MeetingTypeID
    JOIN Subjects s ON s.SubjectID = m.SubjectID
WHERE  m.Meeting_date > getdate()
GROUP BY m.MeetingID, m.MeetingName
```

-- Raport o liczbie zapisanych osób na przyszłe spotkania studyjne
```sql

CREATE VIEW NumberOfPeopleRegisteredForFutureStudyMeetings AS
SELECT ID AS 'Study Meeting ID', NumberOfParticipants, Location
FROM NumberOfPeopleRegisteredForFutureEvents
WHERE Type = 'Study Meeting'
```

-- Raport o liczbie zapisanych osób na przyszłe moduły w ramach kursów
```sql

CREATE VIEW NumberOfPeopleRegisteredForFutureCourseModules AS
SELECT ID AS 'Course Module ID', NumberOfParticipants, Location
FROM NumberOfPeopleRegisteredForFutureEvents
WHERE Type = 'Course Module'
```
-- Raport o liczbie zapisanych osób na przyszłe webinary
```sql

CREATE VIEW NumberOfPeopleRegisteredForFutureWebinars AS
SELECT ID AS 'Webinar ID', NumberOfParticipants, Location
FROM NumberOfPeopleRegisteredForFutureEvents
WHERE Type = 'Webinar'
```
-- Frekwencja na zakończonych wydarzeniach
```sql

CREATE VIEW AttendanceSummary AS
SELECT sa.MeetingID AS 'Event ID',
       (COUNT(SELECT * FROM GetAttendeesByMeetingID(sa.MeetingID)) - COUNT(sa.StudentID))/ COUNT(SELECT * FROM GetAttendeesByMeetingID(sa.MeetingID)) * 100
       AS [% Frequence],
       'Study meeting' AS 'Event type'
FROM StudentAbsence sa INNER JOIN
     Meeting AS m ON sa.MeetingID = m.MeetingID
WHERE (m.Meeting_date < GETDATE())
GROUP BY sa.MeetingID
UNION
SELECT ma.ModuleID AS 'Event ID',
       (COUNT(SELECT * FROM GetAttendeesByModuleID(ma.ModuleID)) - COUNT(sa.StudentID))/ COUNT(SELECT * FROM GetAttendeesByModuleID(ma.ModuleID)) * 100
    AS [% Frequence],
       'Course Module' AS 'Event type'
FROM ModuleAbsence ma INNER JOIN
     Modules AS m ON m.ModuleID = ma.ModuleID
WHERE (m.Meeting_date < GETDATE())
GROUP BY ma.ModuleID
```
-- Frekwencja na zakończonych spotkaniach studyjnych
```sql

CREATE VIEW StudyMeetingsAttendanceSummary AS
SELECT [Event ID] AS 'Study Meeting ID', [% Frequence]
FROM AttendanceSummary
WHERE [Event type] = 'Study Meeting'
```
-- Frekwencja na zakończonych modułach kursów
```sql

CREATE VIEW CourseModulesAttendanceSummary AS
SELECT [Event ID] AS 'Course Module ID', [% Frequence]
FROM AttendanceSummary
WHERE [Event type] = 'Course Module'
```
## FUNKCJE

-- funkcja zwracająca osoby zapisane na meeting po meetingID
```sql

CREATE FUNCTION GetAttendeesByMeetingID(@MeetingID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT
            S.StudentID,
            S.FirstName,
            S.LastName,
            'SingleMeeting' AS Source
        FROM
            Orders O
                JOIN OrderMeeting OM ON O.OrderID = OM.OrderDetailsID
                JOIN Students S ON O.StudentID = S.StudentID
        WHERE
            OM.MeetingID = @MeetingID

        UNION

        SELECT
            S.StudentID,
            S.FirstName,
            S.LastName,
            'SessionWeek' AS Source
        FROM
            Orders O
                JOIN OrderSessionWeek OSW ON O.OrderID = OSW.OrderDetailsID
                JOIN SessionWeek SW ON OSW.SessionWeekID = SW.SessionWeekID
                JOIN Students S ON O.StudentID = S.StudentID
        WHERE
            SW.MeetingID = @MeetingID

        UNION

        SELECT
            FSL.StudentID,
            S.FirstName,
            S.LastName,
            'Studies' AS Source
        FROM
            FieldOfStudyStudentList FSL
                JOIN Students S ON FSL.StudentID = S.StudentID
                JOIN Subjects SB ON FSL.FieldOfStudyID = SB.FieldOfStudyID
                JOIN Meeting M ON SB.SubjectID = M.SubjectID
        WHERE
            M.MeetingID = @MeetingID;
```

-- funkcja zwracająca osoby zapisane na moduł po moduleID
```sql

CREATE FUNCTION GetAttendeesByModuleID(@ModuleID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT
            S.StudentID,
            S.FirstName,
            S.LastName
        FROM
            Orders O
                JOIN OrderCourse OC ON O.OrderID = OC.OrderDetailsID
                JOIN Courses C ON OC.CourseID = C.CourseID
                JOIN Modules M ON C.CourseID = M.CourseID
                JOIN Students S ON O.StudentID = S.StudentID
        WHERE
            M.ModuleID = @ModuleID;
```
-- Nieobecności danego studenta na studiach/kursach/stażu
```sql

CREATE FUNCTION GetAbsencesByStudentID(@StudentID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT
            M.Date AS AbsenceDate,
            'Meeting' AS EventType,
            M.MeetingID AS ID
        FROM
            StudentAbsence SA
                JOIN Meeting M ON SA.MeetingID = M.MeetingID
        WHERE
            SA.StudentID = @StudentID

        UNION

        SELECT
            MA.Date AS AbsenceDate,
            'CourseModule' AS EventType,
            MA.ModuleID AS ID
        FROM
            ModuleAbsence MA
        WHERE
            MA.StudentID = @StudentID

        UNION

        SELECT IA.Absence AS AbsenceDate,
               'Internship' AS EventType,
               IA.InternshipID AS ID
        FROM
            IntershipsAbsence IA
        WHERE
            IA.StudentID = @StudentID;
```
-- Harmonogram danego kierunku studiów
```sql

CREATE FUNCTION GetStudySchedule(@FieldOfStudyID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT
            SB.SubjectName AS Name,
            M.Meeting_date AS Date,
            M.RoomID,
            SB.EmployeeID,
            SB.Semester
        FROM
            Subjects SB
                JOIN Meeting M ON SB.SubjectID = M.SubjectID
        WHERE
            SB.FieldOfStudyID = @FieldOfStudyID
        AND M.Meeting_date > GETDATE();
```
-- Harmonogram danego kursu
```sql

CREATE FUNCTION GetCourseSchedule(@CourseID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT
            MO.ModuleName AS Name,
            CS.Course_date AS Date,
            C.EmployeeID,
            CS.RoomID
        FROM
            Modules MO
                JOIN CourseSchedule CS ON MO.ModuleID = CS.ModuleID
                JOIN Courses C ON MO.CourseID = C.CourseID
        WHERE
            MO.CourseID = @CourseID
        AND CS.Course_date > GETDATE();
```
-- Harmonogram zajęć dla studenta
```sql

CREATE FUNCTION GetStudentSchedule(@StudentID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT
            M.Meeting_date AS Date,
            'Meeting' AS Type,
            M.RoomID,
            S.SubjectName AS NAME,
            E.FirstName + ' ' + E.LastName AS Teacher
        FROM
            Orders O
                JOIN OrderDetails OD ON O.OrderID = OD.OrderID
                JOIN OrderMeeting OM ON OM.OrderDetailsID = OD.OrderDetailsID
                JOIN Meeting M ON OM.MeetingID = M.MeetingID
                JOIN Subject S ON S.SubjectID = M.SubjectID
                JOIN Employees E ON E.EmployeeID = S.EmployeeID
        WHERE
            O.StudentID = @StudentID
        AND CS.Course_date > GETDATE()
        UNION

        SELECT
            CS.Course_date AS Date,
            'Course Module' AS Type,
            CASE
                WHEN CS.RoomID IS NOT NULL THEN CS.RoomID
                ELSE 'Online'
                END AS Location,
            MO.ModuleName AS Name,
            E.FirstName + ' ' + E.LastName AS Teacher
        FROM
            Orders O
                JOIN OrderDetails OD ON O.OrderID = OD.OrderID
                JOIN OrderCourse OC ON OC.OrderDetailsID = OD.OrderDetailsID
                JOIN Modules MO ON OC.CourseID = MO.CourseID
                JOIN CourseSchedule CS ON MO.ModuleID = CS.ModuleID
                JOIN Course C ON MO.CourseID = C.CourseID
                JOIN Employees E ON E.EmployeeID = C.EmployeeID
        WHERE
            O.StudentID = @StudentID
        AND CS.Course_date > GETDATE()
        UNION

        SELECT
            M.Meeting_date AS Date,
            'StudyMeeting' AS Type,
            M.RoomID,
            S.SubjectName AS NAME,
            E.FirstName + ' ' + E.LastName AS Teacher
        FROM
            FieldOfStudyStudentList FSL
                JOIN Subjects SB ON FSL.FieldOfStudyID = SB.FieldOfStudyID
                JOIN Meeting M ON SB.SubjectID = M.SubjectID
        WHERE
            FSL.StudentID = @StudentID
        AND CS.Course_date > GETDATE()

        UNION

        SELECT
            W.Webinar_date AS Date,
            'Webinar' AS Type,
            'Online' AS RoomID,
            W.WebinarName AS NAME,
            E.FirstName + ' ' + E.LastName AS Teacher
        FROM
            Order O
            JOIN OrderDetails OD ON O.OrderID = OD.OrderID
            JOIN OrderWebinar OW ON OW.OrderDetailsID = OD.OrderDetailsID
            JOIN Webinar W ON W.WebinarID = OW.WebinarID
            JOIN Employees E ON E.EmployeeID = W.EmployeeID
        WHERE
            O.StudentID = @StudentID
        AND W.Webinar_date > GETDATE();
```

-- Obliczanie łącznej wartości zamówienia
```sql

CREATE FUNCTION GetTotalOrderValue(@OrderID INT)
    RETURNS MONEY
AS
BEGIN
    DECLARE @TotalValue MONEY = 0;

    SELECT
        @TotalValue = @TotalValue + SUM(C.Price)
    FROM
        OrderCourse OC
            JOIN Courses C ON OC.CourseID = C.CourseID
    WHERE
        OC.OrderDetailsID IN (
            SELECT OrderDetailsID
            FROM OrderDetails
            WHERE OrderID = @OrderID
        );

    SELECT
        @TotalValue = @TotalValue + SUM(W.Price)
    FROM
        OrderWebinar OW
            JOIN Webinar W ON OW.WebinarID = W.WebinarID
    WHERE
        OW.OrderDetailsID IN (
            SELECT OrderDetailsID
            FROM OrderDetails
            WHERE OrderID = @OrderID
        );

    SELECT
        @TotalValue = @TotalValue + SUM(M.Price)
    FROM
        OrderSessionWeek OSW
            JOIN SessionWeek SW ON OSW.SessionWeekID = SW.SessionWeekID
            JOIN Meeting M ON SW.MeetingID = M.MeetingID
    WHERE
        OSW.OrderDetailsID IN (
            SELECT OrderDetailsID
            FROM OrderDetails
            WHERE OrderID = @OrderID
        );

    SELECT
        @TotalValue = @TotalValue + SUM(FOS.EntryFee)
    FROM
        OrderStudies OS
            JOIN FieldOfStudy FOS ON OS.FieldOfStudyID = FOS.FieldOfStudyID
    WHERE
        OS.OrderDetailsID IN (
            SELECT OrderDetailsID
            FROM OrderDetails
            WHERE OrderID = @OrderID
        );

    RETURN @TotalValue;
END;
```
-- Sprawdzanie czy student odbył wszystkie praktyki, które powinien odbyć:
```sql

CREATE FUNCTION CheckInternshipAbsences(@StudentID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT
            I.IntershipID,
            I.IntershipName,
            IA.Absence AS AbsenceDate
        FROM
            FieldOfStudyStudentList FSL
                JOIN Interships I ON FSL.FieldOfStudyID = I.FieldOfStudyID
                LEFT JOIN IntershipsAbsence IA ON I.IntershipID = IA.IntershipID AND IA.StudentID = FSL.StudentID
        WHERE
            FSL.StudentID = @StudentID
          AND IA.Absence IS NOT NULL;
```