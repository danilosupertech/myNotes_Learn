# 📚 Practical Lab: Importing and Exporting Data in MongoDB

## 🚀 Introduction
In this lab, you will learn how to **import data into MongoDB** from **JSON** and **CSV** files, verify the data, and export it.  
The focus is on using the command-line tool **`mongoimport`** and validating with the **MongoDB Shell (`mongosh`)**.

By the end, you will be able to:
- Import data from JSON and CSV files.
- Correctly handle data types.
- Query and validate imported documents.
- Export collections with `mongoexport`.

---

## 📥 Importing Data from a JSON File

### 1. Inspect the JSON File
File: `books.json`

```bash
cat ~/project/books.json
```

You will see a **JSON array**:

```json
[
  {
    "_id": 1,
    "title": "MongoDB Basics",
    "author": "Jane Smith",
    "year": 2023,
    "tags": ["mongodb", "database", "nosql"]
  },
  {
    "_id": 2,
    "title": "Python Programming",
    "author": "John Doe",
    "year": 2022,
    "tags": ["python", "programming"]
  },
  {
    "_id": 3,
    "title": "Data Science Handbook",
    "author": "Alice Johnson",
    "year": 2021,
    "tags": ["data science", "python", "machine learning"]
  }
]
```

---

### 2. Import JSON into MongoDB

```bash
mongoimport --db library_db --collection books --file ~/project/books.json --jsonArray
```

Explanation:
- `--db library_db` → target database.
- `--collection books` → target collection.
- `--file` → file path.
- `--jsonArray` → file contains a JSON array.

---

### 3. Verify Data

```bash
mongosh
use library_db
db.books.countDocuments()
db.books.findOne()
exit
```

✅ Confirms successful import.

---

## 📥 Importing Data from a CSV File

### 1. Inspect CSV
File: `library_members.csv`

```bash
cat ~/project/library_members.csv
```

Example content:

```csv
name,age,membership_status
John Doe,35,active
Jane Smith,28,active
Mike Johnson,42,expired
```

---

### 2. Import CSV

```bash
mongoimport --db library_db --collection members --type csv --file ~/project/library_members.csv --headerline
```

Explanation:
- `--type csv` → define CSV format.
- `--headerline` → use first line as field names.

---

### 3. Handling Data Types (CSV → Integer)

```bash
tail -n +2 ~/project/library_members.csv > ~/project/library_members_no_header.csv
mongoimport --db library_db --collection typed_members --type csv --file ~/project/library_members_no_header.csv --fields "name,age,membership_status"
mongosh library_db --eval "db.typed_members.updateMany({}, [{ $set: { age: { $toInt: \"$age\" } } }])"
```

Verify:

```javascript
db.typed_members.findOne()
typeof db.typed_members.findOne().age
db.typed_members.find({ age: { $gt: 30 } })
exit
```

---

## 🔎 Verification and Export

### List collections:

```javascript
show collections
```

### Quick queries:

```javascript
db.books.find({ tags: "python" })
db.typed_members.find({ membership_status: "active" })
```

### Export collection:

```bash
mongoexport --db library_db --collection books --out ~/project/exported_books.json
cat ~/project/exported_books.json
```

---

## ✅ Summary
You learned how to:
- Import **JSON** and **CSV** files.
- Handle **data types**.
- Query and validate data.
- Export collections using `mongoexport`.
