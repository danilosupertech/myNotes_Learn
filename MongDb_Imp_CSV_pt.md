# 📚 Laboratório Completo: Importação e Exportação de Dados no MongoDB

## 🚀 Introdução
Neste laboratório, você aprenderá a **importar dados para o MongoDB** de arquivos **JSON** e **CSV**, verificá-los e exportá-los. Tudo será criado do zero, incluindo diretórios e arquivos, para que qualquer pessoa possa executar sem pré-requisitos.

Você vai praticar:
- Criar diretório e arquivos de dados.
- Importar JSON e CSV com `mongoimport`.
- Tratar tipos de dados.
- Consultar e validar dados.
- Exportar dados com `mongoexport`.

---

## 1️⃣ Preparar Estrutura de Projeto e Arquivos

### 1.1 Criar diretório do projeto

```bash
mkdir -p ~/project
cd ~/project
```

### 1.2 Criar arquivo JSON (`books.json`)

```bash
cat <<EOL > books.json
[
  {"_id": 1, "title": "MongoDB Basics", "author": "Jane Smith", "year": 2023, "tags": ["mongodb", "database", "nosql"]},
  {"_id": 2, "title": "Python Programming", "author": "John Doe", "year": 2022, "tags": ["python", "programming"]},
  {"_id": 3, "title": "Data Science Handbook", "author": "Alice Johnson", "year": 2021, "tags": ["data science", "python", "machine learning"]}
]
EOL
```

### 1.3 Criar arquivo CSV (`library_members.csv`)

```bash
cat <<EOL > library_members.csv
name,age,membership_status
John Doe,35,active
Jane Smith,28,active
Mike Johnson,42,expired
EOL
```

---

## 2️⃣ Importar Dados JSON

```bash
mongoimport --db library_db --collection books --file ~/project/books.json --jsonArray
```

### Verificar importação

```bash
mongosh
use library_db
db.books.countDocuments()
db.books.findOne()
exit
```

---

## 3️⃣ Importar Dados CSV

```bash
mongoimport --db library_db --collection members --type csv --file ~/project/library_members.csv --headerline
```

### Verificar importação CSV

```bash
mongosh
use library_db
db.members.countDocuments()
db.members.findOne()
exit
```

---

## 4️⃣ Manipular Tipos de Dados CSV

### 4.1 Criar CSV sem cabeçalho

```bash
tail -n +2 ~/project/library_members.csv > ~/project/library_members_no_header.csv
```

### 4.2 Importar e definir campos

```bash
mongoimport --db library_db --collection typed_members --type csv --file ~/project/library_members_no_header.csv --fields "name,age,membership_status"
```

### 4.3 Converter idade para inteiro

```bash
mongosh library_db --eval "db.typed_members.updateMany({}, [{ $set: { age: { $toInt: \"$age\" } } }])"
```

### 4.4 Verificar conversão

```javascript
db.typed_members.findOne()
typeof db.typed_members.findOne().age
db.typed_members.find({ age: { $gt: 30 } })
exit
```

---

## 5️⃣ Consultar e Exportar Dados

### 5.1 Listar coleções

```javascript
show collections
```

### 5.2 Consultas de teste

```javascript
db.books.find({ tags: "python" })
db.typed_members.find({ membership_status: "active" })
exit
```

### 5.3 Exportar coleção JSON

```bash
mongoexport --db library_db --collection books --out ~/project/exported_books.json
cat ~/project/exported_books.json
```

---

## ✅ Resumo
Você aprendeu a:
- Criar diretórios e arquivos de dados do zero.
- Importar **JSON** e **CSV**.
- Tratar tipos de dados no MongoDB.
- Validar dados importados.
- Exportar coleções com `mongoexport`.
