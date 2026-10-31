# Projeto LSOR: Rede virtualizada com 8 VMs e Gateway NAT (Grupo 3)

**Turma:** bsi-26-2 (2026.2) · **Grupo 3:** 
**Integrantes:**

| Nome | Usuário da VM |
|:--|:--|
| Arthur Soares Silva | arthur.soares |
| Pedro Henrique Cavalcante Rocha | pedro.rocha |
| Sarah Amorim dos Santos | sarah.amorim |
| Marco Bueno | marco.bueno |

## 1. Apresentação

Rede privada do Grupo 3 com 8 VMs Ubuntu Server distribuídas em 4 PCs do laboratório, mais uma VM gateway que roteia e faz NAT do tráfego para a rede do laboratório. Todas as máquinas têm IP fixo, nome dentro do domínio `grupo3.bsi-26-2.maceio.lab` e os mesmos usuários.

## 2. Topologia 

<img width="1780" height="830" alt="image" src="https://github.com/user-attachments/assets/0ee64f02-1808-456d-9d0c-1c4a85203cfc" />

## 3. Endereçamento e nomes

- Rede do grupo: `192.168.26.48/28` (máscara 255.255.255.240)
- Broadcast: `192.168.26.63`
- Domínio: `grupo3.bsi-26-2.maceio.lab`
- Rede do laboratório: `172.20.20.0/22`, gateway/DNS `172.20.20.1`

| Host | PC | Interface | IP/máscara | Gateway padrão | FQDN |
|:--|:--:|:--|:--|:--|:--|
| g3-pc1-gw (WAN) | 1 | enp0s3 | 172.20.23.50/22 | 172.20.20.1 | g3-pc1-gw.grupo3.bsi-26-2.maceio.lab |
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
| g3-pc1-gw | - MB | 1 | - GB | Sim |
| g3-pc1-vm1 | - MB | 1 | - GB | Sim |
| g3-pc1-vm2 | - MB | 1 | - GB | Sim |
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

<img width="803" height="357" alt="passo-5" src="https://github.com/user-attachments/assets/d23b41c0-1bbf-4f1b-8dfc-f29bfabf65ed" />

## 6. Usuários

```bash
for u in sarah.amorim pedro.rocha arthur.soares; do
  sudo useradd -m -s /bin/bash "$u"
  sudo usermod -aG sudo "$u"
  sudo passwd "$u"
done

getent passwd sarah.amorim pedro.rocha arthur.soares
ls -l /home
```
<img width="801" height="197" alt="passo-6" src="https://github.com/user-attachments/assets/7bddf4c5-c710-4b67-90a4-3a1c4b37a926" />

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
<img width="801" height="267" alt="passo-7" src="https://github.com/user-attachments/assets/454aada0-9049-4ca9-8176-328389c2e08a" />

## 8. Netplan

Arquivo `/etc/netplan/00-installer-config.yaml`.

### Gateway (g3-pc1-gw)

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:            # WAN - rede do laboratório
      dhcp4: false
      addresses:
        - 172.20.23.50/22
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

<img width="801" height="402" alt="image" src="https://github.com/user-attachments/assets/860f9a1f-410d-40e9-a614-9661769aa397" />

## 9. Gateway: encaminhamento e NAT

Passos conforme a Aula 08:

- comandos usados para ativar ip_forward de forma persistente
- comandos das regras MASQUERADE e FORWARD
- comandos de persistência com iptables-persistent

<img width="1280" height="800" alt="VirtualBox_g3-pc1-gtw_28_10_2026_19_57_23" src="https://github.com/user-attachments/assets/3a9b93cf-c199-4921-b205-c52929c8c054" />



Verificação no gateway:

```bash
sysctl net.ipv4.ip_forward
sudo iptables -t nat -L -n -v
sudo iptables -L -n -v
```

<img width="1280" height="800" alt="VirtualBox_g3-pc1-gtw_28_10_2026_19_57_01" src="https://github.com/user-attachments/assets/b03b4371-f689-4397-923f-4f5990ca296c" />


## 10. SSH

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

<img width="800" height="484" alt="passo-11" src="https://github.com/user-attachments/assets/943e50de-112b-4526-8455-13d3a5b91adc" />

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
<img width="1280" height="800" alt="VirtualBox_g3-pc1-gtw_28_10_2026_20_02_13" src="https://github.com/user-attachments/assets/f686eaca-1f3d-4e8b-af95-2e92e2ea99ee" />

## 12. Problemas encontrados e soluções

| Problema | Causa | Solução |
|:--|:--|:--|
| | | |
