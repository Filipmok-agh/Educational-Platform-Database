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