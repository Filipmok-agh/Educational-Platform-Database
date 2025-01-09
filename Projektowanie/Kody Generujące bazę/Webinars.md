## Kod do generowania tabel z sekcji Webinars
```sql

-- Table: Webinar
CREATE TABLE Webinar (
    WebinarID int NOT NULL,
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

-- Table: WebinarExpirationDate
CREATE TABLE WebinarExpirationDate (
    WebinarID int NOT NULL,
    StudentID int NOT NULL,
    expr_date date NULL,
    CONSTRAINT WebinarExpirationDate_pk PRIMARY KEY (WebinarID, StudentID)
);

```