# 📚 Guia Completo: Importação e Exportação de Dados no MongoDB

## 🚀 Introdução
Este guia é um **tutorial rápido e de bolso** sobre como importar e exportar dados no MongoDB. Ele combina scripts **prontos para copiar e colar** com explicações detalhadas para cada comando, permitindo que qualquer pessoa, mesmo sem experiência, consiga aprender e executar sem precisar de estrutura prévia.

Você vai aprender a:
- Criar diretórios e arquivos de dados.
- Importar **JSON** e **CSV** usando `mongoimport`.
- Tratar tipos de dados no MongoDB.
- Consultar e validar dados importados.
- Exportar coleções usando `mongoexport`.

---

## 1️⃣ Preparar Ambiente

### 1.1 Criar diretório do projeto

```bash
mkdir -p ~/project  # Cria o diretório ~/project se não existir
cd ~/project          # Entra no diretório criado
```
**Explicação:**
- `mkdir -p` → cria diretórios incluindo subdiretórios se necessário.
- `cd` → navega para o diretório criado, onde vamos armazenar arquivos de dados.

---

## 2️⃣ Criar Arquivos de Dados

### 2.1 Arquivo JSON (`books.json`)
```bash
cat <<EOL > books.json
[
  {"_id": 1, "title": "MongoDB Basics", "author": "Jane Smith", "year": 2023, "tags": ["mongodb", "database", "nosql"]},
  {"_id": 2, "title": "Python Programming", "author": "John Doe", "year": 2022, "tags": ["python", "programming"]},
  {"_id": 3, "title": "Data Science Handbook", "author": "Alice Johnson", "year": 2021, "tags": ["data science", "python", "machine learning"]}
]
EOL
```
**Explicação:**
- `cat <<EOL > books.json` → cria um arquivo chamado `books.json` e insere o conteúdo entre `EOL`.
- O JSON contém uma lista de documentos que serão importados no MongoDB.

### 2.2 Arquivo CSV (`library_members.csv`)
```bash
cat <<EOL > library_members.csv
name,age,membership_status
John Doe,35,active
Jane Smith,28,active
Mike Johnson,42,expired
EOL
```
**Explicação:**
- CSV é um formato de dados tabular.
- Primeira linha são os nomes dos campos (`header`).
- As linhas seguintes são os dados que serão importados.

---

## 3️⃣ Importar Dados JSON

```bash
mongoimport --db library_db --collection books --file ~/project/books.json --jsonArray
```
**Explicação detalhada:**
- `mongoimport` → ferramenta para importar dados para MongoDB.
- `--db library_db` → banco de dados de destino chamado `library_db`.
- `--collection books` → coleção de destino chamada `books`.
- `--file ~/project/books.json` → caminho do arquivo a ser importado.
- `--jsonArray` → indica que o arquivo contém **um array JSON**, cada item será um documento.

### Verificar importação
```bash
mongosh
use library_db
# Conta os documentos da coleção books
db.books.countDocuments()
# Visualiza um documento
db.books.findOne()
exit
```
**Explicação:**
- `mongosh` → inicia o shell interativo do MongoDB.
- `use library_db` → muda para o banco de dados `library_db`.
- `countDocuments()` → retorna a quantidade de documentos na coleção.
- `findOne()` → exibe o primeiro documento.
- `exit` → sai do shell.

---

## 4️⃣ Importar Dados CSV

```bash
mongoimport --db library_db --collection members --type csv --file ~/project/library_members.csv --headerline
```
**Explicação detalhada:**
- `--type csv` → indica que o arquivo é CSV.
- `--headerline` → usa a primeira linha do CSV como nomes dos campos.

### Verificar importação CSV
```bash
mongosh
use library_db
db.members.countDocuments()
db.members.findOne()
exit
```
**Explicação:** mesmo conceito do JSON.

---

## 5️⃣ Manipular Tipos de Dados CSV

### 5.1 Criar CSV sem cabeçalho
```bash
tail -n +2 ~/project/library_members.csv > ~/project/library_members_no_header.csv
```
- `tail -n +2` → remove a primeira linha (cabeçalho) e cria um novo arquivo.

### 5.2 Importar com campos explícitos
```bash
mongoimport --db library_db --collection typed_members --type csv --file ~/project/library_members_no_header.csv --fields "name,age,membership_status"
```
- `--fields` → define os nomes dos campos manualmente.

### 5.3 Converter idade para inteiro
```bash
mongosh library_db --eval "db.typed_members.updateMany({}, [{ $set: { age: { $toInt: \"$age\" } } }])"
```
- `updateMany` → atualiza todos os documentos.
- `$set` → define o novo valor para o campo.
- `$toInt` → converte o campo de string para inteiro.

### 5.4 Verificar conversão
```javascript
db.typed_members.findOne()
typeof db.typed_members.findOne().age
db.typed_members.find({ age: { $gt: 30 } })
exit
```
- `typeof` → verifica o tipo do campo.
- `$gt` → operador “maior que” para consultas numéricas.

---

## 6️⃣ Consultar e Validar Dados

### Listar coleções
```javascript
show collections
```
### Teste de consultas
```javascript
db.books.find({ tags: "python" })
db.typed_members.find({ membership_status: "active" })
exit
```
- `$tags` → busca documentos que contenham o valor na array.
- Validação simples de integridade dos dados.

---

## 7️⃣ Exportar Dados

```bash
mongoexport --db library_db --collection books --out ~/project/exported_books.json
cat ~/project/exported_books.json
```
- `mongoexport` → exporta uma coleção para arquivo JSON.
- `--out` → define o arquivo de saída.
- `cat` → exibe o conteúdo do arquivo exportado.

---

## ✅ Resumo
- Criamos diretórios e arquivos do zero.
- Importamos **JSON** e **CSV**.
- Tratamos tipos de dados e realizamos consultas.
- Exportamos coleções para arquivos JSON.
- Cada comando foi explicado detalhadamente para referência rápida e aprendizado completo.
