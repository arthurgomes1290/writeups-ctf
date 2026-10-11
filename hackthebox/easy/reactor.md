# Reactor — HackTheBox

**Dificuldade:** Easy
**OS:** Linux
**Data:** 2026-10-09
**Status:** Em progresso

---

## Sumário

<!-- [preencher ao concluir] -->

---

## Reconhecimento

### Nmap

```bash
nmap -sC -sV -oN nmap.txt <IP>
```

<!-- [PRINT: resultado do nmap] -->

Portas abertas encontradas:

| Porta | Serviço | Versão |
|-------|---------|--------|
| 22    | SSH     |        |
| 3000  | HTTP    | Next.js |

---

## Enumeração

### Aplicação Web (porta 3000)

O scan do Nmap revela que a aplicação rodando na porta 3000 utiliza o framework **Next.js**.

<!-- [PRINT: página inicial da aplicação] -->

---

## Exploração

### CVE-2025-55182 — RCE no Next.js

Pesquisando a versão do Next.js identificada, encontramos a **CVE-2025-55182**, uma vulnerabilidade crítica de **Remote Code Execution (RCE)** no framework.

<!-- [PRINT: exploit / PoC rodando] -->

<!-- [preencher: comando/script utilizado para explorar] -->

---

## Acesso Inicial — User Flag

<!-- [PRINT: user flag] -->

---

## Escalada de Privilégios — Root Flag

<!-- [PRINT: root flag] -->

---

## Conclusão

### Vulnerabilidades Exploradas

| Vulnerabilidade | Descrição |
|----------------|-----------|
|                |           |

### Lições Aprendidas

<!-- [preencher ao concluir] -->
