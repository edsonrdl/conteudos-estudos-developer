#tag #proxmox #virtualizacao #infraestrutura

## 1. Visão Geral
Introdução ao Proxmox VE (Hypervisor Bare-Metal Tipo 1) 
Base do Sistema (Debian)

---
## 2. Tipos de Virtualização (Motores) 
2.1 KVM (Kernel-based Virtual Machine) - Virtualização Total 
2.2 LXC (Linux Containers) - Virtualização de Sistema Operacional (Paravirtualização)

---
## 3. Armazenamento (Storage) 
3.1 Sistemas de Arquivos Locais (ZFS, LVM, LVM-Thin, BTRFS) 
3.2 Armazenamento de Rede (NFS, SMB/CIFS, iSCSI) 
3.3 Ceph (Storage Distribuído para Clusters)

---
## 4. Rede (Networking) 
4.1 Linux Bridge (Ponte padrão) 
4.2 Open vSwitch (OVS) 
4.3 VLANs (802.1Q) e Bonding (Agregação de Links) 
4.4 SDN (Software-Defined Network) no Proxmox

---
## 5. Cluster e Alta Disponibilidade (HA) 
5.1 Arquitetura Multi-Node 
5.2 Corosync e Quorum 
5.3 Fencing e Watchdog 
5.4 Live Migration (Migração a quente)

---
## 6. Backup e Recuperação 
6.1 Snapshots (KVM vs LXC) 
6.2 Vzdump (Ferramenta de backup nativa) 
6.3 Proxmox Backup Server (PBS) e Deduplicação

---
## 7. Recursos Avançados 
7.1 Passthrough de Hardware (PCIe / GPU Passthrough) 
7.2 Cloud-Init (Automação de provisionamento de VMs) 
7.3 Gestão via CLI (Comandos `qm` para VMs e `pct` para LXCs)

---
## 8. Links Relacionados 
Hipervisores (Hypervisors / VMM) 
Linux
Docker (Rodando via LXC vs VM) 
Kubernetes (Clusters bare-metal ou via VMs)