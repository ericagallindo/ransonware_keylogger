# Laboratório de Malware Ofensivo — Ransomware e Keylogger (Kali Linux)

## 📌 Sobre o projeto

Este repositório documenta dois laboratórios práticos de segurança ofensiva realizados em ambiente **isolado e controlado**, a partir de scripts disponibilizados pela professora da trilha, com pequenas adaptações (nomes de arquivos, chave e diretório de testes):

1. **Ransomware** — criptografia e descriptografia de arquivos em um diretório de teste;
2. **Keylogger** — captura de teclas digitadas, registrada em um arquivo de log local.

> ⚠️ **Aviso de uso ético**
> Este projeto foi desenvolvido **exclusivamente para fins educacionais**, em uma VM Kali Linux isolada, sem conexão com redes de produção ou de terceiros, e operando apenas sobre arquivos de teste criados para esse fim. Os scripts foram fornecidos pela professora do curso como material de aula. **Nunca execute ransomware, keyloggers ou qualquer malware fora de um ambiente próprio, isolado e autorizado.** A criação, distribuição ou uso desse tipo de software contra terceiros sem autorização é crime (no Brasil, Lei 12.737/2012). Este repositório documenta a execução e os resultados observados, sem detalhar a implementação interna dos scripts.

---

## 🧰 Ambiente utilizado

| Componente | Versão / Detalhe | Função |
|---|---|---|
| **VirtualBox** | Hipervisor instalado no host | Executa a VM do Kali Linux em ambiente isolado |
| **Kali Linux** | Rodando em VM no VirtualBox | Sistema onde os dois laboratórios foram executados |
| **Python 3** | Interpretador já presente no Kali | Executa os scripts de ransomware e keylogger |

---

## Parte 1 — Ransomware (criptografia / descriptografia controlada)

### Estrutura de arquivos

Diretório de trabalho: `~/malware`

```
~/malware
├── ransoware.py          # script de criptografia
├── descriptografar.py    # script de descriptografia
├── erica.key             # arquivo de chave usado na criptografia
├── LEIA-ME.txt           # instruções/aviso do exercício
└── test_files/           # diretório alvo (controlado) a ser criptografado
    ├── dados_confidenciais
    ├── erica.dados
    └── senhas.txt
```

> 💡 Apenas o conteúdo de `test_files/` é afetado pelo script — um diretório de teste criado especificamente para este laboratório, sem nenhum dado real ou sensível.

### 1.1 Conferir a estrutura antes da execução

```bash
cd ~/malware
ls -lR
```

Saída observada:

```
.:
total 20
-rw-rw-rw- 1 kali kali  966 Oct 22 11:40 descriptografar.py
-rw-rw-rw- 1 kali kali   44 Oct 22 11:39 erica.key
-rw-rw-rw- 1 kali kali  160 Oct 22 11:39 LEIA-ME.txt
-rw-rw-rw- 1 kali kali 1649 Oct 22 11:39 ransoware.py
drwxrwxrwx 2 kali kali 4096 Oct 22 11:41 test_files

./test_files:
total 12
-rw-rw-rw- 1 kali kali 80 Oct 22 11:40 dados_confidenciais
-rw-rw-r-- 1 kali kali 37 Oct 22 11:41 erica.dados
-rw-rw-rw- 1 kali kali 29 Oct 22 11:40 senhas.txt
```

### 1.2 Executar o script de criptografia

```bash
python ransoware.py
```

Saída observada:
```
Ransoware executado! Arquivos criptografados!
```

### 1.3 Verificação visual do conteúdo criptografado

```bash
cat test_files/erica.dados
```

Saída observada (conteúdo ilegível, confirmando a criptografia):
```
gAAAAABq2jOxTWSVhR8bM7iQXlyLM5daIrk0Rg3BhaaujrDW_4nOBqvFR4QMWGLYuxGWbycW_jrL-eQV8cY9uP0O3j8yBldZBT3AYLqNKm0otc6M6mObqKZd1BaaY2ehAyKTdZhrwtX4
```

### 1.4 Executar o script de descriptografia

```bash
python descriptografar.py
```

Saída observada:
```
Arquivos restaurados com sucesso
```

### 1.5 Verificação do conteúdo restaurado

```bash
cat test_files/erica.dados
```

Saída observada (conteúdo original restaurado):
```
Meus dados limpos
(sem criptografia)
```

### Resultado

O ciclo completo **criptografia → verificação → descriptografia → verificação** foi reproduzido com sucesso, confirmando que:
- o script `ransoware.py` transforma o conteúdo de `test_files/` em dados ilegíveis usando a chave `erica.key`;
- o script `descriptografar.py` reverte a operação, restaurando o conteúdo original.

---

## Parte 2 — Keylogger (captura de teclas em ambiente local)

### Pré-requisito: biblioteca pynput

O script depende da biblioteca `pynput`, que **não vem instalada por padrão no Kali Linux**. Antes de executar, instale-a:

```bash
pip install pynput --break-system-packages
```

> 💡 A flag `--break-system-packages` é necessária em versões recentes do Kali/Debian, que bloqueiam `pip install` fora de um ambiente virtual por padrão.

### Estrutura de arquivos

Diretório de trabalho: `~/keylogger`

```
~/keylogger
├── keylogger.pyw          # script executado (captura em background, grava no log local)
├── keylogger_email.py      # variante que envia as capturas por e-mail (não utilizada nesta execução)
└── log.txt                 # arquivo onde as teclas capturadas são registradas
```

### 2.1 Conferir a estrutura antes da execução

```bash
cd ~/keylogger
ls -l
```

Saída observada:
```
total 12
-rwxrwxr-x 1 kali kali 1399 Oct 22 11:42 keylogger_email.py
-rwxrwxr-x 1 kali kali 1102 Oct 22 11:42 keylogger.pyw
-rwxrwxr-x 1 kali kali  259 Oct 22 11:42 log.txt
```

### 2.2 Executar o keylogger

```bash
python keylogger.pyw
```

> O script roda em segundo plano, capturando as teclas digitadas durante a sessão e gravando-as em `log.txt`.

### 2.3 Digitar um texto de teste

Em qualquer campo de texto da VM (editor, terminal, navegador), digite um pequeno trecho de teste para gerar conteúdo capturável.

### 2.4 Verificação do conteúdo capturado

```bash
cat log.txt
```

Confirme que o conteúdo digitado no passo anterior aparece registrado no arquivo.

### Resultado

A execução do `keylogger.pyw` demonstrou a captura das teclas digitadas durante a sessão, com o conteúdo sendo persistido em `log.txt`, confirmando o funcionamento do script em ambiente local e controlado.

---

## 🗂️ Estrutura do repositório

```
.
├── README.md
├── malware/
│   ├── ransoware.py
│   ├── descriptografar.py
│   ├── erica.key
│   ├── LEIA-ME.txt
│   └── test_files/
│       ├── dados_confidenciais
│       ├── erica.dados
│       └── senhas.txt
├── keylogger/
│   ├── keylogger.pyw
│   ├── keylogger_email.py
│   └── log.txt
└── images/
    ├── Captura de Tela (1).png
    ├── Captura de Tela (2).png
    └── Captura de Tela (3).png
```

> 💡 Avalie se `erica.key` e os arquivos de `test_files/` devem realmente ser versionados — se contiverem qualquer dado sensível real (mesmo de teste), prefira adicioná-los ao `.gitignore` e descrever o conteúdo no README em vez de subir os arquivos.

---

## 👤 Autor

Projeto desenvolvido como parte do desafio de laboratório de segurança ofensiva (malware educacional), com foco em ransomware e keylogger em ambiente controlado.
