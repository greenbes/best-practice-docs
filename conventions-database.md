# Database Conventions

This document outlines the conventions used for database design and management within the project. Adhering to these conventions ensures consistency, maintainability, and clarity across the database schema and related operations.

## General Conventions

- **Naming Conventions**:
    - Use `snake_case` for table and column names to maintain consistency and readability.
    - Table names should be plural (e.g., `users`, `orders`) to reflect the collection of records they contain.
    - Primary key columns should be named `id` to standardize identification across tables.

- **Data Types**:
    - Use appropriate data types for each column to optimize storage and performance, considering the specific needs of the application.
    - Prefer `INTEGER` for numeric identifiers and `TEXT` for strings, but consider using `VARCHAR` with a defined length for strings when appropriate.

- **Indexes**:
    - Create indexes on columns that are frequently used in `WHERE` clauses or as join keys to improve query performance.
    - Name indexes with the pattern `idx_<table>_<column>` to clearly indicate their purpose and scope.

- **Foreign Keys**:
    - Use foreign keys to enforce referential integrity between tables, ensuring that relationships between records are maintained.
    - Name foreign key columns with the pattern `<related_table>_id` to clearly indicate their relationship to other tables.

- **Constraints**:
    - Define constraints to enforce data integrity, such as `NOT NULL`, `UNIQUE`, and `CHECK`, to prevent invalid data entry and maintain consistency.

- **Version Control**:
    - Use migration files to track changes to the database schema, allowing for easy rollback and forward migration.
    - Each migration file should have a unique timestamp and a descriptive name to clearly indicate its purpose and order.

## SQLite3 Specific Conventions

SQLite3 is a lightweight, file-based database system that is well-suited for small to medium-sized applications. Here are some conventions and best practices specific to using SQLite3:

- **File Management**:
    - Store the SQLite database file in a secure and accessible location.
    - Use a `.db` extension for the database file to clearly indicate its format.

- **Data Types**:
    - SQLite3 uses dynamic typing, so be mindful of data types and ensure consistency in how data is stored and retrieved.
    - Use `INTEGER PRIMARY KEY` for auto-incrementing primary keys, which is optimized in SQLite3.

- **Performance**:
    - Regularly run the `VACUUM` command to reclaim unused space and optimize the database file.
    - Use `PRAGMA` statements to configure database settings, such as `PRAGMA foreign_keys = ON;` to enforce foreign key constraints.

- **Concurrency, Transaction, and Error Handling**:
    - SQLite3 supports concurrent reads but serializes writes. Design your application to minimize write contention.
    - Use transactions to batch multiple write operations, reducing the overhead of locking.
    - Transactions in SQLite3 are atomic, consistent, isolated, and durable (ACID). This ensures that all operations within a transaction are completed successfully before committing, or none are applied if an error occurs.
    - Begin a transaction with `BEGIN TRANSACTION;`, commit it with `COMMIT;`, and roll it back with `ROLLBACK;` if an error occurs.
    - Use `SAVEPOINT` and `RELEASE` to create nested transactions, allowing partial rollbacks within a larger transaction scope.
    - Handle errors gracefully by using try-catch blocks in your application code to catch exceptions and perform necessary cleanup or rollback operations.
    - Be aware of common SQLite3 errors such as `SQLITE_BUSY` (database is locked) and implement retry logic or backoff strategies to handle these scenarios.
    - Log errors for monitoring and debugging purposes, ensuring that any issues can be traced and resolved efficiently.

- **Backup and Recovery**:
    - Regularly back up the database file, especially before performing major updates or migrations.
    - Use the `.backup` command or copy the database file when the application is not running to ensure a consistent backup.

- **Security**:
    - Protect the database file with appropriate file system permissions to prevent unauthorized access.
    - Consider using encryption for sensitive data, either at the application level or by using an encrypted SQLite3 extension.

