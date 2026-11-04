# Projeto LSOR: Rede virtualizada com 8 VMs e Gateway NAT (Grupo 3)

**Turma:** bsi-26-2 (2026.2) 
**Integrantes:**

| Nome | Usuário da VM |
|:--|:--|
| Arthur Soares Silva | arthur.soares |
| Marco Bueno | marco.bueno |
| Pedro Henrique Cavalcante Rocha | pedro.rocha |
| Sarah Amorim dos Santos | sarah.amorim |


## 1. Apresentação

Rede privada do Grupo 3 com 8 VMs Ubuntu Server distribuídas em 4 PCs do laboratório, mais uma VM gateway que roteia e faz NAT do tráfego para a rede do laboratório. Todas as máquinas têm IP fixo, nome dentro do domínio `grupo3.bsi-26-2.maceio.lab` e os mesmos usuários.

## 2. Topologia 

<img width="1780" height="830" alt="Topologia do Projeto LSOR" src="https://raw.githubusercontent.com/arthur-343/imgs_trabalho/main/3164a57f-7975-4ae8-87a6-d1d05b1ba4f7.jpg" />

## 3. Endereçamento e nomes

- Rede do grupo: `192.168.26.48/28` (máscara 255.255.255.240)
- Broadcast: `192.168.26.63`
- Domínio: `grupo3.bsi-26-2.maceio.lab`
- Rede do laboratório: `172.20.20.0/22`, gateway/DNS `172.20.20.1`

| Host | PC | Interface | IP/máscara | Gateway padrão | FQDN |
|:--|:--:|:--|:--|:--|:--|
| g3-pc1-gw (WAN) | 1 | enp0s3 | 172.20.23.49/22 | 172.20.20.1 | g3-pc1-gw.grupo3.bsi-26-2.maceio.lab |
| g3-pc1-gw (LAN) | 1 | enp0s8 | 192.168.26.49/28 | — | — |
| g3-pc1-vm1 | 1 | enp0s3 | 192.168.26.50/28 | 192.168.26.49 | g3-pc1-vm1.grupo3.bsi-26-2.maceio.lab |
| g3-pc1-vm2 | 1 | enp0s3 | 192.168.26.51/28 | 192.168.26.49 | g3-pc1-vm2.grupo3.bsi-26-2.maceio.lab |
| g3-pc2-vm1 | 2 | enp0s3 | 192.168.26.52/28 | 192.168.26.49 | g3-pc2-vm1.grupo3.bsi-26-2.maceio.lab |
| g3-pc2-vm2 | 2 | enp0s3 | 192.168.26.53/28 | 192.168.26.49 | g3-pc2-vm2.grupo3.bsi-26-2.maceio.lab |
| g3-pc3-vm1 | 3 | enp0s3 | 192.168.26.54/28 | 192.168.26.49 | g3-pc3-vm1.grupo3.bsi-26-2.maceio.lab |
| g3-pc3-vm2 | 3 | enp0s3 | 192.168.26.55/28 | 192.168.26.49 | g3-pc3-vm2.grupo3.bsi-26-2.maceio.lab |
| g3-pc4-vm1 | 4 | enp0s3 | 192.168.26.56/28 | 192.168.26.49 | g3-pc4-vm1.grupo3.bsi-26-2.maceio.lab |
| g3-pc4-vm2 | 4 | enp0s3 | 192.168.26.57/28 | 192.168.26.49 | g3-pc4-vm2.grupo3.bsi-26-2.maceio.lab |

> Todas as placas usam **Placa em Ponte (Bridge)**, pois as VMs estão em 4 PCs diferentes e a Rede Interna do VirtualBox só funciona dentro de um mesmo hospedeiro.

## 4. Hardware das VMs

| VM | Memória | vCPUs | Disco | LVM |
|:--|:--|:--|:--|:--|
| g3-pc1-gw | 2048 MB | 1 | 25 GB | Sim |
| g3-pc1-vm1 | 2048 MB | 1 | 25 GB | Sim |
| g3-pc1-vm2 | 2048 MB | 1 | 25 GB | Sim |
| g3-pc2-vm1 | 2048 MB | 1 | 25 GB | Sim |
| g3-pc2-vm2 | 2048 MB | 1 | 25 GB | Sim |
| g3-pc3-vm1 | 2048 MB | 1 | 25 GB | Sim |
| g3-pc3-vm2 | 2048 MB | 1 | 32 GB | Sim |
| g3-pc4-vm1 | 2048 MB | 1 | 25 GB | Sim |
| g3-pc4-vm2 | 2048 MB | 1 | 25 GB | Sim |

Ambiente: VMs criadas especificamente para este projeto, no VirtualBox, com Ubuntu Server.

## 5. Instalação das VMs e LVM

```bash
lsblk
sudo pvs
sudo vgs
sudo lvs
df -h /
```
Este bloco de comandos analisa a estrutura de armazenamento do sistema, listando todos os discos e partições disponíveis com o ```lsblk```. Em seguida, inspeciona a hierarquia do LVM exibindo os volumes físicos (```pvs```), grupos de volumes (```vgs```) e volumes lógicos (```lvs```) existentes. Por fim, o ```df -h /``` verifica a capacidade e o espaço livre restante no sistema de arquivos da partição raiz.

<img width="813" height="563" alt="image" src="https://github.com/user-attachments/assets/a9e389d1-d4fa-4d08-898a-47bb53663569" />

## 6. Usuários

```bash
for u in sarah.amorim pedro.rocha arthur.soares marco.bueno; do
  sudo useradd -m -s /bin/bash "$u"
  sudo usermod -aG sudo "$u"
  sudo passwd "$u"
done

getent passwd sarah.amorim pedro.rocha arthur.soares marco.bueno
ls -l /home
```
Este script cria quatro utilizadores (`sarah.amorim`, `pedro.rocha`, `arthur.soares`, `marco.bueno`) com diretórios pessoais, shell Bash, privilégios de `sudo` e solicita a definição das respetivas senhas.
Em seguida, verifica a criação das contas no sistema (`getent passwd`) e confirma a existência dos seus diretórios em `/home` (`ls -l`).

<img width="927" height="300" alt="image" src="https://github.com/user-attachments/assets/7236730b-e712-4b4e-a781-517369f84b24" />

## 7. Hostname e /etc/hosts

```bash
sudo hostnamectl set-hostname g3-pc2-vm1.grupo3.bsi-26-2.maceio.lab
hostname
cat /etc/hostname
```

Conteúdo do `/etc/hosts` (igual nas 9 VMs, mudando só a linha `127.0.1.1`):

```text
127.0.1.1    <FQDN da própria VM>    <nome curto>

# Grupo 3 - bsi-26-2 - grupo3.bsi-26-2.maceio.lab
192.168.26.49    g3-pc1-gw.grupo3.bsi-26-2.maceio.lab     g3-pc1-gw
192.168.26.50    g3-pc1-vm1.grupo3.bsi-26-2.maceio.lab    g3-pc1-vm1
192.168.26.51    g3-pc1-vm2.grupo3.bsi-26-2.maceio.lab    g3-pc1-vm2
192.168.26.52    g3-pc2-vm1.grupo3.bsi-26-2.maceio.lab    g3-pc2-vm1
192.168.26.53    g3-pc2-vm2.grupo3.bsi-26-2.maceio.lab    g3-pc2-vm2
192.168.26.54    g3-pc3-vm1.grupo3.bsi-26-2.maceio.lab    g3-pc3-vm1
192.168.26.55    g3-pc3-vm2.grupo3.bsi-26-2.maceio.lab    g3-pc3-vm2
192.168.26.56    g3-pc4-vm1.grupo3.bsi-26-2.maceio.lab    g3-pc4-vm1
192.168.26.57    g3-pc4-vm2.grupo3.bsi-26-2.maceio.lab    g3-pc4-vm2
```

```bash
grep grupo3 /etc/hosts
getent hosts g3-pc1-gw
getent hosts g3-pc4-vm2.grupo3.bsi-26-2.maceio.lab
```

* **FQDN (*Fully Qualified Domain Name*):** É o nome completo e inequívoco de uma máquina na rede (ex.: `g3-pc2-vm1.grupo3.bsi-26-2.maceio.lab`). Ele é composto pelo nome do host (`g3-pc2-vm1`) seguido por toda a estrutura do domínio.
* **Hostname:** É o nome individual atribuído ao computador/VM (ex.: `g3-pc2-vm1`). Serve para identificar o dispositivo na rede de forma amigável, sem a necessidade de memorizar o endereço IP.
* **`/etc/hosts`:** É um arquivo de texto do sistema operacional que mapeia endereços IP para nomes de host manualmente. O sistema consulta este arquivo localmente antes de fazer requisições a um servidor DNS externo.
* **Resolução de nomes:** É o processo de traduzir um nome legível por humanos (como `g3-pc1-gw`) para o seu respectivo endereço IP numérico (como `192.168.26.49`), permitindo a comunicação entre as máquinas da rede.
* **`127.0.1.1`:** É um endereço IP de loopback (muito comum em distribuições como Debian e Ubuntu) reservado no `/etc/hosts` para associar o FQDN e o hostname da própria máquina local ao seu endereço interno.

<img width="801" height="267" alt="passo-7" src="https://github.com/user-attachments/assets/454aada0-9049-4ca9-8176-328389c2e08a" />

## 8. Netplan

`/etc/netplan/00-installer-config.yaml` é o arquivo de configuração de rede padrão do **Netplan** (utilitário de gerenciamento de rede usado no Ubuntu/Debian). Nele são definidos as placas de rede, endereços IP (estáticos ou via DHCP), gateways e servidores DNS do sistema em formato YAML.

### Gateway (g3-pc1-gw)

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:            # WAN - rede do laboratório
      dhcp4: false
      addresses:
        - 172.20.23.49/22
      routes:
        - to: default
          via: 172.20.20.1
      nameservers:
        addresses: [172.20.20.1, 1.1.1.1, 8.8.8.8]
    enp0s8:            # LAN - rede do grupo
      dhcp4: false
      addresses:
        - 192.168.26.49/28
```

### VMs do grupo (exemplo: g3-pc2-vm1)

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: false
      addresses:
        - 192.168.26.52/28
      routes:
        - to: default
          via: 192.168.26.49
      nameservers:
        addresses: [172.20.20.1, 1.1.1.1, 8.8.8.8]
```

```bash
sudo netplan apply
ip a
ip route
```

* **`sudo netplan apply`**  
  Aplica imediatamente as alterações feitas nos arquivos de configuração do Netplan (em `/etc/netplan/`).
* **`ip a`** *(ou `ip address`)*  
  Exibe todas as interfaces de rede do sistema com seus respectivos endereços IP, endereços MAC e status (se estão ativas/UP ou inativas/DOWN).
* **`ip route`**  
  Exibe a tabela de roteamento IP do sistema, mostrando por qual interface e gateway os pacotes de dados trafegam para alcançar outras redes e a Internet (rota *default*).
  
<img width="801" height="402" alt="image" src="https://github.com/user-attachments/assets/860f9a1f-410d-40e9-a614-9661769aa397" />

## 9. Gateway: encaminhamento e NAT

* **Gateway:** Máquina que atua como ponto de saída e ponte entre a rede interna (VMs) e a rede externa (Internet).
* **`ip_forward`:** Parâmetro do kernel Linux que habilita o roteamento de pacotes entre diferentes interfaces de rede.
* **NAT / MASQUERADE:** O NAT traduz os IPs da rede privada para o IP do Gateway. O `MASQUERADE` altera dinamicamente o IP de origem dos pacotes para permitir o acesso da rede interna à Internet.
* **Regras `FORWARD`:** Regras do firewall (`iptables`) que autorizam o tráfego de pacotes a passar através do Gateway de uma interface para outra.
* **`iptables-persistent`:** Utilitário que salva e recarrega automaticamente as regras do `iptables` durante a inicialização do sistema.

Passos conforme a Aula 08:

- comandos usados para ativar ip_forward de forma persistente
- comandos das regras MASQUERADE e FORWARD
- comandos de persistência com iptables-persistent

Verificação no gateway:

```bash
sysctl net.ipv4.ip_forward
sudo iptables -t nat -L -n -v
sudo iptables -L -n -v
```

## 10. SSH

O **SSH (Secure Shell)** é um protocolo de rede seguro que permite aceder e gerir um servidor ou máquina remota através de uma linha de comandos encriptada. Ele substitui protocolos inseguros como o Telnet, garantindo a confidencialidade e a integridade dos dados transmitidos, tais como credenciais e comandos. Além do acesso remoto, também é amplamente utilizado para a transferência segura de ficheiros (via SCP/SFTP) e o encaminhamento de portas (*port forwarding*).

```bash
sudo apt install -y openssh-server
sudo systemctl enable --now ssh
systemctl status ssh
```

<img width="802" height="461" alt="passo-10" src="https://github.com/user-attachments/assets/e2eca398-e394-4b74-8bfc-741f375c4156" />


## 11. Testes

### 11.1 Ping entre todas as VMs

Por IP, nome curto e FQDN.

```bash
ping -c 3 192.168.26.50
ping -c 3 g3-pc1-vm1
ping -c 3 g3-pc1-vm1.grupo3.bsi-26-2.maceio.lab
```

| Origem \ Destino | gw | pc1-vm1 | pc1-vm2 | pc2-vm1 | pc2-vm2 | pc3-vm1 | pc3-vm2 | pc4-vm1 | pc4-vm2 |
|:--|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| gw | — | | | | | | | | |
| pc1-vm1 | | — | | | | | | | |
| pc1-vm2 | | | — | | | | | | |
| pc2-vm1 | | | | — | | | | | |
| pc2-vm2 | | | | | — | | | | |
| pc3-vm1 | | | | | | — | | | |
| pc3-vm2 | | | | | | | — | | |
| pc4-vm1 | | | | | | | | — | |
| pc4-vm2 | | | | | | | | | — |

### 11.2 SSH entre as VMs

```bash
ssh usuario1.sobrenome@g3-pc3-vm2
```

<img width="802" height="565" alt="image" src="https://github.com/user-attachments/assets/989105b7-0800-4a11-8a95-e4edd212bb09" />


### 11.3 Internet pelo gateway

O 1º salto do `traceroute` deve ser `192.168.26.49`.

```bash
ping -c 3 1.1.1.1
ping -c 3 google.com
traceroute google.com
```
## 12. Problemas encontrados e soluções

| Problema | Causa | Solução |
|:--|:--|:--|
| | | |
