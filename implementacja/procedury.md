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
-- dodawanie zamówienia
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

-- dodawanie szczegółów zamówienia
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