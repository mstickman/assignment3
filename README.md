Purpose:
  Library Management API allows managing books, categories, and different types of books (ebooks and printed books) through REST-like CRUD operations in the console.
  The project demonstrates object-oriented programming principles, JDBC database operations, and exception handling.

Entities and Relationships:
  BookBase (abstract)
    Subclasses: Ebook, PrintedBook
  Category
    BookBase contains Category (composition)
  Ebook / PrintedBook inherit from BookBase
  Database tables: category, books, ebooks, printed_books

OOP Design Documentation

Abstract Class:
  BookBase
    Fields: id, name, author, price, category
    Abstract methods: getEntityType(), validate()
    Concrete method: getPrice()
    Getters and setters for encapsulation

Subclasses:
  Ebook
    Field: fileSizeMB
    Implements validate() and getEntityType()

  PrintedBook
    Field: pages
    Implements validate() and getEntityType()

Interfaces:
  Validatable
    Method: validate()
    Ensures correct business rules

  PricedItem
    Method: getPrice()
    Returns the price of the book

Composition / Aggregation:
  BookBase contains Category

Polymorphism:
  BookBase book = new Ebook(...);
  System.out.println(book.getEntityType());

Database Description

Schema:

CREATE TABLE category (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) UNIQUE NOT NULL
);

CREATE TABLE books (
    id SERIAL PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    author VARCHAR(200) NOT NULL,
    price NUMERIC(8,2) NOT NULL CHECK (price > 0),
    category_id INT NOT NULL,
    book_type VARCHAR(20) NOT NULL,
    CONSTRAINT fk_category FOREIGN KEY (category_id) REFERENCES category(id)
);

CREATE TABLE ebooks (
    book_id INT PRIMARY KEY,
    file_size_mb NUMERIC(6,2) NOT NULL CHECK (file_size_mb > 0),
    CONSTRAINT fk_ebook_book FOREIGN KEY (book_id) REFERENCES books(id) ON DELETE CASCADE
);

CREATE TABLE printed_books (
    book_id INT PRIMARY KEY,
    pages INT NOT NULL CHECK (pages > 0),
    CONSTRAINT fk_printed_book FOREIGN KEY (book_id) REFERENCES books(id) ON DELETE CASCADE
);

Sample Inserts:
INSERT INTO category (name) VALUES ('Fiction');
INSERT INTO books (name, author, price, category_id, book_type) VALUES ('Breaking Bad', 'Mister White', 15.00, 1, 'EBOOK');
INSERT INTO ebooks (book_id, file_size_mb) VALUES (1, 5.2);

Controller / Main Demo

Create categories and books:
  Category fiction = categoryService.addCategory(new Category(0, "Fiction"));
  BookBase ebook = new Ebook(0, "Breaking Bad", "Mister White", fiction, 15.0, 5.2);
  BookBase printed = new PrintedBook(0, "1984", "George Orwell", fiction, 12.5, 328);

  service.addBook(ebook);
  service.addBook(printed);

CRUD operations:
  service.getBookById(1);
  service.removeBook(2);
  service.showAll();

Validation example:
  BookBase invalid = new PrintedBook(0, "", "", fiction, -5, 0);
  service.addBook(invalid); // throws InvalidInputException

Instructions to Compile and Run

Compile:
  javac -d out src/**/*.java

Run:
  java -cp out model.Main

Screenshots

<img width="1406" height="366" alt="db" src="https://github.com/user-attachments/assets/7120fcb6-7607-4a81-8564-13d9c2866121" />
<img width="420" height="363" alt="image" src="https://github.com/user-attachments/assets/64d4cb99-4e68-4424-9a52-25fee492c83d" />


What i learned:
Working with JDBC and transactions
Creating multi-layer architecture: Controller → Service → Repository
Applying OOP: abstraction, inheritance, interfaces, composition
Using polymorphism and interfaces for flexible code
