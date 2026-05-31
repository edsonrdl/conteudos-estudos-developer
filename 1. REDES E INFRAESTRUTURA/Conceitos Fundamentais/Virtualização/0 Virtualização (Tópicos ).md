# 📚 Guia de Estudos: Virtualização
#tag #redes #infraestrutura #virtualização #cloud

> [!info] Visão Geral
> Virtualização é a tecnologia que abstrai recursos físicos (servidores, rede, storage) criando versões lógicas gerenciáveis por software. É a fundação técnica da computação em nuvem. Este guia cobre desde os fundamentos e tipos de hypervisor, passando por VMs e contêineres, até as plataformas enterprise (VMware, Hyper-V, KVM) e as operações avançadas de produção (live migration, snapshots, HA).

---

## 1 Domínio: Fundamentos de Virtualização

### 1.1 O que é Virtualização
- **1.1.1. Definição e Propósito**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Virtualização/1. Fundamentos de Virtualização/1.1 O que é Virtualização/1.1.1. Definição e Propósito/1.1.1.1. O que é virtualização, como abstrai hardware e por que revolucionou a infraestrutura de TI|1.1.1.1. O que é virtualização, como abstrai hardware e por que revolucionou a infraestrutura de TI]]

### 1.2 Hypervisor
- **1.2.1. Tipos de Hypervisor**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Virtualização/1. Fundamentos de Virtualização/1.2 Hypervisor/1.2.1. Tipos de Hypervisor/1.2.1.1. Hypervisor Tipo 1 (bare-metal) vs Tipo 2 (hosted) — diferenças, exemplos e quando usar cada um|1.2.1.1. Hypervisor Tipo 1 (bare-metal) vs Tipo 2 (hosted) — diferenças, exemplos e quando usar cada um]]

---

## 2 Domínio: Tipos de Virtualização

### 2.1 Virtualização de Servidores
- **2.1.1. VMs (Virtual Machines)**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Virtualização/2. Tipos de Virtualização/2.1 Virtualização de Servidores/2.1.1. VMs (Virtual Machines)/2.1.1.1. Máquinas virtuais — isolamento completo, overhead e casos de uso em produção|2.1.1.1. Máquinas virtuais — isolamento completo, overhead e casos de uso em produção]]

### 2.2 Virtualização de Contêineres
- **2.2.1. Contêineres vs VMs**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Virtualização/2. Tipos de Virtualização/2.2 Virtualização de Contêineres/2.2.1. Contêineres vs VMs/2.2.1.1. Contêineres vs VMs — isolamento por namespace vs hypervisor, densidade e trade-offs|2.2.1.1. Contêineres vs VMs — isolamento por namespace vs hypervisor, densidade e trade-offs]]

### 2.3 Outros Tipos
- **2.3.1. Virtualização de Rede e Storage**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Virtualização/2. Tipos de Virtualização/2.3 Outros Tipos/2.3.1. Virtualização de Rede e Storage/2.3.1.1. SDN (Software-Defined Networking) e SDS — virtualização além do servidor|2.3.1.1. SDN (Software-Defined Networking) e SDS — virtualização além do servidor]]

---

## 3 Domínio: Tecnologias e Plataformas

### 3.1 Hypervisors Populares
- **3.1.1. VMware e Hyper-V**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Virtualização/3. Tecnologias e Plataformas/3.1 Hypervisors Populares/3.1.1. VMware e Hyper-V/3.1.1.1. VMware vSphere e Microsoft Hyper-V — soluções enterprise de virtualização|3.1.1.1. VMware vSphere e Microsoft Hyper-V — soluções enterprise de virtualização]]
- **3.1.2. KVM e QEMU**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Virtualização/3. Tecnologias e Plataformas/3.1 Hypervisors Populares/3.1.2. KVM e QEMU/3.1.2.1. KVM e QEMU — virtualização open source no Linux, base das nuvens públicas|3.1.2.1. KVM e QEMU — virtualização open source no Linux, base das nuvens públicas]]

### 3.2 Contêineres
- **3.2.1. Docker e OCI**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Virtualização/3. Tecnologias e Plataformas/3.2 Contêineres/3.2.1. Docker e OCI/3.2.1.1. Docker e o padrão OCI — imagens, contêineres e o ecossistema de empacotamento|3.2.1.1. Docker e o padrão OCI — imagens, contêineres e o ecossistema de empacotamento]]

---

## 4 Domínio: Virtualização na Nuvem e Casos Práticos

### 4.1 Cloud e Virtualização
- **4.1.1. IaaS e a abstração de hardware**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Virtualização/4. Virtualização na Nuvem e Casos Práticos/4.1 Cloud e Virtualização/4.1.1. IaaS e a abstração de hardware/4.1.1.1. Como IaaS (AWS EC2, Azure VM, GCP Compute) usa virtualização para entregar infraestrutura sob demanda|4.1.1.1. Como IaaS (AWS EC2, Azure VM, GCP Compute) usa virtualização para entregar infraestrutura sob demanda]]
- **4.1.2. Live Migration e Alta Disponibilidade**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Virtualização/4. Virtualização na Nuvem e Casos Práticos/4.1 Cloud e Virtualização/4.1.2. Live Migration e Alta Disponibilidade/4.1.2.1. Live migration de VMs, snapshots e HA — operações avançadas de virtualização em produção|4.1.2.1. Live migration de VMs, snapshots e HA — operações avançadas de virtualização em produção]]

---

> **Links Relacionados:**
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Proxy/0 Proxy (Tópicos )|Proxy]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/0 Firewalls (Tópicos )|Firewalls]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/VPN/0 VPN (Tópicos )|VPN]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/0 TCP IP (Tópicos )|TCP/IP]]
