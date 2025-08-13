#Drop tables if they exist to avoid conflicts on rerun
DROP TABLE IF EXISTS Loans;
DROP TABLE IF EXISTS Borrowers;
DROP TABLE IF EXISTS Books;
DROP TABLE IF EXISTS Authors;

-- Create Authors table
CREATE TABLE Authors (
    AuthorID INT PRIMARY KEY,
    Name VARCHAR(100) NOT NULL,
    Country VARCHAR(50)
);

-- Create Books table
CREATE TABLE Books (
    BookID INT PRIMARY KEY,
    Title VARCHAR(150) NOT NULL,
    AuthorID INT,
    PublishedYear INT,
    CopiesAvailable INT DEFAULT 1,
    FOREIGN KEY (AuthorID) REFERENCES Authors(AuthorID)
);

-- Create Borrowers table
CREATE TABLE Borrowers (
    BorrowerID INT PRIMARY KEY,
    Name VARCHAR(100) NOT NULL,
    Email VARCHAR(100)
);

-- Create Loans table to track book borrowings
CREATE TABLE Loans (
    LoanID INT PRIMARY KEY,
    BookID INT,
    BorrowerID INT,
    LoanDate DATE,
    ReturnDate DATE,
    FOREIGN KEY (BookID) REFERENCES Books(BookID),
    FOREIGN KEY (BorrowerID) REFERENCES Borrowers(BorrowerID)
);

-- Insert sample authors
INSERT INTO Authors VALUES (1, 'J.K. Rowling', 'United Kingdom');
INSERT INTO Authors VALUES (2, 'George Orwell', 'United Kingdom');
INSERT INTO Authors VALUES (3, 'Agatha Christie', 'United Kingdom');
INSERT INTO Authors VALUES (4, 'Mark Twain', 'United States');

-- Insert sample books
INSERT INTO Books VALUES (1, 'Harry Potter and the Sorcerer''s Stone', 1, 1997, 3);
INSERT INTO Books VALUES (2, '1984', 2, 1949, 2);
INSERT INTO Books VALUES (3, 'Murder on the Orient Express', 3, 1934, 1);
INSERT INTO Books VALUES (4, 'Adventures of Huckleberry Finn', 4, 1884, 2);

-- Insert sample borrowers
INSERT INTO Borrowers VALUES (1, 'Alice Johnson', 'alice@example.com');
INSERT INTO Borrowers VALUES (2, 'Bob Smith', 'bob@example.com');
INSERT INTO Borrowers VALUES (3, 'Charlie Brown', 'charlie@example.com');

-- Insert sample loans
INSERT INTO Loans VALUES (1, 1, 1, '2025-08-01', NULL);
INSERT INTO Loans VALUES (2, 2, 2, '2025-07-15', '2025-07-25');
INSERT INTO Loans VALUES (3, 3, 3, '2025-07-20', NULL);

-- Queries you can run:

-- 1. List all available books with copies count
SELECT Title, CopiesAvailable FROM Books WHERE CopiesAvailable > 0;

-- 2. List all books currently loaned out and not yet returned
SELECT b.Title, br.Name AS Borrower, l.LoanDate
FROM Loans l
JOIN Books b ON l.BookID = b.BookID
JOIN Borrowers br ON l.BorrowerID = br.BorrowerID
WHERE l.ReturnDate IS NULL;

-- 3. List all authors with the number of books they have in the library
SELECT a.Name, COUNT(b.BookID) AS NumberOfBooks
FROM Authors a
LEFT JOIN Books b ON a.AuthorID = b.AuthorID
GROUP BY a.Name;

-- 4. Borrower borrowing history (all loans)
SELECT br.Name AS Borrower, b.Title, l.LoanDate, l.ReturnDate
FROM Loans l
JOIN Borrowers br ON l.BorrowerID = br.BorrowerID
JOIN Books b ON l.BookID = b.BookID
ORDER BY l.LoanDate DESC;

-- 5. Update book copies when a loan is made (example)
-- UPDATE Books SET CopiesAvailable = CopiesAvailable - 1 WHERE BookID = 1;

-- 6. Update book copies when a book is returned (example)
-- UPDATE Books SET CopiesAvailable = CopiesAvailable + 1 WHERE BookID = 1;
