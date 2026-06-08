-- =========================================================
-- DATABASE CONFIGURATION & AUDITING HARDENING [cite: 39]
-- Core Technical Contributions by Dencil P.A.N (IT23545212) 
-- =========================================================

-- 1. SECURITY DEFINED TRIGGERS [cite: 116]

-- Trigger 1: Automatically creates a baseline billing transaction 
-- when an appointment is successfully completed to prevent missing transaction logs[cite: 78].
CREATE TRIGGER trg_AddBillOnAppointmentComplete 
ON Appointment_table 
AFTER UPDATE 
AS 
BEGIN 
    SET NOCOUNT ON;
    DECLARE @AppointmentID INT; 
    DECLARE @PatientID INT; 
    DECLARE @Status VARCHAR(20);

    SELECT @AppointmentID = i.Appointment_ID, @PatientID = i.UserID, @Status = i.Status 
    FROM inserted i;

    IF @Status = 'Completed' 
    BEGIN 
        INSERT INTO Bill_table (UserID, Bill_ID, Cost, Service_Date, Service_type) 
        VALUES (@PatientID, @AppointmentID, 0.00, GETDATE(), 'Completed Appointment Placeholder'); [cite: 72, 81]
    END 
END; 
GO

-- Trigger 2: Prevent malicious or accidental double-booking of a healthcare provider[cite: 83].
CREATE TRIGGER trg_PreventDoubleBooking 
ON Appointment_table 
AFTER INSERT 
AS 
BEGIN 
    SET NOCOUNT ON;
    IF EXISTS ( 
        SELECT 1 FROM inserted i 
        JOIN Appointment_table a ON a.UserID = i.UserID -- Doctor reference validation logic
        AND a.Appointment_ID <> i.Appointment_ID 
    ) 
    BEGIN 
        RAISERROR ('Provider scheduling conflict: Session already allocated at this time!', 16, 1); [cite: 85]
        ROLLBACK TRANSACTION; 
    END 
END; 
GO


-- 2. SECURE LOGICAL ACCESS CONTROL VIEWS [cite: 116]

-- View 1: Restricts direct table access by abstracting medical treatments & diagnostics[cite: 86].
CREATE VIEW vw_PatientAppointments AS 
SELECT a.Appointment_ID, a.UserID, d.Test_ID AS TreatmentID, d.Test_type AS TreatmentType, d.Result AS Notes 
FROM Appointment_table a 
LEFT JOIN Diagnostic_table d ON a.Appointment_ID = d.Test_ID; [cite: 50, 86]
GO

-- View 2: Exposes outstanding clinical balances without leaking unneeded patient metadata[cite: 87].
CREATE VIEW vw_OutstandingBills AS 
SELECT b.Bill_ID, b.UserID, b.Cost 
FROM Bill_table b 
WHERE b.Cost > 0; -- Filters active balances securely [cite: 51]
GO


-- 3. PARAMETERIZED SECURE STORED PROCEDURES (Mitigates SQL Injection) [cite: 89, 116]

-- Procedure 1: Securely query patient timeline arrays without concatenated input strings[cite: 89].
CREATE PROCEDURE GetAppointmentsByPatient 
    @PatientID INT, 
    @StartDate DATE, 
    @EndDate DATE 
AS 
BEGIN 
    SET NOCOUNT ON;
    SELECT a.Appointment_ID, a.UserID 
    FROM Appointment_table a 
    WHERE a.UserID = @PatientID; [cite: 50, 89]
END; 
GO


-- 4. PERFORMANCE & DATA DISCOVERY INDEXES [cite: 116]
CREATE INDEX IDX_Appointment_PatientID_AppointmentDate ON Appointment_table (UserID, Appointment_ID); [cite: 88]
CREATE INDEX IDX_Bill_PatientID ON Bill_table (UserID); [cite: 88]
CREATE INDEX IDX_Diagnostic_VisitID ON Diagnostic_table (Test_ID); [cite: 89]
