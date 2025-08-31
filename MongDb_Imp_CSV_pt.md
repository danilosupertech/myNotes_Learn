# 📚 Laboratório Prático: Importação e Exportação de Dados no MongoDB

## 🚀 Introdução
Neste laboratório, você aprenderá a **importar dados para um banco de dados MongoDB** a partir de arquivos **JSON** e **CSV**, além de verificar e exportar dados.  
O foco é a prática com a ferramenta de linha de comando **`mongoimport`** e a validação usando o **MongoDB Shell (`mongosh`)**.

Ao final, você dominará:
- Importar dados de arquivos JSON e CSV.
- Tratar corretamente tipos de dados.
- Consultar e verificar documentos importados.
- Exportar dados com `mongoexport`.

---

## 📥 Importar Dados de um Arquivo JSON

### 1. Inspecionar o Arquivo JSON
Arquivo: `books.json`

```bash
cat ~/project/books.json
```

Você verá uma **matriz de documentos JSON**:

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

### 2. Importar o JSON para o MongoDB

```bash
mongoimport --db library_db --collection books --file ~/project/books.json --jsonArray
```

Explicação:
- `--db library_db` → banco de dados de destino.
- `--collection books` → coleção de destino.
- `--file` → caminho do arquivo.
- `--jsonArray` → arquivo contém um array JSON.

---

### 3. Verificar os Dados

```bash
mongosh
use library_db
db.books.countDocuments()
db.books.findOne()
exit
```

✅ Confirma que os dados foram importados.

---

## 📥 Importar Dados de um Arquivo CSV

### 1. Inspecionar o CSV
Arquivo: `library_members.csv`

```bash
cat ~/project/library_members.csv
```

Exemplo de conteúdo:

```csv
name,age,membership_status
John Doe,35,active
Jane Smith,28,active
Mike Johnson,42,expired
```

---

### 2. Importar CSV

```bash
mongoimport --db library_db --collection members --type csv --file ~/project/library_members.csv --headerline
```

Explicação:
- `--type csv` → define formato CSV.
- `--headerline` → usa a primeira linha como nomes de campos.

---

### 3. Manipulando Tipos de Dados (CSV → Inteiro)

```bash
tail -n +2 ~/project/library_members.csv > ~/project/library_members_no_header.csv
mongoimport --db library_db --collection typed_members --type csv --file ~/project/library_members_no_header.csv --fields "name,age,membership_status"
mongosh library_db --eval "db.typed_members.updateMany({}, [{ $set: { age: { $toInt: \"$age\" } } }])"
```

Verifique:

```javascript
db.typed_members.findOne()
typeof db.typed_members.findOne().age
db.typed_members.find({ age: { $gt: 30 } })
exit
```

---

## 🔎 Verificação e Exportação

### Listar coleções:

```javascript
show collections
```

### Consultas rápidas:

```javascript
db.books.find({ tags: "python" })
db.typed_members.find({ membership_status: "active" })
```

### Exportar coleção:

```bash
mongoexport --db library_db --collection books --out ~/project/exported_books.json
cat ~/project/exported_books.json
```

---

## ✅ Resumo
Você aprendeu a:
- Importar **JSON** e **CSV**.
- Tratar **tipos de dados**.
- Consultar e validar dados.
- Exportar dados com `mongoexport`.
