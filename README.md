### **Library Management Database System: Design and Implementation Report**

---

#### **1. Objective**

The objective of this database design was to create a simple, functional library management system in MongoDB. The system enables tracking of library members (users), books in the library, librarians who manage the library operations, and records of books borrowed by users. This relational-style NoSQL database design ensures an efficient way to manage and query essential library data.

---

#### **2. Database Design Overview**

The database is organized into four main collections:

- **Users**: Stores information about library members.

- **Books**: Holds details about each book in the library.

- **Librarians**: Contains data on library staff responsible for managing the books.

- **BorrowedBooks**: Tracks book borrowing transactions, linking users, books, and librarians.

MongoDB's document-oriented NoSQL structure does not enforce traditional foreign key constraints, so references are established using ObjectIds. This allows for relational-like data retrieval while maintaining MongoDB's flexibility.

---

#### **3. Collection Design**

Each collection was created with a specific purpose, ensuring efficient storage and retrieval of information.

---

**3.1 Users Collection**

This collection stores data about library members, including their names, email addresses, contact information, and membership dates.

- **Fields**:

  - `name`: Name of the user (String).

  - `email`: User's email address (String).

  - `phone`: User's phone number (String).

  - `membership_date`: Date the user joined the library (Date).

**Sample Data**:

```json

{

    "name": "Alice Johnson",

    "email": "alice@example.com",

    "phone": "123-456-7890",

    "membership_date": ISODate("2023-01-15")

}

```

---

**3.2 Books Collection**

This collection holds information about each book in the library, including the title, author, publisher, year of publication, and availability status.

- **Fields**:

  - `title`: Title of the book (String).

  - `author`: Author of the book (String).

  - `publisher`: Publisher of the book (String).

  - `year_published`: Year the book was published (Number).

  - `status`: Availability status of the book (String, values: "Available" or "Borrowed").

**Sample Data**:

```json

{

    "title": "To Kill a Mockingbird",

    "author": "Harper Lee",

    "publisher": "J.B. Lippincott & Co.",

    "year_published": 1960,

    "status": "Available"

}

```

---

**3.3 Librarians Collection**

This collection stores information about librarians who manage the library's operations.

- **Fields**:

  - `name`: Name of the librarian (String).

  - `email`: Librarian's email address (String).

  - `phone`: Librarian's phone number (String).

  - `hire_date`: Date the librarian was hired (Date).

**Sample Data**:

```json

{

    "name": "John Doe",

    "email": "john.doe@example.com",

    "phone": "555-1234",

    "hire_date": ISODate("2022-08-20")

}

```

---

**3.4 BorrowedBooks Collection**

This collection tracks records of books borrowed by users, linking **Users**, **Books**, and **Librarians** collections through references to ObjectIds.

- **Fields**:

  - `user_id`: Reference to the User (ObjectId).

  - `book_id`: Reference to the Book (ObjectId).

  - `librarian_id`: Reference to the Librarian who managed the transaction (ObjectId).

  - `borrow_date`: Date the book was borrowed (Date).

  - `return_date`: Date the book was returned (Date, can be null if not yet returned).

**Sample Data**:

```json

{

    "user_id": ObjectId("USER_ID"),

    "book_id": ObjectId("BOOK_ID"),

    "librarian_id": ObjectId("LIBRARIAN_ID"),

    "borrow_date": ISODate("2023-03-01"),

    "return_date": ISODate("2023-03-10")

}

```

---

### **4. Implementation Steps**

#### Step 1: Insert Documents into `Users`, `Books`, and `Librarians` Collections

Initial data was inserted into the **Users**, **Books**, and **Librarians** collections using MongoDB shell commands. MongoDB generated unique `_id` values for each document, allowing us to use these as references in other collections.

Example:

```js

db.Users.insertMany([{ "name": "Alice Johnson", "email": "alice@example.com", ... }]);

```

#### Step 2: Retrieve ObjectId References for `BorrowedBooks` Collection

To connect documents across collections, specific documents' `_id` values were retrieved. These values serve as references in the `BorrowedBooks` collection to create relational links between users, books, and librarians.

Example:

```js

const aliceId = db.Users.findOne({ "email": "alice@example.com" })._id;

const mockingbirdId = db.Books.findOne({ "title": "To Kill a Mockingbird" })._id;

const johnDoeId = db.Librarians.findOne({ "email": "john.doe@example.com" })._id;

```

#### Step 3: Insert Document into `BorrowedBooks` with References

The `_id` values retrieved in Step 2 were used to insert a borrowing record into `BorrowedBooks`, linking user, book, and librarian data.

Example:

```js

db.BorrowedBooks.insertOne({

    "user_id": aliceId,

    "book_id": mockingbirdId,

    "librarian_id": johnDoeId,

    "borrow_date": ISODate("2023-03-01"),

    "return_date": ISODate("2023-03-10")

});

```

---

### **5. Querying the Data**

Using MongoDB queries, various operations can be performed on the database to retrieve information.

- **Find all books borrowed by a specific user**:

  ```js

  const aliceId = db.Users.findOne({ "email": "alice@example.com" })._id;

  db.BorrowedBooks.find({ "user_id": aliceId });

  ```

- **List all available books**:

  ```js

  db.Books.find({ "status": "Available" });

  ```

- **Find all transactions managed by a specific librarian**:

  ```js

  const johnDoeId = db.Librarians.findOne({ "email": "john.doe@example.com" })._id;

  db.BorrowedBooks.find({ "librarian_id": johnDoeId });

  ```

- **Update the status of a returned book to "Available"**:

  ```js

  const mockingbirdId = db.Books.findOne({ "title": "To Kill a Mockingbird" })._id;

  db.Books.updateOne({ "_id": mockingbirdId }, { $set: { "status": "Available" } });

  ```
  
