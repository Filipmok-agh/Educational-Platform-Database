# Funkcjonalności systemu – lista użytkowników i ich uprawnienia

<p style="text-align: left; font-size: medium;"><br>Filip Mokrzycki,<br> Mateusz Wójcik,<br> Piotr Kacprzak </p>

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

## Tabela: `Courses`
Przechowuje informacje o kursach.

- `CourseID` *(int, NOT NULL)*: Unikalny identyfikator kursu.
- `CourseName` *(varchar(50), NOT NULL)*: Nazwa kursu.
- `EmployeeID` *(int, NOT NULL)*: Identyfikator pracownika odpowiedzialnego za kurs.
- `Price` *(money, NOT NULL)*: Cena kursu.
- `CourseType` *(varchar(50), NOT NULL)*: Typ kursu (np. online, stacjonarny).
- `Limit` *(int, NULL)*: Maksymalna liczba uczestników kursu.
- `LanguageID` *(int, NOT NULL)*: Identyfikator języka kursu.
- `TranslatorID` *(int, NULL)*: Identyfikator tłumacza (jeśli dotyczy).
- **Klucze i ograniczenia:**
  - `Courses_pk`: Klucz główny na kolumnie `CourseID`.
  - `chk_Courses`: Ograniczenia sprawdzające, że:
    - `CourseType` i `CourseName` mają co najmniej 1 znak.
    - `Price` jest nieujemne.
    - `Limit` jest nieujemne.
```sql

CREATE TABLE Courses (
    CourseID int IDENTITY(1,1) NOT NULL,
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
## Tabela: `CourseModulesProgress`
Przechowuje informacje o postępach uczestników w modułach kursów.

- `CourseID` *(int, NOT NULL)*: Identyfikator kursu.
- `ModuleID` *(int, NOT NULL)*: Identyfikator modułu w kursie.
- `StudentID` *(int, NOT NULL)*: Identyfikator uczestnika.
- `Passed` *(bit, NOT NULL)*: Flaga określająca, czy moduł został zaliczony.
- **Klucze i ograniczenia:**
  - `CourseModulesProgress_pk`: Klucz główny złożony z `CourseID`, `ModuleID` i `StudentID`.
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
## Tabela: `CourseSchedule`
Przechowuje harmonogram modułów kursu.

- `ModuleID` *(int, NOT NULL)*: Identyfikator modułu.
- `RoomID` *(int, NULL)*: Identyfikator sali, w której odbywa się moduł (jeśli dotyczy).
- `LiveLink` *(varchar(max), NULL)*: Link do transmisji na żywo (jeśli dotyczy).
- `VideoLink` *(varchar(max), NULL)*: Link do nagrania wideo (jeśli dotyczy).
- `Course_date` *(datetime, NOT NULL)*: Data realizacji modułu.
- **Klucze i ograniczenia:**
  - `CourseSchedule_pk`: Klucz główny na kolumnie `ModuleID`.
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

## Tabela: `Modules`
Przechowuje informacje o modułach wchodzących w skład kursów.

- `ModuleID` *(int, NOT NULL)*: Unikalny identyfikator modułu.
- `ModuleName` *(varchar(50), NOT NULL)*: Nazwa modułu.
- `CourseID` *(int, NOT NULL)*: Identyfikator kursu, do którego należy moduł.
- `ModuleType` *(varchar(50), NOT NULL)*: Typ modułu(stacjonarny, online).
- `MeetingsQuantity` *(int, NOT NULL)*: Liczba spotkań w ramach modułu.
- **Klucze i ograniczenia:**
  - `Modules_pk`: Klucz główny na kolumnie `ModuleID`.
  - `chk_Modules`: Ograniczenia sprawdzające, że:
    - `ModuleName` i `ModuleType` mają co najmniej 1 znak.
    - `MeetingsQuantity` jest większe od 0. 

```sql
CREATE TABLE Modules (
    ModuleID int NOT NULL IDENTITY(1,1),
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

## Tabela: `ModuleAbsence`
Przechowuje informacje o nieobecnościach uczestników na spotkaniach modułów.

- `ModuleID` *(int, NOT NULL)*: Identyfikator modułu.
- `StudentID` *(int, NOT NULL)*: Identyfikator uczestnika.
- `Date` *(datetime, NOT NULL)*: Data nieobecności.
- **Klucze i ograniczenia:**
  - `ModuleAbsence_pk`: Klucz główny złożony z `ModuleID`, `StudentID` i `Date`.
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

# Sekcji Orders

## Tabela: `Orders`
Przechowuje informacje o zamówieniach składanych przez studentów.

- `OrderID` *(int, NOT NULL)*: Unikalny identyfikator zamówienia.
- `Paid` *(money, NULL)*: Kwota zapłacona za zamówienie (opcjonalnie).
- `OrderDate` *(datetime, NOT NULL)*: Data złożenia zamówienia.
- `StudentID` *(int, NOT NULL)*: Unikalny identyfikator studenta, który złożył zamówienie
- **Klucze i ograniczenia:**
  - `OrderID_pk`: Klucz główny na kolumnie `OrderID`.
  - `chk_Orders`: Ograniczenie sprawdzające, że:
    - Kwota zapłacona jest większa lub równa 0.

```sql
CREATE TABLE Orders (
    OrderID int NOT NULL IDENTITY(1,1),
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



## Tabela: `OrderDetails`
Przechowuje szczegóły dotyczące zamówienia, takie jak data zapłaty i dostęp do kursów.

- `OrderDetailsID` *(int, NOT NULL)*: Unikalny identyfikator szczegółów zamówienia.
- `PaidDate` *(datetime, NULL)*: Data zapłaty za zamówienie (opcjonalnie).
- `OrderID` *(int, NOT NULL)*: Unikalny identyfikator zamówienia.
- `AccessGiven` *(bit, NOT NULL)*: Flaga wskazująca, czy dostęp do zamówionych kursów został przyznany.
- **Klucze i ograniczenia:**
  - `OrderDetails_pk`: Klucz główny na kolumnie `OrderDetailsID`.

```sql
CREATE TABLE OrderDetails (
    OrderDetailsID int NOT NULL IDENTITY(1,1),
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

## Tabela: `OrderCourse`
Przechowuje informacje o kursach przypisanych do zamówień.

- `OrderDetailsID` *(int, NOT NULL)*: Unikalny identyfikator szczegółów zamówienia.
- `CourseID` *(int, NOT NULL)*: Unikalny identyfikator kursu przypisanego do zamówienia.
- **Klucze i ograniczenia:**
  - `OrderCourse_pk`: Klucz główny na kolumnie `OrderDetailsID`.

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

## Tabela: `OrderMeeting`
Przechowuje informacje o spotkaniach przypisanych do zamówień.

- `OrderDetailsID` *(int, NOT NULL)*: Unikalny identyfikator szczegółów zamówienia.
- `MeetingID` *(int, NOT NULL)*: Unikalny identyfikator spotkania przypisanego do zamówienia.
- **Klucze i ograniczenia:**
  - `OrderMeeting_pk`: Klucz główny na kolumnie `OrderDetailsID`.

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

## Tabela: `OrderStudies`
Przechowuje informacje o kierunkach studiów przypisanych do zamówień.

- `OrderDetailsID` *(int, NOT NULL)*: Unikalny identyfikator szczegółów zamówienia.
- `FieldOfStudyID` *(int, NOT NULL)*: Unikalny identyfikator kierunku studiów przypisanego do zamówienia.
- **Klucze i ograniczenia:**
  - `OrderStudies_pk`: Klucz główny na kolumnie `OrderDetailsID`.

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

## Tabela: `OrderWebinar`
Przechowuje informacje o webinarach przypisanych do zamówień.

- `OrderDetailsID` *(int, NOT NULL)*: Unikalny identyfikator szczegółów zamówienia.
- `WebinarID` *(int, NOT NULL)*: Unikalny identyfikator webinaru przypisanego do zamówienia.
- **Klucze i ograniczenia:**
  - `OrderWebinar_pk`: Klucz główny na kolumnie `OrderDetailsID`.

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

## Tabela: `OrderStationaryWeek`
Przechowuje informacje o tygodniach stacjonarnych przypisanych do zamówień.

- `OrderDetailsID` *(int, NOT NULL)*: Unikalny identyfikator szczegółów zamówienia.
- `StationaryID` *(int, NOT NULL)*: Unikalny identyfikator tygodnia stacjonarnego przypisanego do zamówienia.
- **Klucze i ograniczenia:**
  - `OrderStationaryWeek_pk`: Klucz główny na kolumnie `OrderDetailsID`.

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

# Sekcja People
## Tabela: `Translator`
Przechowuje informacje o tłumaczach.

- `TranslatorID` *(int, NOT NULL)*: Unikalny identyfikator tłumacza.
- `FirstName` *(varchar(50), NOT NULL)*: Imię tłumacza.
- `LastName` *(varchar(50), NOT NULL)*: Nazwisko tłumacza.
- `DateOfBirth` *(date, NOT NULL)*: Data urodzenia tłumacza.
- `Country` *(varchar(50), NOT NULL)*: Kraj tłumacza.
- `City` *(varchar(50), NOT NULL)*: Miasto tłumacza.
- `Address` *(varchar(50), NOT NULL)*: Adres tłumacza.
- `Mail` *(varchar(50), NOT NULL)*: Adres e-mail tłumacza.
- `Phone` *(varchar(15), NOT NULL)*: Numer telefonu tłumacza.
- **Klucze i ograniczenia:**
  - `Translator_pk`: Klucz główny na kolumnie `TranslatorID`.
  - `chk_translator_validations`: Ograniczenia sprawdzające, że:
    - Tłumacz ma co najmniej 18 lat.
    - Wszystkie pola mają co najmniej 1 znak.

```sql
CREATE TABLE Translator (
    TranslatorID int NOT NULL IDENTITY(1,1),
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

## Tabela: `Employees`
Przechowuje informacje o pracownikach.

- `EmployeeID` *(int, NOT NULL, IDENTITY)*: Unikalny identyfikator pracownika.
- `FirstName` *(varchar(50), NOT NULL)*: Imię pracownika.
- `LastName` *(varchar(50), NOT NULL)*: Nazwisko pracownika.
- `DateOfBirth` *(date, NOT NULL)*: Data urodzenia pracownika.
- `Country` *(varchar(50), NOT NULL)*: Kraj pracownika.
- `City` *(varchar(50), NOT NULL)*: Miasto pracownika.
- `Address` *(varchar(50), NOT NULL)*: Adres pracownika.
- `Mail` *(varchar(50), NOT NULL)*: Adres e-mail pracownika.
- `Phone` *(varchar(15), NOT NULL)*: Numer telefonu pracownika.
- **Klucze i ograniczenia:**
  - `Employees_pk`: Klucz główny na kolumnie `EmployeeID`.
  - `chk_Employees`: Ograniczenia sprawdzające, że:
    - Pracownik ma co najmniej 18 lat.
    - Wszystkie pola mają co najmniej 1 znak.

```sql
CREATE TABLE Employees (
    EmployeeID int NOT NULL IDENTITY(1,1),
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

## Tabela: `EmployeeType`
Przechowuje informacje o typach pracowników, tj. ich stanowiskach.

- `EmployeeID` *(int, NOT NULL)*: Unikalny identyfikator pracownika (klucz obcy).
- `HeldPosition` *(varchar(50), NOT NULL)*: Stanowisko, które pracownik zajmuje.
- **Klucze i ograniczenia:**
  - `EmployeeType_pk`: Klucz główny na kolumnie `EmployeeID`.
  - `chk_EmployeeType`: Ograniczenie sprawdzające, że `HeldPosition` zawiera co najmniej 1 znak.

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

## Tabela: `Students`
Przechowuje informacje o studentach.

- `StudentID` *(int, NOT NULL, IDENTITY)*: Unikalny identyfikator studenta.
- `FirstName` *(varchar(50), NOT NULL)*: Imię studenta.
- `LastName` *(varchar(50), NOT NULL)*: Nazwisko studenta.
- `DateOfBirth` *(date, NOT NULL)*: Data urodzenia studenta.
- `Country` *(varchar(50), NOT NULL)*: Kraj studenta.
- `City` *(varchar(50), NOT NULL)*: Miasto studenta.
- `Address` *(varchar(50), NOT NULL)*: Adres studenta.
- `Mail` *(varchar(50), NOT NULL)*: Adres e-mail studenta.
- `Phone` *(varchar(15), NOT NULL)*: Numer telefonu studenta.
- **Klucze i ograniczenia:**
  - `Students_pk`: Klucz główny na kolumnie `StudentID`.
  - `chk_Students`: Ograniczenia sprawdzające, że:
    - Student ma co najmniej 16 lat.
    - Wszystkie pola mają co najmniej 1 znak.

```sql
CREATE TABLE Students (
    StudentID int NOT NULL IDENTITY(1,1),
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

## Tabela: `LectureRoomDetails`
Przechowuje szczegóły dotyczące sal wykładowych.

- `RoomID` *(int, NOT NULL)*: Unikalny identyfikator sali wykładowej.
- `BuildingNr` *(varchar(10), NOT NULL)*: Numer budynku, w którym znajduje się sala.
- `Floor` *(int, NOT NULL)*: Piętro, na którym znajduje się sala.
- `ClassNumber` *(int, NOT NULL)*: Numer klasy, w której odbywają się zajęcia.
- **Klucze i ograniczenia:**
  - `LectureRoomDetails_pk`: Klucz główny na kolumnie `RoomID`.
  - `chk_LectureRoomDetails`: Ograniczenie sprawdzające, że:
    - Numer budynku ma co najmniej 1 znak.
    - Numer klasy jest większy lub równy 0.

```sql
CREATE TABLE LectureRoomDetails (
    RoomID int NOT NULL IDENTITY(1,1),
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

## Tabela: `Languages`
Przechowuje informacje o językach, które są przypisane do tłumaczy.

- `TranslatorID` *(int, NOT NULL)*: Unikalny identyfikator tłumacza (klucz obcy).
- `LanguageID` *(int, NOT NULL)*: Unikalny identyfikator języka.
- **Klucze i ograniczenia:**
  - `LanguageID_pk`: Klucz główny na kombinacji kolumn `TranslatorID` i `LanguageID`.

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

## Tabela: `AvailableLanguages`
Przechowuje dostępne języki w systemie.

- `LanguageID` *(int, NOT NULL)*: Unikalny identyfikator języka.
- `Language` *(varchar(50), NOT NULL)*: Nazwa języka.
- **Klucze i ograniczenia:**
  - `AvailableLanguages_pk`: Klucz główny na kolumnie `LanguageID`.
  - `chk_AvailableLanguages`: Ograniczenie sprawdzające, że:
    - Nazwa języka ma co najmniej 1 znak.

```sql
CREATE TABLE AvailableLanguages (
    LanguageID int NOT NULL IDENTITY(1,1),
    Language varchar(50) NOT NULL,
    CONSTRAINT AvailableLanguages_pk PRIMARY KEY (LanguageID),
    CONSTRAINT chk_AvailableLanguages CHECK (
        LEN(Language) >= 1
    )
);
```

# Sekcji Studies


## Tabela: `FieldOfStudy`
Przechowuje informacje o kierunkach studiów.

- `FieldOfStudyID` *(int, NOT NULL)*: Unikalny identyfikator kierunku studiów.
- `Name` *(varchar(50), NOT NULL)*: Nazwa kierunku studiów.
- `Description` *(varchar(50), NOT NULL)*: Opis kierunku studiów.
- `Limit` *(int, NOT NULL)*: Limit liczby studentów na kierunku studiów.
- `EntryFee` *(money, NOT NULL)*: Opłata rejestracyjna za kierunek studiów.
- **Klucze i ograniczenia:**
  - `FieldOfStudy_pk`: Klucz główny na kolumnie `FieldOfStudyID`.
  - `chk_FieldOfStudy`: Ograniczenie sprawdzające, że:
    - Nazwa i opis kierunku studiów mają co najmniej 1 znak.
    - Opłata rejestracyjna oraz limit są większe lub równe 0.

```sql
CREATE TABLE FieldOfStudy (
    FieldOfStudyID int NOT NULL IDENTITY(1,1),
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
## Tabela: `FieldOfStudyStudentList`
Przechowuje informacje o studentach zapisanych na określony kierunek studiów oraz szczegóły dotyczące ich semestru.

- `FieldOfStudyID` *(int, NOT NULL)*: Unikalny identyfikator kierunku studiów.
- `StudentID` *(int, NOT NULL)*: Unikalny identyfikator studenta.
- `Semester` *(int, NOT NULL)*: Numer semestru, na którym student jest zapisany.
- `StartDate` *(date, NOT NULL)*: Data rozpoczęcia nauki na danym kierunku studiów.
- `EndDate` *(date, NULL)*: Data zakończenia nauki na danym kierunku studiów (opcjonalnie).
- **Klucze i ograniczenia:**
  - `FieldOfStudyStudentList_pk`: Klucz główny na kombinacji kolumn `StudentID` i `FieldOfStudyID`.
  - `chk_FieldOfStudyStudentList`: Ograniczenie sprawdzające, że:
    - Numer semestru jest większy lub równy 0.
    - Jeśli `EndDate` jest określona, to `StartDate` musi być wcześniejsze niż `EndDate`.

```sql
CREATE TABLE FieldOfStudyStudentList (
    FieldOfStudyID int NOT NULL,
    StudentID int NOT NULL,
    Semester int NOT NULL,
    StartDate date NOT NULL,
    EndDate date NULL,
    CONSTRAINT FieldOfStudyStudentList_pk PRIMARY KEY (StudentID, FieldOfStudyID, Semester),
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

## Tabela: `Subjects`
Przechowuje informacje o przedmiotach w danym kierunku studiów.

- `SubjectID` *(int, NOT NULL)*: Unikalny identyfikator przedmiotu.
- `FieldOfStudyID` *(int, NOT NULL)*: Unikalny identyfikator kierunku studiów, do którego należy przedmiot.
- `SubjectName` *(varchar(50), NOT NULL)*: Nazwa przedmiotu.
- `Description` *(varchar(50), NOT NULL)*: Opis przedmiotu.
- `MeetingsQuantity` *(int, NOT NULL)*: Liczba spotkań związanych z danym przedmiotem.
- `EmployeeID` *(int, NOT NULL)*: Identyfikator pracownika odpowiedzialnego za przedmiot.
- `Semester` *(int, NOT NULL)*: Numer semestru, w którym przedmiot jest realizowany.
- **Klucze i ograniczenia:**
  - `Subjects_pk`: Klucz główny na kolumnie `SubjectID`.
  - `chk_Subjects`: Ograniczenie sprawdzające, że:
    - Nazwa i opis przedmiotu mają co najmniej 1 znak.
    - Liczba spotkań jest większa niż 0.
    - Numer semestru jest większy lub równy 0.

```sql
CREATE TABLE Subjects (
    SubjectID int NOT NULL IDENTITY(1,1),
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

## Tabela: `SubjectGrades`
Przechowuje informacje o ocenach studentów z przedmiotów.

- `SubjectID` *(int, NOT NULL)*: Unikalny identyfikator przedmiotu.
- `StudentID` *(int, NOT NULL)*: Unikalny identyfikator studenta.
- `Grade` *(int, NOT NULL)*: Ocena przypisana studentowi z przedmiotu.
- **Klucze i ograniczenia:**
  - `SubjectGrades_pk`: Klucz główny na kombinacji kolumn `SubjectID` i `StudentID`.
  - `chk_SubjectGrades`: Ograniczenie sprawdzające, że:
    - Ocena jest większa lub równa 0.

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

## Tabela: `Meeting`
Przechowuje informacje o spotkaniach związanych z przedmiotami.

- `MeetingID` *(int, NOT NULL)*: Unikalny identyfikator spotkania.
- `MeetingTypeID` *(int, NOT NULL)*: Typ spotkania (np. wykład, ćwiczenia).
- `SubjectID` *(int, NOT NULL)*: Unikalny identyfikator przedmiotu, z którym związane jest spotkanie.
- `Meeting_date` *(datetime, NOT NULL)*: Data i godzina spotkania.
- `Link` *(varchar(max), NULL)*: Link do spotkania online (opcjonalnie).
- `RoomID` *(int, NULL)*: Identyfikator sali, w której odbywa się spotkanie (opcjonalnie).
- `LanguageID` *(int, NOT NULL)*: Identyfikator języka, w którym odbywa się spotkanie.
- `TranslatorID` *(int, NULL)*: Identyfikator tłumacza (opcjonalnie).
- `Price` *(money, NOT NULL)*: Cena za uczestnictwo w spotkaniu.
- `StationaryID` *(int, NULL)*: Identyfikator stacjonarnego narzędzia (opcjonalnie).
- **Klucze i ograniczenia:**
  - `Meeting_pk`: Klucz główny na kolumnie `MeetingID`.
  - `chk_Meeting`: Ograniczenie sprawdzające, że:
    - Cena spotkania jest większa lub równa 0.

```sql
CREATE TABLE Meeting (
    MeetingID int NOT NULL IDENTITY(1,1),
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

## Tabela: `StudentAbsence`
Przechowuje informacje o nieobecnościach studentów na spotkaniach.

- `MeetingID` *(int, NOT NULL)*: Unikalny identyfikator spotkania, na którym student był nieobecny.
- `StudentID` *(int, NOT NULL)*: Unikalny identyfikator studenta, który był nieobecny.
- `ClassRetakeID` *(int, NULL)*: Identyfikator egzaminu poprawkowego lub innego rozwiązania w przypadku nieobecności (opcjonalnie).
- **Klucze i ograniczenia:**
  - `StudentAbsence_pk`: Klucz główny na kombinacji kolumn `MeetingID` i `StudentID`.

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

## Tabela: `Interships`
Przechowuje informacje o praktykach związanych z kierunkami studiów.

- `IntershipID` *(int, NOT NULL)*: Unikalny identyfikator praktyk.
- `FieldOfStudyID` *(int, NOT NULL)*: Unikalny identyfikator kierunku studiów, do którego przypisane są praktyki.
- `IntershipName` *(varchar(50), NOT NULL)*: Nazwa praktyk.
- `StartDate` *(date, NOT NULL)*: Data rozpoczęcia praktyk.
- `EndDate` *(date, NOT NULL)*: Data zakończenia praktyk.
- **Klucze i ograniczenia:**
  - `Interships_pk`: Klucz główny na kolumnie `IntershipID`.
  - `chk_Interships`: Ograniczenie sprawdzające, że:
    - Nazwa praktyk ma co najmniej 1 znak.
    - Data rozpoczęcia jest wcześniejsza niż data zakończenia.

```sql
CREATE TABLE Interships (
    IntershipID int NOT NULL IDENTITY(1,1),
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

## Tabela: `IntershipsAbsence`
Przechowuje informacje o nieobecnościach studentów na praktykach.

- `IntershipID` *(int, NOT NULL)*: Unikalny identyfikator praktyk, na których student był nieobecny.
- `StudentID` *(int, NOT NULL)*: Unikalny identyfikator studenta, który był nieobecny.
- `Absence` *(datetime, NOT NULL)*: Data i godzina nieobecności studenta.
- **Klucze i ograniczenia:**
  - `IntershipsAbsence_pk`: Klucz główny na kombinacji kolumn `IntershipID`, `StudentID` i `Absence`.

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

## Tabela: `StationaryWeek`
Przechowuje informacje o tygodniu stacjonarnym związanym z kierunkiem studiów.

- `Semester` *(int, NOT NULL)*: Numer semestru, w którym odbywa się tydzień stacjonarny.
- `StartDate` *(date, NOT NULL)*: Data rozpoczęcia tygodnia stacjonarnego.
- `EndDate` *(date, NOT NULL)*: Data zakończenia tygodnia stacjonarnego.
- `FieldOfStudyID` *(int, NOT NULL)*: Unikalny identyfikator kierunku studiów, do którego przypisany jest tydzień stacjonarny.
- `StationaryID` *(int, NOT NULL)*: Unikalny identyfikator tygodnia stacjonarnego.
- `Price` *(money, NOT NULL)*: Cena związana z tygodniem stacjonarnym.
- `Quantity` *(int, NOT NULL)*: Ilość dostępnych miejsc na tydzień stacjonarny.
- **Klucze i ograniczenia:**
  - `StationaryWeek_pk`: Klucz główny na kolumnie `StationaryID`.
  - `chk_StationaryWeek`: Ograniczenie sprawdzające, że:
    - Data rozpoczęcia jest wcześniejsza niż data zakończenia.
    - Numer semestru jest większy lub równy 0.

```sql
CREATE TABLE StationaryWeek (
    Semester int NOT NULL,
    StartDate date NOT NULL,
    EndDate date NOT NULL,
    FieldOfStudyID int NOT NULL,
    StationaryID int NOT NULL IDENTITY(1,1),
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

## Tabela: `MeetingType`
Przechowuje informacje o typach spotkań.

- `MeetingTypeID` *(int, NOT NULL)*: Unikalny identyfikator typu spotkania.
- `Description` *(varchar(50), NOT NULL)*: Opis typu spotkania (np. wykład, ćwiczenia).
- **Klucze i ograniczenia:**
  - `MeetingType_pk`: Klucz główny na kolumnie `MeetingTypeID`.
  - `chk_MeetingType`: Ograniczenie sprawdzające, że:
    - Opis ma co najmniej 1 znak.

```sql
CREATE TABLE MeetingType (
    MeetingTypeID int NOT NULL IDENTITY(1,1),
    Description varchar(50) NOT NULL,
    CONSTRAINT MeetingType_pk PRIMARY KEY (MeetingTypeID),
    CONSTRAINT chk_MeetingType CHECK (
        LEN(Description) >= 1
    )
);
```

# Sekcja Webinars
## Tabela: `Webinar`
Przechowuje informacje o webinarach.

- `WebinarID` *(int, NOT NULL)*: Unikalny identyfikator webinaru.
- `WebinarName` *(varchar(50), NOT NULL)*: Nazwa webinaru.
- `Price` *(money, NOT NULL)*: Cena uczestnictwa w webinarze.
- `Webinar_date` *(datetime, NOT NULL)*: Data i godzina odbywania się webinaru.
- `LanguageID` *(int, NOT NULL)*: Identyfikator języka, w którym odbywa się webinar.
- `TranslatorID` *(int, NULL)*: Identyfikator tłumacza (opcjonalnie), który tłumaczy webinar.
- `EmployeeID` *(int, NOT NULL)*: Identyfikator pracownika organizującego webinar.
- `OnlineLink` *(varchar(max), NULL)*: Link do uczestnictwa w webinarze na żywo.
- `VideoLink` *(varchar(max), NULL)*: Link do nagrania webinaru.
- **Klucze i ograniczenia:**
  - `Webinar_pk`: Klucz główny na kolumnie `WebinarID`.
  - `chk_Webinar`: Ograniczenie sprawdzające, że:
    - Cena webinaru jest większa lub równa 0.
    - Nazwa webinaru ma co najmniej 1 znak.

```sql
CREATE TABLE Webinar (
    WebinarID int NOT NULL IDENTITY(1,1),
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

## Tabela: `WebinarExpirationDate`
Przechowuje informacje o dacie wygaśnięcia dostępu do webinaru dla studenta.

- `WebinarID` *(int, NOT NULL)*: Unikalny identyfikator webinaru.
- `StudentID` *(int, NOT NULL)*: Unikalny identyfikator studenta.
- `expr_date` *(date, NULL)*: Data wygaśnięcia dostępu do webinaru dla studenta.
- **Klucze i ograniczenia:**
  - `WebinarExpirationDate_pk`: Klucz główny na kombinacji kolumn `WebinarID` i `StudentID`.

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
# Widoki
## Zestawienie przychodów dla każdego szkolenia

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
## Zestawienie przychodów dla każdego webinaru

```sql
CREATE VIEW WebinarsFinancialReport AS
SELECT ID AS 'Webinar ID', Name, Income
FROM FinancialReport
WHERE Type = 'Webinar'
```
## Zestawienie przychodów dla każdego kursu
```sql
CREATE VIEW CoursesFinancialReport AS
SELECT ID AS 'Course ID', Name, Income
FROM FinancialReport
WHERE Type = 'Course'
```

## Zestawienie przychodów dla każdego studium
```sql
CREATE VIEW StudiesFinancialReport AS
SELECT ID AS 'FieldOfStudy ID', Name, Income
FROM FinancialReport
WHERE Type = 'Study'
```

## Lista dłużników
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
    OD.PaidDate IS NULL
```

## Raport o liczbie zapisanych osób na przyszłe wydarzenia

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

## Raport o liczbie zapisanych osób na przyszłe spotkania studyjne
```sql
CREATE VIEW NumberOfPeopleRegisteredForFutureStudyMeetings AS
SELECT ID AS 'StudyMeetingID', NumberOfParticipants
FROM NumberOfPeopleRegisteredForFutureEvents
WHERE Type = 'Study Meeting'
```

## Raport o liczbie zapisanych osób na przyszłe moduły w ramach kursów
```sql
CREATE VIEW NumberOfPeopleRegisteredForFutureCourseModules AS
SELECT ID AS 'CourseModuleID', NumberOfParticipants
FROM NumberOfPeopleRegisteredForFutureEvents
WHERE Type = 'Course Module'
```
## Raport o liczbie zapisanych osób na przyszłe webinary
```sql
CREATE VIEW NumberOfPeopleRegisteredForFutureWebinars AS
SELECT ID AS 'WebinarID', NumberOfParticipants
FROM NumberOfPeopleRegisteredForFutureEvents
WHERE Type = 'Webinar'
```
## Frekwencja na zakończonych wydarzeniach
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
FROM ModuleAttendance
```
## Frekwencja na zakończonych spotkaniach studyjnych
```sql
CREATE VIEW StudyMeetingsAttendanceSummary AS
SELECT EventID AS 'StudyMeetingID', FrequencePercentage
FROM AttendanceSummary
WHERE Eventtype = 'Study Meeting'
```
## Frekwencja na zakończonych modułach kursów
```sql
CREATE VIEW CourseModulesAttendanceSummary AS
SELECT EventID AS 'CourseModuleID', FrequencePercentage
FROM AttendanceSummary
WHERE Eventtype = 'Course Module'
```
## Lista obecności na każdy meeting
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
    M.Meeting_date < GETDATE()
```
## Lista obecności na każdy moduł kursu

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
    CS.Course_date < GETDATE()
```

## Lista osób zapisanych na co najmniej 2 przyszłe szkolenia, które kolidują ze sobą czasowo

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
            AND E1.EventID < E2.EventID
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
    Conflicts
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

        SELECT DISTINCT
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
            M.MeetingID = @MeetingID
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
            M.ModuleID = @ModuleID
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
            W.WebinarID = @WebinarID
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
            C.CourseID = @CourseID
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
            IA.StudentID = @StudentID
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
        AND M.Meeting_date > @StartDate AND M.Meeting_date < @EndDate
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
        AND CS.Course_date > @StartDate AND CS.Course_date < @EndDate
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
        AND W.Webinar_date > @StartDate AND W.Webinar_date < @EndDate
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
END
```
## Sprawdzanie czy student odbył wszystkie praktyki, które powinien odbyć
```sql
CREATE FUNCTION CheckInternshipAbsences(@StudentID INT)
    RETURNS TABLE
        AS
        RETURN
        SELECT DISTINCT
            I.IntershipID,
            I.IntershipName,
            IA.Absence AS AbsenceDate
        FROM
            FieldOfStudyStudentList FSL
                JOIN Interships I ON FSL.FieldOfStudyID = I.FieldOfStudyID
                JOIN IntershipsAbsence IA ON I.IntershipID = IA.IntershipID AND IA.StudentID = FSL.StudentID
        WHERE
            FSL.StudentID = @StudentID
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
            StartDate > GETDATE()
```

# Triggery

## trigger ustawiający datę wygaśnięcia webinaru po jego zakupie
```sql
CREATE TRIGGER trg_AddWebinarExpirationDate
ON OrderWebinar
AFTER INSERT
AS
BEGIN
    DECLARE @OrderDetailID INT, @WebinarID INT, @StudentID INT, @WebinarDate DATETIME, @PaidDate DATETIME;

    -- Pobieramy dane z tabeli inserted
    SELECT @OrderDetailID = OrderDetailsID, @WebinarID = WebinarID
    FROM inserted;

    -- Znajdź datę odbycia się webinaru
    SELECT @WebinarDate = Webinar_date
    FROM Webinar
    WHERE WebinarID = @WebinarID;

    -- Pobierz datę zakupu (PaidDate) z tabeli OrderDetails
    SELECT @PaidDate = PaidDate
    FROM OrderDetails
    WHERE OrderDetailsID = @OrderDetailID;

    -- Pobierz ID studenta z OrderDetails
    SELECT @StudentID = StudentID
    FROM Orders
    WHERE OrderID = (SELECT OrderID FROM OrderDetails WHERE OrderDetailsID = @OrderDetailID);

    -- Jeżeli webinar się jeszcze nie odbył, ustawiamy datę wygaśnięcia na 30 dni po webinarze
    IF @WebinarDate > GETDATE()
    BEGIN
        INSERT INTO WebinarExpirationDate (WebinarID, StudentID, expr_date)
        VALUES (@WebinarID, @StudentID, DATEADD(DAY, 30, @WebinarDate));
    END
    -- Jeżeli webinar już się odbył, ustawiamy datę wygaśnięcia na 30 dni po dacie zakupu
    ELSE
    BEGIN
        INSERT INTO WebinarExpirationDate (WebinarID, StudentID, expr_date)
        VALUES (@WebinarID, @StudentID, DATEADD(DAY, 30, @PaidDate));
    END
END;
```
## trigger dodający studenta do modułów zakupionego kursu
```sql
CREATE TRIGGER trg_AddStudentToCourseModules
ON OrderCourse
AFTER INSERT
AS
BEGIN
    SET NOCOUNT ON;

    -- Zmienna do przechowywania StudentID i CourseID
    DECLARE @StudentID INT;
    DECLARE @CourseID INT;

    -- Pobierz CourseID z wstawionego rekordu w OrderCourse
    SELECT @CourseID = i.CourseID
    FROM inserted i;

    -- Pobierz StudentID na podstawie OrderID z OrderDetails, a następnie z Orders
    SELECT @StudentID = o.StudentID
    FROM OrderDetails od
    INNER JOIN inserted i ON i.OrderDetailsID = od.OrderDetailsID
    INNER JOIN Orders o ON od.OrderID = o.OrderID;

    -- Dodaj rekordy do CourseModulesProgress dla każdego modułu powiązanego z kursem
    INSERT INTO CourseModulesProgress (CourseID, ModuleID, StudentID, Passed)
    SELECT @CourseID, m.ModuleID, @StudentID, 0 
    FROM Modules m
    WHERE m.CourseID = @CourseID;

END;
```

## trigger dodający styudenta do kierunku zakupionych studiów
```sql
CREATE TRIGGER trg_AddStudentToFieldOfStudy
ON OrderStudies
AFTER INSERT
AS
BEGIN
    SET NOCOUNT ON;

    -- Zmienne do przechowywania StudentID i FieldOfStudyID
    DECLARE @StudentID INT;
    DECLARE @FieldOfStudyID INT;

    -- Pobierz StudentID na podstawie OrderDetailsID z OrderDetails i Orders
    SELECT @StudentID = o.StudentID
    FROM OrderDetails od
    INNER JOIN inserted i ON od.OrderDetailsID = i.OrderDetailsID
    INNER JOIN Orders o ON od.OrderID = o.OrderID;

    -- Pobierz FieldOfStudyID z wstawionego rekordu w OrderStudies
    SELECT @FieldOfStudyID = i.FieldOfStudyID
    FROM inserted i;

    -- Dodaj nowy rekord do FieldOfStudyStudentList
    INSERT INTO FieldOfStudyStudentList (FieldOfStudyID, StudentID, Semester, StartDate, EndDate)
    VALUES (@FieldOfStudyID, @StudentID, 1,
            NULL);
END;
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
        RAISERROR('Kierunek studiów o podanym ID nie istnieje.', 16, 1);
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
-- Sprawdź, czy istnieje student o podanym StudentID
    if not exists (select 1 from Students where StudentID = @StudentID)
        begin
            raiserror('Student o podanym ID nie istnieje.', 16, 1);
        end
-- Wstaw nowe zamówienie do tabeli Orders
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
    @MeetingID int = null,
    @StudentID int = null
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        -- Sprawdź, czy istnieje zamówienie o podanym OrderID
        IF NOT EXISTS (SELECT 1 FROM Orders WHERE OrderID = @OrderID)
        BEGIN
            RAISERROR('Zamówienie o podanym ID nie istnieje.', 16, 1);
        END

        SELECT @OrderDetailID = ISNULL(MAX(OrderDetailsID), 0) + 1
        FROM OrderDetails;

        -- Znajdź ID studenta składającego zamówienie
        SELECT @StudentID = StudentID
        FROM Orders
        WHERE OrderID = @OrderID;

        -- TRANSAKCJA: Dodanie webinaru
        IF @WebinarID IS NOT NULL
        BEGIN
            BEGIN TRANSACTION;
            BEGIN TRY
                EXEC CheckResources @OrderID, @WebinarID, 'Webinar';
                
                IF @PaidDate IS NOT NULL
                    BEGIN
                        INSERT INTO OrderDetails (OrderDetailsID, OrderID, PaidDate, AccessGiven)
                        VALUES (@OrderDetailID, @OrderID, @PaidDate, 1);
                    END
                ELSE
                    BEGIN
                        INSERT INTO OrderDetails (OrderDetailsID, OrderID, PaidDate, AccessGiven)
                        VALUES (@OrderDetailID, @OrderID, @PaidDate, 0);
                    END

                INSERT INTO OrderWebinar (OrderDetailsID, WebinarID)
                VALUES (@OrderDetailID, @WebinarID);
                    
                COMMIT;
                PRINT 'Szczegół zamówienia dodany pomyślnie (Webinar).';
            END TRY
            BEGIN CATCH
                ROLLBACK;
                THROW;
            END CATCH
        END

        -- TRANSAKCJA: Dodanie kursu
        IF @CourseID IS NOT NULL
        BEGIN
            BEGIN TRANSACTION;
            BEGIN TRY
                SELECT 1 FROM OrderCourse WITH (TABLOCKX);
                EXEC CheckResources @OrderID, @CourseID, 'Course';
                
                IF @PaidDate IS NOT NULL
                    BEGIN
                        INSERT INTO OrderDetails (OrderDetailsID, OrderID, PaidDate, AccessGiven)
                        VALUES (@OrderDetailID, @OrderID, @PaidDate, 1);
                    END
                ELSE
                    BEGIN
                        INSERT INTO OrderDetails (OrderDetailsID, OrderID, PaidDate, AccessGiven)
                        VALUES (@OrderDetailID, @OrderID, @PaidDate, 0);
                    END

                INSERT INTO OrderCourse (OrderDetailsID, CourseID)
                VALUES (@OrderDetailID, @CourseID);
                    
                COMMIT;
                PRINT 'Szczegół zamówienia dodany pomyślnie (Course).';
            END TRY
            BEGIN CATCH
                ROLLBACK;
                THROW;
            END CATCH
        END

        -- TRANSAKCJA: Dodanie studiów
        IF @StudiesID IS NOT NULL
        BEGIN
            BEGIN TRANSACTION;
            BEGIN TRY
                SELECT 1 FROM OrderStudies WITH (TABLOCKX);
                EXEC CheckResources @OrderID, @StudiesID, 'Studies';
                
                IF @PaidDate IS NOT NULL
                    BEGIN
                        INSERT INTO OrderDetails (OrderDetailsID, OrderID, PaidDate, AccessGiven)
                        VALUES (@OrderDetailID, @OrderID, @PaidDate, 1);
                    END
                ELSE
                    BEGIN
                        INSERT INTO OrderDetails (OrderDetailsID, OrderID, PaidDate, AccessGiven)
                        VALUES (@OrderDetailID, @OrderID, @PaidDate, 0);
                    END

                INSERT INTO OrderStudies (OrderDetailsID, FieldOfStudyID)
                VALUES (@OrderDetailID, @StudiesID);
                EXEC AddStudentToFieldOfStudy @StudentId = @StudentId, @FieldOfStudyId = @StudiesID;
                    
                COMMIT;
                PRINT 'Szczegół zamówienia dodany pomyślnie (Studies).';
            END TRY
            BEGIN CATCH
                ROLLBACK;
                THROW;
            END CATCH
        END

        -- TRANSAKCJA: Dodanie spotkania
        IF @MeetingID IS NOT NULL
        BEGIN
            BEGIN TRANSACTION;
            BEGIN TRY
                SELECT 1 FROM OrderMeeting WITH (TABLOCKX);
                EXEC CheckResources @OrderID, @MeetingID, 'Meeting';
                
                IF @PaidDate IS NOT NULL
                    BEGIN
                        INSERT INTO OrderDetails (OrderDetailsID, OrderID, PaidDate, AccessGiven)
                        VALUES (@OrderDetailID, @OrderID, @PaidDate, 1);
                    END
                ELSE
                    BEGIN
                        INSERT INTO OrderDetails (OrderDetailsID, OrderID, PaidDate, AccessGiven)
                        VALUES (@OrderDetailID, @OrderID, @PaidDate, 0);
                    END

                INSERT INTO OrderMeeting (OrderDetailsID, MeetingID)
                VALUES (@OrderDetailID, @MeetingID);
                    
                COMMIT;
                PRINT 'Szczegół zamówienia dodany pomyślnie (Meeting).';
            END TRY
            BEGIN CATCH
                ROLLBACK;
                THROW;
            END CATCH
        END


    END TRY
    BEGIN CATCH
        -- Globalny błąd, gdyby coś poszło nie tak poza transakcjami
        DECLARE @ErrorMessage NVARCHAR(4000), @ErrorSeverity INT, @ErrorState INT;
        SELECT @ErrorMessage = ERROR_MESSAGE(), @ErrorSeverity = ERROR_SEVERITY(), @ErrorState = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```
## Procedura pomocnicza
```sql
CREATE PROCEDURE CheckResources
    @OrderID INT,
    @ResourceID INT,
    @ResourceType NVARCHAR(50)
AS
BEGIN
    BEGIN TRY
        DECLARE @StudentID INT;

        -- Pobranie `StudentID` na podstawie `OrderID`
        SELECT @StudentID = StudentID
        FROM Orders
        WHERE OrderID = @OrderID;

        IF @StudentID IS NULL
            THROW 50013, 'Nie znaleziono studenta dla danego zamówienia.', 1;

        -- Weryfikacja, czy dany zasób istnieje
        IF @ResourceType = 'Webinar' AND NOT EXISTS (
            SELECT 1 FROM Webinar WHERE WebinarID = @ResourceID
        )
            THROW 50008, 'Webinar o podanym ID nie istnieje.', 1;

        ELSE IF @ResourceType = 'Course' AND NOT EXISTS (
            SELECT 1 FROM Courses WHERE CourseID = @ResourceID
        )
            THROW 50009, 'Kurs o podanym ID nie istnieje.', 1;

        ELSE IF @ResourceType = 'Studies' AND NOT EXISTS (
            SELECT 1 FROM FieldOfStudy WHERE FieldOfStudyID = @ResourceID
        )
            THROW 50010, 'Studia o podanym ID nie istnieją.', 1;

        ELSE IF @ResourceType = 'Meeting' AND NOT EXISTS (
            SELECT 1 FROM Meeting WHERE MeetingID = @ResourceID
        )
            THROW 50011, 'Spotkanie o podanym ID nie istnieje.', 1;

        -- Weryfikacja, czy student jest już zapisany na dany zasób
        IF @ResourceType = 'Webinar' AND EXISTS (
            SELECT 1 FROM GetAttendeesByWebinarID(@ResourceID) WHERE StudentID = @StudentID
        )
            THROW 50001, 'Student jest już zapisany na ten webinar.', 1;

        ELSE IF @ResourceType = 'Course' AND EXISTS (
            SELECT 1 FROM GetAttendeesByCourseID(@ResourceID) WHERE StudentID = @StudentID
        )
            THROW 50002, 'Student jest już zapisany na ten kurs.', 1;

        ELSE IF @ResourceType = 'Studies' AND EXISTS (
            SELECT 1 FROM FieldOfStudyStudentList 
            WHERE FieldOfStudyID = @ResourceID AND StudentID = @StudentID
        )
            THROW 50003, 'Student jest już zapisany na te studia.', 1;

        ELSE IF @ResourceType = 'Meeting' AND EXISTS (
            SELECT 1 FROM GetAttendeesByMeetingID(@ResourceID) WHERE StudentID = @StudentID
        )
            THROW 50004, 'Student jest już zapisany na to spotkanie.', 1;

        -- Weryfikacja wolnych miejsc
        IF @ResourceType = 'Course' AND EXISTS (
            SELECT 1 FROM Courses
            WHERE CourseID = @ResourceID
              AND Limit <= (SELECT COUNT(*) FROM GetAttendeesByCourseID(@ResourceID))
        )
            THROW 50006, 'Brak wolnych miejsc na kurs.', 1;

        ELSE IF @ResourceType = 'Studies' AND EXISTS (
            SELECT 1 FROM FieldOfStudy
            WHERE FieldOfStudyID = @ResourceID
              AND Limit <= (
                  SELECT COUNT(*) 
                  FROM FieldOfStudyStudentList 
                  WHERE FieldOfStudyID = @ResourceID AND EndDate IS NULL
              )
        )
            THROW 50007, 'Brak wolnych miejsc na studiach.', 1;

        ELSE IF @ResourceType = 'Meeting' AND EXISTS (
            SELECT 1
            FROM Meeting m
            JOIN Subjects s ON s.SubjectID = m.SubjectID
            JOIN FieldOfStudy f ON f.FieldOfStudyID = s.FieldOfStudyID
            WHERE m.MeetingID = @ResourceID
              AND f.Limit <= (
                  SELECT COUNT(*)
                  FROM GetAttendeesByMeetingID(@ResourceID)
              )
        )
            THROW 50012, 'Brak wolnych miejsc na spotkaniu.', 1;

    END TRY
    BEGIN CATCH
        THROW;
    END CATCH
END;
go
```

# Role

## Admin
```sql
create role Admin;
grant all privileges on u_mokrzyck.dbo to Admin
```

## Director
```sql
CREATE ROLE Director;

-- Przypisanie uprawnień do funkcji
GRANT EXECUTE ON GetAttendeesByMeetingID TO Director;
GRANT EXECUTE ON GetAttendeesByModuleID TO Director;
GRANT EXECUTE ON GetAttendeesByWebinarID TO Director;
GRANT EXECUTE ON GetAttendeesByCourseID TO Director;
GRANT EXECUTE ON GetAbsencesByStudentID TO Director;
GRANT EXECUTE ON GetStudySchedule TO Director;
GRANT EXECUTE ON GetCourseSchedule TO Director;
GRANT EXECUTE ON GetStudentSchedule TO Director;
GRANT EXECUTE ON GetTotalOrderValue TO Director;
GRANT EXECUTE ON CheckInternshipAbsences TO Director;
GRANT EXECUTE ON GetUnpaidStationaryWeeks TO Director;

-- Przypisanie uprawnień do widoków
GRANT SELECT ON FinancialReport TO Director;
GRANT SELECT ON WebinarsFinancialReport TO Director;
GRANT SELECT ON CoursesFinancialReport TO Director;
GRANT SELECT ON StudiesFinancialReport TO Director;
GRANT SELECT ON DebtorsList TO Director;
GRANT SELECT ON NumberOfPeopleRegisteredForFutureEvents TO Director;
GRANT SELECT ON NumberOfPeopleRegisteredForFutureStudyMeetings TO Director;
GRANT SELECT ON NumberOfPeopleRegisteredForFutureCourseModules TO Director;
GRANT SELECT ON NumberOfPeopleRegisteredForFutureWebinars TO Director;
GRANT SELECT ON AttendanceSummary TO Director;
GRANT SELECT ON StudyMeetingsAttendanceSummary TO Director;
GRANT SELECT ON CourseModulesAttendanceSummary TO Director;
GRANT SELECT ON MeetingPresenceList TO Director;
GRANT SELECT ON AttendanceListForModules TO Director;
GRANT SELECT ON ConflictingFutureEventRegistrations TO Director;

-- Przypisanie uprawnień do procedur
GRANT EXECUTE ON AddStudent TO Director;
GRANT EXECUTE ON AddEmployee TO Director;
GRANT EXECUTE ON AddEmployeeType TO Director;
GRANT EXECUTE ON AddAvailableLanguage TO Director;
GRANT EXECUTE ON AddTranslator TO Director;
GRANT EXECUTE ON AddLanguage TO Director;
GRANT EXECUTE ON AddLectureRoomDetails TO Director;
GRANT EXECUTE ON AddWebinar TO Director;
GRANT EXECUTE ON AddCourse TO Director;
GRANT EXECUTE ON AddFieldOfStudy TO Director;
```

## Prowadzący kurs
```sql
CREATE ROLE CourseInstructor;

-- Przyznajemy dostęp do funkcji
GRANT EXECUTE ON GetAttendeesByModuleID TO CourseInstructor;
GRANT EXECUTE ON GetAttendeesByCourseID TO CourseInstructor;
GRANT EXECUTE ON GetCourseSchedule TO CourseInstructor;

-- Przyznajemy dostęp do procedur
GRANT EXECUTE ON AddModule TO CourseInstructor;
GRANT EXECUTE ON AddModuleAbsence TO CourseInstructor;
GRANT EXECUTE ON AddCourseSchedule TO CourseInstructor;

-- Przyznajemy dostęp do widoków
GRANT SELECT ON NumberOfPeopleRegisteredForFutureEvents TO CourseInstructor;
GRANT SELECT ON NumberOfPeopleRegisteredForFutureCourseModules TO CourseInstructor;
GRANT SELECT ON AttendanceSummary TO CourseInstructor;
GRANT SELECT ON CourseModulesAttendanceSummary TO CourseInstructor;
GRANT SELECT ON AttendanceListForModules TO CourseInstructor;
```

## Prowadzący webinar
```sql
CREATE ROLE WebinarInstructor;

-- Nadawanie dostępu do funkcji
GRANT EXECUTE ON GetAttendeesByWebinarID TO WebinarInstructor;

-- Nadawanie dostępu do widoków
GRANT SELECT ON NumberOfPeopleRegisteredForFutureEvents TO WebinarInstructor;
GRANT SELECT ON NumberOfPeopleRegisteredForFutureWebinars TO WebinarInstructor;
```

## Prowadzący przedmiot
```sql
CREATE ROLE SubjectInstructor;

-- Nadawanie dostępu do funkcji
GRANT EXECUTE ON GetAttendeesByMeetingID TO SubjectInstructor;
GRANT EXECUTE ON GetAbsencesByStudentID TO SubjectInstructor;
GRANT EXECUTE ON GetStudySchedule TO SubjectInstructor;
GRANT EXECUTE ON CheckInternshipAbsences TO SubjectInstructor;

-- Nadawanie dostępu do procedur
GRANT EXECUTE ON AddSubjectGrade TO SubjectInstructor;
GRANT EXECUTE ON AddStationaryWeek TO SubjectInstructor;
GRANT EXECUTE ON AddMeetingType TO SubjectInstructor;
GRANT EXECUTE ON AddMeeting TO SubjectInstructor;
GRANT EXECUTE ON AddStudentAbsence TO SubjectInstructor;
GRANT EXECUTE ON SetClassRetakeID TO SubjectInstructor;
GRANT EXECUTE ON AddIntership TO SubjectInstructor;
GRANT EXECUTE ON AddIntershipAbsence TO SubjectInstructor;

-- Nadawanie dostępu do widoków
GRANT SELECT ON NumberOfPeopleRegisteredForFutureEvents TO SubjectInstructor;
GRANT SELECT ON NumberOfPeopleRegisteredForFutureStudyMeetings TO SubjectInstructor;
GRANT SELECT ON AttendanceSummary TO SubjectInstructor;
GRANT SELECT ON StudyMeetingsAttendanceSummary TO SubjectInstructor;
GRANT SELECT ON MeetingPresenceList TO SubjectInstructor;
```

## Student
```sql
CREATE ROLE Student;

-- Nadawanie dostępu do funkcji
GRANT EXECUTE ON GetAbsencesByStudentID TO Student;
GRANT EXECUTE ON GetStudySchedule TO Student;
GRANT EXECUTE ON GetCourseSchedule TO Student;
GRANT EXECUTE ON GetStudentSchedule TO Student;
GRANT EXECUTE ON CheckInternshipAbsences TO Student;
GRANT EXECUTE ON GetUnpaidStationaryWeeks TO Student;

-- Nadawanie uprawnień SELECT do tabel
GRANT SELECT ON Webinar TO Student;
GRANT SELECT ON AvailableLanguages TO Student;
```