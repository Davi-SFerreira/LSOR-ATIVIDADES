```markdown
# Relatório de Atividade Prática — Laboratório de Sistemas Operacionais e Redes

## 1. Identificação

- **Aluno(a):** Davi da Silva Ferreira
- **Matrícula:** 2023011930
- **Disciplina:** Laboratório de Sistemas Operacionais e Redes
- **Professor(a):** Alaelson
- **Data de realização:** 26/08/2026
- **Título da atividade:** Estrutura de Diretórios, Pastas do Sistema (FHS) e Permissões Avançadas no Linux Server (Aula 03)

---

## 2. Objetivo

Esta atividade prática teve como objetivo explorar a árvore padrão de diretórios do Linux (Filesystem Hierarchy Standard - FHS), compreendendo a utilidade das pastas essenciais do sistema operacional (`/etc`, `/var`, `/srv`, `/tmp`, `/bin`, `/sbin`, `/home`). Além disso, buscou-se criar estruturas de diretórios corporativos de forma recursiva com `mkdir -p`, gerenciar acessos departamentais por meio de grupos do sistema e permissões octais, e validar o isolamento de acesso simulando sessões reais de login com os comandos `su` e `su -`.

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

- **Usuários e grupos herdados da Aula 2:**
  - Usuários: `fulano`, `cicrano`, `beltrano`, `novato`
  - Grupos: `devs`, `financeiro`

---

## 4. Procedimento

**Passo 1 — Inspeção de pastas do sistema (FHS)**
```bash
cd /etc
ls -F | head -n 15
pwd
sudo tail -n 10 /var/log/auth.log
```
<img width="435" height="337" alt="image" src="https://github.com/user-attachments/assets/d8a3b7f9-30a2-41ba-9805-913abec699ff" />
<img width="817" height="300" alt="image" src="https://github.com/user-attachments/assets/e1189502-36fa-44b6-9c28-a54e86e10254" />

Essa inspeção permitiu observar arquivos de configuração em `/etc` e os registros de autenticação/`sudo` em `/var/log/auth.log`.

**Passo 2 — Criação recursiva de diretórios departamentais**
```bash
cd /srv
sudo mkdir -p ti-dept/projetos vendas-dept/relatorios
ls -R
```
clear
<img width="706" height="452" alt="image" src="https://github.com/user-attachments/assets/dbf73386-f6e4-4d96-b82b-c5349e568e54" />

A flag `-p` do `mkdir` permitiu criar as pastas e subpastas em um único comando, sem erro mesmo que os diretórios pais ainda não existissem.

**Passo 3 — Criação de grupos departamentais e associação de usuários**
```bash
sudo groupadd ti-group
sudo groupadd vendas-group
sudo usermod -aG ti-group fulano
sudo usermod -aG vendas-group cicrano
```
<img width="590" height="140" alt="image" src="https://github.com/user-attachments/assets/243e4291-272f-4032-8b20-d2092641fcf3" />

O usuário `novato` foi mantido sem grupo departamental, representando o acesso externo.

**Passo 4 — Ajuste de propriedades e permissões de isolamento**
```bash
ls -ld ti-dept vendas-dept

sudo chown administrador:ti-group ti-dept
sudo chown administrador:vendas-group vendas-dept

sudo chmod 770 ti-dept
sudo chmod 770 vendas-dept

sudo chown -R administrador:ti-group ti-dept/

ls -ld ti-dept vendas-dept
```
<img width="693" height="206" alt="image" src="https://github.com/user-attachments/assets/bbc30629-93e2-4214-89c5-f64244f53c35" />


**Passo 5 — Criação de arquivo de teste interno**
```bash
sudo touch ti-dept/projetos/arquitetura_rede_vpn.txt
sudo chown administrador:ti-group ti-dept/projetos/arquitetura_rede_vpn.txt
sudo chmod 660 ti-dept/projetos/arquitetura_rede_vpn.txt
```
<img width="812" height="168" alt="image" src="https://github.com/user-attachments/assets/3e5cf733-7a77-4765-b373-5d297b66728d" />


**Desafio de laboratório — grupo `diretoria-group`**
```bash
sudo mkdir -p /srv/diretoria-dept
sudo groupadd diretoria-group
sudo usermod -aG diretoria-group beltrano
sudo chown administrador:diretoria-group /srv/diretoria-dept
sudo chmod 770 /srv/diretoria-dept
sudo touch /srv/diretoria-dept/orcamento_ti.txt
sudo chown administrador:diretoria-group /srv/diretoria-dept/orcamento_ti.txt
sudo chmod 660 /srv/diretoria-dept/orcamento_ti.txt
```
<img width="818" height="251" alt="image" src="https://github.com/user-attachments/assets/2d4163ac-b4f0-4180-9451-910e714bc9e1" />
---

## 5. Testes com Capturas de Tela das Etapas-Chave

**Teste A — Usuário membro do grupo (`fulano`) acessando `ti-dept`**
```bash
su - fulano
pwd
cd /srv/ti-dept
ls -l projetos/
```
Saída esperada:
```
/home/fulano
-rw-rw---- 1 administrador ti-group 0 Aug 19 14:35 arquitetura_rede_vpn.txt
```
<img width="634" height="161" alt="image" src="https://github.com/user-attachments/assets/ae7b3903-1c57-421a-9a89-55f729168028" />

*Figura 1: Acesso permitido — `fulano` navega e lista o conteúdo de `ti-dept` por pertencer ao `ti-group`.*

**Teste B — Bloqueio com usuário externo (`cicrano`) tentando acessar `ti-dept`**
```bash
su - cicrano
cd /srv/ti-dept
```
Saída esperada:
```
-bash: cd: /srv/ti-dept: Permission denied
```
<img width="406" height="112" alt="image" src="https://github.com/user-attachments/assets/95e9cf24-dd4b-4402-8d67-7ac8f06277d5" />

*Figura 2: Acesso negado — `cicrano` pertence ao `vendas-group`, não ao `ti-group`.*

**Teste C — Desafio de laboratório: `beltrano` acessando `diretoria-dept`**
```bash
su - beltrano
cd /srv/diretoria-dept
cat orcamento_ti.txt
```
<img width="564" height="123" alt="image" src="https://github.com/user-attachments/assets/2806b6dc-f188-41a7-9e0d-dfb2d89ccf53" />

*Figura 3: Acesso permitido — `beltrano` pertence ao `diretoria-group`.*

**Teste D — Desafio de laboratório: `fulano` tentando acessar `diretoria-dept`**
```bash
su - fulano
cd /srv/diretoria-dept
```
Saída esperada:
```
-bash: cd: /srv/diretoria-dept: Permission denied
```
<img width="428" height="106" alt="image" src="https://github.com/user-attachments/assets/a7d54afc-652e-4031-84eb-e9ee41b69a1b" />

*Figura 4: Acesso negado — `fulano` não pertence ao `diretoria-group`.*

---

## 6. Problemas e Soluções

| Problema encontrado | Causa provável | Solução aplicada |
|---|---|---|
| [ex.: Confusão entre `su usuario` e `su - usuario`] | O comando sem hífen preserva o diretório e variáveis de ambiente do usuário anterior, gerando confusão no teste | Uso consistente de `su -` para garantir sessão de login completa e ambiente limpo |
| [preencha com problemas reais, se houver] | | |

Ocorram 2 problemas, nos passos iniciais alguns comandos referentes a alguns arquivos não estavam indo, as mensagens de erro me mostravam que não haviam arquivos nas pastas que eu estava tentando acessar/modificar. O outro foi na atividade do beltrano onde ele deveria ser aceito para editar o arquivo orcamento_ti.txt mas não pôde.
Depois acabei verificando que não havia feito o passo 2. Depois de refazer tudo deu certo.

---

## 7. Conclusão

A atividade permitiu compreender a organização hierárquica do sistema de arquivos Linux segundo o padrão FHS, identificando o propósito de diretórios essenciais como `/etc`, `/var`, `/srv`, `/bin` e `/sbin`. A criação de estruturas departamentais com `mkdir -p`, aliada à segregação de acesso por meio de grupos (`ti-group`, `vendas-group`, `diretoria-group`) e permissões octais (`770`/`660`), demonstrou na prática como implementar isolamento de dados entre setores de uma organização. Os testes com `su -` reforçaram a diferença crítica entre uma sessão de login completa e uma simples troca de identidade, evidenciando por que essa distinção é essencial para validar corretamente restrições de acesso em servidores Linux corporativos.

```

Deixei a data em aberto para você preencher com o dia real da prática. Os prints (`[INSERIR CAPTURA DE TELA AQUI]`) são para os 4 testes (A, B do roteiro principal + C, D do desafio da diretoria).
