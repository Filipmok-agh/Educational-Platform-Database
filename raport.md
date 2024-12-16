# Funkcjonalności systemu – lista użytkowników i ich uprawnienia

<p style="text-align: center; font-size: medium;"><br>Mokrzycki Filip,<br> Mateusz Wójcik,<br> Piotr Kacprzak </p>

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

```sql
-- tables

-- Table: CourseModulesPassed
CREATE TABLE CourseModulesPassed (
    CourseID int  NOT NULL,
    StudentID int  NOT NULL,
    ModuleID int  NOT NULL,
    CONSTRAINT CourseModulesPassed_pk PRIMARY KEY  (CourseID)
);

-- Table: CourseSchedule
CREATE TABLE CourseSchedule (
    ModuleID int  NOT NULL,
    RoomID int  NOT NULL,
    LiveLink varchar(max)  NOT NULL,
    VideoLink varchar(max)  NOT NULL,
    Date datetime  NOT NULL,
    CONSTRAINT CourseSchedule_pk PRIMARY KEY  (ModuleID)
);

-- Table: Courses
CREATE TABLE Courses (
    CourseID int  NOT NULL IDENTITY(1,1),
    EmployeeID int  NOT NULL,
    Price money  NOT NULL,
    CourseType varchar(50)  NOT NULL,
    Limit int  NULL,
    TranslatorID int  NULL,
    LanguageID int  NOT NULL,
    ModulesQuantity int  NOT NULL,
    CONSTRAINT Courses_pk PRIMARY KEY  (CourseID)
);

-- Table: EmloyeeType
CREATE TABLE EmployeeType (
    EmployeeID int  NOT NULL,
    HeldPosition varchar(50)  NOT NULL,
    CONSTRAINT EmloyeeType_pk PRIMARY KEY  (EmployeeID)
);

-- Table: Employees
CREATE TABLE Employees (
    EmployeeID int  NOT NULL  IDENTITY(1,1),
    FirstName varchar(50)  NOT NULL,
    LastName varchar(50)  NOT NULL,
    Country varchar(50)  NOT NULL,
    Region varchar(50)  NOT NULL,
    City varchar(50)  NOT NULL,
    Address varchar(50)  NOT NULL,
    DateOfBirth date  NOT NULL,
    Mail varchar(50)  NOT NULL,
    Phone varchar(15)  NOT NULL,
    CONSTRAINT Employees_pk PRIMARY KEY  (EmployeeID)
);

-- Table: FacultyStudentList
CREATE TABLE FacultyStudentList (
    FieldOfStudyID int  NOT NULL,
    StudentID int  NOT NULL,
    Semester int  NOT NULL,
    CONSTRAINT FacultyStudentList_pk PRIMARY KEY  (StudentID)
);

-- Table: FieldOfStudy
CREATE TABLE FieldOfStudy (
    FieldOfStudyID int  NOT NULL IDENTITY(1,1),
    Name text  NOT NULL,
    Description text  NOT NULL,
    Limit int  NOT NULL,
    EntryFee int  NOT NULL,
    CONSTRAINT FieldOfStudy_pk PRIMARY KEY  (FieldOfStudyID)
);

-- Table: Interships
CREATE TABLE Interships (
    IntershipID int  NOT NULL IDENTITY(1,1),
    FieldOfStudyID int  NOT NULL,
    StartDate date  NOT NULL,
    EndDate date  NOT NULL,
    CONSTRAINT Interships_pk PRIMARY KEY  (IntershipID)
);

-- Table: IntershipsAbsence
CREATE TABLE IntershipsAbsence (
    IntershipID int  NOT NULL,
    Absence datetime  NOT NULL,
    StudentID int  NOT NULL,
    CONSTRAINT IntershipsAbsence_pk PRIMARY KEY  (IntershipID)
);

-- Table: Languages
CREATE TABLE Languages (
    LanguageID int  NOT NULL IDENTITY(1,1),
    Language varchar(50)  NOT NULL,
    TranslatorID int  NOT NULL,
    CONSTRAINT LanguageID PRIMARY KEY  (LanguageID)
);

-- Table: LectureRoomDetails
CREATE TABLE LectureRoomDetails (
    RoomID int  NOT NULL IDENTITY(1,1),
    BuildingNr int  NOT NULL,
    Floor int  NOT NULL,
    ClassNumber int  NOT NULL,
    CONSTRAINT LectureRoomDetails_pk PRIMARY KEY  (RoomID)
);

-- Table: Meeting
CREATE TABLE Meeting (
    MeetingID int  NOT NULL IDENTITY(1,1),
    MeetingTypeID int  NOT NULL,
    SubjectID int  NOT NULL,
    Date datetime  NOT NULL,
    Link varchar(max)  NOT NULL,
    RoomID int  NOT NULL,
    LanguageID int  NOT NULL,
    TranslatorID int  NULL,
    EmployeeID int  NOT NULL ,
    CONSTRAINT Meeting_pk PRIMARY KEY  (MeetingID)
);

-- Table: MeetingType
CREATE TABLE MeetingType (
    MeetingTypeID int  NOT NULL,
    Description varchar(50)  NOT NULL,
    CONSTRAINT MeetingType_pk PRIMARY KEY  (MeetingTypeID)
);

-- Table: ModuleAbsence
CREATE TABLE ModuleAbsence (
    ModuleID int  NOT NULL,
    StudentID int  NOT NULL,
    Absence datetime  NOT NULL,
    CONSTRAINT ModuleAbsence_pk PRIMARY KEY  (ModuleID)
);

-- Table: ModuleType
CREATE TABLE ModuleType (
    ModuleTypeID int  NOT NULL,
    Description varchar(50)  NOT NULL,
    CONSTRAINT ModuleType_pk PRIMARY KEY  (ModuleTypeID)
);

-- Table: Modules
CREATE TABLE Modules (
    ModuleID int  NOT NULL IDENTITY(1,1),
    CourseID int  NOT NULL,
    ModuleTypeID int  NOT NULL,
    MettingsQuantity int  NOT NULL,
    CONSTRAINT Modules_pk PRIMARY KEY  (ModuleID)
);

-- Table: OrderCourse
CREATE TABLE OrderCourse (
    OrderDetailsID int  NOT NULL,
    CourseID int  NOT NULL,
    CONSTRAINT OrderCourse_pk PRIMARY KEY  (OrderDetailsID)
);

-- Table: OrderDetails
CREATE TABLE OrderDetails (
    OrderDetailsID int  NOT NULL IDENTITY(1,1),
    PaidDate datetime  NOT NULL,
    OrderID int  NOT NULL,
    CONSTRAINT OrderDetails_pk PRIMARY KEY  (OrderDetailsID)
);

-- Table: OrderMeeting
CREATE TABLE OrderMeeting (
    OrderDetailsID int  NOT NULL,
    MeetingID int  NOT NULL,
    CONSTRAINT OrderMeeting_pk PRIMARY KEY  (OrderDetailsID)
);

-- Table: OrderStudies
CREATE TABLE OrderStudies (
    DetailsID int  NOT NULL,
    FieldOfStudyID int  NOT NULL,
    CONSTRAINT OrderStudies_pk PRIMARY KEY  (DetailsID)
);

-- Table: OrderWebinar
CREATE TABLE OrderWebinar (
    OrderDetailsID int  NOT NULL,
    WebinarID int  NOT NULL,
    CONSTRAINT OrderWebinar_pk PRIMARY KEY  (OrderDetailsID)
);

-- Table: Orders
CREATE TABLE Orders (
    OrderID int  NOT NULL IDENTITY(1,1),
    Price money  NOT NULL,
    Paid money  NULL,
    OrderDate datetime  NOT NULL,
    StudentID int  NOT NULL,
    CONSTRAINT OrderID PRIMARY KEY  (OrderID)
);

-- Table: StudentAbsence
CREATE TABLE StudentAbsence (
    Absence datetime  NOT NULL,
    FieldOfStudyID int  NOT NULL,
    SubjectID int  NOT NULL,
    StudentID int  NOT NULL,
    CONSTRAINT StudentAbsence_pk PRIMARY KEY  (StudentID)
);

-- Table: Students
CREATE TABLE Students (
    StudentID int  NOT NULL IDENTITY(1,1),
    FirstName varchar(50)  NOT NULL,
    LastName varchar(50)  NOT NULL,
    Country varchar(50)  NOT NULL,
    Region varchar(50)  NOT NULL,
    City varchar(50)  NOT NULL,
    Address varchar(50)  NOT NULL,
    DateOfBirth date  NOT NULL,
    Mail varchar(50)  NOT NULL,
    Phone varchar(15)  NOT NULL,
    CONSTRAINT Students_pk PRIMARY KEY  (StudentID)
);

-- Table: SubjectGrades
CREATE TABLE SubjectGrades (
    SubjectID int  NOT NULL,
    Grade datetime  NOT NULL,
    StudentID int  NOT NULL,
    CONSTRAINT SubjectGrades_pk PRIMARY KEY  (SubjectID)
);

-- Table: Subjects
CREATE TABLE Subjects (
    SubjectID int  NOT NULL IDENTITY(1,1),
    FieldOfStudyID int  NOT NULL,
    SubjectName text  NOT NULL,
    Description text  NOT NULL,
    MeetingTypeID int  NOT NULL,
    MeetingsQuantity int  NOT NULL,
    EmployeeID int  NOT NULL,
    CONSTRAINT Subjects_pk PRIMARY KEY  (SubjectID)
);

-- Table: Translator
CREATE TABLE Translator (
    TranslatorID int  NOT NULL IDENTITY(1,1),
    FirstName varchar(50)  NOT NULL,
    LastName varchar(50)  NOT NULL,
    DateOfBirth date  NOT NULL,
    Country varchar(50)  NOT NULL,
    Region varchar(50)  NOT NULL,
    City varchar(50)  NOT NULL,
    Address varchar(50)  NOT NULL,
    Mail varchar(50)  NOT NULL,
    Phone varchar(15)  NOT NULL,
    CONSTRAINT Translator_pk PRIMARY KEY  (TranslatorID)
);

-- Table: Webinar
CREATE TABLE Webinar (
    WebinarID int  NOT NULL IDENTITY(1,1),
    Price money  NOT NULL,
    Date datetime  NOT NULL,
    LanguageID int  NOT NULL,
    TranslatorID int  NULL,
    EmployeeID int  NOT NULL,
    OnlineLink varchar(max)  NOT NULL,
    VideoLink varchar(max)  NOT NULL,
    CONSTRAINT Webinar_pk PRIMARY KEY  (WebinarID)
);

-- Table: WebinarExpirationDate
CREATE TABLE WebinarExpirationDate (
    WebinarID int  NOT NULL,
    StudentID int  NOT NULL,
    expr_date date  NULL,
    CONSTRAINT WebinarExpirationDate_pk PRIMARY KEY  (WebinarID)
);

-- foreign keys
-- Reference: CourseDetails_Courses (table: CourseModulesPassed)
ALTER TABLE CourseModulesPassed ADD CONSTRAINT CourseDetails_Courses
    FOREIGN KEY (CourseID)
    REFERENCES Courses (CourseID);

-- Reference: CourseDetails_Modules (table: CourseModulesPassed)
ALTER TABLE CourseModulesPassed ADD CONSTRAINT CourseDetails_Modules
    FOREIGN KEY (ModuleID)
    REFERENCES Modules (ModuleID);

-- Reference: CourseDetails_Students (table: CourseModulesPassed)
ALTER TABLE CourseModulesPassed ADD CONSTRAINT CourseDetails_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: CourseSchedule_AdresDetails (table: CourseSchedule)
ALTER TABLE CourseSchedule ADD CONSTRAINT CourseSchedule_AdresDetails
    FOREIGN KEY (RoomID)
    REFERENCES LectureRoomDetails (RoomID);

-- Reference: CourseSchedule_Modules (table: CourseSchedule)
ALTER TABLE CourseSchedule ADD CONSTRAINT CourseSchedule_Modules
    FOREIGN KEY (ModuleID)
    REFERENCES Modules (ModuleID);

-- Reference: Courses_Employees (table: Courses)
ALTER TABLE Courses ADD CONSTRAINT Courses_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: Courses_Languages (table: Courses)
ALTER TABLE Courses ADD CONSTRAINT Courses_Languages
    FOREIGN KEY (LanguageID)
    REFERENCES Languages (LanguageID);

-- Reference: Courses_Translator (table: Courses)
ALTER TABLE Courses ADD CONSTRAINT Courses_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);

-- Reference: EmloyeeType_Employees (table: EmloyeeType)
ALTER TABLE EmployeeType ADD CONSTRAINT EmployeeType_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: FieldDetails_Field (table: FacultyStudentList)
ALTER TABLE FacultyStudentList ADD CONSTRAINT FieldDetails_Field
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: FieldDetails_Students (table: FacultyStudentList)
ALTER TABLE FacultyStudentList ADD CONSTRAINT FieldDetails_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: IntershipsDetails_Interships (table: IntershipsAbsence)
ALTER TABLE IntershipsAbsence ADD CONSTRAINT IntershipsDetails_Interships
    FOREIGN KEY (IntershipID)
    REFERENCES Interships (IntershipID);

-- Reference: IntershipsDetails_Students (table: IntershipsAbsence)
ALTER TABLE IntershipsAbsence ADD CONSTRAINT IntershipsDetails_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: Interships_Field (table: Interships)
ALTER TABLE Interships ADD CONSTRAINT Interships_Field
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: Languages_Translator (table: Languages)
ALTER TABLE Languages ADD CONSTRAINT Languages_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);

-- Reference: Meeting_AdresDetails (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_AdresDetails
    FOREIGN KEY (RoomID)
    REFERENCES LectureRoomDetails (RoomID);

-- Reference: Meeting_Employees (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: Meeting_Languages (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_Languages
    FOREIGN KEY (LanguageID)
    REFERENCES Languages (LanguageID);

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

-- Reference: ModuleDetails_Modules (table: ModuleAbsence)
ALTER TABLE ModuleAbsence ADD CONSTRAINT ModuleDetails_Modules
    FOREIGN KEY (ModuleID)
    REFERENCES Modules (ModuleID);

-- Reference: ModuleDetails_Students (table: ModuleAbsence)
ALTER TABLE ModuleAbsence ADD CONSTRAINT ModuleDetails_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: Modules_Courses (table: Modules)
ALTER TABLE Modules ADD CONSTRAINT Modules_Courses
    FOREIGN KEY (CourseID)
    REFERENCES Courses (CourseID);

-- Reference: Modules_ModuleType (table: Modules)
ALTER TABLE Modules ADD CONSTRAINT Modules_ModuleType
    FOREIGN KEY (ModuleTypeID)
    REFERENCES ModuleType (ModuleTypeID);

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

-- Reference: OrderStudies_Field (table: OrderStudies)
ALTER TABLE OrderStudies ADD CONSTRAINT OrderStudies_Field
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: OrderStudies_OrderDetails (table: OrderStudies)
ALTER TABLE OrderStudies ADD CONSTRAINT OrderStudies_OrderDetails
    FOREIGN KEY (DetailsID)
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

-- Reference: StudentAbsence_Faculties (table: StudentAbsence)
ALTER TABLE StudentAbsence ADD CONSTRAINT StudentAbsence_Faculties
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: StudentAbsence_Students (table: StudentAbsence)
ALTER TABLE StudentAbsence ADD CONSTRAINT StudentAbsence_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: StudentAbsence_Subjects (table: StudentAbsence)
ALTER TABLE StudentAbsence ADD CONSTRAINT StudentAbsence_Subjects
    FOREIGN KEY (SubjectID)
    REFERENCES Subjects (SubjectID);

-- Reference: SubjectDetails_Students (table: SubjectGrades)
ALTER TABLE SubjectGrades ADD CONSTRAINT SubjectDetails_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: SubjectDetails_Subjects (table: SubjectGrades)
ALTER TABLE SubjectGrades ADD CONSTRAINT SubjectDetails_Subjects
    FOREIGN KEY (SubjectID)
    REFERENCES Subjects (SubjectID);

-- Reference: Subjects_Employees (table: Subjects)
ALTER TABLE Subjects ADD CONSTRAINT Subjects_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: Subjects_Field (table: Subjects)
ALTER TABLE Subjects ADD CONSTRAINT Subjects_Field
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: WebinarDetails_Students (table: WebinarExpirationDate)
ALTER TABLE WebinarExpirationDate ADD CONSTRAINT WebinarDetails_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: WebinarDetails_Webinar (table: WebinarExpirationDate)
ALTER TABLE WebinarExpirationDate ADD CONSTRAINT WebinarDetails_Webinar
    FOREIGN KEY (WebinarID)
    REFERENCES Webinar (WebinarID);

-- Reference: Webinar_Employees (table: Webinar)
ALTER TABLE Webinar ADD CONSTRAINT Webinar_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: Webinar_Languages (table: Webinar)
ALTER TABLE Webinar ADD CONSTRAINT Webinar_Languages
    FOREIGN KEY (LanguageID)
    REFERENCES Languages (LanguageID);

-- Reference: Webinar_Translator (table: Webinar)
ALTER TABLE Webinar ADD CONSTRAINT Webinar_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);
```