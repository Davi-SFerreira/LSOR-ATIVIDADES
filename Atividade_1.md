# Relatório de Atividade Prática — Laboratório de Sistemas Operacionais e Redes

## 1. Identificação

- **Aluno(a):** Davi da Silva Ferreira
- **Matrícula:** 2023011930
- **Disciplina:** Laboratório de Sistemas Operacionais e Redes
- **Professor(a):** Alaelson
- **Data de realização:** 12/08/2026
- **Título da atividade:** Configuração de uma Máquina Virtual (VM) com VirtualBox

---

## 2. Objetivo

O objetivo desta atividade prática foi criar e configurar uma máquina virtual utilizando o software Oracle VirtualBox, instalando o sistema operacional Ubuntu (Linux) e realizando os ajustes básicos de hardware virtual e de rede necessários para o funcionamento adequado da VM em um ambiente de laboratório. A atividade também visou consolidar conceitos de virtualização, alocação de recursos (CPU, memória, disco) e configuração de interfaces de rede virtuais (NAT, Bridge ou Rede Interna).

---

## 3. Ambiente

Para a realização da atividade, foram utilizados os seguintes recursos:

- **Máquina física (hospedeira/host):**
  - Sistema operacional: Windows 11 
  - Processador: AMD Ryzen 7 PRO 8700GE
  - Memória RAM: 16Gb
  - Espaço em disco disponível: 32Gb

- **Software de virtualização:**
  - Oracle VirtualBox

- **Sistema operacional convidado (guest):**
  - Ubuntu 22.04 Server

- **Configuração da máquina virtual:**
  - Memória RAM alocada: 2048MB
  - Núcleos de CPU: 1
  - Espaço em disco virtual: 32 GB, tipo VDI

---

## 4. Procedimento

A seguir estão descritas as etapas realizadas para a configuração da máquina virtual:

1. **Download e instalação do VirtualBox**
   Foi realizado o download do instalador do VirtualBox a partir do site oficial e a instalação seguiu o assistente padrão, aceitando os componentes de rede (adaptadores virtuais) exigidos pelo software.

2. **Download da imagem ISO do sistema operacional**
   A imagem ISO do Ubuntu foi baixada do site oficial, garantindo a integridade do arquivo.

3. **Criação da nova máquina virtual**
   No VirtualBox, foi utilizada a opção "Nova" para criar a VM.
   
4. **Alocação de recursos de hardware**
   Foram definidas as quantidades de memória RAM e núcleos de processador conforme a disponibilidade da máquina hospedeira, buscando equilíbrio entre desempenho da VM e da máquina física.

5. **Criação do disco rígido virtual**
   Foi criado um disco virtual no formato VDI, com alocação dinâmica, definindo o tamanho máximo do disco.

6. **Configuração da unidade óptica (ISO de boot)**
   A imagem ISO do Ubuntu foi associada à unidade óptica virtual da VM para permitir a inicialização e instalação do sistema.

7. **Configuração da interface de rede**
   O adaptador de rede da VM foi configurado no modo [NAT/Bridge/Rede Interna], permitindo que a VM tivesse acesso à rede [externa/apenas entre VMs, conforme o caso].

8. **Instalação do sistema operacional**
   A VM foi iniciada e o processo de instalação do Ubuntu foi conduzido, incluindo: seleção de idioma, particionamento de disco, criação de usuário e senha, e conclusão da instalação.

9. **Instalação do VirtualBox Guest Additions**
   Após a primeira inicialização do sistema, foram instalados os Guest Additions para melhorar a integração entre host e guest (resolução de tela, compartilhamento de área de transferência, etc.).

10. **Testes de conectividade**
    Foram realizados testes básicos de rede (como `ping`) para validar se a VM possuía acesso à rede configurada.

---

## 5. Testes com Capturas de Tela das Etapas-Chave

<img width="830" height="653" alt="image" src="https://github.com/user-attachments/assets/4245cdab-9488-4ee9-a71a-02640aaefc95" />
<img width="522" height="114" alt="image" src="https://github.com/user-attachments/assets/8f5540fd-b95c-4aeb-8451-fc0dba840719" />

---

## 6. Problemas e Soluções

Não houve problema neste tópico.

---

## 7. Conclusão

A atividade prática permitiu compreender, de forma aplicada, o processo de virtualização de sistemas operacionais, desde a instalação do hipervisor (VirtualBox). Foi possível consolidar conhecimentos sobre alocação de recursos de hardware virtual (CPU, memória e disco), essenciais para o estudo de redes de computadores em laboratório. A experiência reforçou a importância da virtualização como ferramenta de estudo e testes em ambientes controlados, sem a necessidade de hardware físico dedicado para cada sistema operacional.
