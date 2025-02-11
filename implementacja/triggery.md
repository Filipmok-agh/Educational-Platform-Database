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