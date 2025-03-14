# Projeto Final  #PB - NOV 2024 | DevSecOps


## Contexto
A **Fast Engineering S/A** , possui um e-Commerce que está crescendo e a solução atual não está atendendo mais a alta demanda de acessos e compras.

Servidores atuais:
- 01 servidor para Banco de Dados Mysql (500GB de dados, 10Gb de RAM, 3 Core CPU);
- 01 servidor para a aplicação utilizando REACT – frontend (5GB de dados, 2Gb de RAM, 1 Core CPU);
- 01 servidor de backend com 3 APIs, com o Nginx servindo de balanceador de carga e que armazena estáticos como fotos e links. (5GB de dados, 4Gb de RAM, 2 Core CPU);

### Diagrama da situalção atual

![Image](https://github.com/user-attachments/assets/8c9c073d-3c98-476d-8253-459a36aa8c6e)

A proposta é realizar a migração de seu ambiente para a AWS, separado em duas etapas:

1. Migração para AWS (Lift-and-Shift)
2. Modernização para AWS (EKS e Serviços Gerenciados)
## Etapa 1. Migração para AWS (Lift-and-Shift)

### Atividades Necessárias para a Migração
1. Levantamento dos recursos on-premises e planejamento da migração.
2. Configuração do AWS Application Migration Service (MGN) para replicação de servidores.
3. Configuração do AWS Database Migration Service (DMS) para migração do banco de dados.
4. Criação das VPCs, subnets e grupos de segurança necessários.
5. Execução da replicação e verificação da integridade dos dados migrados.
6. Testes e validação dos servidores e banco de dados migrados e EFS objetos estáticos.
7. Descomissionamento do ambiente antigo após a validação.

### Ferramentas Utilizadas
- **AWS MGN (Application Migration Service)** - Para migrar servidores do ambiente on-premises para EC2.
- **AWS DMS (Database Migration Service)** - Para migrar e replicar o banco de dados MySQL para RDS.
- **Amazon EC2** - Para hospedar as instâncias do frontend e backend.
- **Amazon RDS** - Para banco de dados gerenciado com alta disponibilidade.
- **Amazon S3** - Para armazenamento de arquivos estáticos.
- **AWS IAM, Security Groups e Network ACLs** - Para controle de segurança e acesso.
- **AWS Cost Calculator** - Para estimativa de custos.

### Diagrama da Infraestrutura na AWS (Lift-and-Shift)
![Image](https://github.com/user-attachments/assets/2b9efcc9-7903-4b6a-b837-7ad84ec157af)
#### Descrição do Diagrama
O ambiente consiste em uma VPC com múltiplas subnets e zonas de disponibilidade (AZs):
1. **Fase de Preparação (On-Premises para AWS):**
   - O **AWS Replication Agent** é instalado nos servidores on-premises, permitindo a captura contínua das mudanças dos discos e enviando para a AWS.
   - O **AWS MGN** recebe essas replicações e mantém um estado atualizado na AWS para garantir uma migração rápida e sem downtime significativo.
   - Uma vez que os servidores EC2 na AWS estão sincronizados, o tráfego pode ser redirecionado e os servidores antigos podem ser desativados.

2. **Migração do Banco de Dados:**
   - O **AWS DMS** é utilizado para migrar o banco de dados MySQL on-premises para o **Amazon RDS**, garantindo replicação contínua até a virada de chave.
   - A replicação permite testes antes da migração definitiva, minimizando riscos de perda de dados.

3. **Infraestrutura AWS Pós-Migração:**
   - Instâncias **EC2** para frontend e backend, cada uma conectada a volumes **EBS** e **ASG**.
   - Um banco de dados **Amazon RDS** configurado em **Multi-AZ** para garantir disponibilidade.
   - Um **Application Load Balancer (ALB)** para balanceamento de carga entre as instâncias EC2.
   - **NAT Gateway** para permitir comunicação segura entre servidores privados e serviços externos.
   - **Route 53** e **CloudFront** para distribuição e roteamento de tráfego global.
   - **WAF** para proteção contra ataques na camada de aplicação.

![Image](https://github.com/user-attachments/assets/77f70c1c-40d1-443b-88df-e410c5bb00bc)
Diagrama da infraestrutura migrada.
### Segurança
- **Separação de rede com VPC, subnets privadas e públicas.**
- **Uso de Security Groups e Network ACLs** para controle de tráfego entre instâncias.
- **Monitoramento com AWS GuardDuty e AWS CloudTrail.**
- **Proteção contra ataques DDoS com AWS WAF e AWS Shield.**
- **Uso de IAM para controle de permissões e autenticação.**

### Backup
- **Amazon S3** para armazenar snapshots e backups.
- **Snapshots de EBS e RDS** para recuperação rápida em caso de falha.

### Custo da Infraestrutura na AWS
Cálculo dos custos estimados dos serviços na AWS utilizando o **Pricing Calculator.**
![Image](https://github.com/user-attachments/assets/0535439f-6554-4908-804b-b0b6bc3a3057)
Estimativa de custo da migração. [Estimativa Migração](/Estimativa Migração.pdf)
![Image](https://github.com/user-attachments/assets/83307d63-75f3-4150-9d6c-ff35c7b33361)
Estimativa da infraestrutura migrada. [Estimativa Migrada](/Estimativa Migrada.pdf)

---

## Etapa 2. Modernização para AWS (EKS e Serviços Gerenciados)

### Atividades Necessárias para a Modernização
1. Configuração da infraestrutura como código com Terraform e CloudFormation.
2. Criação do cluster **Kubernetes** no **Amazon EKS**.
3. Configuração do CI/CD usando **GitHub**, **CodePipeline**, **CodeBuild** e **ECR**.
4. Implantação do **back-end** e **front-end** como microservices em pods do **EKS**.
5. Configuração do autoescalonamento de pods e nodes (**HPA** e **ASG**).
6. Configuração de armazenamento persistente para banco de dados e objetos estáticos.
7. Implementação de monitoramento, segurança e backup.

### Ferramentas Utilizadas
- **Amazon EKS (Elastic Kubernetes Service)** - Para orquestração de containers.
- **Amazon RDS (Multi-AZ)** - Para banco de dados gerenciado.
- **Amazon S3** - Para armazenamento de imagens e objetos estáticos.
- **AWS CodePipeline + CodeBuild + ECR** - Para pipeline de CI/CD.
- **AWS Auto Scaling** - Para escalar aplicações automaticamente.
- **AWS IAM e Secrets Manager** - Para gerenciamento de credenciais e permissões.
- **AWS CloudWatch e GuardDuty** - Para monitoramento e segurança.
- **AWS EFS** - O EFS é usado para armazenamento persistente de dados compartilhados entre os pods do EKS.Ele é integrado ao Kubernetes através de Persistent Volumes (PV) e Persistent Volume Claims (PVC).

### Diagrama da Infraestrutura na AWS (Modernização com EKS)
![Image](https://github.com/user-attachments/assets/67331a0c-effc-4276-97a6-6994258d261c)
#### Descrição do Diagrama
A nova infraestrutura será baseada em Kubernetes no EKS e conterá:
- **CI/CD:** O fluxo de desenvolvimento ocorre no GitHub, seguido pelo Terraform para provisionamento e CodePipeline para automação da entrega contínua. CodeBuild compila as imagens e armazena no ECR.
- **Cluster EKS:** Distribuído em três AZs, dividido entre ambiente de produção e teste.
  - **Produção:** Roda em instâncias EC2 com node groups e autoescalonamento (HPA e ASG).
  - **Testes:** Roda no AWS Fargate para menor custo e escalabilidade sob demanda.
  - **Ingress Controller** gerencia a entrada de tráfego e encaminha para os serviços apropriados.
- **Banco de Dados:** Amazon RDS em Multi-AZ para alta disponibilidade.
- **Networking:** NAT Gateway para permitir acesso seguro a serviços externos.
- **Segurança:** WAF, GuardDuty, Secrets Manager e políticas de acesso rigorosas.

### Segurança
- **Network Policies no Kubernetes** para controlar tráfego entre pods.
- **IAM e Secrets Manager** para credenciais seguras.
- **Monitoramento** com **CloudWatch** e **GuardDuty.**
- **WAF** e **Shield** para proteção contra ataques externos.

### Backup
- **Amazon S3** para armazenar backups automatizados.
- **AWS Backup e snapshots do RDS** para recuperação em desastres.

### Custo da Infraestrutura na AWS
Cálculo dos custos estimados dos serviços na AWS utilizando o **Pricing Calculator.**
![Image](https://github.com/user-attachments/assets/a7df2a44-8fd9-4e42-8b09-0b0f7b1e29d7)
Estimativa de custo da infraestrutura. [Estimativa Migrada](/Estimativa Modernização.pdf)

---

## 3. Conclusão
A migração inicial foi realizada via lift-and-shift, garantindo uma transição rápida e segura para a AWS. Posteriormente, a modernização foi implementada utilizando serviços gerenciados como EKS, RDS, CodePipeline e Fargate, garantindo escalabilidade, segurança e eficiência operacional. A nova infraestrutura reduz a complexidade do gerenciamento, melhora a resiliência e otimiza custos a longo prazo. Caso haja necessidade de ajustes ou novas implementações, a arquitetura permite flexibilidade para futuras evoluções.


## Licença
Este projeto está licenciado sob a [MIT License](LICENSE).

## Créditos
Projeto desenvolvido como parte do Projeto Final para #PB - NOV 2024 | DevSecOps.

