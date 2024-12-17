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
---Tables

--- Table: FieldOfStudy
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

-- Table: OrderCourse
CREATE TABLE OrderCourse (
    OrderDetailsID int  NOT NULL,
    CourseID int  NOT NULL,
    CONSTRAINT OrderCourse_pk PRIMARY KEY  (OrderDetailsID)
);

-- Table: OrderDetails
CREATE TABLE OrderDetails (
    OrderDetailsID int  NOT NULL,
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
    OrderDetailsID int  NOT NULL,
    FieldOfStudyID int  NOT NULL,
    CONSTRAINT OrderStudies_pk PRIMARY KEY  (OrderDetailsID)
);

-- Table: OrderWebinar
CREATE TABLE OrderWebinar (
    OrderDetailsID int  NOT NULL,
    WebinarID int  NOT NULL,
    CONSTRAINT OrderWebinar_pk PRIMARY KEY  (OrderDetailsID)
);

-- Table: Orders
CREATE TABLE Orders (
    OrderID int  NOT NULL,
    Paid money  NULL,
    OrderDate datetime  NOT NULL,
    StudentID int  NOT NULL,
    CONSTRAINT OrderID PRIMARY KEY  (OrderID)
);

-- Table: Webinar
CREATE TABLE Webinar (
    WebinarID int  NOT NULL,
    Price money  NOT NULL,
    Webinar_date datetime  NOT NULL,
    LanguageID int  NOT NULL,
    TranslatorID int  NULL,
    EmployeeID int  NOT NULL,
    OnlineLink varchar(max)  NULL,
    VideoLink varchar(max)  NULL,
    CONSTRAINT Webinar_pk PRIMARY KEY  (WebinarID)
);

-- Table: WebinarExpirationDate
CREATE TABLE WebinarExpirationDate (
    WebinarID int  NOT NULL,
    StudentID int  NOT NULL,
    expr_date date  NULL,
    CONSTRAINT WebinarExpirationDate_pk PRIMARY KEY  (WebinarID,StudentID)
);


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

-- Reference: Courses_AvalibleLanguages (table: Courses)
ALTER TABLE Courses ADD CONSTRAINT Courses_AvalibleLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvalibleLanguages (LanguageID);

-- Reference: Courses_Employees (table: Courses)
ALTER TABLE Courses ADD CONSTRAINT Courses_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: Courses_Translator (table: Courses)
ALTER TABLE Courses ADD CONSTRAINT Courses_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);

-- Reference: EmloyeeType_Employees (table: EmployeeType)
ALTER TABLE EmployeeType ADD CONSTRAINT EmloyeeType_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: FieldDetails_Field (table: FieldOfStudyStudentList)
ALTER TABLE FieldOfStudyStudentList ADD CONSTRAINT FieldDetails_Field
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: FieldDetails_Students (table: FieldOfStudyStudentList)
ALTER TABLE FieldOfStudyStudentList ADD CONSTRAINT FieldDetails_Students
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

-- Reference: Languages_AvalibleLanguages (table: Languages)
ALTER TABLE Languages ADD CONSTRAINT Languages_AvalibleLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvalibleLanguages (LanguageID);

-- Reference: Languages_Translator (table: Languages)
ALTER TABLE Languages ADD CONSTRAINT Languages_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);

-- Reference: Meeting_AdresDetails (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_AdresDetails
    FOREIGN KEY (RoomID)
    REFERENCES LectureRoomDetails (RoomID);

-- Reference: Meeting_AvalibleLanguages (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_AvalibleLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvalibleLanguages (LanguageID);

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

-- Reference: StudentAbsence_Meeting (table: StudentAbsence)
ALTER TABLE StudentAbsence ADD CONSTRAINT StudentAbsence_Meeting
    FOREIGN KEY (MeetingID)
    REFERENCES Meeting (MeetingID);

-- Reference: StudentAbsence_Students (table: StudentAbsence)
ALTER TABLE StudentAbsence ADD CONSTRAINT StudentAbsence_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

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

-- Reference: Webinar_AvalibleLanguages (table: Webinar)
ALTER TABLE Webinar ADD CONSTRAINT Webinar_AvalibleLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvalibleLanguages (LanguageID);

-- Reference: Webinar_Employees (table: Webinar)
ALTER TABLE Webinar ADD CONSTRAINT Webinar_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: Webinar_Translator (table: Webinar)
ALTER TABLE Webinar ADD CONSTRAINT Webinar_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);
```