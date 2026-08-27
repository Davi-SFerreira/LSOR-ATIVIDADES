```markdown
# Relatório de Atividade Prática — Laboratório de Sistemas Operacionais e Redes

## 1. Identificação

- **Aluno(a):** Davi da Silva Ferreira
- **Matrícula:** 2023011930
- **Disciplina:** Laboratório de Sistemas Operacionais e Redes
- **Professor(a):** Alaelson
- **Turma:** 2026.02
- **Data de realização:** 26/08/2026
- **Título da atividade:** /Manipulação, Edição, Permissões e Automação de Arquivos no Linux (Aula 04)

---

## 2. Objetivo

Esta atividade prática teve como objetivo desenvolver habilidades de manipulação, visualização e edição de arquivos via terminal no Ubuntu Server, utilizando editores de texto (Nano) e comandos de leitura (`cat`, `less`, `head`, `tail`). Além disso, buscou-se dar os primeiros passos em automação de tarefas administrativas, por meio da criação de scripts em Shell capazes de cadastrar usuários e definir senhas em lote, eliminando a necessidade de execução manual e repetitiva de comandos.

---

## 3. Ambiente

- **Máquina física (hospedeira/host):**
  - Sistema operacional: Windows 11
  - Processador: AMD Ryzen 7 PRO 8700GE
  - Memória RAM: 16GB
  - Espaço em disco disponível: 32GB

- **Software de virtualização:**
  - Oracle VirtualBox

- **Sistema operacional convidado (guest):**
  - Ubuntu Server 26.04 LTS

- **Configuração da máquina virtual:**
  - Memória RAM alocada: 512 MB
  - Núcleos de CPU: 1
  - Espaço em disco virtual: 32 GB, tipo VDI

- **Usuário de acesso ao servidor:**
  - Login: `administrador`
  - Senha: `adminifal`

---

## 4. Procedimento

**Passo 1 — Criação e edição básica de arquivo**
```bash
touch configuracao.conf
nano configuracao.conf
```
Conteúdo inserido no arquivo (salvo com `Ctrl+O` e fechado com `Ctrl+X`):
```text
# Configuração de Teste do Laboratório
PORTA=8080
TIMEOUT=30
```
<img width="516" height="625" alt="image" src="https://github.com/user-attachments/assets/5c8311ae-63e7-4ce2-af46-f198b110ce0e" />

**Passo 2 — Cópia e movimentação de arquivos**
```bash
mkdir backups
cp configuracao.conf backups/
mv configuracao.conf config_antiga.conf
```
<img width="590" height="99" alt="image" src="https://github.com/user-attachments/assets/71442ccb-44e8-4aa4-921d-2d1c4bbc5cf1" />

**Passo 3 — Remoção segura e remoção forçada**
```bash
rm -i config_antiga.conf
# rm: remove regular file 'config_antiga.conf'? y

rm -rf backups
```
<img width="451" height="81" alt="image" src="https://github.com/user-attachments/assets/ba6e1623-7f8f-4a38-ac17-3ebf0da7053c" />

**Passo 4 — Criação da lista de usuários (`usuarios.txt`)**
```bash
nano usuarios.txt
```
Conteúdo (20 linhas, `aluno01` a `aluno20`):
```text
aluno01
aluno02
...
aluno20
```
<img width="486" height="642" alt="image" src="https://github.com/user-attachments/assets/28d2558d-939b-473f-b7dc-43cf91e18057" />

**Passo 5 — Script de criação de usuários em lote (`passo1_criar.sh`)**
```bash
nano passo1_criar.sh
```
```bash
#!/bin/bash

# Script de criação de usuários em lote
for usuario in $(cat usuarios.txt); do
    echo "Processando criação do usuário: $usuario"
    sudo useradd -m -s /bin/bash $usuario
done

echo "Processo de criação concluído!"
```
<img width="498" height="623" alt="image" src="https://github.com/user-attachments/assets/9bd9b063-af79-4d31-9ed1-100ed64e4cdd" />

**Passo 6 — Script de definição de senhas em lote (`passo2_senhas.sh`)**
```bash
nano passo2_senhas.sh
```
```bash
#!/bin/bash

# Script de definição de senhas em lote
for usuario in $(cat usuarios.txt); do
    echo "Definindo senha padronizada para: $usuario"
    echo "$usuario:$usuario" | sudo chpasswd
done

echo "Todas as senhas foram atualizadas com sucesso!"
```
<img width="502" height="632" alt="image" src="https://github.com/user-attachments/assets/22888fd4-199a-4221-b495-91f9b31e1cf9" />

**Passo 7 — Concessão de permissão de execução**
```bash
chmod +x passo1_criar.sh
chmod +x passo2_senhas.sh
```
<img width="479" height="72" alt="image" src="https://github.com/user-attachments/assets/7dfbe92a-73ee-4c47-8449-52adc2eafc54" />

**Passo 8 — Execução dos scripts**
```bash
./passo1_criar.sh
./passo2_senhas.sh
```
<img width="838" height="636" alt="image" src="https://github.com/user-attachments/assets/6ec7c950-8c25-44dc-b251-5b4ddfd6d854" />
<img width="484" height="642" alt="image" src="https://github.com/user-attachments/assets/900e33ea-19d9-4113-9a58-b6bdf027e208" />

**Passo 9 — Validação das contas criadas**
```bash
getent passwd | tail -n 20
getent group | tail -n 20
```
<img width="742" height="362" alt="image" src="https://github.com/user-attachments/assets/79578c6c-fe8b-41ea-99a8-bb6ba8a4e50e" />
<img width="448" height="369" alt="image" src="https://github.com/user-attachments/assets/67a0aefb-f684-4322-a1bc-246ff5c569d6" />
---

## 5. Testes com Capturas de Tela das Etapas-Chave

**Teste A — Execução do script de criação (`passo1_criar.sh`)**
```bash
./passo1_criar.sh
```
Saída esperada:
```
Processando criação do usuário: aluno01
Processando criação do usuário: aluno02
...
Processo de criação concluído!
```
<img width="847" height="705" alt="image" src="https://github.com/user-attachments/assets/12e71605-0826-4a4a-8ec3-7bcafef85800" />

*Figura 1: Execução bem-sucedida do script de criação em lote dos 20 usuários.*

**Teste B — Execução do script de senhas (`passo2_senhas.sh`)**
```bash
./passo2_senhas.sh
```
Saída esperada:
```
Definindo senha padronizada para: aluno01
...
Todas as senhas foram atualizadas com sucesso!

<img width="456" height="376" alt="image" src="https://github.com/user-attachments/assets/518fc343-75d3-468a-b678-54f2b5d6e923" />

*Figura 2: Execução bem-sucedida do script de definição de senhas.*

**Teste C — Validação via `getent`**
```bash
getent passwd | tail -n 20
getent group | tail -n 20
```

<img width="507" height="401" alt="image" src="https://github.com/user-attachments/assets/6b544fda-4656-4637-b657-11a74cf5e37f" />

<img width="475" height="356" alt="image" src="https://github.com/user-attachments/assets/65131e8f-42c3-4de3-8ffa-7aeb0a896b1d" />

*Figura 3: Contas e grupos `aluno01` a `aluno20` confirmados na base de dados do sistema.*

**Teste D — Login de teste com usuário criado**
```bash
su - aluno01
Password: aluno01
pwd
exit
```
Saída esperada:
```
/home/aluno01
```
<img width="370" height="118" alt="image" src="https://github.com/user-attachments/assets/8f6a9907-f6ac-4c2c-b73e-d0c762a68ec3" />

*Figura 4: Login bem-sucedido com `aluno01`, confirmando a criação da conta, senha e shell corretos.*

---

## 6. Problemas e Soluções

| Problema encontrado | Causa provável | Solução aplicada |
|---|---|---|
| [ex.: `permission denied` ao executar `./passo1_criar.sh`] | Arquivo criado sem permissão de execução | Aplicado `chmod +x passo1_criar.sh` antes de rodar o script |
| [ex.: Comando `useradd` falhou dentro do script] | Esquecimento do prefixo `sudo` dentro do laço `for` | Adicionado `sudo` antes de `useradd` no script |
| [preencha com problemas reais, se houver] | | |

Tive que recorrear a IA para resolver uns erros relacionados aos scripts. Fora isso, foi tranquilo!

---

## 7. Conclusão

A atividade permitiu consolidar habilidades práticas de manipulação de arquivos no terminal Linux (criação, cópia, movimentação e remoção segura com `touch`, `cp`, `mv` e `rm`), além do uso de editores de texto como o Nano. Mais importante, a criação dos scripts `passo1_criar.sh` e `passo2_senhas.sh` demonstrou como a automação via Shell Script, combinada com estruturas de repetição (`for`) e utilitários como `useradd` e `chpasswd`, torna viável o gerenciamento de dezenas de contas de usuário de forma rápida, padronizada e escalável — um recurso essencial em ambientes corporativos de administração de redes.
```
