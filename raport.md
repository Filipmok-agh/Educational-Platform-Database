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
        LEN(CourseType) >= 1 AND
        Price >= 0 AND
        Limit >=0 AND
        LEN(CourseName)>=1
    )
);
```
-- Reference: Courses_AvalibleLanguages (table: Courses)
```sql
ALTER TABLE Courses ADD CONSTRAINT Courses_AvailableLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvailableLanguages (LanguageID);
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
    MeetingsQuantity int NOT NULL,
    CONSTRAINT Modules_pk PRIMARY KEY (ModuleID),
    CONSTRAINT chk_Modules CHECK (
        LEN(ModuleName) >= 1 AND
        MeetingsQuantity > 0 AND
        LEN(ModuleType) >= 1
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
    ModuleID int NOT NULL,
    StudentID int NOT NULL,
    Date datetime NOT NULL,
    CONSTRAINT ModuleAbsence_pk PRIMARY KEY (ModuleID, StudentID, Date)
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
    OrderDetailsID int NOT NULL,
    CourseID int NOT NULL,
    CONSTRAINT OrderCourse_pk PRIMARY KEY (OrderDetailsID)
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
    OrderDetailsID int NOT NULL,
    PaidDate datetime NULL,
    OrderID int NOT NULL,
    AccessGiven bit NOT NULL,
    CONSTRAINT OrderDetails_pk PRIMARY KEY (OrderDetailsID)
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
    OrderDetailsID int NOT NULL,
    MeetingID int NOT NULL,
    CONSTRAINT OrderMeeting_pk PRIMARY KEY (OrderDetailsID)
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
    OrderDetailsID int NOT NULL,
    FieldOfStudyID int NOT NULL,
    CONSTRAINT OrderStudies_pk PRIMARY KEY (OrderDetailsID)
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
    OrderDetailsID int NOT NULL,
    WebinarID int NOT NULL,
    CONSTRAINT OrderWebinar_pk PRIMARY KEY (OrderDetailsID)
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

-- Table: OrderStationaryWeek
```sql
CREATE TABLE OrderStationaryWeek (
    OrderDetailsID int NOT NULL,
    StationaryID int NOT NULL,
    CONSTRAINT OrderStationaryWeek_pk PRIMARY KEY (OrderDetailsID)
);
```

-- Reference: OrderStationaryWeek_OrderDetails (table: OrderStationaryWeek)
```sql
ALTER TABLE OrderStationaryWeek ADD CONSTRAINT OrderStationaryWeek_OrderDetails
    FOREIGN KEY (OrderDetailsID)
    REFERENCES OrderDetails (OrderDetailsID);
```

-- Reference: OrderStationaryWeek_StationaryWeek (table: OrderStationaryWeek)
```sql
ALTER TABLE OrderStationaryWeek ADD CONSTRAINT OrderStationaryWeek_StationaryWeek
    FOREIGN KEY (StationaryID)
    REFERENCES StationaryWeek (StationaryID);
```

-- Table: Translator
```sql

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
```

-- Table: Employees
```sql

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
```

-- Table: EmployeeType
```sql

CREATE TABLE EmployeeType (
    EmployeeID int NOT NULL,
    HeldPosition varchar(50) NOT NULL,
    CONSTRAINT EmployeeType_pk PRIMARY KEY (EmployeeID),
    CONSTRAINT chk_EmployeeType CHECK (
        LEN(HeldPosition) >= 1
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
        LEN(BuildingNr) >= 1 AND
        ClassNumber >= 0
    )
);
```

-- Table: Languages
```sql

CREATE TABLE Languages (
    TranslatorID int NOT NULL,
    LanguageID int NOT NULL,
    CONSTRAINT LanguageID_pk PRIMARY KEY (TranslatorID, LanguageID)
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

CREATE TABLE AvailableLanguages (
    LanguageID int NOT NULL,
    Language varchar(50) NOT NULL,
    CONSTRAINT AvailableLanguages_pk PRIMARY KEY (LanguageID),
    CONSTRAINT chk_AvailableLanguages CHECK (
        LEN(Language) >= 1
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
        LEN(Name) >= 1 AND
        LEN(Description) >= 1 AND
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
        LEN(SubjectName) >= 1 AND
        LEN(Description) >= 1 AND
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
    StationaryID int NULL,
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

ALTER TABLE Meeting ADD CONSTRAINT Meeting_AvailableLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvailableLanguages (LanguageID);
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
-- Reference: Meeting_StationaryWeek (table: Meeting)
```sql
ALTER TABLE Meeting ADD CONSTRAINT Meeting_StationaryWeek
    FOREIGN KEY (StationaryID)
    REFERENCES StationaryWeek (StationaryID);
```

-- Table: StudentAbsence
```sql

CREATE TABLE StudentAbsence (
    MeetingID int NOT NULL,
    StudentID int NOT NULL,
    ClassRetakeID int NULL,
    CONSTRAINT StudentAbsence_pk PRIMARY KEY (MeetingID, StudentID)
);
```

-- Reference: StudentAbsence_Meeting (table: StudentAbsence)
```sql

ALTER TABLE StudentAbsence ADD CONSTRAINT StudentAbsence_Meeting
    FOREIGN KEY (MeetingID)
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
        LEN(IntershipName) >= 1 AND
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
    IntershipID int NOT NULL,
    StudentID int NOT NULL,
    Absence datetime NOT NULL,
    CONSTRAINT IntershipsAbsence_pk PRIMARY KEY (IntershipID, StudentID, Absence)
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

-- Table: StationaryWeek
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

-- Reference: StationaryWeek_FieldOfStudy (table: StationaryWeek)
```sql
ALTER TABLE StationaryWeek ADD CONSTRAINT StationaryWeek_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);
```

-- Table: MeetingType
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

-- Table: Webinar
```sql

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
    WebinarID int NOT NULL,
    StudentID int NOT NULL,
    expr_date date NULL,
    CONSTRAINT WebinarExpirationDate_pk PRIMARY KEY (WebinarID, StudentID)
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
UNION ALL
SELECT c.CourseID AS ID, c.CourseName AS Name, 'Course' AS Type, c.Price *
     (SELECT count(*)
      FROM OrderCourse oc JOIN
           OrderDetails od ON oc.OrderDetailsID = od.OrderDetailsID JOIN
           Orders o ON od.OrderID = o.OrderID
      WHERE oc.CourseID = c.CourseID) AS Income
FROM Courses c
UNION ALL
SELECT s.FieldOfStudyID AS ID, s.Name AS Name, 'Study' AS Type, s.EntryFee *
      (SELECT count(*)
       FROM OrderStudies os JOIN
            OrderDetails od ON os.OrderDetailsID = od.OrderDetailsID JOIN
            Orders o ON od.OrderID = o.OrderID
       WHERE os.FieldOfStudyID = s.FieldOfStudyID) +
      (SELECT sum(m.Price)
       FROM Meeting m JOIN
            Subjects sb ON m.SubjectID = sb.SubjectID
            JOIN OrderMeeting om ON om.MeetingID = m.MeetingID
       WHERE sb.FieldOfStudyID = s.FieldOfStudyID) AS Income
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
        JOIN OrderDetails OD ON O.OrderID = OD.OrderID
WHERE
    OD.PaidDate IS NULL;
```

-- Raport o liczbie zapisanych osób na przyszłe wydarzenia
```sql

CREATE VIEW NumberOfPeopleRegisteredForFutureEvents AS
SELECT w.WebinarID AS ID, w.WebinarName AS Name,
       count(*) AS NumberOfParticipants, 'Webinar' AS Type
FROM Webinar w JOIN
     WebinarExpirationDate wd ON w.WebinarID = wd.WebinarID
WHERE Webinar_date > getdate()
GROUP BY w.WebinarID, w.WebinarName
UNION ALL
SELECT
    M.ModuleID AS ID,
    M.ModuleName AS Name,
    COUNT(CMP.StudentID) AS NumberOfParticipants,
    'CouseModule' AS Type
FROM
    CourseSchedule CS
        JOIN Modules M ON CS.ModuleID = M.ModuleID
        LEFT JOIN CourseModulesProgress CMP ON M.ModuleID = CMP.ModuleID
WHERE
    CS.Course_date > GETDATE()
GROUP BY
    M.ModuleID, M.ModuleName, CS.Course_date
UNION ALL
SELECT m.MeetingID AS ID, s.SubjectName AS Name,
       count(*) AS NumberOfParticipants, 'Study Meeting' AS Type
FROM Meeting m JOIN
     MeetingType mt ON m.MeetingTypeID = mt.MeetingTypeID
    JOIN Subjects s ON s.SubjectID = m.SubjectID
WHERE  m.Meeting_date > getdate()
GROUP BY m.MeetingID, mt.Description, s.SubjectName
```

-- Raport o liczbie zapisanych osób na przyszłe spotkania studyjne
```sql

CREATE VIEW NumberOfPeopleRegisteredForFutureStudyMeetings AS
SELECT ID AS 'StudyMeetingID', NumberOfParticipants
FROM NumberOfPeopleRegisteredForFutureEvents
WHERE Type = 'Study Meeting'
```

-- Raport o liczbie zapisanych osób na przyszłe moduły w ramach kursów
```sql

CREATE VIEW NumberOfPeopleRegisteredForFutureCourseModules AS
SELECT ID AS 'CourseModuleID', NumberOfParticipants
FROM NumberOfPeopleRegisteredForFutureEvents
WHERE Type = 'Course Module'
```
-- Raport o liczbie zapisanych osób na przyszłe webinary
```sql

CREATE VIEW NumberOfPeopleRegisteredForFutureWebinars AS
SELECT ID AS 'WebinarID', NumberOfParticipants
FROM NumberOfPeopleRegisteredForFutureEvents
WHERE Type = 'Webinar'
```
-- Frekwencja na zakończonych wydarzeniach
```sql

CREATE VIEW AttendanceSummary AS
WITH MeetingAttendance AS (
    SELECT sa.MeetingID AS EventID,
           COUNT(*) AS TotalAbsences,
           (SELECT COUNT(*) FROM GetAttendeesByMeetingID(sa.MeetingID)) AS TotalAttendees,
           'Study meeting' AS EventType
    FROM StudentAbsence sa
             INNER JOIN Meeting AS m ON sa.MeetingID = m.MeetingID
    WHERE m.Meeting_date < GETDATE()
    GROUP BY sa.MeetingID
),
     ModuleAttendance AS (
         SELECT ma.ModuleID AS EventID,
                COUNT(*) AS TotalAbsences,
                (SELECT COUNT(*) FROM GetAttendeesByModuleID(ma.ModuleID)) AS TotalAttendees,
                'Course Module' AS EventType
         FROM ModuleAbsence ma
                  INNER JOIN Modules AS m ON m.ModuleID = ma.ModuleID
                  JOIN CourseSchedule cs ON m.ModuleID = cs.ModuleID
         WHERE cs.Course_date < GETDATE()
         GROUP BY ma.ModuleID
     )
SELECT EventID,
       (TotalAttendees - TotalAbsences) * 100.0 / TotalAttendees AS FrequencePercentage,
       EventType
FROM MeetingAttendance
UNION ALL
SELECT EventID,
       (TotalAttendees - TotalAbsences) * 100.0 / TotalAttendees AS FrequencePercentage,
       EventType
FROM ModuleAttendance;
```
-- Frekwencja na zakończonych spotkaniach studyjnych
```sql

CREATE VIEW StudyMeetingsAttendanceSummary AS
SELECT EventID AS 'StudyMeetingID', FrequencePercentage
FROM AttendanceSummary
WHERE Eventtype = 'Study Meeting'
```
-- Frekwencja na zakończonych modułach kursów
```sql

CREATE VIEW CourseModulesAttendanceSummary AS
SELECT EventID AS 'CourseModuleID', FrequencePercentage
FROM AttendanceSummary
WHERE Eventtype = 'Course Module'
```
-- Lista obecności na każdy meeting
```sql
CREATE VIEW MeetingPresenceList AS
SELECT
    M.MeetingID,
    M.Meeting_date AS MeetingDate,
    A.StudentID,
    A.FirstName,
    A.LastName,
    CASE
        WHEN SA.StudentID IS NOT NULL THEN 'Absent'
        ELSE 'Present'
    END AS AttendanceStatus
FROM
    Meeting M
        CROSS APPLY GetAttendeesByMeetingID(M.MeetingID) A
        LEFT JOIN StudentAbsence SA ON M.MeetingID = SA.MeetingID AND A.StudentID = SA.StudentID
WHERE
    M.Meeting_date < GETDATE();
```
-- Lista obecności na każdy moduł kursu

```sql
CREATE VIEW AttendanceListForModules AS
SELECT
    M.ModuleID,
    CS.Course_date AS ModuleDate,
    A.StudentID,
    A.FirstName,
    A.LastName,
    CASE
        WHEN MA.StudentID IS NOT NULL THEN 'Absent'
        ELSE 'Present'
        END AS AttendanceStatus
FROM
    CourseSchedule CS
        JOIN Modules M ON CS.ModuleID = M.ModuleID
        CROSS APPLY GetAttendeesByModuleID(M.ModuleID) A
        LEFT JOIN ModuleAbsence MA ON M.ModuleID = MA.ModuleID AND A.StudentID = MA.StudentID
WHERE
    CS.Course_date < GETDATE();
```

-- Lista osób zapisanych na co najmniej 2 przyszłe szkolenia, które kolidują ze sobą czasowo

```sql
CREATE VIEW ConflictingFutureEventRegistrations AS
WITH AllEvents AS (
    -- Moduły kursów
    SELECT
        A.StudentID,
        A.FirstName,
        A.LastName,
        'Module' AS EventType,
        M.ModuleID AS EventID,
        CS.Course_date AS EventDate
    FROM
        CourseSchedule CS
            JOIN Modules M ON CS.ModuleID = M.ModuleID
            CROSS APPLY GetAttendeesByModuleID(M.ModuleID) A
    WHERE
        CS.Course_date > GETDATE()

    UNION ALL

    -- Webinary
    SELECT
        A.StudentID,
        A.FirstName,
        A.LastName,
        'Webinar' AS EventType,
        W.WebinarID AS EventID,
        W.Webinar_date AS EventDate
    FROM
        Webinar W
            CROSS APPLY GetAttendeesByWebinarID(W.WebinarID) A
    WHERE
        W.Webinar_date > GETDATE()

    UNION ALL

    -- Spotkania studyjne
    SELECT
        A.StudentID,
        A.FirstName,
        A.LastName,
        'Meeting' AS EventType,
        M.MeetingID AS EventID,
        M.Meeting_date AS EventDate
    FROM
        Meeting M
            CROSS APPLY GetAttendeesByMeetingID(M.MeetingID) A
    WHERE
        M.Meeting_date > GETDATE()
),
Conflicts AS (
    SELECT
        E1.StudentID,
        E1.FirstName,
        E1.LastName,
        E1.EventType AS EventType1,
        E1.EventID AS EventID1,
        E1.EventDate AS EventDate1,
        E2.EventType AS EventType2,
        E2.EventID AS EventID2,
        E2.EventDate AS EventDate2
    FROM
        AllEvents E1
        JOIN AllEvents E2 ON E1.StudentID = E2.StudentID
            AND E1.EventID <> E2.EventID
            AND E1.EventDate = E2.EventDate
)
SELECT DISTINCT
    StudentID,
    FirstName,
    LastName,
    EventType1,
    EventID1,
    EventDate1,
    EventType2,
    EventID2,
    EventDate2
FROM
    Conflicts;
```
# FUNKCJE

## funkcja zwracająca osoby zapisane na meeting po meetingID
```sql

CREATE FUNCTION GetAttendeesByMeetingID(@MeetingID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT
            MeetingID,
            S.StudentID,
            S.FirstName,
            S.LastName,
            'SingleMeeting' AS Source
        FROM
            Orders O
                JOIN OrderDetails OD On OD.OrderID = O.OrderID
                JOIN OrderMeeting OM ON OD.OrderDetailsID = OM.OrderDetailsID
                JOIN Students S ON O.StudentID = S.StudentID
        WHERE
            OM.MeetingID = @MeetingID

        UNION ALL

        SELECT
            MeetingID,
            S.StudentID,
            S.FirstName,
            S.LastName,
            'SessionWeek' AS Source
        FROM
            Orders O
                JOIN OrderDetails OD ON O.OrderID = OD.OrderID
                JOIN OrderStationaryWeek OSW ON OD.OrderDetailsID = OSW.OrderDetailsID
                JOIN StationaryWeek SW ON OSW.StationaryID = SW.StationaryID
                JOIN Students S ON O.StudentID = S.StudentID
                JOIN Meeting M ON M.StationaryID = SW.StationaryID
        WHERE
            M.MeetingID = @MeetingID

        UNION ALL

        SELECT
            M.MeetingID,
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

## funkcja zwracająca osoby zapisane na moduł po moduleID
```sql

CREATE FUNCTION GetAttendeesByModuleID(@ModuleID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT
            ModuleID,
            S.StudentID,
            S.FirstName,
            S.LastName
        FROM
            Orders O
                JOIN OrderDetails OD ON OD.OrderID = O.OrderID
                JOIN OrderCourse OC ON OD.OrderDetailsID = OC.OrderDetailsID
                JOIN Courses C ON OC.CourseID = C.CourseID
                JOIN Modules M ON C.CourseID = M.CourseID
                JOIN Students S ON O.StudentID = S.StudentID
        WHERE
            M.ModuleID = @ModuleID;
```

## funkcja zwracająca osoby zapisane na webinar po webinarID
```sql
CREATE FUNCTION GetAttendeesByWebinarID(@WebinarID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT
            W.WebinarID,
            S.StudentID,
            S.FirstName,
            S.LastName
        FROM
            Orders O
                JOIN OrderDetails OD ON OD.OrderID = O.OrderID
                JOIN OrderWebinar OW ON OD.OrderDetailsID = OW.OrderDetailsID
                JOIN Webinar W ON OW.WebinarID = W.WebinarID
                JOIN Students S ON O.StudentID = S.StudentID
        WHERE
            W.WebinarID = @WebinarID;
```
## funkcja zwracająca osoby zapisane na kurs po CourseID
```sql
CREATE FUNCTION GetAttendeesByCourseID(@CourseID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT
            C.CourseID,
            S.StudentID,
            S.FirstName,
            S.LastName
        FROM
            Orders O
                JOIN OrderDetails OD ON OD.OrderID = O.OrderID
                JOIN OrderCourse OC ON OD.OrderDetailsID = OC.OrderDetailsID
                JOIN Courses C ON OC.CourseID = C.CourseID
                JOIN Students S ON O.StudentID = S.StudentID
        WHERE
            C.CourseID = @CourseID;
```

## Nieobecności danego studenta na studiach/kursach/stażu
```sql

CREATE FUNCTION GetAbsencesByStudentID(@StudentID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT
            M.Meeting_date AS AbsenceDate,
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
               IA.IntershipID AS ID
        FROM
            IntershipsAbsence IA
        WHERE
            IA.StudentID = @StudentID;
```
## Harmonogram danego kierunku studiów
```sql

CREATE FUNCTION GetStudySchedule(@FieldOfStudyID INT, @StartDate DATE, @EndDate DATE)
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
        AND M.Meeting_date > @StartDate AND M.Meeting_date < @EndDate;
```
## Harmonogram danego kursu
```sql

CREATE FUNCTION GetCourseSchedule(@CourseID INT, @StartDate DATE, @EndDate DATE)
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
        AND CS.Course_date > @StartDate AND CS.Course_date < @EndDate;
```
## Harmonogram zajęć dla studenta
```sql

CREATE FUNCTION GetStudentSchedule(@StudentID INT, @StartDate DATE, @EndDate DATE )
    RETURNS TABLE
        AS
        RETURN
        SELECT
            M.Meeting_date AS Date,
            'Meeting' AS Type,
            M.RoomID,
            S.SubjectName AS NAME,
            E.FirstName AS TeacherFirstName,
            E.LastName AS TeacherLastName
        FROM
            Orders O
                JOIN OrderDetails OD ON O.OrderID = OD.OrderID
                JOIN OrderMeeting OM ON OM.OrderDetailsID = OD.OrderDetailsID
                JOIN Meeting M ON OM.MeetingID = M.MeetingID
                JOIN Subjects S ON S.SubjectID = M.SubjectID
                JOIN Employees E ON E.EmployeeID = S.EmployeeID
        WHERE
            O.StudentID = @StudentID
        AND M.Meeting_date > @StartDate AND M.Meeting_date < @EndDate
        UNION ALL

        SELECT
            CS.Course_date AS Date,
            'Course Module' AS Type,
            CASE
                WHEN CS.RoomID IS NOT NULL THEN CS.RoomID
                ELSE 'Online'
                END AS Location,
            MO.ModuleName AS Name,
            E.FirstName AS TeacherFirstName,
            E.LastName AS TeacherLastName
        FROM
            Orders O
                JOIN OrderDetails OD ON O.OrderID = OD.OrderID
                JOIN OrderCourse OC ON OC.OrderDetailsID = OD.OrderDetailsID
                JOIN Modules MO ON OC.CourseID = MO.CourseID
                JOIN CourseSchedule CS ON MO.ModuleID = CS.ModuleID
                JOIN Courses C ON MO.CourseID = C.CourseID
                JOIN Employees E ON E.EmployeeID = C.EmployeeID
        WHERE
            O.StudentID = @StudentID
        AND CS.Course_date > @StartDate AND CS.Course_date < @EndDate

        UNION ALL

        SELECT
            W.Webinar_date AS Date,
            'Webinar' AS Type,
            'Online' AS RoomID,
            W.WebinarName AS NAME,
            E.FirstName As TeacherFirstName,
            E.LastName AS TeacherLastName
        FROM
            Orders O
            JOIN OrderDetails OD ON O.OrderID = OD.OrderID
            JOIN OrderWebinar OW ON OW.OrderDetailsID = OD.OrderDetailsID
            JOIN Webinar W ON W.WebinarID = OW.WebinarID
            JOIN Employees E ON E.EmployeeID = W.EmployeeID
        WHERE
            O.StudentID = @StudentID
        AND W.Webinar_date > @StartDate AND W.Webinar_date < @EndDate;
```

## Obliczanie łącznej wartości zamówienia
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
        OrderStationaryWeek OSW
            JOIN StationaryWeek SW ON OSW.StationaryID = SW.StationaryID
            JOIN Meeting M ON SW.StationaryID = M.StationaryID
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
## Sprawdzanie czy student odbył wszystkie praktyki, które powinien odbyć:
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
##  funkcja wyświetlająca listę zjazdów do zapłaty przez studenta
```sql
CREATE FUNCTION GetUnpaidStationaryWeeks(@StudentID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT
            Semester,
            sw.StationaryID,
            FieldOfStudyID,
            Price
        FROM
            StationaryWeek sw
            JOIN OrderStationaryWeek ON sw.StationaryID = OrderStationaryWeek.StationaryID
            JOIN OrderDetails ON OrderStationaryWeek.OrderDetailsID = OrderDetails.OrderDetailsID
            JOIN Orders ON OrderDetails.OrderID = Orders.OrderID AND Orders.StudentID = @StudentID
        WHERE
            StartDate > GETDATE();
```

# Procedury

# People
## Dodanie studenta
```sql
CREATE PROCEDURE AddStudent
    @FirstName varchar(50),
    @LastName varchar(50),
    @DateOfBirth date,
    @Country varchar(50),
    @City varchar(50),
    @Address varchar(50),
    @Mail varchar(50),
    @Phone varchar(15)
AS
BEGIN
    INSERT INTO Students (FirstName, LastName, DateOfBirth, Country, City, Address, Mail, Phone)
    VALUES (@FirstName, @LastName, @DateOfBirth, @Country, @City, @Address, @Mail, @Phone);
END;
```

## Dodanie pracownika
```sql
CREATE PROCEDURE AddEmployee
    @FirstName varchar(50),
    @LastName varchar(50),
    @DateOfBirth date,
    @Country varchar(50),
    @City varchar(50),
    @Address varchar(50),
    @Mail varchar(50),
    @Phone varchar(15)
AS
BEGIN
    INSERT INTO Employees (FirstName, LastName, DateOfBirth, Country, City, Address, Mail, Phone)
    VALUES (@FirstName, @LastName, @DateOfBirth, @Country, @City, @Address, @Mail, @Phone);
END;
```
## Przypisanie pracownikowi jego roli
```sql
CREATE PROCEDURE AddEmployeeType
    @EmployeeID int,
    @HeldPosition varchar(50)
AS
BEGIN
    IF EXISTS (SELECT 1 FROM Employees WHERE EmployeeID = @EmployeeID)
    BEGIN
        INSERT INTO EmployeeType (EmployeeID, HeldPosition)
        VALUES (@EmployeeID, @HeldPosition);
    END
    ELSE
    BEGIN
        RAISERROR('EmployeeID does not exist.', 16, 1);
    END
END;
```
## Dodanie języka
```sql
CREATE PROCEDURE AddAvailableLanguage
    @Language varchar(50)
AS
BEGIN
    INSERT INTO AvailableLanguages (Language)
    VALUES (@Language);
END;
```

## Dodanie tłumacza
```sql
CREATE PROCEDURE AddTranslator
    @FirstName varchar(50),
    @LastName varchar(50),
    @DateOfBirth date,
    @Country varchar(50),
    @City varchar(50),
    @Address varchar(50),
    @Mail varchar(50),
    @Phone varchar(15)
AS
BEGIN
    INSERT INTO Translator (FirstName, LastName, DateOfBirth, Country, City, Address, Mail, Phone)
    VALUES (@FirstName, @LastName, @DateOfBirth, @Country, @City, @Address, @Mail, @Phone);
END;
```
## Przypisanie tłumaczowi języka z którego tłumaczy
```sql
CREATE PROCEDURE AddLanguage
    @TranslatorID int,
    @LanguageID int
AS
BEGIN
    IF EXISTS (SELECT 1 FROM Translator WHERE TranslatorID = @TranslatorID)
       AND EXISTS (SELECT 1 FROM AvailableLanguages WHERE LanguageID = @LanguageID)
    BEGIN
        INSERT INTO Languages (TranslatorID, LanguageID)
        VALUES (@TranslatorID, @LanguageID);
    END
    ELSE
    BEGIN
        RAISERROR('Either TranslatorID or LanguageID does not exist.', 16, 1);
    END
END;
```

## Dodanie miejsca odbywania się zajęć
```sql
CREATE PROCEDURE AddLectureRoomDetails
    @BuildingNr varchar(10),
    @Floor int,
    @ClassNumber int
AS
BEGIN
    INSERT INTO LectureRoomDetails (BuildingNr, Floor, ClassNumber)
    VALUES (@BuildingNr, @Floor, @ClassNumber);
END;
```
# Webinars

## Dodanie webinaru
```sql
CREATE PROCEDURE AddWebinar
    @WebinarName VARCHAR(50),
    @Price MONEY,
    @Webinar_date DATETIME,
    @LanguageID INT,
    @TranslatorID INT = NULL,
    @EmployeeID INT,
    @OnlineLink VARCHAR(MAX) = NULL,
    @VideoLink VARCHAR(MAX) = NULL
AS
BEGIN
    IF NOT EXISTS (SELECT 1 FROM Employees WHERE EmployeeID = @EmployeeID)
    BEGIN
        RAISERROR('Pracownik o podanym ID nie istnieje.', 16, 1);
    END

    IF NOT EXISTS (SELECT 1 FROM AvailableLanguages WHERE LanguageID = @LanguageID)
    BEGIN
        RAISERROR('Język o podanym ID nie istnieje.', 16, 1);
    END

    IF @TranslatorID IS NOT NULL AND NOT EXISTS (SELECT 1 FROM Translator WHERE TranslatorID = @TranslatorID)
    BEGIN
        RAISERROR('Tłumacz o podanym ID nie istnieje.', 16, 1);
    END

    IF @TranslatorID IS NOT NULL AND NOT EXISTS (SELECT 1 FROM Languages WHERE TranslatorID = @TranslatorID AND LanguageID = @LanguageID)
    BEGIN
        RAISERROR('Tłumacz o podanym ID nie jest przypisany do tego języka.', 16, 1);
    END

    IF @Webinar_date <= GETDATE()
    BEGIN
        RAISERROR('Data webinarium musi być w przyszłości.', 16, 1);
    END

    IF (@OnlineLink IS NULL AND @VideoLink IS NULL)
    BEGIN
        RAISERROR('Przynajmniej jeden z linków: OnlineLink lub VideoLink musi zostać podany.', 16, 1);
    END

    INSERT INTO Webinar (WebinarName, Price, Webinar_date, LanguageID, TranslatorID, EmployeeID, OnlineLink, VideoLink)
    VALUES (@WebinarName, @Price, @Webinar_date, @LanguageID, @TranslatorID, @EmployeeID, @OnlineLink, @VideoLink);
END;
```

# Courses

## Dodanie kursu
```sql
CREATE PROCEDURE AddCourse
    @CourseName VARCHAR(50),
    @EmployeeID INT,
    @Price MONEY,
    @CourseType VARCHAR(50),
    @Limit INT = NULL,
    @LanguageID INT,
    @TranslatorID INT = NULL
AS
BEGIN
    IF NOT EXISTS (SELECT 1 FROM Employees WHERE EmployeeID = @EmployeeID)
    BEGIN
        RAISERROR('Pracownik o podanym ID nie istnieje.', 16, 1);
    END

    IF NOT EXISTS (SELECT 1 FROM AvailableLanguages WHERE LanguageID = @LanguageID)
    BEGIN
        RAISERROR('Język o podanym ID nie istnieje.', 16, 1);
    END

    IF @TranslatorID IS NOT NULL AND NOT EXISTS (SELECT 1 FROM Translator WHERE TranslatorID = @TranslatorID)
    BEGIN
        RAISERROR('Tłumacz o podanym ID nie istnieje.', 16, 1);
    END

    IF @TranslatorID IS NOT NULL AND NOT EXISTS (SELECT 1 FROM Languages WHERE TranslatorID = @TranslatorID AND LanguageID = @LanguageID)
    BEGIN
        RAISERROR('Tłumacz o podanym ID nie jest przypisany do tego języka.', 16, 1);
    END

    INSERT INTO Courses (CourseName, EmployeeID, Price, CourseType, Limit, LanguageID, TranslatorID)
    VALUES (@CourseName, @EmployeeID, @Price, @CourseType, @Limit, @LanguageID, @TranslatorID);
END;
```

## Dodanie modułu
```sql
CREATE PROCEDURE AddModule
    @ModuleName VARCHAR(50),
    @CourseID INT,
    @ModuleType VARCHAR(50),
    @MeetingsQuantity INT
AS
BEGIN
    IF NOT EXISTS (SELECT 1 FROM Courses WHERE CourseID = @CourseID)
    BEGIN
        RAISERROR('Kurs o podanym ID nie istnieje.', 16, 1);
    END

    INSERT INTO Modules (ModuleName, CourseID, ModuleType, MeetingsQuantity)
    VALUES (@ModuleName, @CourseID, @ModuleType, @MeetingsQuantity);
END;
```

## Dodanie nieobecności na module
```sql
CREATE PROCEDURE AddModuleAbsence
    @ModuleID INT,
    @StudentID INT,
    @Date DATETIME
AS
BEGIN
    IF NOT EXISTS (SELECT 1 FROM Modules WHERE ModuleID = @ModuleID)
    BEGIN
        RAISERROR('Moduł o podanym ID nie istnieje.', 16, 1);
    END

    IF NOT EXISTS (SELECT 1 FROM Students WHERE StudentID = @StudentID)
    BEGIN
        RAISERROR('Student o podanym ID nie istnieje.', 16, 1);
    END

    INSERT INTO ModuleAbsence (ModuleID, StudentID, Date)
    VALUES (@ModuleID, @StudentID, @Date);
END;
```

## Dodanie harmonogramu dla kursu
```sql
CREATE PROCEDURE AddCourseSchedule
    @ModuleID INT,
    @RoomID INT = NULL,
    @LiveLink VARCHAR(MAX) = NULL,
    @VideoLink VARCHAR(MAX) = NULL,
    @Course_date DATETIME
AS
BEGIN
    IF NOT EXISTS (SELECT 1 FROM Modules WHERE ModuleID = @ModuleID)
    BEGIN
        RAISERROR('Moduł o podanym ID nie istnieje.', 16, 1);
    END

    IF @Course_date <= GETDATE()
    BEGIN
        RAISERROR('Data kursu musi być w przyszłości.', 16, 1);
    END

    IF (@RoomID IS NULL AND @LiveLink IS NULL AND @VideoLink IS NULL)
    BEGIN
        RAISERROR('Przynajmniej jedno z pól: RoomID, LiveLink, VideoLink musi być wypełnione.', 16, 1);
    END

    IF @RoomID IS NOT NULL AND NOT EXISTS (SELECT 1 FROM LectureRoomDetails WHERE RoomID = @RoomID)
    BEGIN
        RAISERROR('Podany RoomID nie istnieje w tabeli LectureRoomDetails.', 16, 1);
    END

    INSERT INTO CourseSchedule (ModuleID, RoomID, LiveLink, VideoLink, Course_date)
    VALUES (@ModuleID, @RoomID, @LiveLink, @VideoLink, @Course_date);
END;
```

# Studies

## Dodanie kierunku studiów
```sql
CREATE PROCEDURE AddFieldOfStudy
    @Name VARCHAR(50),
    @Description VARCHAR(50),
    @Limit INT,
    @EntryFee MONEY
AS
BEGIN
    INSERT INTO FieldOfStudy (Name, Description, Limit, EntryFee)
    VALUES (@Name, @Description, @Limit, @EntryFee);
END;
```

## Dodanie przedmiotu 
```sql
CREATE PROCEDURE AddSubject
    @FieldOfStudyID INT,
    @SubjectName VARCHAR(50),
    @Description VARCHAR(50),
    @MeetingsQuantity INT,
    @EmployeeID INT,
    @Semester INT
AS
BEGIN
    IF NOT EXISTS (SELECT 1 FROM FieldOfStudy WHERE FieldOfStudyID = @FieldOfStudyID)
    BEGIN
        RAISERROR('Pole studiów o podanym ID nie istnieje.', 16, 1);
    END

    IF NOT EXISTS (SELECT 1 FROM Employees WHERE EmployeeID = @EmployeeID)
    BEGIN
        RAISERROR('Pracownik o podanym ID nie istnieje.', 16, 1);
    END

    INSERT INTO Subjects (FieldOfStudyID, SubjectName, Description, MeetingsQuantity, EmployeeID, Semester)
    VALUES (@FieldOfStudyID, @SubjectName, @Description, @MeetingsQuantity, @EmployeeID, @Semester);
END;
```

## Dodanie studentowi oceny za przedmiot
```sql
CREATE PROCEDURE AddSubjectGrade
    @SubjectID INT,
    @StudentID INT,
    @Grade INT
AS
BEGIN
    IF NOT EXISTS (SELECT 1 FROM Students WHERE StudentID = @StudentID)
    BEGIN
        RAISERROR('Student o podanym ID nie istnieje.', 16, 1);
    END

    IF NOT EXISTS (SELECT 1 FROM Subjects WHERE SubjectID = @SubjectID)
    BEGIN
        RAISERROR('Przedmiot o podanym ID nie istnieje.', 16, 1);
    END

    IF NOT EXISTS (
        SELECT 1
        FROM FieldOfStudyStudentList fs
        JOIN Subjects s ON fs.FieldOfStudyID = s.FieldOfStudyID
        WHERE fs.StudentID = @StudentID AND s.SubjectID = @SubjectID
    )
    BEGIN
        RAISERROR('Student nie jest zapisany na podany przedmiot w ramach swojego kierunku studiów.', 16, 1);
    END

    INSERT INTO SubjectGrades (SubjectID, StudentID, Grade)
    VALUES (@SubjectID, @StudentID, @Grade);
END;
```

## Dodanie zjazdu
```sql
CREATE PROCEDURE AddStationaryWeek
    @Semester INT,
    @StartDate DATE,
    @EndDate DATE,
    @FieldOfStudyID INT,
    @Price MONEY,
    @Quantity INT
AS
BEGIN
    IF NOT EXISTS (SELECT 1 FROM FieldOfStudy WHERE FieldOfStudyID = @FieldOfStudyID)
    BEGIN
        RAISERROR('Kierunek studiów o podanym ID nie istnieje.', 16, 1);
    END

    INSERT INTO StationaryWeek (Semester, StartDate, EndDate, FieldOfStudyID, Price, Quantity)
    VALUES (@Semester, @StartDate, @EndDate, @FieldOfStudyID, @Price, @Quantity);
END;
```
## Dodanie typu spotkania
```sql
CREATE PROCEDURE AddMeetingType
    @Description VARCHAR(50)
AS
BEGIN
    INSERT INTO MeetingType (Description)
    VALUES (@Description);
END;
```

## Dodanie spotkania
```sql
CREATE PROCEDURE AddMeeting
    @MeetingTypeID INT,
    @SubjectID INT,
    @Meeting_date DATETIME,
    @Link VARCHAR(MAX) = NULL,
    @RoomID INT = NULL,
    @LanguageID INT,
    @TranslatorID INT = NULL,
    @Price MONEY,
    @StationaryID INT = NULL
AS
BEGIN
    IF NOT EXISTS (SELECT 1 FROM Languages WHERE LanguageID = @LanguageID)
    BEGIN
        RAISERROR('Język o podanym ID nie istnieje.', 16, 1);
    END

    IF @TranslatorID IS NOT NULL AND NOT EXISTS (SELECT 1 FROM Translator WHERE TranslatorID = @TranslatorID AND EXISTS (SELECT 1 FROM Languages WHERE LanguageID = @LanguageID AND LanguageID = @LanguageID))
    BEGIN
        RAISERROR('Tłumacz o podanym ID nie istnieje lub nie jest przypisany do tego języka.', 16, 1);
    END

    IF NOT EXISTS (SELECT 1 FROM MeetingType WHERE MeetingTypeID = @MeetingTypeID)
    BEGIN
        RAISERROR('Typ spotkania o podanym ID nie istnieje.', 16, 1);
    END

    IF NOT EXISTS (SELECT 1 FROM Subjects WHERE SubjectID = @SubjectID)
    BEGIN
        RAISERROR('Przedmiot o podanym ID nie istnieje.', 16, 1);
    END

    IF @RoomID IS NOT NULL AND NOT EXISTS (SELECT 1 FROM LectureRoomDetails WHERE RoomID = @RoomID)
    BEGIN
        RAISERROR('Pokój o podanym ID nie istnieje.', 16, 1);
    END

    IF @Meeting_date <= GETDATE()
    BEGIN
        RAISERROR('Data spotkania musi być w przyszłości.', 16, 1);
    END

    IF @Link IS NULL AND @RoomID IS NULL AND @StationaryID IS NULL
    BEGIN
        RAISERROR('Musisz podać link, pokój lub stationary ID.', 16, 1);
    END

    IF @StationaryID IS NOT NULL AND NOT EXISTS (SELECT 1 FROM StationaryWeek WHERE StationaryID = @StationaryID)
    BEGIN
        RAISERROR('ID Stationary nie istnieje.', 16, 1);
    END

    INSERT INTO Meeting (MeetingTypeID, SubjectID, Meeting_date, Link, RoomID, LanguageID, TranslatorID, Price, StationaryID)
    VALUES (@MeetingTypeID, @SubjectID, @Meeting_date, @Link, @RoomID, @LanguageID, @TranslatorID, @Price, @StationaryID);
END;
```

## Dodanie nieobecności na spotkaniu
```sql
CREATE PROCEDURE AddStudentAbsence
    @MeetingID INT,
    @StudentID INT
AS
BEGIN
    IF NOT EXISTS (SELECT 1 FROM Meeting WHERE MeetingID = @MeetingID)
    BEGIN
        RAISERROR('Spotkanie o podanym ID nie istnieje.', 16, 1);
    END

    IF NOT EXISTS (SELECT 1 FROM Students WHERE StudentID = @StudentID)
    BEGIN
        RAISERROR('Student o podanym ID nie istnieje.', 16, 1);
    END

    INSERT INTO StudentAbsence (MeetingID, StudentID, ClassRetakeID)
    VALUES (@MeetingID, @StudentID, NULL);
END;
```
## Ustawienie odrobienia zajęć
```sql
CREATE PROCEDURE SetClassRetakeID
    @MeetingID INT,
    @StudentID INT,
    @ClassRetakeID INT
AS
BEGIN
    IF NOT EXISTS (SELECT 1 FROM Meeting WHERE MeetingID = @MeetingID)
    BEGIN
        RAISERROR('Spotkanie o podanym ID nie istnieje.', 16, 1);
    END

    IF NOT EXISTS (SELECT 1 FROM Students WHERE StudentID = @StudentID)
    BEGIN
        RAISERROR('Student o podanym ID nie istnieje.', 16, 1);
    END

    IF NOT EXISTS (SELECT 1 FROM Meeting WHERE MeetingID = @ClassRetakeID)
    BEGIN
        RAISERROR('Spotkanie o podanym ClassRetakeID nie istnieje.', 16, 1);
    END

    UPDATE StudentAbsence
    SET ClassRetakeID = @ClassRetakeID
    WHERE MeetingID = @MeetingID AND StudentID = @StudentID;
END;
```
## Dodanie praktyk
```sql
CREATE PROCEDURE AddIntership
    @FieldOfStudyID INT,
    @IntershipName VARCHAR(50),
    @StartDate DATE,
    @EndDate DATE
AS
BEGIN
    IF NOT EXISTS (SELECT 1 FROM FieldOfStudy WHERE FieldOfStudyID = @FieldOfStudyID)
    BEGIN
        RAISERROR('Kierunek studiów o podanym ID nie istnieje.', 16, 1);
    END

    INSERT INTO Interships (FieldOfStudyID, IntershipName, StartDate, EndDate)
    VALUES (@FieldOfStudyID, @IntershipName, @StartDate, @EndDate);
END;
```

## Dodanie nieobecności na praktykach
```sql
CREATE PROCEDURE AddIntershipAbsence
    @IntershipID INT,
    @StudentID INT,
    @Absence DATETIME
AS
BEGIN
    IF NOT EXISTS (SELECT 1 FROM Interships WHERE IntershipID = @IntershipID)
    BEGIN
        RAISERROR('Staże o podanym ID nie istnieje.', 16, 1);
    END

    IF NOT EXISTS (SELECT 1 FROM Students WHERE StudentID = @StudentID)
    BEGIN
        RAISERROR('Student o podanym ID nie istnieje.', 16, 1);
    END

    INSERT INTO IntershipsAbsence (IntershipID, StudentID, Absence)
    VALUES (@IntershipID, @StudentID, @Absence);
END;
```
# Orders
## dodawanie zamówienia
```sql
create procedure AddOrder
@OrderID int,
@StudentID int,
@Paid money
as
begin
    set nocount on;

    if not exists (select 1 from Students where StudentID = @StudentID)
        begin
            raiserror('Student o podanym ID nie istnieje.', 16, 1);
        end

    insert into Orders (OrderID, StudentID, Paid, OrderDate)
    values (@OrderID, @StudentID, @Paid, getdate());
    print 'Zamówienie dodane pomyślnie.';
end;
```

## dodawanie szczegółów zamówienia
```sql
CREATE PROCEDURE AddOrderDetails
@OrderDetailID int,
@OrderID int,
@PaidDate datetime = null,
@WebinarID int = null,
@CourseID int = null,
@StudiesID int = null,
@MeetingID int = null
as
begin
    set nocount on;
    if @PaidDate is null
        begin
            set @PaidDate = getdate();
        end

    if not exists (select 1 from Orders where OrderID = @OrderID)
        begin
            raiserror('Zamówienie o podanym ID nie istnieje.', 16, 1);
        end

    IF @WebinarID IS NOT NULL AND EXISTS (
        SELECT StudentID
        FROM Orders
        WHERE @OrderId = Orders.OrderID
          AND StudentID IN (
            SELECT DISTINCT StudentID
            FROM GetAttendeesByWebinarID(@WebinarID)
        )
    )
        BEGIN
            RAISERROR('Student o podanym ID jest już zapisany na ten webinar.', 16, 1);
        END

    ELSE IF @CourseID IS NOT NULL AND EXISTS (
        SELECT StudentID
        FROM Orders
        WHERE @OrderId = Orders.OrderID
          AND StudentID IN (
            SELECT DISTINCT StudentID
            FROM GetAttendeesByCourseID(@CourseID)
        )
    )
        BEGIN
            RAISERROR('Student o podanym ID jest już zapisany na ten kurs.', 16, 1);
        END

    ELSE IF @StudiesID IS NOT NULL AND EXISTS (
        SELECT StudentID
        FROM Orders
        WHERE @OrderId = Orders.OrderID
          AND StudentID IN (
            SELECT DISTINCT StudentID
            FROM FieldOfStudy
            WHERE FieldOfStudyID = @StudiesID
        )
    )
        BEGIN
            RAISERROR('Student o podanym ID jest już zapisany na te studia.', 16, 1);
        END
    ELSE IF @MeetingID IS NOT NULL AND EXISTS (
        SELECT StudentID
        FROM Orders
        WHERE @OrderId = Orders.OrderID
          AND StudentID IN (
            SELECT DISTINCT StudentID
            FROM GetAttendeesByMeetingID(@MeetingID)
        )
    )
        BEGIN
            RAISERROR('Student o podanym ID jest już zapisany na to spotkanie studyjne.', 16, 1);
        END


    if @PaidDate is not null
        begin
            insert into OrderDetails (OrderDetailsID, OrderID, PaidDate, AccessGiven)
            values (@OrderDetailID, @OrderID, @PaidDate, 1);
        end
    else
        begin
            insert into OrderDetails (OrderDetailsID, OrderID, PaidDate, AccessGiven)
            values (@OrderDetailID, @OrderID, @PaidDate, 0);
        end

    if @WebinarID is not null and not exists (select 1 from Webinar where WebinarID = @WebinarID)
        begin
            raiserror('Webinar o podanym ID nie istnieje.', 16, 1);
        end

    else if @WebinarID is not null and exists (select 1 from Webinar where WebinarID = @WebinarID)
        begin
            insert into OrderWebinar (OrderDetailsID, WebinarID)
            values (@OrderDetailID, @WebinarID);
            print 'Szczegół zamówienia dodany pomyślnie.';
            return;
        end

    if @CourseID is not null and not exists (select 1 from Courses where CourseID = @CourseID)
        begin
            raiserror('Kurs o podanym ID nie istnieje.', 16, 1);
        end
    else if @CourseID is not null and (Select Limit from Courses) > (Select count(*) from GetAttendeesByCourseID(@CourseID))
        begin
            raiserror('Kurs o podanym ID nie ma wolnych miejsc.', 16, 1);
        end

    else if @CourseID is not null and exists (select 1 from Courses where CourseID = @CourseID)
        begin

            insert into OrderCourse (OrderDetailsID, CourseID)
            values (@OrderDetailID, @CourseID);
            print 'Szczegół zamówienia dodany pomyślnie.';
            return;
        end

    if @StudiesID is not null and not exists (select 1 from FieldOfStudy where FieldOfStudyID = @StudiesID)
        begin
            raiserror('Studia o podanym ID nie istnieją.', 16, 1);
        end
    else if @StudiesID is not null and (Select Limit from Courses) > (Select count(*) from FieldOfStudyStudentList where FieldOfStudyID = @StudiesID)
        begin
            raiserror('Studia o podanym ID nie mają wolnych miejsc.', 16, 1);
        end
    else if @StudiesID is not null and exists (select 1 from Studies where StudiesID = @StudiesID)
        begin
            insert into OrderStudies (OrderDetailsID, FieldOfStudyID)
            values (@OrderDetailID, @StudiesID);
            print 'Szczegół zamówienia dodany pomyślnie.';
            return;
        end

    if @MeetingID is not null and not exists (select 1 from Meeting where MeetingID = @MeetingID)
        begin
            raiserror('Spotkanie o podanym ID nie istnieje.', 16, 1);
        end
    else if @MeetingID is not null and exists (select 1 from Meeting where MeetingID = @MeetingID)
        begin
            insert into OrderMeeting (OrderDetailsID, MeetingID)
            values (@OrderDetailID, @MeetingID);
            print 'Szczegół zamówienia dodany pomyślnie.';
            return;
        end
END;
```