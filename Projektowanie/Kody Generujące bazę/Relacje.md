## Relacje między tabelami
```sql

-- Reference: CourseModulesProgress_Courses (table: CourseModulesProgress)
ALTER TABLE CourseModulesProgress ADD CONSTRAINT CourseModulesProgress_Courses
    FOREIGN KEY (CourseID)
    REFERENCES Courses (CourseID);

-- Reference: CourseModulesProgress_Modules (table: CourseModulesProgress)
ALTER TABLE CourseModulesProgress ADD CONSTRAINT CourseModulesProgress_Modules
    FOREIGN KEY (ModuleID)
    REFERENCES Modules (ModuleID);

-- Reference: CourseModulesProgress_Students (table: CourseModulesProgress)
ALTER TABLE CourseModulesProgress ADD CONSTRAINT CourseModulesProgress_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: CourseSchedule_LectureRoomDetails (table: CourseSchedule)
ALTER TABLE CourseSchedule ADD CONSTRAINT CourseSchedule_LectureRoomDetails
    FOREIGN KEY (RoomID)
    REFERENCES LectureRoomDetails (RoomID);

-- Reference: CourseSchedule_Modules (table: CourseSchedule)
ALTER TABLE CourseSchedule ADD CONSTRAINT CourseSchedule_Modules
    FOREIGN KEY (ModuleID)
    REFERENCES Modules (ModuleID);

-- Reference: Courses_AvailableLanguages (table: Courses)
ALTER TABLE Courses ADD CONSTRAINT Courses_AvailableLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvailableLanguages (LanguageID);

-- Reference: Courses_Employees (table: Courses)
ALTER TABLE Courses ADD CONSTRAINT Courses_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: Courses_Translator (table: Courses)
ALTER TABLE Courses ADD CONSTRAINT Courses_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);

-- Reference: EmployeeType_Employees (table: EmployeeType)
ALTER TABLE EmployeeType ADD CONSTRAINT EmployeeType_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: FieldOfStudyStudentList_FieldOfStudy (table: FieldOfStudyStudentList)
ALTER TABLE FieldOfStudyStudentList ADD CONSTRAINT FieldOfStudyStudentList_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: FieldOfStudyStudentList_Students (table: FieldOfStudyStudentList)
ALTER TABLE FieldOfStudyStudentList ADD CONSTRAINT FieldOfStudyStudentList_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: IntershipsAbsence_Interships (table: IntershipsAbsence)
ALTER TABLE IntershipsAbsence ADD CONSTRAINT IntershipsAbsence_Interships
    FOREIGN KEY (IntershipID)
    REFERENCES Interships (IntershipID);

-- Reference: IntershipsAbsence_Students (table: IntershipsAbsence)
ALTER TABLE IntershipsAbsence ADD CONSTRAINT IntershipsAbsence_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: Interships_FieldOfStudy (table: Interships)
ALTER TABLE Interships ADD CONSTRAINT Interships_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: Languages_AvailableLanguages (table: Languages)
ALTER TABLE Languages ADD CONSTRAINT Languages_AvailableLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvailableLanguages (LanguageID);

-- Reference: Languages_Translator (table: Languages)
ALTER TABLE Languages ADD CONSTRAINT Languages_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);

-- Reference: Meeting_LectureRoomDetails (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_LectureRoomDetails
    FOREIGN KEY (RoomID)
    REFERENCES LectureRoomDetails (RoomID);

-- Reference: Meeting_AvailableLanguages (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_AvailableLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvailableLanguages (LanguageID);

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

-- Reference: Meeting_StationaryWeek (table: Meeting)
ALTER TABLE Meeting ADD CONSTRAINT Meeting_StationaryWeek
    FOREIGN KEY (StationaryID)
    REFERENCES StationaryWeek (StationaryID);

-- Reference: ModuleAbsence_Modules (table: ModuleAbsence)
ALTER TABLE ModuleAbsence ADD CONSTRAINT ModuleAbsence_Modules
    FOREIGN KEY (ModuleID)
    REFERENCES Modules (ModuleID);

-- Reference: ModuleAbsence_Students (table: ModuleAbsence)
ALTER TABLE ModuleAbsence ADD CONSTRAINT ModuleAbsence_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: Modules_Courses (table: Modules)
ALTER TABLE Modules ADD CONSTRAINT Modules_Courses
    FOREIGN KEY (CourseID)
    REFERENCES Courses (CourseID);

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

-- Reference: OrderStationaryWeek_OrderDetails (table: OrderStationaryWeek)
ALTER TABLE OrderStationaryWeek ADD CONSTRAINT OrderStationaryWeek_OrderDetails
    FOREIGN KEY (OrderDetailsID)
    REFERENCES OrderDetails (OrderDetailsID);

-- Reference: OrderStationaryWeek_StationaryWeek (table: OrderStationaryWeek)
ALTER TABLE OrderStationaryWeek ADD CONSTRAINT OrderStationaryWeek_StationaryWeek
    FOREIGN KEY (StationaryID)
    REFERENCES StationaryWeek (StationaryID);

-- Reference: OrderStudies_FieldOfStudy (table: OrderStudies)
ALTER TABLE OrderStudies ADD CONSTRAINT OrderStudies_FieldOfStudy
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

-- Reference: StationaryWeek_FieldOfStudy (table: StationaryWeek)
ALTER TABLE StationaryWeek ADD CONSTRAINT StationaryWeek_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: StudentAbsence_Meeting (table: StudentAbsence)
ALTER TABLE StudentAbsence ADD CONSTRAINT StudentAbsence_Meeting
    FOREIGN KEY (MeetingID)
    REFERENCES Meeting (MeetingID);

-- Reference: StudentAbsence_Students (table: StudentAbsence)
ALTER TABLE StudentAbsence ADD CONSTRAINT StudentAbsence_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: SubjectGrades_Students (table: SubjectGrades)
ALTER TABLE SubjectGrades ADD CONSTRAINT SubjectGrades_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: SubjectGrades_Subjects (table: SubjectGrades)
ALTER TABLE SubjectGrades ADD CONSTRAINT SubjectGrades_Subjects
    FOREIGN KEY (SubjectID)
    REFERENCES Subjects (SubjectID);

-- Reference: Subjects_Employees (table: Subjects)
ALTER TABLE Subjects ADD CONSTRAINT Subjects_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: Subjects_FieldOfStudy (table: Subjects)
ALTER TABLE Subjects ADD CONSTRAINT Subjects_FieldOfStudy
    FOREIGN KEY (FieldOfStudyID)
    REFERENCES FieldOfStudy (FieldOfStudyID);

-- Reference: WebinarExpirationDate_Students (table: WebinarExpirationDate)
ALTER TABLE WebinarExpirationDate ADD CONSTRAINT WebinarExpirationDate_Students
    FOREIGN KEY (StudentID)
    REFERENCES Students (StudentID);

-- Reference: WebinarExpirationDate_Webinar (table: WebinarExpirationDate)
ALTER TABLE WebinarExpirationDate ADD CONSTRAINT WebinarExpirationDate_Webinar
    FOREIGN KEY (WebinarID)
    REFERENCES Webinar (WebinarID);

-- Reference: Webinar_AvailableLanguages (table: Webinar)
ALTER TABLE Webinar ADD CONSTRAINT Webinar_AvailableLanguages
    FOREIGN KEY (LanguageID)
    REFERENCES AvailableLanguages (LanguageID);

-- Reference: Webinar_Employees (table: Webinar)
ALTER TABLE Webinar ADD CONSTRAINT Webinar_Employees
    FOREIGN KEY (EmployeeID)
    REFERENCES Employees (EmployeeID);

-- Reference: Webinar_Translator (table: Webinar)
ALTER TABLE Webinar ADD CONSTRAINT Webinar_Translator
    FOREIGN KEY (TranslatorID)
    REFERENCES Translator (TranslatorID);

```