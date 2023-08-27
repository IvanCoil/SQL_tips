# SQL_tips
SQl Tips

Primjer kreiranja Pogleda

---- kreiranje pogleda koji pokazuje knjige koje su slobodne za posudbu --

CREATE VIEW SlobodneKnjige 
AS
SELECT DISTINCT naslov AS naziv_knjige
FROM Knjiga k
LEFT OUTER JOIN Posudba p ON ISBN = knjiga_isbn
WHERE dat_povratka <= GETDATE() or dat_posudbe is null


---- Primjer kreiranje okidača koji sprječava brisanje podataka iz tablice Posudba

CREATE TRIGGER TR_PreventDeletePosudbeKnjiga
ON Posudba
INSTEAD OF DELETE
AS
BEGIN
    RAISERROR ('Brisanje podataka iz tablice Posudba nije dopušteno.', 16, 1);
    ROLLBACK TRANSACTION;
END;

---- Kreiranje pohranjene procedure koja kreira tablicu NEAKTIVNI i u nju kopira sve članove koji nisu nikada posudili nijednu knjigu, zatim iz tablice Članovi te iste članove briše

CREATE PROCEDURE ObrisiNeaktivne AS
BEGIN
	SELECT Clan.*
	INTO Neaktivni
	FROM Clan
	LEFT JOIN Posudba ON clanski_broj = Clan_ID
	WHERE ID_posudbe IS NULL;
END

BEGIN
DELETE C
FROM Clan C
LEFT JOIN Posudba  ON Clan_ID = clanski_broj
	WHERE ID_posudbe IS NULL
END

EXEC ObrisiNeaktivne
