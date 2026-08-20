# Relatório de Atividade Prática — Laboratório de Sistemas Operacionais e Redes

## 1. Identificação

- **Aluno(a):** Davi da Silva Ferreira
- **Matrícula:** 2023011930
- **Disciplina:** Laboratório de Sistemas Operacionais e Redes
- **Professor(a):** Alaelson
- **Data de realização:** 19/08/2026
- **Título da atividade:** Administração de Usuários, Grupos e Permissões no Linux (Aula 02)

---

## 2. Objetivo

Esta atividade prática teve como objetivo capacitar o aluno a administrar usuários, grupos e permissões de acesso a arquivos e diretórios no Ubuntu Server, por meio da criação de contas de usuário, organização em grupos de trabalho, manipulação de permissões (notação simbólica e octal) e controle de posse com `chown`/`chgrp`. Buscou-se ainda validar, na prática, o funcionamento do modelo de permissões (dono, grupo e outros), simulando diferentes perfis de acesso no terminal.

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
  - Ubuntu 22.04 Server

- **Configuração da máquina virtual:**
  - Memória RAM alocada: 2048 MB
  - Núcleos de CPU: 1
  - Espaço em disco virtual: 32 GB, tipo VDI

- **Usuário de acesso ao servidor:**
  - Login: `administrador`
  - Senha: `adminifal`

---

## 4. Procedimento

**Passo 1 — Criação dos usuários**
Foram criados quatro usuários (`fulano`, `cicrano`, `beltrano`, `novato`) com o comando `adduser`, que cria automaticamente o diretório home, define o shell padrão (bash) e solicita senha individual:

```bash
sudo adduser fulano
sudo adduser cicrano
sudo adduser beltrano
sudo adduser novato
```

<img width="583" height="149" alt="image" src="https://github.com/user-attachments/assets/284458d0-f2fe-4287-a34c-64bf2751db3b" />

**Passo 2 — Criação e organização do grupo `devs`**
```bash
sudo groupadd devs
sudo usermod -aG devs fulano
sudo usermod -aG devs cicrano
sudo usermod -aG devs beltrano
```
O usuário `novato` propositalmente não foi adicionado ao grupo, para representar o perfil "Outros".

<img width="505" height="86" alt="image" src="https://github.com/user-attachments/assets/589e5447-feea-4b5a-a626-6602bee48f0c" />

**Passo 3 — Criação do diretório compartilhado**
sudo
<img width="540" height="98" alt="image" src="https://github.com/user-attachments/assets/eb472a34-0dc2-4df9-b5b7-c51555ecf4f1" />


**Passo 4 — Alteração de dono e grupo (`chown`/`chgrp`)**

<img width="597" height="146" alt="image" src="https://github.com/user-attachments/assets/3584108e-13aa-42cf-a2fa-1feec54f8d94" />


**Passo 5 — Definição de permissões (modo octal 770)**
ec
<img width="564" height="130" alt="image" src="https://github.com/user-attachments/assets/5cbb2bdc-e51e-4e96-96af-3e7e12517f16" />

Resultado: `drwxrwx---`, garantindo acesso total ao dono e ao grupo `devs`, e nenhum acesso a outros usuários.

**Passo 6 — Criação e permissão de arquivo de teste**

<img width="842" height="213" alt="image" src="https://github.com/user-attachments/assets/9cd223d0-6ca0-415a-b316-d88f7529af04" />

**Exercício de fixação — grupo `financeiro`**

<img width="470" height="146" alt="image" src="https://github.com/user-attachments/assets/a377587d-e2fd-48d6-951f-107511cdcdad" />

## 5. Testes com Capturas de Tela das Etapas-Chave

**Teste A — Usuário do grupo (`fulano`) acessando `/srv/projeto`**
```bash
su - fulano
cd /srv/projeto
ls -l
echo "Revisado por Fulano" >> config_redes.txt
cat config_redes.txt
exit
```
<img width="664" height="182" alt="image" src="https://github.com/user-attachments/assets/c179857e-b2ef-4f2a-b32d-09dd197f3797" />

**Teste B — Usuário externo (`novato`) tentando acessar `/srv/projeto`**
```bash
su - novato
cd /srv/projeto
ls -l /srv/projeto
exit
```
<img width="515" height="128" alt="image" src="https://github.com/user-attachments/assets/3ce8bbfe-54db-430b-9931-df23dd2030d3" />


**Teste C — Exercício de fixação: `cicrano` no grupo `financeiro`**
```bash
su - cicrano
cd /srv/financeiro
echo "Relatorio financeiro Q3" > relatorio.txt
cat relatorio.txt
exit
```
<img width="804" height="143" alt="image" src="https://github.com/user-attachments/assets/00072af8-924c-484b-bfed-2f10ed4b8412" />

**Teste D — `fulano` e `novato` tentando acessar `/srv/financeiro`**
```bash
su - fulano
ls -l /srv/financeiro
exit
```
<img width="561" height="115" alt="image" src="https://github.com/user-attachments/assets/b7ffef03-377b-4949-8c78-6e069c86e480" />

su - novato
ls -l /srv/financeiro
exit
<img width="553" height="113" alt="image" src="https://github.com/user-attachments/assets/aadf6609-b9f4-4ac4-9ae6-3dbdfcd7bf34" />

---

## 6. Problemas e Soluções

Não houve problema neste tópico.

---

## 7. Conclusão

A atividade permitiu compreender, na prática, como o Linux organiza o controle de acesso por meio do modelo dono/grupo/outros, e como a combinação de `adduser`, `groupadd`, `usermod`, `chown`, `chgrp` e `chmod` possibilita implementar políticas de segurança granulares em servidores. Os testes com `su -` demonstraram de forma clara a diferença entre acesso concedido (usuários pertencentes ao grupo) e acesso negado (usuários externos), reforçando a importância da correta gestão de usuários e permissões para a integridade e a segurança de ambientes corporativos Linux.
