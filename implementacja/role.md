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