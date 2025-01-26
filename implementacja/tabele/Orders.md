# Sekcji Orders

## Tabela: `Orders`
Przechowuje informacje o zamówieniach składanych przez studentów.

- `OrderID` *(int, NOT NULL)*: Unikalny identyfikator zamówienia.
- `Paid` *(money, NULL)*: Kwota zapłacona za zamówienie (opcjonalnie).
- `OrderDate` *(datetime, NOT NULL)*: Data złożenia zamówienia.
- `StudentID` *(int, NOT NULL)*: Unikalny identyfikator studenta, który złożył zamówienie (klucz obcy).
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

## Tabela: `OrderDetails`
Przechowuje szczegóły dotyczące zamówienia, takie jak data zapłaty i dostęp do kursów.

- `OrderDetailsID` *(int, NOT NULL)*: Unikalny identyfikator szczegółów zamówienia.
- `PaidDate` *(datetime, NULL)*: Data zapłaty za zamówienie (opcjonalnie).
- `OrderID` *(int, NOT NULL)*: Unikalny identyfikator zamówienia (klucz obcy).
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

## Tabela: `OrderCourse`
Przechowuje informacje o kursach przypisanych do zamówień.

- `OrderDetailsID` *(int, NOT NULL)*: Unikalny identyfikator szczegółów zamówienia.
- `CourseID` *(int, NOT NULL)*: Unikalny identyfikator kursu przypisanego do zamówienia (klucz obcy).
- **Klucze i ograniczenia:**
  - `OrderCourse_pk`: Klucz główny na kolumnie `OrderDetailsID`.

```sql
CREATE TABLE OrderCourse (
    OrderDetailsID int NOT NULL,
    CourseID int NOT NULL,
    CONSTRAINT OrderCourse_pk PRIMARY KEY (OrderDetailsID)
);
```

## Tabela: `OrderMeeting`
Przechowuje informacje o spotkaniach przypisanych do zamówień.

- `OrderDetailsID` *(int, NOT NULL)*: Unikalny identyfikator szczegółów zamówienia (klucz obcy).
- `MeetingID` *(int, NOT NULL)*: Unikalny identyfikator spotkania przypisanego do zamówienia (klucz obcy).
- **Klucze i ograniczenia:**
  - `OrderMeeting_pk`: Klucz główny na kolumnie `OrderDetailsID`.

```sql
CREATE TABLE OrderMeeting (
    OrderDetailsID int NOT NULL,
    MeetingID int NOT NULL,
    CONSTRAINT OrderMeeting_pk PRIMARY KEY (OrderDetailsID)
);
```

## Tabela: `OrderStudies`
Przechowuje informacje o kierunkach studiów przypisanych do zamówień.

- `OrderDetailsID` *(int, NOT NULL)*: Unikalny identyfikator szczegółów zamówienia (klucz obcy).
- `FieldOfStudyID` *(int, NOT NULL)*: Unikalny identyfikator kierunku studiów przypisanego do zamówienia (klucz obcy).
- **Klucze i ograniczenia:**
  - `OrderStudies_pk`: Klucz główny na kolumnie `OrderDetailsID`.

```sql
CREATE TABLE OrderStudies (
    OrderDetailsID int NOT NULL,
    FieldOfStudyID int NOT NULL,
    CONSTRAINT OrderStudies_pk PRIMARY KEY (OrderDetailsID)
);
```

## Tabela: `OrderWebinar`
Przechowuje informacje o webinarach przypisanych do zamówień.

- `OrderDetailsID` *(int, NOT NULL)*: Unikalny identyfikator szczegółów zamówienia (klucz obcy).
- `WebinarID` *(int, NOT NULL)*: Unikalny identyfikator webinaru przypisanego do zamówienia (klucz obcy).
- **Klucze i ograniczenia:**
  - `OrderWebinar_pk`: Klucz główny na kolumnie `OrderDetailsID`.

```sql
CREATE TABLE OrderWebinar (
    OrderDetailsID int NOT NULL,
    WebinarID int NOT NULL,
    CONSTRAINT OrderWebinar_pk PRIMARY KEY (OrderDetailsID)
);
```

## Tabela: `OrderStationaryWeek`
Przechowuje informacje o tygodniach stacjonarnych przypisanych do zamówień.

- `OrderDetailsID` *(int, NOT NULL)*: Unikalny identyfikator szczegółów zamówienia (klucz obcy).
- `StationaryID` *(int, NOT NULL)*: Unikalny identyfikator tygodnia stacjonarnego przypisanego do zamówienia (klucz obcy).
- **Klucze i ograniczenia:**
  - `OrderStationaryWeek_pk`: Klucz główny na kolumnie `OrderDetailsID`.

```sql
CREATE TABLE OrderStationaryWeek (
    OrderDetailsID int NOT NULL,
    StationaryID int NOT NULL,
    CONSTRAINT OrderStationaryWeek_pk PRIMARY KEY (OrderDetailsID)
);
```