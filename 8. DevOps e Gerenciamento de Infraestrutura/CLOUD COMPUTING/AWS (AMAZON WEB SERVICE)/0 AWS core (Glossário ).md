# 📚 Guia de Estudos: AWS — Amazon Web Services
#tag #devops #cloud #aws #infraestrutura

> [!info] Visão Geral
> Este guia é um mapa arquitetural dos fundamentos da AWS, projetado para cobrir os 95% do que sustenta operações reais e escaláveis no mercado. Vai além do catálogo de serviços: aborda como os componentes interagem por baixo do capô — redes (VPC), segurança (IAM), computação, armazenamento, banco de dados, integração de aplicações e governança de custos, sempre sob a ótica da AWS Well-Architected Framework.

---

**Visão Geral** Este guia não é um mero catálogo de serviços em nuvem; é um mapa arquitetural projetado de especialista para especialista. Ele foi estruturado rigorosamente para isolar os fundamentos críticos da Amazon Web Services (AWS) e afastar o "ruído" de serviços de nicho, focando exclusivamente no que sustenta 95% das operações reais e escaláveis do mercado.

O objetivo deste material é ir além da superfície e entender como os componentes da nuvem interagem por baixo do capô. Em vez de apenas definir ferramentas, o escopo exige uma compreensão profunda das dinâmicas de rede (VPC, roteamento), do motor de avaliação de políticas de segurança (IAM) e das estratégias de desacoplamento para sistemas distribuídos.

**Aplicações Práticas e Objetivos** O domínio desta taxonomia capacita a equipe técnica a enfrentar desafios do mundo real, servindo como base para três pilares essenciais:

- **Escalabilidade Crítica e Resiliência:** Fornece as diretrizes para desenhar plataformas que exigem alta simultaneidade e disponibilidade. É o alicerce necessário para suportar, por exemplo, sistemas educacionais e plataformas de preparação para o vestibular, onde a infraestrutura precisa absorver picos massivos e repentinos de acesso usando filas (SQS), elasticidade (Auto Scaling) e separação de computação e armazenamento (Aurora/RDS), garantindo que o sistema não caia na hora que o usuário mais precisa.

- **Padronização Corporativa:** Serve como a espinha dorsal técnica para alinhar diretrizes de infraestrutura dentro de uma fábrica de software moderna focada em sistemas de inteligência avançada. Garante que todos os projetos nasçam com segurança por design, infraestrutura como código (IaC) e otimização de custos (FinOps).

- **Mentoria e Onboarding Técnico:** Atua como um roteiro definitivo para guiar a trajetória de desenvolvimento de profissionais juniores. Ao seguir esta estrutura hierárquica, o desenvolvedor em transição para pleno ou sênior compreende rapidamente as vantagens e desvantagens (o custo de cada escolha arquitetural) ao invés de se perder em tentativas e erros não documentados.

**Como Utilizar Este Guia** A estrutura segue uma taxonomia estrita de pai e filho. Recomenda-se que o avanço para o próximo domínio só ocorra após a validação conceitual do domínio anterior. Nenhum serviço deve ser estudado de forma isolada; sempre o analise sob a ótica da AWS Well-Architected Framework: _Como este serviço afeta o meu custo? Como ele impacta a segurança? O que acontece se a Zona de Disponibilidade dele falhar?_

---

## 1 Domínio: Conceitos de Nuvem

### 1.1 Definir a Nuvem AWS e sua proposta de valor
- **1.1.1. Benefícios da computação em nuvem**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.1 Definir a Nuvem AWS e sua proposta de valor/1.1.1. Benefícios da computação em nuvem/1.1.1.1. Economia de escala|1.1.1.1. Economia de escala]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.1 Definir a Nuvem AWS e sua proposta de valor/1.1.1. Benefícios da computação em nuvem/1.1.1.2. Agilidade e velocidade|1.1.1.2. Agilidade e velocidade]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.1 Definir a Nuvem AWS e sua proposta de valor/1.1.1. Benefícios da computação em nuvem/1.1.1.3. Elasticidade e escalabilidade|1.1.1.3. Elasticidade e escalabilidade]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.1 Definir a Nuvem AWS e sua proposta de valor/1.1.1. Benefícios da computação em nuvem/1.1.1.4. Alta disponibilidade e tolerância a falhas|1.1.1.4. Alta disponibilidade e tolerância a falhas]]
- **1.1.2. Modelo de despesas (CapEx vs OpEx)**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.1 Definir a Nuvem AWS e sua proposta de valor/1.1.2. Modelo de despesas (CapEx vs OpEx)/1.1.2.1. CapEx vs OpEx — como a nuvem muda o modelo financeiro de TI|1.1.2.1. CapEx vs OpEx — como a nuvem muda o modelo financeiro de TI]]
- **1.1.3. Princípios de design da AWS Well-Architected Framework**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.1 Definir a Nuvem AWS e sua proposta de valor/1.1.3. Princípios de design da AWS Well-Architected Framework/1.1.3.1. Excelência operacional|1.1.3.1. Excelência operacional]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.1 Definir a Nuvem AWS e sua proposta de valor/1.1.3. Princípios de design da AWS Well-Architected Framework/1.1.3.2. Segurança|1.1.3.2. Segurança]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.1 Definir a Nuvem AWS e sua proposta de valor/1.1.3. Princípios de design da AWS Well-Architected Framework/1.1.3.3. Confiabilidade|1.1.3.3. Confiabilidade]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.1 Definir a Nuvem AWS e sua proposta de valor/1.1.3. Princípios de design da AWS Well-Architected Framework/1.1.3.4. Eficiência de performance|1.1.3.4. Eficiência de performance]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.1 Definir a Nuvem AWS e sua proposta de valor/1.1.3. Princípios de design da AWS Well-Architected Framework/1.1.3.5. Otimização de custos|1.1.3.5. Otimização de custos]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.1 Definir a Nuvem AWS e sua proposta de valor/1.1.3. Princípios de design da AWS Well-Architected Framework/1.1.3.6. Sustentabilidade|1.1.3.6. Sustentabilidade]]

### 1.2 Identificar aspectos da economia da nuvem
- **1.2.1. Modelos e ferramentas de custo**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.2 Identificar aspectos da economia da nuvem/1.2.1. Modelos e ferramentas de custo/1.2.1.1. Modelo de precificação Pay-as-you-go|1.2.1.1. Modelo de precificação Pay-as-you-go]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.2 Identificar aspectos da economia da nuvem/1.2.1. Modelos e ferramentas de custo/1.2.1.2. Total Cost of Ownership (TCO)|1.2.1.2. Total Cost of Ownership (TCO)]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.2 Identificar aspectos da economia da nuvem/1.2.1. Modelos e ferramentas de custo/1.2.1.3. Calculadoras de custos AWS|1.2.1.3. Calculadoras de custos AWS]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.2 Identificar aspectos da economia da nuvem/1.2.1. Modelos e ferramentas de custo/1.2.1.4. Conceito de Right Sizing|1.2.1.4. Conceito de Right Sizing]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.2 Identificar aspectos da economia da nuvem/1.2.1. Modelos e ferramentas de custo/1.2.1.5. Benefícios da automação|1.2.1.5. Benefícios da automação]]

### 1.3 Explicar os diferentes princípios de design de nuvem
- **1.3.1. Princípios fundamentais**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.3 Explicar os diferentes princípios de design de nuvem/1.3.1. Princípios fundamentais/1.3.1.1. Design para falhas|1.3.1.1. Design para falhas]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.3 Explicar os diferentes princípios de design de nuvem/1.3.1. Princípios fundamentais/1.3.1.2. Desacoplamento de componentes|1.3.1.2. Desacoplamento de componentes]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.3 Explicar os diferentes princípios de design de nuvem/1.3.1. Princípios fundamentais/1.3.1.3. Implementação de elasticidade|1.3.1.3. Implementação de elasticidade]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/1. Conceitos de Nuvem/1.3 Explicar os diferentes princípios de design de nuvem/1.3.1. Princípios fundamentais/1.3.1.4. Paralelização|1.3.1.4. Paralelização]]

---

## 2 Domínio: Segurança e Conformidade

### 2.1 Definir o modelo de responsabilidade compartilhada da AWS
- **2.1.1. Divisão de responsabilidades**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.1 Definir o modelo de responsabilidade compartilhada da AWS/2.1.1. Divisão de responsabilidades/2.1.1.1. Responsabilidades da AWS (segurança DA nuvem)|2.1.1.1. Responsabilidades da AWS (segurança DA nuvem)]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.1 Definir o modelo de responsabilidade compartilhada da AWS/2.1.1. Divisão de responsabilidades/2.1.1.2. Responsabilidades do cliente (segurança NA nuvem)|2.1.1.2. Responsabilidades do cliente (segurança NA nuvem)]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.1 Definir o modelo de responsabilidade compartilhada da AWS/2.1.1. Divisão de responsabilidades/2.1.1.3. Controles herdados, compartilhados e do cliente|2.1.1.3. Controles herdados, compartilhados e do cliente]]

### 2.2 Conceitos de segurança e conformidade na nuvem
- **2.2.1. Conformidade**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.2 Conceitos de segurança e conformidade na nuvem/2.2.1. Conformidade/2.2.1.1. AWS Compliance Programs|2.2.1.1. AWS Compliance Programs]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.2 Conceitos de segurança e conformidade na nuvem/2.2.1. Conformidade/2.2.1.2. AWS Artifact|2.2.1.2. AWS Artifact]]
- **2.2.2. Princípios de segurança**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.2 Conceitos de segurança e conformidade na nuvem/2.2.2. Princípios de segurança/2.2.2.1. Princípio do menor privilégio|2.2.2.1. Princípio do menor privilégio]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.2 Conceitos de segurança e conformidade na nuvem/2.2.2. Princípios de segurança/2.2.2.2. Defense in depth|2.2.2.2. Defense in depth]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.2 Conceitos de segurança e conformidade na nuvem/2.2.2. Princípios de segurança/2.2.2.3. Segregação de funções|2.2.2.3. Segregação de funções]]

### 2.3 Capacidades de gerenciamento de acesso da AWS
- **2.3.1. Identity and Access Management (IAM)**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.3 Capacidades de gerenciamento de acesso da AWS/2.3.1. Identity and Access Management (IAM)/2.3.1.1. Usuários, Grupos e Roles|2.3.1.1. Usuários, Grupos e Roles]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.3 Capacidades de gerenciamento de acesso da AWS/2.3.1. Identity and Access Management (IAM)/2.3.1.2. Políticas (Policies)|2.3.1.2. Políticas (Policies)]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.3 Capacidades de gerenciamento de acesso da AWS/2.3.1. Identity and Access Management (IAM)/2.3.1.3. Multi-Factor Authentication (MFA)|2.3.1.3. Multi-Factor Authentication (MFA)]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.3 Capacidades de gerenciamento de acesso da AWS/2.3.1. Identity and Access Management (IAM)/2.3.1.4. IAM Identity Center (SSO)|2.3.1.4. IAM Identity Center (SSO)]]
- **2.3.2. Serviços de segurança**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.3 Capacidades de gerenciamento de acesso da AWS/2.3.2. Serviços de segurança/2.3.2.1. AWS Organizations|2.3.2.1. AWS Organizations]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.3 Capacidades de gerenciamento de acesso da AWS/2.3.2. Serviços de segurança/2.3.2.2. AWS Shield (Standard e Advanced)|2.3.2.2. AWS Shield (Standard e Advanced)]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.3 Capacidades de gerenciamento de acesso da AWS/2.3.2. Serviços de segurança/2.3.2.3. AWS WAF|2.3.2.3. AWS WAF]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.3 Capacidades de gerenciamento de acesso da AWS/2.3.2. Serviços de segurança/2.3.2.4. Amazon GuardDuty|2.3.2.4. Amazon GuardDuty]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.3 Capacidades de gerenciamento de acesso da AWS/2.3.2. Serviços de segurança/2.3.2.5. AWS Security Hub|2.3.2.5. AWS Security Hub]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.3 Capacidades de gerenciamento de acesso da AWS/2.3.2. Serviços de segurança/2.3.2.6. Amazon Inspector|2.3.2.6. Amazon Inspector]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.3 Capacidades de gerenciamento de acesso da AWS/2.3.2. Serviços de segurança/2.3.2.7. AWS Key Management Service (KMS)|2.3.2.7. AWS Key Management Service (KMS)]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.3 Capacidades de gerenciamento de acesso da AWS/2.3.2. Serviços de segurança/2.3.2.8. AWS Secrets Manager|2.3.2.8. AWS Secrets Manager]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.3 Capacidades de gerenciamento de acesso da AWS/2.3.2. Serviços de segurança/2.3.2.9. AWS Certificate Manager|2.3.2.9. AWS Certificate Manager]]

### 2.4 Recursos de suporte à segurança
- **2.4.1. Ferramentas de governança**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.4 Recursos de suporte à segurança/2.4.1. Ferramentas de governança/2.4.1.1. AWS Trusted Advisor|2.4.1.1. AWS Trusted Advisor]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/2. Segurança e Conformidade/2.4 Recursos de suporte à segurança/2.4.1. Ferramentas de governança/2.4.1.2. AWS Well-Architected Tool|2.4.1.2. AWS Well-Architected Tool]]

---

## 3 Domínio: Tecnologia e Serviços

### 3.1 Métodos de implantação e operação na AWS
- **3.1.1. Modelos de implantação**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.1 Métodos de implantação e operação na AWS/3.1.1. Modelos de implantação/3.1.1.1. All-in cloud|3.1.1.1. All-in cloud]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.1 Métodos de implantação e operação na AWS/3.1.1. Modelos de implantação/3.1.1.2. Híbrido|3.1.1.2. Híbrido]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.1 Métodos de implantação e operação na AWS/3.1.1. Modelos de implantação/3.1.1.3. On-premises|3.1.1.3. On-premises]]
- **3.1.2. Opções de conectividade**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.1 Métodos de implantação e operação na AWS/3.1.2. Opções de conectividade/3.1.2.1. VPN|3.1.2.1. VPN]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.1 Métodos de implantação e operação na AWS/3.1.2. Opções de conectividade/3.1.2.2. AWS Direct Connect|3.1.2.2. AWS Direct Connect]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.1 Métodos de implantação e operação na AWS/3.1.2. Opções de conectividade/3.1.2.3. Internet pública|3.1.2.3. Internet pública]]

### 3.2 Infraestrutura global da AWS
- **3.2.1. Componentes**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.2 Infraestrutura global da AWS/3.2.1. Componentes/3.2.1.1. Regiões (Regions)|3.2.1.1. Regiões (Regions)]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.2 Infraestrutura global da AWS/3.2.1. Componentes/3.2.1.2. Zonas de Disponibilidade (AZs)|3.2.1.2. Zonas de Disponibilidade (AZs)]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.2 Infraestrutura global da AWS/3.2.1. Componentes/3.2.1.3. Edge Locations|3.2.1.3. Edge Locations]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.2 Infraestrutura global da AWS/3.2.1. Componentes/3.2.1.4. Local Zones|3.2.1.4. Local Zones]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.2 Infraestrutura global da AWS/3.2.1. Componentes/3.2.1.5. Wavelength Zones|3.2.1.5. Wavelength Zones]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.2 Infraestrutura global da AWS/3.2.1. Componentes/3.2.1.6. AWS Outposts|3.2.1.6. AWS Outposts]]
- **3.2.2. Benefícios**
  - [x] [[3.2.2.1. Alta disponibilidade|3.2.2.1. Alta disponibilidade]]
  - [x] [[3.2.2.2. Baixa latência|3.2.2.2. Baixa latência]]
  - [x] [[3.2.2.3. Recuperação de desastres|3.2.2.3. Recuperação de desastres]]

### 3.3 Serviços principais da AWS
- **3.3.1. Computação**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.1. Amazon EC2/0 Amazon EC2 (Tópicos )|3.3.1.1. Amazon EC2]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.2. AWS Lambda/0 AWS Lambda (Tópicos )|3.3.1.2. AWS Lambda]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.3. Amazon ECS/0 Amazon ECS (Tópicos )|3.3.1.3. Amazon ECS]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.4. Amazon EKS/0 Amazon EKS (Tópicos )|3.3.1.4. Amazon EKS]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.5. AWS Elastic Beanstalk/0 AWS Elastic Beanstalk (Tópicos )|3.3.1.5. AWS Elastic Beanstalk]]
  - [ ] 3.3.1.6. Amazon Lightsail
  - [ ] 3.3.1.7. AWS Batch
- **3.3.2. Armazenamento**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.2. Armazenamento/3.3.2.1. Amazon S3/0 Amazon S3 (Tópicos )|3.3.2.1. Amazon S3]]
  - [ ] 3.3.2.2. Amazon EBS
  - [ ] 3.3.2.3. Amazon EFS
  - [ ] 3.3.2.4. AWS Storage Gateway
  - [ ] 3.3.2.5. Amazon FSx
  - [ ] 3.3.2.6. AWS Backup
- **3.3.3. Banco de Dados**
  - [ ] 3.3.3.1. Amazon RDS
  - [ ] 3.3.3.2. Amazon DynamoDB
  - [ ] 3.3.3.3. Amazon Aurora
  - [ ] 3.3.3.4. Amazon ElastiCache
  - [ ] 3.3.3.5. Amazon Redshift
  - [ ] 3.3.3.6. Amazon DocumentDB
  - [ ] 3.3.3.7. Amazon Neptune
- **3.3.4. Rede**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.4. Rede/3.3.4.1. Amazon VPC/0 Amazon VPC (Tópicos )|3.3.4.1. Amazon VPC]]
  - [ ] 3.3.4.2. Subnets (públicas e privadas)
  - [ ] 3.3.4.3. Security Groups e NACLs
  - [ ] 3.3.4.4. Internet Gateway e NAT Gateway
  - [ ] 3.3.4.5. Amazon Route 53
  - [ ] 3.3.4.6. Amazon CloudFront
  - [ ] 3.3.4.7. Elastic Load Balancing
  - [ ] 3.3.4.8. AWS Global Accelerator
- **3.3.5. Gerenciamento e Governança**
  - [ ] 3.3.5.1. AWS CloudWatch
  - [ ] 3.3.5.2. AWS CloudTrail
  - [ ] 3.3.5.3. AWS Config
  - [ ] 3.3.5.4. AWS Systems Manager
  - [ ] 3.3.5.5. AWS CloudFormation
  - [ ] 3.3.5.6. AWS Service Catalog
  - [ ] 3.3.5.7. AWS Control Tower
- **3.3.6. Análise e Analytics**
  - [ ] 3.3.6.1. Amazon Athena
  - [ ] 3.3.6.2. Amazon Kinesis
  - [ ] 3.3.6.3. Amazon QuickSight
  - [ ] 3.3.6.4. AWS Glue
- **3.3.7. Integração de Aplicações**
  - [ ] 3.3.7.1. Amazon SQS
  - [ ] 3.3.7.2. Amazon SNS
  - [ ] 3.3.7.3. Amazon EventBridge
  - [ ] 3.3.7.4. AWS Step Functions
- **3.3.8. Migração e Transferência**
  - [ ] 3.3.8.1. AWS Migration Hub
  - [ ] 3.3.8.2. AWS Database Migration Service
  - [ ] 3.3.8.3. AWS DataSync
  - [ ] 3.3.8.4. AWS Snow Family

---

## 4 Domínio: Faturamento, Preços e Suporte

### 4.1 Modelos de preços da AWS
- **4.1.1. Modelos de instância EC2**
  - [ ] 4.1.1.1. On-Demand
  - [ ] 4.1.1.2. Reserved Instances
  - [ ] 4.1.1.3. Savings Plans
  - [ ] 4.1.1.4. Spot Instances
  - [ ] 4.1.1.5. Dedicated Hosts
- **4.1.2. Outros modelos de preço**
  - [ ] 4.1.2.1. Modelos de preços de armazenamento
  - [ ] 4.1.2.2. Preços de transferência de dados
  - [ ] 4.1.2.3. AWS Free Tier (tipos e limites)

### 4.2 Recursos de gerenciamento de custos
- **4.2.1. Ferramentas de custo**
  - [ ] 4.2.1.1. AWS Budgets
  - [ ] 4.2.1.2. AWS Cost Explorer
  - [ ] 4.2.1.3. AWS Cost and Usage Report
  - [ ] 4.2.1.4. AWS Billing Dashboard
  - [ ] 4.2.1.5. Tags para alocação de custos
  - [ ] 4.2.1.6. Consolidated Billing

### 4.3 Planos de suporte da AWS
- **4.3.1. Níveis de suporte**
  - [ ] 4.3.1.1. Basic
  - [ ] 4.3.1.2. Developer
  - [ ] 4.3.1.3. Business
  - [ ] 4.3.1.4. Enterprise On-Ramp
  - [ ] 4.3.1.5. Enterprise
- **4.3.2. Recursos de cada plano**
  - [ ] 4.3.2.1. Tempos de resposta
  - [ ] 4.3.2.2. Technical Account Manager (TAM)
  - [ ] 4.3.2.3. AWS Trusted Advisor (verificações disponíveis)
  - [ ] 4.3.2.4. AWS Support API

### 4.4 Recursos de suporte técnico
- **4.4.1. Documentação e comunidade**
  - [ ] 4.4.1.1. AWS Documentation e Whitepapers
  - [ ] 4.4.1.2. AWS Knowledge Center e re:Post
  - [ ] 4.4.1.3. AWS Professional Services e Partner Network (APN)
  - [ ] 4.4.1.4. AWS Marketplace

---

> **Links Relacionados:**
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Virtualização/0 Virtualização (Tópicos )|Virtualização]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/0 DNS (Tópicos )|DNS]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/0 Firewalls (Tópicos )|Firewalls]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/VPN/0 VPN (Tópicos )|VPN]]
