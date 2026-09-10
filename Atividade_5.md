# Relatório de Atividade Prática — Laboratório de Sistemas Operacionais e Redes

## 1. Identificação

- **Aluno(a):** Davi da Silva Ferreira
- **Matrícula:** 2023011930
- **Disciplina:** Laboratório de Sistemas Operacionais e Redes
- **Professor(a):** Alaelson
- **Turma:** BSI 2026.02
- **Data de realização:** 09/09/2026
- **Título da atividade:** Acesso Remoto SSH via Redirecionamento de Portas no VirtualBox e Diagnóstico de Rede (Aula 05)

---

## 2. Objetivo

Esta atividade prática teve como objetivo configurar o acesso remoto seguro via SSH à máquina virtual Ubuntu Server, utilizando o mecanismo de Redirecionamento de Portas (Port Forwarding) do modo NAT do VirtualBox. Além disso, buscou-se realizar o diagnóstico das interfaces de rede, rotas padrão, rastreamento de pacotes, sessões ativas de terminal e monitoramento de portas TCP, tanto no sistema Linux convidado (Guest) quanto no sistema Windows hospedeiro (Host), consolidando o entendimento do fluxo de comunicação em redes virtualizadas.

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

- **Configuração de rede da VM:**
  - Modo: NAT
  - IP da VM (Guest): `10.0.2.15/24`
  - Gateway virtual do VirtualBox: `10.0.2.2`

- **Regra de redirecionamento de portas configurada:**

| Nome | Protocolo | IP Hospedeiro | Porta Hospedeiro | IP Convidado | Porta Convidado |
|---|---|---|---|---|---|
| SSH | TCP | 127.0.0.1 | 5222 | 10.0.2.15 | 22 |

- **Usuário de acesso ao servidor:**
  - Login: `administrador`
  - Senha: `adminifal`

---

## 4. Procedimento

**Passo 1 — Leitura do estado da rede com Netplan**
```bash
netplan status
```
Confirmada a interface `enp0s3` em modo DHCP, com IP `10.0.2.15/24` atribuído pelo VirtualBox.

**Passo 2 — Verificação do OpenSSH e instalação de utilitários de diagnóstico**
```bash
dpkg -l | grep openssh-server
sudo apt update
sudo apt install -y net-tools traceroute
```

**Passo 3 — Inspeção de interfaces com `ifconfig`**
```bash
ifconfig
```
Identificados o IP `10.0.2.15`, a máscara `255.255.255.0` e o endereço MAC da interface `enp0s3`.

**Passo 4 — Verificação do gateway padrão com `route -n`**
```bash
route -n
```
Confirmado o gateway padrão `10.0.2.2` como rota de saída da VM via `enp0s3`.

**Passo 5 — Rastreamento de rotas com `traceroute`**
```bash
traceroute 8.8.8.8
```
Primeiro salto identificado como o gateway interno do VirtualBox (`10.0.2.2`).

**Passo 6 — Verificação de usuários ativos com `w`**
```bash
w
```

**Passo 7 — Diagnóstico no Windows Host (antes do redirecionamento)**
```powershell
ipconfig /all
netstat -an | findstr 5222
```
Nenhuma linha retornada, confirmando que a porta `5222` ainda não estava em uso.

**Passo 8 — Configuração do redirecionamento de portas no VirtualBox**
Configurações da VM → Rede → Adaptador 1 (NAT) → Avançado → Redirecionamento de Portas, com a regra:
- Nome: SSH
- Protocolo: TCP
- IP Hospedeiro: 127.0.0.1 / Porta: 5222
- IP Convidado: 10.0.2.15 / Porta: 22

**Passo 9 — Validação da regra no Windows (depois do redirecionamento)**
```powershell
netstat -an | findstr 5222
```
Saída confirmando estado `LISTENING` na porta `5222`.

**Passo 10 — Conexão SSH remota a partir do Windows**
```powershell
ssh -p 5222 administrador@127.0.0.1
```

**Passo 11 — Validação da conexão ativa**
```powershell
netstat -an | findstr 5222
```
Saída confirmando estado `ESTABLISHED` durante a sessão SSH ativa.

**Passo 12 — Verificação da sessão remota com `w`**
```bash
w
```
Confirmada a presença do terminal `pts/0`, originado do gateway NAT (`10.0.2.2`), evidenciando a sessão SSH ativa.

---

## 5. Capturas de Tela das Etapas-Chave

<img width="397" height="157" alt="Captura de tela 2026-09-09 212010" src="https://github.com/user-attachments/assets/47919ed2-a474-4f22-81f5-eea9bede9112" />
<img width="858" height="680" alt="Captura de tela 2026-09-09 211930" src="https://github.com/user-attachments/assets/de288496-58a0-47b1-899c-df580718317d" />
<img width="660" height="410" alt="Captura de tela 2026-09-09 211747" src="https://github.com/user-attachments/assets/1c6fd29b-1637-4e49-a041-798551fa55c7" />
<img width="874" height="720" alt="Captura de tela 2026-09-09 211625" src="https://github.com/user-attachments/assets/9f08e5ef-5b86-479b-a5bb-5d2825f13c90" />
<img width="829" height="630" alt="Captura de tela 2026-09-09 211324" src="https://github.com/user-attachments/assets/de88fdc1-6357-499a-be1b-c28378f75ecc" />
<img width="817" height="623" alt="Captura de tela 2026-09-09 211259" src="https://github.com/user-attachments/assets/6426e937-a509-4e61-a290-dd0275445c9c" />
<img width="846" height="647" alt="Captura de tela 2026-09-09 211109" src="https://github.com/user-attachments/assets/c40553cc-22e5-41c6-8e5f-991cb2fb02b6" />
<img width="816" height="665" alt="Captura de tela 2026-09-09 210837" src="https://github.com/user-attachments/assets/2820dfc9-dad9-4298-844a-c9bc627cb59f" />
<img width="843" height="648" alt="Captura de tela 2026-09-09 210735" src="https://github.com/user-attachments/assets/fd1aa251-3f3f-47ed-907f-93d33d2cb368" />
<img width="334" height="82" alt="Captura de tela 2026-09-09 212023" src="https://github.com/user-attachments/assets/8dacccfd-1a27-4dcf-951a-aa88ce2cb064" />

---

## 6. Problemas e Soluções

| Problema encontrado | Causa provável | Solução aplicada |
|---|---|---|
| [ex.: Conexão SSH recusada (`Connection refused`)] | Serviço `openssh-server` não instalado ou não iniciado | Instalado o pacote com `sudo apt install openssh-server` e verificado o status com `sudo systemctl status ssh` |
| [ex.: `netstat -an` não mostrava a porta 5222 mesmo após configurar a regra] | Regra de redirecionamento criada com a VM ligada, exigindo reinício da rede/VM | VM reiniciada após a criação da regra no VirtualBox |

---

## 7. Conclusão

A atividade permitiu compreender, na prática, como o modo NAT do VirtualBox isola a rede da máquina virtual e como o mecanismo de Redirecionamento de Portas viabiliza o acesso remoto a serviços internos, como o SSH, a partir do sistema hospedeiro. O uso combinado de ferramentas de diagnóstico — `ifconfig`, `route -n`, `traceroute`, `w` no Linux, e `netstat -an` no Windows — evidenciou de forma clara o fluxo de pacotes entre host e guest, desde a ausência de escuta na porta (`5222` fechada), passando pelo estado `LISTENING` após a configuração da regra, até o estado `ESTABLISHED` durante a sessão ativa. A prática reforçou a importância de compreender o comportamento de portas TCP e do NAT para a administração segura e eficiente de servidores em ambientes de rede virtualizados.
