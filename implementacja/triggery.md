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