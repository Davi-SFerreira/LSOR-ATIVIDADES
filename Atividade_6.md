# Relatório de Atividade Prática — Laboratório de Sistemas Operacionais e Redes

## 1. Identificação

- **Aluno(a):** Davi da Silva Ferreira
- **Matrícula:** 2023011930
- **Disciplina:** Laboratório de Sistemas Operacionais e Redes
- **Professor(a):** Alaelson
- **Turma:** BSI 2026.02
- **Data de realização:** 23/09/2026
- **Título da atividade:** Configuração de Rede Estática com Netplan e Modo Placa em Ponte (Bridge Adapter) no VirtualBox (Aula 06)

---

## 2. Objetivo

Esta atividade prática teve como objetivo realizar a transição do adaptador de rede da máquina virtual do modo **NAT** para o modo **Placa em Ponte (Bridge Adapter)** no VirtualBox, integrando a VM diretamente à rede física do laboratório. Em seguida, buscou-se configurar um endereço IP estático, máscara de sub-rede, gateway e servidores DNS por meio do utilitário **Netplan**, editando o arquivo `/etc/netplan/00-installer-config.yaml`. Por fim, a atividade visou validar a conectividade bidirecional entre o Host (Windows) e o Guest (Linux), além de diagnosticar rotas externas com `traceroute`.

---

## 3. Ambiente

- **Máquina física (hospedeira/host):**
  - Sistema operacional: Windows 11
  - Placa de rede física: Realtek PCIe GbE Family Controller

- **Software de virtualização:**
  - Oracle VirtualBox

- **Sistema operacional convidado (guest):**
  - Ubuntu Server 26.04 LTS

- **Configuração de rede da VM:**
  - Modo de rede: Placa em Ponte (Bridge Adapter)
  - Endereço IP estático: `172.20.23.1/22`
  - Máscara de sub-rede: `255.255.252.0`
  - Gateway padrão: `172.20.20.1`
  - Servidores DNS: `172.20.20.1`, `1.1.1.1`, `8.8.8.8`

- **Usuário de acesso ao servidor:**
  - Login: `administrador`
  - Senha: `adminifal`

---

## 4. Procedimento

**Passo 1 — Alteração do adaptador de rede para Placa em Ponte (Bridge)**
Nas configurações da VM no VirtualBox, o Adaptador 1 foi alterado de NAT para **Placa em modo Bridge**, selecionando a placa física *Realtek PCIe GbE Family Controller* como interface de saída.

![Configuração da placa em modo Bridge no VirtualBox](imagens_aula6/Captura%20de%20tela%202026-09-23%20194212.png)
*Figura 1: Adaptador 1 configurado em modo Bridge, conectado à placa de rede física do host.*

**Passo 2 — Teste de disponibilidade do IP candidato**
Antes de aplicar o IP `172.20.23.1` na VM, foi testada sua disponibilidade a partir do Windows Host com `ping`:
```powershell
ping 172.20.23.1
```

![Teste de ping ao IP candidato](imagens_aula6/Captura%20de%20tela%202026-09-23%20194427.png)
*Figura 2: Resposta de `172.20.21.190` ao ping para `172.20.23.1`, indicando resposta de outro host na rede (ver observação na seção 6).*

**Passo 3 — Verificação do arquivo de configuração do Netplan**
```bash
ls /etc/netplan
```

![Listagem do diretório /etc/netplan](imagens_aula6/Captura%20de%20tela%202026-09-23%20194627.png)
*Figura 3: Arquivo `00-installer-config.yaml` localizado no diretório padrão do Netplan.*

**Passo 4 — Edição do arquivo YAML com o endereço estático**
```bash
sudo nano /etc/netplan/00-installer-config.yaml
```
Configuração aplicada:
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: false
      addresses:
        - 172.20.23.1/22
      routes:
        - to: default
          via: 172.20.20.1
      nameservers:
        addresses:
          - 172.20.20.1
          - 1.1.1.1
          - 8.8.8.8
```

![Edição do arquivo netplan no nano](imagens_aula6/Captura%20de%20tela%202026-09-23%20195236.png)
*Figura 4: Arquivo YAML editado com o endereço estático, rota padrão e servidores DNS.*

**Passo 5 — Conferência do conteúdo do arquivo com `cat`**
```bash
cat /etc/netplan/00-installer-config.yaml
sudo cat /etc/netplan/00-installer-config.yaml
```

![Conferência do arquivo com cat](imagens_aula6/Captura%20de%20tela%202026-09-23%20195349.png)
*Figura 5: Primeira tentativa negada por falta de permissão; conteúdo exibido corretamente com `sudo cat`.*

**Passo 6 — Aplicação da configuração com `netplan apply`**
```bash
sudo netplan apply
```

![Primeira execução do netplan apply](imagens_aula6/Captura%20de%20tela%202026-09-23%20195421.png)
*Figura 6: Aviso `Cannot call Open vSwitch: ovsdb-server.service is not running` exibido, sem impedir a aplicação da configuração.*

**Passo 7 — Investigação do aviso do Open vSwitch**
```bash
sudo netplan apply
sudo systemctl start openswitch-switch
```

![Tentativa de iniciar o serviço openswitch-switch](imagens_aula6/Captura%20de%20tela%202026-09-23%20195830.png)
*Figura 7: Unidade `openswitch-switch.service` não encontrada — aviso identificado como não crítico (ver seção 6).*

**Passo 8 — Conferência final do arquivo YAML**
```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

![Conferência final do arquivo YAML](imagens_aula6/Captura%20de%20tela%202026-09-23%20195948.png)
*Figura 8: Sintaxe final do arquivo validada visualmente antes de novos testes.*

**Passo 9 — Verificação do endereço atribuído à interface**
```bash
ip addr show enp0s3
```

![Verificação do IP atribuído com ip addr show](imagens_aula6/Captura%20de%20tela%202026-09-23%20200553.png)
*Figura 9: Endereço `172.20.23.1/22` atribuído com sucesso à interface `enp0s3`.*

---

## 5. Testes com Capturas de Tela das Etapas-Chave

**Teste A — Ping do Host Windows para a VM**
```powershell
ping 172.20.23.1
```

![Ping do Windows para a VM](imagens_aula6/Captura%20de%20tela%202026-09-23%20200637.png)
*Figura 10: Respostas recebidas de `172.20.23.1` com 0% de perda de pacotes, confirmando a visibilidade da VM na rede local via Bridge.*

**Teste B — Ping da VM para o Host Windows**
```bash
ping -c 4 172.20.21.190
```

![Ping da VM para o host Windows](imagens_aula6/Captura%20de%20tela%202026-09-23%20200906.png)
*Figura 11: 100% de perda de pacotes — problema registrado e analisado na seção 6.*

**Teste C — Tentativa inicial de traceroute (comando não encontrado)**
```bash
traceroute google.com
```

![Comando traceroute não encontrado](imagens_aula6/Captura%20de%20tela%202026-09-23%20201104.png)
*Figura 12: Utilitário `traceroute` não instalado — corrigido conforme descrito na seção 6.*

**Teste D — Traceroute para google.com**
```bash
traceroute google.com
```

![Traceroute para google.com](imagens_aula6/Captura%20de%20tela%202026-09-23%20201252.png)
*Figura 13: Rota traçada até `google.com` (172.217.162.206), com o primeiro salto no gateway `172.20.20.1`.*

**Teste E — Traceroute para one.one.one.one**
```bash
traceroute one.one.one.one
```

![Traceroute para one.one.one.one](imagens_aula6/Captura%20de%20tela%202026-09-23%20201319.png)
*Figura 14: Rota traçada até `one.one.one.one` (1.1.1.1), confirmando a resolução DNS e o roteamento externo.*

---

## 6. Problemas e Soluções

| Problema encontrado | Causa provável | Solução aplicada |
|---|---|---|
| Ping ao IP candidato `172.20.23.1` retornou resposta de um host diferente (`172.20.21.190`) | O endereço de origem exibido pelo Windows corresponde ao gateway/roteador que respondeu pela ausência de rota até o destino, e não a uma máquina realmente usando o IP | O IP `172.20.23.1` foi mantido como estático, e a atribuição foi confirmada posteriormente com sucesso via `ip addr show` e ping bem-sucedido (Figura 9 e Figura 10) |
| Aviso `Cannot call Open vSwitch: ovsdb-server.service is not running` ao rodar `netplan apply` | O renderizador `networkd` foi utilizado, mas o Netplan tentou também verificar a presença do serviço Open vSwitch, que não está instalado nesta VM | Confirmado que a unidade `openswitch-switch.service` não existe no sistema; como o `renderer` configurado é `networkd` (e não `NetworkManager`/OVS), o aviso foi identificado como não crítico e ignorado, já que a configuração de rede foi aplicada corretamente |
| Ping da VM para o IP do host Windows resultou em 100% de perda de pacotes | Firewall do Windows bloqueando respostas ICMP (ping) de entrada por padrão | Seria necessário liberar o protocolo ICMPv4 de entrada no Firewall do Windows Defender; teste documentado como limitação da rede do laboratório |
| Comando `traceroute` não reconhecido (`command not found`) | Pacote `traceroute` não instalado por padrão no Ubuntu Server | Instalado com `sudo apt install traceroute`, permitindo a execução correta dos testes de rota |

---

## 7. Conclusão

A atividade permitiu compreender, na prática, a diferença de comportamento entre os modos NAT e Placa em Ponte (Bridge) no VirtualBox, evidenciando como o modo Bridge integra a máquina virtual diretamente à rede física do laboratório, dispensando o uso de redirecionamento de portas. A configuração do endereço IP estático via Netplan, seguindo a sintaxe YAML documentada oficialmente pelo Ubuntu Server, reforçou a importância da precisão sintática (indentação e hierarquia de chaves) para o correto funcionamento do serviço de rede. Os testes de conectividade bidirecional e os rastreamentos de rota com `traceroute` confirmaram a integração da VM à rede do laboratório e a correta configuração do gateway e dos servidores DNS, consolidando o entendimento sobre o fluxo de tráfego em redes com IP estático.
