## Kod do generowania tabel z sekcji Orders
```sql

-- Table: Orders
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

-- Table: OrderCourse
CREATE TABLE OrderCourse (
    OrderDetailsID int NOT NULL,
    CourseID int NOT NULL,
    CONSTRAINT OrderCourse_pk PRIMARY KEY (OrderDetailsID)
);

-- Table: OrderDetails
CREATE TABLE OrderDetails (
    OrderDetailsID int NOT NULL,
    PaidDate datetime NULL,
    OrderID int NOT NULL,
    AccessGiven bit NOT NULL,
    CONSTRAINT OrderDetails_pk PRIMARY KEY (OrderDetailsID)
);

-- Table: OrderMeeting
CREATE TABLE OrderMeeting (
    OrderDetailsID int NOT NULL,
    MeetingID int NOT NULL,
    CONSTRAINT OrderMeeting_pk PRIMARY KEY (OrderDetailsID)
);

-- Table: OrderStudies
CREATE TABLE OrderStudies (
    OrderDetailsID int NOT NULL,
    FieldOfStudyID int NOT NULL,
    CONSTRAINT OrderStudies_pk PRIMARY KEY (OrderDetailsID)
);

-- Table: OrderWebinar
CREATE TABLE OrderWebinar (
    OrderDetailsID int NOT NULL,
    WebinarID int NOT NULL,
    CONSTRAINT OrderWebinar_pk PRIMARY KEY (OrderDetailsID)
);

-- Table: OrderStationaryWeek
CREATE TABLE OrderStationaryWeek (
    OrderDetailsID int NOT NULL,
    StationaryID int NOT NULL,
    CONSTRAINT OrderStationaryWeek_pk PRIMARY KEY (OrderDetailsID)
);

```