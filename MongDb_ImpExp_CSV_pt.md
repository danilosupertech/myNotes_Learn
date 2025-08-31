# 📚 Guia Completo: Importação e Exportação de Dados no MongoDB

## 🚀 Introdução
Este guia é uma **referência rápida e tutorial de bolso** sobre como importar e exportar dados no MongoDB. Combina **scripts prontos para copiar e colar** com **explicações detalhadas** para cada comando, permitindo que qualquer pessoa aprenda e execute desde o zero.

Você aprenderá a:
- Criar diretórios de projeto e arquivos de dados.
- Importar **JSON** em uma coleção e **CSV** em outra coleção independente usando `mongoimport`.
- Manipular tipos de dados no MongoDB.
- Consultar e validar dados importados.
- Exportar coleções usando `mongoexport`.

---

## 1️⃣ Preparar o Ambiente

### 1.1 Criar diretório de projeto
```bash
mkdir -p ~/project  # Cria o diretório ~/project caso não exista
cd ~/project          # Navega para o diretório criado
```
**Explicação:**
- `mkdir -p` → cria diretórios, incluindo os pais, se necessário.
- `cd` → navega até o diretório onde os arquivos de dados serão armazenados.

---

## 2️⃣ Criar Arquivos de Dados

### 2.1 Arquivo JSON (`books.json`) - para a coleção `books`
```bash
cat <<EOL > books.json
[
  {"_id": 1, "title": "MongoDB Básico", "author": "Jane Smith", "year": 2023, "tags": ["mongodb", "database", "nosql"]},
  {"_id": 2, "title": "Programação em Python", "author": "John Doe", "year": 2022, "tags": ["python", "programming"]},
  {"_id": 3, "title": "Manual de Data Science", "author": "Alice Johnson", "year": 2021, "tags": ["data science", "python", "machine learning"]}
]
EOL
```
**Explicação:** Este arquivo JSON será importado para a coleção independente `books`.

### 2.2 Arquivo CSV (`library_members.csv`) - para a coleção `members`
```bash
cat <<EOL > library_members.csv
name,age,membership_status
John Doe,35,active
Jane Smith,28,active
Mike Johnson,42,expired
EOL
```
**Explicação:** Este arquivo CSV será importado para a coleção independente `members`. Cada arquivo é **exemplo separado** para mostrar a diferença entre importação JSON e CSV.

---

## 3️⃣ Importar JSON para a coleção `books`
```bash
mongoimport --db library_db --collection books --file ~/project/books.json --jsonArray
```
**Explicação:**
- `mongoimport` → ferramenta CLI para importar dados no MongoDB.
- `--db library_db` → banco de dados alvo.
- `--collection books` → coleção alvo.
- `--file ~/project/books.json` → caminho do arquivo de entrada.
- `--jsonArray` → indica que o arquivo contém um array JSON, cada elemento se torna um documento.

### Verificar importação JSON
```bash
mongosh
use library_db
db.books.countDocuments()
db.books.findOne()
exit
```

---

## 4️⃣ Importar CSV para a coleção `members`
```bash
mongoimport --db library_db --collection members --type csv --file ~/project/library_members.csv --headerline
```
**Explicação:**
- `--type csv` → arquivo de entrada é CSV.
- `--headerline` → usa a primeira linha como nomes dos campos.

### Verificar importação CSV
```bash
mongosh
use library_db
db.members.countDocuments()
db.members.findOne()
exit
```

---

## 5️⃣ Manipular Tipos de Dados no CSV

### 5.1 Remover cabeçalho para campos manuais
```bash
tail -n +2 ~/project/library_members.csv > ~/project/library_members_no_header.csv
```

### 5.2 Importar CSV com campos explícitos para `typed_members`
```bash
mongoimport --db library_db --collection typed_members --type csv --file ~/project/library_members_no_header.csv --fields "name,age,membership_status"
```

### 5.3 Converter campo `age` de string para inteiro
```bash
mongosh library_db --eval "db.typed_members.updateMany({}, [{ $set: { age: { $toInt: \"$age\" } } }])"
```

### 5.4 Verificar conversão
```javascript
db.typed_members.findOne()
typeof db.typed_members.findOne().age
db.typed_members.find({ age: { $gt: 30 } })
exit
```

---

## 6️⃣ Consultar e Validar Dados

### Listar coleções
```javascript
show collections
```
### Consultas de exemplo
```javascript
db.books.find({ tags: "python" })
db.members.find({ membership_status: "active" })
db.typed_members.find({ age: { $gt: 30 } })
exit
```
- Cada coleção é independente e as consultas validam a estrutura dos dados.

---

## 7️⃣ Exportar Dados

### Exportar coleção `books` para JSON
```bash
mongoexport --db library_db --collection books --out ~/project/exported_books.json
cat ~/project/exported_books.json
```
- `mongoexport` → exporta uma coleção para arquivo JSON.
- `--out` → especifica arquivo de saída.
- `cat` → exibe o conteúdo exportado.

---

## ✅ Resumo
- Criou diretórios e arquivos de dados independentes.
- Importou **JSON** para `books` e **CSV** para `members` (e `typed_members`).
- Manipulou tipos de dados para operações numéricas.
- Executou consultas e validou integridade dos dados.
- Exportou coleções para JSON.
- Explicações detalhadas fornecem tanto **referência rápida** quanto **tutorial completo**.
