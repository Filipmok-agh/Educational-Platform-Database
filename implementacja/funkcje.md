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