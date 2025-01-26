# Sqlite3 Database Conventions

This document outlines the conventions used for database design and management within the project. Adhering to these conventions ensures consistency, maintainability, and clarity across the database schema and related operations. These guidelines are crucial for facilitating collaboration among developers and ensuring the database can scale and adapt to future requirements.

## General Conventions

These conventions apply to all database systems used within the project, ensuring a unified approach to database design and management.

- **Naming Conventions**:
    - Use `snake_case` for table and column names to maintain consistency and readability.
    - Table names should be plural (e.g., `users`, `orders`) to reflect the collection of records they contain.
    - Primary key columns should be named `id` to standardize identification across tables.

- **Schema Design**:
    - Normalize data to reduce redundancy and improve data integrity. Use denormalization judiciously for performance optimization.
    - Use consistent naming conventions for schemas, tables, and columns to avoid confusion and ensure clarity.

- **Data Types**:
    - Be aware of SQLite3's type affinity system and ensure that data is stored consistently to avoid unexpected behavior.
    - Choose data types that best fit the data being stored, considering both current and future needs.
    - Use appropriate data types for each column to optimize storage and performance, considering the specific needs of the application.
    - Prefer `INTEGER` for numeric identifiers and `TEXT` for strings, but consider using `VARCHAR` with a defined length for strings when appropriate.

- **Indexes**:
    - Regularly review and optimize indexes to ensure they are providing the expected performance benefits without unnecessary overhead.
    - Create indexes on columns that are frequently used in `WHERE` clauses or as join keys to improve query performance.
    - Name indexes with the pattern `idx_<table>_<column>` to clearly indicate their purpose and scope.

- **Foreign Keys**:
    - Document relationships between tables clearly in the schema to aid understanding and maintenance.
    - Use foreign keys to enforce referential integrity between tables, ensuring that relationships between records are maintained.
    - Name foreign key columns with the pattern `<related_table>_id` to clearly indicate their relationship to other tables.

- **Constraints**:
    - Regularly review constraints to ensure they are still relevant and effective as the database evolves.
    - Define constraints to enforce data integrity, such as `NOT NULL`, `UNIQUE`, and `CHECK`, to prevent invalid data entry and maintain consistency.

- **Version Control**:
    - Use a consistent naming convention for migration files, such as `YYYYMMDDHHMMSS_description.sql`, to ensure they are applied in the correct order.
    - Use migration files to track changes to the database schema, allowing for easy rollback and forward migration.
    - Each migration file should have a unique timestamp and a descriptive name to clearly indicate its purpose and order.

## SQLite3 Specific Conventions

SQLite3 is chosen for its simplicity and ease of use, making it ideal for development and small-scale applications. The following conventions are tailored to maximize its benefits.

SQLite3 is a lightweight, file-based database system that is well-suited for small to medium-sized applications. Here are some conventions and best practices specific to using SQLite3:

- **File Management**:
    - Ensure the database file is included in regular backup routines to prevent data loss.
    - Store the SQLite database file in a secure and accessible location.
    - Use a `.db` extension for the database file to clearly indicate its format.

- **Data Types**:
    - SQLite3 uses dynamic typing, so be mindful of data types and ensure consistency in how data is stored and retrieved.
    - Use `INTEGER PRIMARY KEY` for auto-incrementing primary keys, which is optimized in SQLite3.

- **Date and Time Handling**:
    - SQLite3 does not have a dedicated `DATE` or `TIME` data type. Instead, it stores date and time values as `TEXT`, `REAL`, or `INTEGER`.
    - Use the `TEXT` format (ISO 8601 strings) for storing date and time, e.g., `YYYY-MM-DD HH:MM:SS.SSS`.
    - Alternatively, use `REAL` for Julian day numbers or `INTEGER` for Unix timestamps (seconds since 1970-01-01 00:00:00 UTC).
    - Use SQLite3's built-in date and time functions, such as `date()`, `time()`, `datetime()`, `julianday()`, and `strftime()`, to manipulate and format date and time values.
    - Be consistent in the format you choose to store date and time values to avoid confusion and ensure compatibility with application logic.

- **Performance**:
    - Analyze query performance regularly and adjust indexes and queries as needed to maintain optimal performance.
    - Regularly run the `VACUUM` command to reclaim unused space and optimize the database file.
    - Use `PRAGMA` statements to configure database settings, such as `PRAGMA foreign_keys = ON;` to enforce foreign key constraints.
    - Configure the database to use `Write-Ahead Logging` for improved performance by performing `PRAGMA journal_mode=WAL;`.

- **Concurrency, Transaction, and Error Handling**:
    - Implement robust error handling and logging mechanisms to quickly identify and resolve issues.
    - SQLite3 supports concurrent reads but serializes writes. Design your application to minimize write contention.
    - Use transactions to batch multiple write operations, reducing the overhead of locking.
    - Transactions in SQLite3 are atomic, consistent, isolated, and durable (ACID). This ensures that all operations within a transaction are completed successfully before committing, or none are applied if an error occurs.
    - Begin a transaction with `BEGIN TRANSACTION;`, commit it with `COMMIT;`, and roll it back with `ROLLBACK;` if an error occurs.
    - Use `SAVEPOINT` and `RELEASE` to create nested transactions, allowing partial rollbacks within a larger transaction scope.
    - Handle errors gracefully by using try-catch blocks in your application code to catch exceptions and perform necessary cleanup or rollback operations.
    - Be aware of common SQLite3 errors such as `SQLITE_BUSY` (database is locked) and implement retry logic or backoff strategies to handle these scenarios.
    - Log errors for monitoring and debugging purposes, ensuring that any issues can be traced and resolved efficiently.

- **Backup and Recovery**:
    - Test backup and recovery procedures regularly to ensure they work as expected and data can be restored quickly in case of failure.
    - Regularly back up the database file, especially before performing major updates or migrations.
    - Use the `.backup` command or copy the database file when the application is not running to ensure a consistent backup.

- **Security**:
    - Regularly review and update security measures to protect against new vulnerabilities and threats.
    - Protect the database file with appropriate file system permissions to prevent unauthorized access.
    - Consider using encryption for sensitive data, either at the application level or by using an encrypted SQLite3 extension.

