# Learning SQLite3 in Python with a CRUD and JOIN Project

## Project Overview
We'll create a system to manage books and authors with these features:
- Add, view, update, and delete books
- Add, view, update, and delete authors
- View books with their author information (using JOIN)
- Search functionality

## Complete Implementation

```python
import sqlite3
from sqlite3 import Error

def create_connection(db_file):
    """ Create a database connection to a SQLite database """
    conn = None
    try:
        conn = sqlite3.connect(db_file)
        print(f"Connected to {db_file}, SQLite version: {sqlite3.version}")
        return conn
    except Error as e:
        print(e)
    
    return conn

def create_tables(conn):
    """ Create tables for books and authors """
    try:
        sql_create_authors_table = """CREATE TABLE IF NOT EXISTS authors (
                                        author_id INTEGER PRIMARY KEY AUTOINCREMENT,
                                        name TEXT NOT NULL,
                                        email TEXT
                                    );"""
        
        sql_create_books_table = """CREATE TABLE IF NOT EXISTS books (
                                    book_id INTEGER PRIMARY KEY AUTOINCREMENT,
                                    title TEXT NOT NULL,
                                    author_id INTEGER NOT NULL,
                                    published_year INTEGER,
                                    isbn TEXT UNIQUE,
                                    FOREIGN KEY (author_id) REFERENCES authors (author_id)
                                );"""
        
        cursor = conn.cursor()
        cursor.execute(sql_create_authors_table)
        cursor.execute(sql_create_books_table)
        conn.commit()
        print("Tables created successfully")
    except Error as e:
        print(e)

# CRUD Operations for Authors
def create_author(conn, author):
    """ Create a new author """
    sql = ''' INSERT INTO authors(name, email)
              VALUES(?,?) '''
    cursor = conn.cursor()
    cursor.execute(sql, author)
    conn.commit()
    return cursor.lastrowid

def get_all_authors(conn):
    """ Get all authors """
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM authors")
    return cursor.fetchall()

def update_author(conn, author_id, name, email):
    """ Update author information """
    sql = ''' UPDATE authors
              SET name = ?,
                  email = ?
              WHERE author_id = ?'''
    cursor = conn.cursor()
    cursor.execute(sql, (name, email, author_id))
    conn.commit()

def delete_author(conn, author_id):
    """ Delete an author """
    sql = 'DELETE FROM authors WHERE author_id=?'
    cursor = conn.cursor()
    cursor.execute(sql, (author_id,))
    conn.commit()

# CRUD Operations for Books
def create_book(conn, book):
    """ Create a new book """
    sql = ''' INSERT INTO books(title, author_id, published_year, isbn)
              VALUES(?,?,?,?) '''
    cursor = conn.cursor()
    cursor.execute(sql, book)
    conn.commit()
    return cursor.lastrowid

def get_all_books(conn):
    """ Get all books with author information (using JOIN) """
    sql = ''' SELECT b.book_id, b.title, a.name as author, b.published_year, b.isbn
              FROM books b
              JOIN authors a ON b.author_id = a.author_id '''
    cursor = conn.cursor()
    cursor.execute(sql)
    return cursor.fetchall()

def update_book(conn, book_id, title, author_id, published_year, isbn):
    """ Update book information """
    sql = ''' UPDATE books
              SET title = ?,
                  author_id = ?,
                  published_year = ?,
                  isbn = ?
              WHERE book_id = ?'''
    cursor = conn.cursor()
    cursor.execute(sql, (title, author_id, published_year, isbn, book_id))
    conn.commit()

def delete_book(conn, book_id):
    """ Delete a book """
    sql = 'DELETE FROM books WHERE book_id=?'
    cursor = conn.cursor()
    cursor.execute(sql, (book_id,))
    conn.commit()

def search_books(conn, search_term):
    """ Search books by title or author name """
    sql = ''' SELECT b.book_id, b.title, a.name as author, b.published_year, b.isbn
              FROM books b
              JOIN authors a ON b.author_id = a.author_id
              WHERE b.title LIKE ? OR a.name LIKE ? '''
    cursor = conn.cursor()
    cursor.execute(sql, (f'%{search_term}%', f'%{search_term}%'))
    return cursor.fetchall()

# User Interface Functions
def display_menu():
    print("\nLibrary Management System")
    print("1. Add Author")
    print("2. View All Authors")
    print("3. Update Author")
    print("4. Delete Author")
    print("5. Add Book")
    print("6. View All Books")
    print("7. Update Book")
    print("8. Delete Book")
    print("9. Search Books")
    print("0. Exit")

def main():
    database = "library.db"
    
    # Create database connection
    conn = create_connection(database)
    if conn is not None:
        create_tables(conn)
    else:
        print("Error! Cannot create the database connection.")
        return
    
    while True:
        display_menu()
        choice = input("Enter your choice: ")
        
        if choice == '1':  # Add Author
            name = input("Enter author name: ")
            email = input("Enter author email: ")
            create_author(conn, (name, email))
            print("Author added successfully!")
            
        elif choice == '2':  # View All Authors
            authors = get_all_authors(conn)
            print("\nList of Authors:")
            for author in authors:
                print(f"ID: {author[0]}, Name: {author[1]}, Email: {author[2]}")
                
        elif choice == '3':  # Update Author
            author_id = input("Enter author ID to update: ")
            name = input("Enter new name: ")
            email = input("Enter new email: ")
            update_author(conn, author_id, name, email)
            print("Author updated successfully!")
            
        elif choice == '4':  # Delete Author
            author_id = input("Enter author ID to delete: ")
            delete_author(conn, author_id)
            print("Author deleted successfully!")
            
        elif choice == '5':  # Add Book
            title = input("Enter book title: ")
            author_id = input("Enter author ID: ")
            year = input("Enter publication year: ")
            isbn = input("Enter ISBN: ")
            create_book(conn, (title, author_id, year, isbn))
            print("Book added successfully!")
            
        elif choice == '6':  # View All Books
            books = get_all_books(conn)
            print("\nList of Books:")
            for book in books:
                print(f"ID: {book[0]}, Title: {book[1]}, Author: {book[2]}, Year: {book[3]}, ISBN: {book[4]}")
                
        elif choice == '7':  # Update Book
            book_id = input("Enter book ID to update: ")
            title = input("Enter new title: ")
            author_id = input("Enter new author ID: ")
            year = input("Enter new publication year: ")
            isbn = input("Enter new ISBN: ")
            update_book(conn, book_id, title, author_id, year, isbn)
            print("Book updated successfully!")
            
        elif choice == '8':  # Delete Book
            book_id = input("Enter book ID to delete: ")
            delete_book(conn, book_id)
            print("Book deleted successfully!")
            
        elif choice == '9':  # Search Books
            search_term = input("Enter search term (title or author): ")
            books = search_books(conn, search_term)
            print("\nSearch Results:")
            for book in books:
                print(f"ID: {book[0]}, Title: {book[1]}, Author: {book[2]}, Year: {book[3]}, ISBN: {book[4]}")
                
        elif choice == '0':  # Exit
            print("Goodbye!")
            break
            
        else:
            print("Invalid choice. Please try again.")
    
    conn.close()

if __name__ == '__main__':
    main()
```

## Key Concepts Covered

1. **Database Connection**: Using `sqlite3.connect()` to create/connect to a database
2. **Table Creation**: Creating tables with foreign key relationships
3. **CRUD Operations**:
   - **Create**: `INSERT` statements to add records
   - **Read**: `SELECT` statements to retrieve data
   - **Update**: `UPDATE` statements to modify records
   - **Delete**: `DELETE` statements to remove records
4. **JOIN Operation**: Combining data from multiple tables in a single query
5. **Parameterized Queries**: Using placeholders (`?`) to prevent SQL injection
6. **Transaction Management**: Using `commit()` to save changes

## How to Use This Project

1. Save the code to a file (e.g., `library_system.py`)
2. Run the script with Python: `python library_system.py`
3. Follow the menu prompts to interact with the system
4. A database file named `library.db` will be created in the same directory

## Extending the Project

To further your learning, consider adding:
- More tables (e.g., for book categories or publishers)
- Additional JOIN operations (e.g., books with categories)
- Data validation
- Error handling for database operations
- A graphical user interface (GUI) using Tkinter or PyQt

This project gives you hands-on experience with all the fundamental SQLite3 operations in Python while building a practical application.
