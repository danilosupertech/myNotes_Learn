# 📚 Complete Guide: Importing and Exporting Data in MongoDB

## 🚀 Introduction
This guide is a **quick reference and pocket tutorial** on how to import and export data in MongoDB. It combines **copy & paste scripts** with **detailed explanations** for each command, so anyone can learn and execute from scratch.

You will learn to:
- Create project directories and data files.
- Import **JSON** into one collection and **CSV** into another independent collection using `mongoimport`.
- Handle data types in MongoDB.
- Query and validate imported data.
- Export collections using `mongoexport`.

---

## 1️⃣ Prepare Environment

### 1.1 Create project directory
```bash
mkdir -p ~/project  # Creates the ~/project directory if it doesn't exist
cd ~/project          # Navigate into the created directory
```
**Explanation:**
- `mkdir -p` → creates directories, including parent directories if needed.
- `cd` → navigates to the created directory where data files will be stored.

---

## 2️⃣ Create Data Files

### 2.1 JSON file (`books.json`) - for the `books` collection
```bash
cat <<EOL > books.json
[
  {"_id": 1, "title": "MongoDB Basics", "author": "Jane Smith", "year": 2023, "tags": ["mongodb", "database", "nosql"]},
  {"_id": 2, "title": "Python Programming", "author": "John Doe", "year": 2022, "tags": ["python", "programming"]},
  {"_id": 3, "title": "Data Science Handbook", "author": "Alice Johnson", "year": 2021, "tags": ["data science", "python", "machine learning"]}
]
EOL
```
**Explanation:** This JSON file will be imported into the independent `books` collection.

### 2.2 CSV file (`library_members.csv`) - for the `members` collection
```bash
cat <<EOL > library_members.csv
name,age,membership_status
John Doe,35,active
Jane Smith,28,active
Mike Johnson,42,expired
EOL
```
**Explanation:** This CSV file will be imported into the independent `members` collection. The files are **independent** examples to show JSON vs CSV import.

---

## 3️⃣ Import JSON Data into `books` collection
```bash
mongoimport --db library_db --collection books --file ~/project/books.json --jsonArray
```
**Explanation:**
- `mongoimport` → CLI tool to import data into MongoDB.
- `--db library_db` → target database `library_db`.
- `--collection books` → target collection `books`.
- `--file ~/project/books.json` → path to input file.
- `--jsonArray` → indicates the file contains a **JSON array**, each element becomes a document.

### Verify JSON import
```bash
mongosh
use library_db
db.books.countDocuments()
db.books.findOne()
exit
```

---

## 4️⃣ Import CSV Data into `members` collection
```bash
mongoimport --db library_db --collection members --type csv --file ~/project/library_members.csv --headerline
```
**Explanation:**
- `--type csv` → indicates input file is CSV.
- `--headerline` → uses the first line as field names.

### Verify CSV import
```bash
mongosh
use library_db
db.members.countDocuments()
db.members.findOne()
exit
```

---

## 5️⃣ Manipulate Data Types for CSV

### 5.1 Remove header for manual field assignment
```bash
tail -n +2 ~/project/library_members.csv > ~/project/library_members_no_header.csv
```

### 5.2 Import CSV with explicit field names into `typed_members`
```bash
mongoimport --db library_db --collection typed_members --type csv --file ~/project/library_members_no_header.csv --fields "name,age,membership_status"
```

### 5.3 Convert `age` field from string to integer
```bash
mongosh library_db --eval "db.typed_members.updateMany({}, [{ $set: { age: { $toInt: \"$age\" } } }])"
```

### 5.4 Verify conversion
```javascript
db.typed_members.findOne()
typeof db.typed_members.findOne().age
db.typed_members.find({ age: { $gt: 30 } })
exit
```

---

## 6️⃣ Query and Validate Data

### List collections
```javascript
show collections
```
### Example queries
```javascript
db.books.find({ tags: "python" })
db.members.find({ membership_status: "active" })
db.typed_members.find({ age: { $gt: 30 } })
exit
```
- Each collection is independent and queries validate the data structure.

---

## 7️⃣ Export Data

### Export `books` collection to JSON
```bash
mongoexport --db library_db --collection books --out ~/project/exported_books.json
cat ~/project/exported_books.json
```
- `mongoexport` → exports a collection to a JSON file.
- `--out` → specifies the output file.
- `cat` → view exported content.

---

## ✅ Summary
- Created directories and independent data files.
- Imported **JSON** into `books` and **CSV** into `members` (and `typed_members`).
- Managed data types for numeric operations.
- Performed queries and validated data integrity.
- Exported collections to JSON.
- Detailed explanations make this both a **quick reference** and a **learning guide**.
