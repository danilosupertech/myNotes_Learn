# 🚀 MongoDB Tutorial: Update, Delete & Schema Validation

## 🎯 Introduction
Welcome to this comprehensive **MongoDB tutorial**! Here you'll learn essential database operations to efficiently manage and maintain your MongoDB data.

---

## 📚 What You'll Learn
- 🔄 Update single and multiple documents using **`updateOne()`** and **`updateMany()`**
- ⚙️ Use powerful update operators: **`$set`**, **`$inc`**, and **`$unset`**
- ✨ Use the **`$upsert`** option to create documents if they don't exist
- ❌ Delete single and multiple documents using **`deleteOne()`** and **`deleteMany()`**
- 🔍 Perform conditional deletions using query operators
- 🧹 Clean up entire collections with **`deleteMany({})`** and **`drop()`**
- 🛡️ Apply **schema validation** on collections with the **`collMod`** command

---

## 📋 Table of Contents
1. [Updating Documents](#1️⃣-updating-documents)  
2. [Deleting Documents](#2️⃣-deleting-documents)  
3. [Cleaning Collections](#3️⃣-cleaning-a-collection)  
4. [Schema Validation](#4️⃣-schema-validation-with-collmod)  
5. [Projection](#-projection-selecionando-campos-específicos)  
6. [Classificar, Pular e Limitar Resultados](#-classificar-pular-e-limitar-resultados)  
7. [Summary](#-summary)  
8. [Next Steps](#-next-steps)  

---

## 1️⃣ Updating Documents

### 1.1 Update a Single Document with `updateOne()`
```javascript
db.users.updateOne(
  { name: "John" },            // Filter to find the document
  { $set: { age: 30 } }        // Update operation
)
```

### 1.2 Update Multiple Documents with `updateMany()`
```javascript
db.users.updateMany(
  { status: "active" },
  { $inc: { loginCount: 1 } }  // Increment loginCount by 1
)
```

### 1.3 Common Update Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `$set`   | Sets or adds a field value | `{ $set: { age: 30 } }` |
| `$inc`   | Increments or decrements a value | `{ $inc: { score: 5 } }` |
| `$unset` | Removes a field from a document | `{ $unset: { temp: "" } }` |

### 1.4 Create Documents with `$upsert`
```javascript
db.users.updateOne(
  { name: "Alice" },
  { $set: { age: 25 } },
  { upsert: true }  // Creates document if it doesn't exist
)
```
> 📌 **Note:** Creates a new document if one with name `"Alice"` doesn't exist.

---

## 2️⃣ Deleting Documents

### 2.1 Delete a Single Document with `deleteOne()`
```javascript
db.users.deleteOne({ name: "John" })
```
> 📌 **Note:** Removes the first document matching the filter.

### 2.2 Delete Multiple Documents with `deleteMany()`
```javascript
db.users.deleteMany({ status: "inactive" })
```
> 📌 **Note:** Removes all documents with status `"inactive"`.

### 2.3 Conditional Deletes Using Query Operators
```javascript
db.employees.deleteMany({ age: { $lt: 25 } })  // Delete employees younger than 25
```
> 📌 **Note:** Deletes all employees younger than 25 years old.

---

## 3️⃣ Cleaning a Collection

### 3.1 Remove All Documents with `deleteMany({})`
```javascript
db.products.deleteMany({})  // Empty filter matches all documents
```

### 3.2 Drop Entire Collection with `drop()`
```javascript
db.products.drop()  // Completely removes the collection
```
> ⚠️ **Warning:** Completely removes the collection including indexes and metadata.

---

## 4️⃣ Schema Validation with collMod
Apply schema validation to existing collections using the **collMod** command.

**Example:** Enforce required `name` (string) and `age` (int >= 0)

```javascript
db.runCommand({
  collMod: "users",
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "age"],
      properties: {
        name: {
          bsonType: "string",
          description: "must be a string and is required"
        },
        age: {
          bsonType: "int",
          minimum: 0,
          description: "must be an integer >= 0 and is required"
        }
      }
    }
  },
  validationLevel: "moderate"
})
```

> 📌 **Note:** Blocks inserts or updates that violate the schema.

**Validation Levels:**
- `"strict"` - Validate all operations  
- `"moderate"` - Validate only existing valid documents  
- `"off"` - No validation  

---


## 📑 Projection: Selecionando Campos Específicos

No MongoDB, a **projeção** define quais campos de um documento devem ser retornados em uma consulta.  
Isso é útil para **otimizar a performance**, **reduzir a quantidade de dados retornados** e **mostrar apenas as informações relevantes**.

### 📌 Exemplo:
```javascript
db.books.find({}, { title: 1, author: 1, _id: 0 })
```

### 🔎 O que esse comando faz?
- `{}` → Filtro vazio, ou seja, busca **todos os documentos** da coleção `books`.  
- `{ title: 1, author: 1, _id: 0 }` → Projeção que define quais campos serão retornados:  
  - `title: 1` → Inclui o campo **title** no resultado  
  - `author: 1` → Inclui o campo **author** no resultado  
  - `_id: 0` → Exclui o campo **_id** (que vem por padrão nas consultas)  

### ✅ Resultado esperado:
```json
[
  { "title": "Clean Code", "author": "Robert C. Martin" },
  { "title": "Design Patterns", "author": "Erich Gamma" },
  { "title": "The Pragmatic Programmer", "author": "Andrew Hunt" }
]
```

> 📌 **Resumo:** A projeção permite **incluir (`1`) ou excluir (`0`) campos** no retorno de uma consulta.  
É possível combinar várias inclusões, mas a exclusão só pode ser feita junto do campo `_id`.



## 📊 Classificar, Pular e Limitar Resultados

Frequentemente, você precisa controlar a **ordem** e a **quantidade** dos resultados retornados por uma consulta.  
O MongoDB fornece métodos de **cursor** que podem ser encadeados em uma consulta `find()` para **classificar**, **pular** e **limitar** os documentos.

---

### 🔽 Classificar Resultados com `.sort()`

O método `.sort()` recebe um documento que especifica o campo a ser classificado e a direção:

- `1` → **Ordem crescente**  
- `-1` → **Ordem decrescente**  

📌 Exemplo: Listar os livros por **ano** em ordem decrescente (mais recentes primeiro):

```javascript
db.books.find().sort({ year: -1 })
```

✅ **Explicação:**  
- `find()` → busca todos os documentos da coleção `books`  
- `sort({ year: -1 })` → ordena os livros pelo campo `year` do mais recente para o mais antigo  

---

### 🔢 Limitar Resultados com `.limit()`

Use `.limit()` para restringir a quantidade de documentos retornados.

📌 Exemplo: Obter apenas os **2 livros mais recentes**:

```javascript
db.books.find().sort({ year: -1 }).limit(2)
```

✅ **Explicação:**  
- `sort({ year: -1 })` → ordena os livros por ano do mais recente ao mais antigo  
- `limit(2)` → retorna somente os **dois primeiros documentos** após a ordenação  

---

### ⏭️ Pular Resultados com `.skip()`

O método `.skip()` é usado para **ignorar** um número específico de documentos no início do conjunto de resultados.  
Isso é útil para implementar **paginação**.

📌 Exemplo: Recuperar todos os livros, mas pular o **primeiro**:

```javascript
db.books.find().sort({ year: 1 }).skip(1)
```

✅ **Explicação:**  
- `sort({ year: 1 })` → ordena os livros por ano em ordem crescente (mais antigos primeiro)  
- `skip(1)` → ignora o primeiro documento da lista ordenada  

---

### 🔗 Encadeando Métodos

Você pode encadear `.sort()`, `.skip()` e `.limit()` para consultas mais precisas.

📌 Exemplo: Encontrar o **segundo livro mais antigo**:

```javascript
db.books.find().sort({ year: 1 }).skip(1).limit(1)
```

✅ **Explicação:**  
- `sort({ year: 1 })` → ordena os livros do mais antigo para o mais recente  
- `skip(1)` → ignora o livro mais antigo  
- `limit(1)` → retorna apenas **um documento**, que será o **segundo mais antigo**  

---

### 🚪 Saindo do Mongo Shell

Para sair do shell interativo do MongoDB:

```bash
exit
```

ou pressione `Ctrl + D`.

✅ **Explicação:** Encerra a sessão atual do MongoDB shell.


## ✅ Summary
You've learned how to:
- 📝 Update documents individually and in bulk using powerful operators  
- 🆕 Use upsert to create documents when needed  
- 🗑️ Delete single or multiple documents conditionally  
- 🧽 Clean entire collections safely or drop them completely  
- 🛡️ Apply schema validation to keep data consistent and reliable  

---

## 🚀 Next Steps
- 🎯 Practice these commands in your MongoDB environment  
- 🧪 Experiment with custom schema validation rules  
- 🔍 Explore advanced queries and aggregations  
- 💻 Integrate these operations into your applications  
- 📚 Check the official [MongoDB Documentation](https://www.mongodb.com/docs/)  
