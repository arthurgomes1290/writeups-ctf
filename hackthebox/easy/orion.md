# Orion — HackTheBox

**Dificuldade:** Easy
**OS:** Linux
**Data:** 2026-10-10
**Status:** In Progress

---

## Sumário

<!-- Preencher após concluir a máquina -->

---

## Reconhecimento

### Nmap

```bash
nmap -sC -sV -oN nmap.txt <IP>
```

Portas abertas encontradas:

| Porta | Serviço | Versão |
|-------|---------|--------|
| 22    | SSH     |        |
| 80    | HTTP    |        |

---

## Enumeração

### Gobuster

```bash
gobuster dir -u http://orion.htb -w /usr/share/wordlists/dirb/common.txt -x txt,php
```

Diretórios encontrados:

| Path | Status |
|------|--------|
| /admin | 200 |

### CraftCMS

Ao acessar `http://orion.htb/admin`, encontramos o painel de login do CraftCMS.

**Versão identificada:** CraftCMS 5.6.16

---

## Exploração



---

## Acesso Inicial — User Flag

```bash
cat user.txt
```

---

## Escalada de Privilégios — Root Flag

```bash
cat /root/root.txt
```

---

## Conclusão

### Vulnerabilidades Exploradas

| Vulnerabilidade | Descrição |
|----------------|-----------|
|                |           |

### Lições Aprendidas

- 
