# 🌐 Deploy de Arquitetura VPC Escalável na AWS

![AWS-Cloud](https://imgur.com/AXD50yl.png)

## 📌 Sobre o Projeto

Este projeto demonstra a implementação de uma arquitetura de rede modular, escalável e altamente disponível na AWS utilizando **Terraform** para provisionamento automatizado.

A solução simula um ambiente corporativo real, com separação entre camadas administrativas e de aplicação, conectividade privada entre VPCs, controle de tráfego, observabilidade via Flow Logs e escalabilidade automatizada.

O objetivo é construir uma base de infraestrutura preparada para **workloads seguros e escaláveis em produção**.

---

# 🏗️ Arquitetura da Solução

A arquitetura é composta por duas VPCs separadas:

## 🔹 VPC 1 – Camada Administrativa (Bastion)
- CIDR: `192.168.0.0/16`
- Subnet Pública
- Bastion Host com Elastic IP
- Internet Gateway
- Security Group restritivo (porta 22)

## 🔹 VPC 2 – Camada de Aplicação
- CIDR: `172.32.0.0/16`
- Subnets Públicas e Privadas distribuídas em múltiplas AZs
- Auto Scaling Group
- Network Load Balancer
- NAT Gateway para tráfego de saída
- Instâncias privadas altamente disponíveis

## 🔹 Conectividade entre VPCs
- AWS Transit Gateway
- Comunicação privada segura entre ambientes

---

# 🧠 Decisões Arquiteturais

- Separação de VPCs para isolamento administrativo
- Uso de subnets privadas para servidores de aplicação
- NAT Gateway para permitir saída à internet sem expor instâncias
- Bastion Host para acesso SSH controlado
- Uso de Session Manager para reduzir dependência de SSH público
- VPC Flow Logs para auditoria e rastreamento de tráfego
- Auto Scaling para garantir resiliência e elasticidade

---

# ⚙️ Stack Tecnológica

- AWS VPC
- EC2
- Auto Scaling Group
- Network Load Balancer
- Transit Gateway
- NAT Gateway
- CloudWatch
- VPC Flow Logs
- IAM
- Route 53
- S3
- Apache Web Server
- AWS CLI
- AWS Systems Manager (SSM)
- Terraform >= 1.0

---

# 🔄 Pré-Deploy (Golden AMI)

Foi criada uma **Golden AMI** contendo:

- AWS CLI configurado
- Apache Web Server instalado
- Git
- CloudWatch Agent
- Script para envio de métricas customizadas de memória
- AWS SSM Agent

Esta abordagem reduz tempo de provisionamento e garante padronização das instâncias.

---

# 📂 Estrutura de Pastas Recomendada

```
terraform-vpc-project/
├── modules/
│   ├── vpc/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── bastion/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── app-layer/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── networking/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   ├── variables.tfvars
│   │   └── backend.tf
│   └── prod/
│       ├── main.tf
│       ├── variables.tfvars
│       └── backend.tf
├── scripts/
│   ├── userdata.sh
│   └── metrics.sh
├── README.md
└── .gitignore
```

**Descrição:**

- **modules/** – Módulos Terraform reutilizáveis para VPC, Bastion, camada de aplicação e networking  
- **environments/** – Configurações específicas por ambiente (dev, prod)  
- **scripts/** – Scripts de inicialização, UserData, métricas customizadas  
- **README.md** – Documentação do projeto  
- **.gitignore** – Ignorar arquivos sensíveis e estados locais do Terraform  

---

# 🚀 Provisionamento da Infraestrutura com Terraform

## 1️⃣ Inicializar Terraform

```bash
terraform init
```

## 2️⃣ Validar e Planejar

```bash
terraform plan -var-file=variables.tfvars
```

## 3️⃣ Aplicar Infraestrutura

```bash
terraform apply -var-file=variables.tfvars --auto-approve
```

### Recursos Provisionados

- Duas VPCs (Administrativa e de Aplicação)
- Subnets públicas e privadas
- Internet Gateway e NAT Gateway
- Transit Gateway e associações
- Security Groups e IAM Roles
- Auto Scaling Group
- Network Load Balancer
- Route 53 CNAME configurado
- CloudWatch Log Groups e VPC Flow Logs

---

# 📦 Camada de Aplicação

## Launch Configuration
- Base: Golden AMI
- Instance Type: `t2.micro`
- UserData:
  - Pull do código do repositório
  - Deploy no DocumentRoot
  - Start do serviço httpd
- IAM Role com:
  - Acesso ao SSM
  - Permissão restrita ao bucket S3 de configuração
- Security Group:
  - Porta 22 apenas do Bastion
  - Porta 80 pública
- Key Pair configurada via Terraform

## Auto Scaling Group
- Min: 2
- Max: 4
- Subnets privadas distribuídas em AZ 1a e 1b

## Load Balancer
- Network Load Balancer público
- Target Group associado ao ASG

## DNS
- Registro CNAME configurado no Route 53
- Domínio apontando para o NLB

---

# 🔐 Segurança Implementada

## Rede
- Princípio de menor privilégio
- Instâncias privadas não expostas à internet
- Bastion como ponto único de entrada SSH
- Session Manager para acesso seguro via console

## Monitoramento
- VPC Flow Logs habilitados
- Logs centralizados no CloudWatch
- Métricas customizadas de memória

## IAM
- Permissões mínimas necessárias
- Evitado uso de políticas amplas como S3 Full Access

---

# 📊 Validação

- Acesso às instâncias privadas via Bastion Host
- Acesso via AWS Session Manager
- Teste da aplicação via navegador público utilizando domínio configurado
- Verificação de escalabilidade automática ao gerar carga

---

# 📈 Resultados Técnicos

✔ Arquitetura modular e isolada  
✔ Comunicação privada entre VPCs  
✔ Alta disponibilidade com Auto Scaling  
✔ Controle de acesso seguro  
✔ Observabilidade implementada  
✔ Ambiente pronto para produção  

---

# 📚 Aprendizados Aplicados

- Design de redes complexas na AWS
- Estratégias de isolamento entre ambientes
- Implementação de alta disponibilidade
- Observabilidade de tráfego em nível de VPC
- Boas práticas de segurança e IAM
- Integração de DNS com Load Balancer
- Provisionamento automatizado com Terraform

---

# ⭐ Se este projeto foi útil

Considere:

- Dar uma estrela ⭐
- Compartilhar com sua rede
- Contribuir com melhorias

---

> Este projeto simula uma arquitetura corporativa real focada em escalabilidade, segurança e alta disponibilidade na AWS, provisionada de forma automatizada com Terraform.