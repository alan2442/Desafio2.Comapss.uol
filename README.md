# Desafio2.Comapass.uol

# 1. Tecnologia	Utilizadas

- Docker ->	Containerização local do WordPress e banco MySQL para testes

- Docker Compose ->	Orquestração dos containers

- AWS VPC ->	Isolamento da rede e segmentação em subnets públicas/privadas

- Amazon EC2 ->	Hospedagem das instâncias do WordPress

- Amazon RDS (MySQL) ->	Banco de dados relacional gerenciado

- Amazon EFS ->	Armazenamento compartilhado entre instâncias

- Elastic Load Balancer (ALB) ->	Distribuição de carga entre instâncias EC2

- Auto Scaling Group ->	Escalabilidade automática baseada em demanda

- Security Groups	Firewall -> para controle de acesso

- User-data ->	Automação da configuração das instâncias EC2

  _________________________________________________________________________________________________________________________________________________________________

# 2. Arquitetura da Solução

A arquitetura implementada segue um modelo altamente escalável e modular baseado em boas práticas da AWS para ambientes WordPress em produção. A aplicação foi estruturada utilizando:

- Docker e Docker Compose para orquestrar o WordPress localmente em ambiente de testes.

- Amazon VPC com sub-redes públicas e privadas para segmentação e segurança.

- Elastic Load Balancer (ALB) para distribuir tráfego e permitir escalabilidade horizontal via Auto Scaling.

- Amazon EC2 para hospedar o ambiente Docker do WordPress em instâncias privadas.

- Amazon RDS para banco de dados gerenciado com MySQL.

- Amazon EFS para armazenamento compartilhado e persistente do diretório wp-content.

- Auto Scaling Group para balanceamento automático da carga com base na demanda.

- Security Groups para controle refinado do tráfego interno e externo.

- Toda a arquitetura foi construída com foco em segurança, alta disponibilidade e escalabilidade.

  _________________________________________________________________________________________________________________________________________________________________

# Etapas do Desafio:

## etapa 1 - rodando o wordpress via Docker composse para se familiarizar:

- 1.1 - Crie um diretório

- 1.2 - Entre no diretório e crie um arquivo utilizando o codigo: nano docker-compose.yml

- 1.3 - Dentro do arquivo digite:

-------------
![Imagem 1](C:\Users\pc\Pictures\projeto2\imagem1.png)
-------------
- 1.4 - Dentro do diretório que contém o arquivo criado digite no terminal: docker-compose up -d

- 1.5 - Entre no wordpress e crie um site básico para aprender a usá-lo

 _________________________________________________________________________________________________________________________________________________________________


## Etapa 2 - Criando uma VPC: 

- 2.1 - Vá para o Console da AWS
  
- 2.2 - Vá para VPC > suas VPCs > Clique em criar VPC e preencha
  
- 2.3 - Recursos a serem criados: Escolhe a opção somente VPC
  
- 2.4 - Tag de Nome: vpc-wordpress
  
- 2.5 - Bloco CLDR IPv4: Entrada manual de CLDR IPv4
  
- 2.6 - CLDR IPv4: 10.0.0.0/16
  
- 2.5 - Clique em criar VPC
---

 _________________________________________________________________________________________________________________________________________________________________

## Etapa 3 - Criar 4 subnets ( 2 públicas, 2 privadas): 

- 3.1 - Ir em VPC > Subnets > Cria Subnets
  
- 3.2- ID da VPC: selecione sua vpc criada (vpc_wordpress)
  
- 3.3 - Crie as seguintes sub-redes preenchendo:
  
Nome da sub-rede                  	CLDR                 AZ
subnet-public-a			        10.0.1.0/16	     use1-az6 (us-east-1a)
subnet-public-b			        10.0.2.0/16          use1-az1 (us-east-1b)
subnet-ec2-private-a		        10.0.3.0/16	     use1-az6 (us-east-1a)
subnet-ec2-private-b		        10.0.4.0/16          use1-az1 (us-east-1b)
subnet-rds-private-a		        10.0.5.0/16	     use1-az6 (us-east-1a)
subnet-rds-private-b		        10.0.6.0/16          use1-az1 (us-east-1b)

- 3.4 - Crie suas Tags(Opcional)

 _________________________________________________________________________________________________________________________________________________________________

## Etapa 4 - Criar o Gateways da Internet (IGW) 

- 4.1 - Vá em VPC > Gateways da Internet

- 4.2 - Clique em Criar Gateways da Internet

- 4.3 - Nome: igw-wordpress

- 4.4 - Clique em Criar

- 4.5 - Depois de criado, selecione e clique em Ações > associar a VPC

- 4.6 - Selecione sua VPC (vpc-wordpress)

OBS: Quando criei a vpc a aws criou um internet Gateway(IGW) e associou a minha VPC, faça essa etapa se a AWS não criar automaticamente

 _________________________________________________________________________________________________________________________________________________________________


## Etapa 5 - Criar a Route Table para Subnets Públicas

- 5.1 - Vá em VPC > Tabelas de rotas(Route Tables)
  
- 5.2 - Clique em Criar Tabela de rotas(route table)
  
- 5.3 - Nome: rt-public
  
- 5.4 - VPC: vpc-wordpress
  
- 5.5 - Após criado Selecione rt-public
  
- 5.6 - Clique em Rotas (Routes) > Editar Rotas (Edit routes) > Adicionar Rotas (Add route) e preencha:
  
- 5.7 - Destino(Destination): 0.0.0.0/0
  
- 5.8 - Alvo(Target): Internet Gateway (selecione igw-wordpress)
  
- 5.9 - Clique em Salvar rotas(Save routes)

 _________________________________________________________________________________________________________________________________________________________________


## Etapa 6 - Criar NAT Gateway

- 6.1 - Vá em VPC > NAT Gateways

- 6.2 - Clique em Criar NAT Gateway (crie dois)

- 6.3 - Preencha:

- 6.4 - Sub-rede: subnet-public-a, já o segundo nat gateway será subnet-public-b

- 6.5 - Nome: nat-wordpress1 e o segundo será chamado de nat-wordpress2

- 6.6 - ID e alocação do IP elástico (Elastic IP): Clique em Alocar IP elástico quando estiver criando (Allocate Elastic IP)

- 6.7 - Clique em Criar NAT Gateway

 _________________________________________________________________________________________________________________________________________________________________

## Etapa 7 - Criar Tabela de Rota(Route Table) para Subnets Privadas:

- 7.1 - Vá em VPC > Tabelas de Rotas (Route Tables) > Criar tabela de rota (route table), Crie duas tabelas de rotas privadas

- 7.2 - Preencha:

- 7.3 - Nome: rt-private1 e o nome da segunda será rt-private2

- 7.4 - Tipo de Conectividade: Público

- 7.5 - VPC: vpc-wordpress

- 7.6 - Clique em criar Criar Nat gateway

- 7.7 - Após criar Selecione a tabela de rota criada > Clique em Ações > Editar Rotas > adicionar rota

- 7.8 - Destino (Destination): 0.0.0.0/0

- 7.9 - Alvo (Target): NAT Gateway (nat-wordpress1) e a segunda rota será ligada ao segundo NAT Gateway (nat-wordpress2)

- 7.10 - Clique em Salvar Rotas


 _________________________________________________________________________________________________________________________________________________________________

## Etapa 9 - Criando os Grupos de Segurança (Security Group):

8.1 - Vá até o serviço EC2 > Security Groups > Clique em criar grupo de segurança

8.2 - Você irá criar 5 grupo, um de cada vez, eles serão chamado: sgBastion, sgInstance, sgLoadBalancer, sgEFS, sgRDS

8.3 - Criando o SgBastion:
- 8.3.1 - Nome do grupo de segurança: sgBastion
- 8.3.2 - Descrição: SG Bastion
- 8.3.3 - VPC: vpc-wordpress
- 8.3.4 - Vá na aba Regras de Entrada (Inbound rules) >  Clique em Adicionar Regra (Add Rule) e preencha:
-- Tipo (Type): SSH
-- Protocolo (Protocol): TCP
-- Port Intervalo de portas (Range): 22
-- Origem: selecione Personalizado (Custom)
-- No campo de origem, selecione o 0.0.0.0
-- Clique em Salvar Regra
- 8.3.5 - Clique em criar grupo de Segurança


- 8.4 - Criando o SgInstance:
- 8.4.1 - Nome do grupo de segurança: sgInstance
- 8.4.2 - Descrição: SG Instance
- 8.4.3 - VPC: vpc-wordpress
- 8.4.4 - Vá na aba Regras de Entrada (Inbound rules) >  Clique em Adicionar Regra (Add Rule) e preencha:
-- Tipo (Type): HTTP
-- Protocolo (Protocol): TCP
-- Port Intervalo de portas (Range): 80
-- Origem: selecione Personalizado (Custom)
-- No campo de origem, selecione 0.0.0.0/0
-- Clique em Salvar Regra
- 8.4.5 - Clique em Adicionar Regra("Add Rule") e preencha:
-- Tipo (Type): SSH
-- Protocolo (Protocol): TCP
-- Port Intervalo de portas (Range): 22
-- Origem: selecione Personalizado (Custom)
-- No campo de origem, selecione o sgBastion
-- Clique em Salvar Regra
- 8.4.6 - Na parte de regra de saida, Clique em Adicionar Regra de Saida e preencha:
-- Tipo (Type): HTTP
-- Protocolo (Protocol): TCP
-- Port Intervalo de portas (Range): 80
-- Origem: selecione Personalizado (Custom)
-- No campo de origem, selecione 0.0.0.0/0
-- Clique em Salvar Regra
- 8.4.7 - Clique em criar grupo de Segurança


- 8.5 - Criando o sgLoadBalancer:
- 8.5.1 - Nome do grupo de segurança: sgLoadBalancer
- 8.5.2 - Descrição: SG LoadBalancer
- 8.5.3 - VPC: vpc-wordpress
- 8.5.4 - Vá na aba Regras de Entrada (Inbound rules) >  Clique em Adicionar Regra (Add Rule) e preencha:
-- Tipo (Type): HTTP
-- Protocolo (Protocol): TCP
-- Port Intervalo de portas (Range): 80
-- Origem: selecione Personalizado (Custom)
-- No campo de origem, selecione 0.0.0.0/0
-- Clique em Salvar Regra
8.5.5 - Clique em criar grupo de Segurança


- 8.6 - Criando o sgEFS:
- 8.6.1 - Nome do grupo de segurança: sgEFS
- 8.6.2 - Descrição: SG EFS
- 8.6.3 - VPC: vpc-wordpress
- 8.6.4 - Vá na aba Regras de Entrada (Inbound rules) >  Clique em Adicionar Regra (Add Rule) e preencha:
-- Tipo (Type): NFS
-- Protocolo (Protocol): TCP
-- Port Intervalo de portas (Range): 2049
-- Origem: selecione Personalizado (Custom)
-- No campo de origem, selecione o grupo de segurança sgInstance
-- Clique em Salvar Regra
- 8.6.5 - Clique em criar grupo de Segurança


- 8.7 - Criando o sgRDS:
- 8.7.1 - Nome do grupo de segurança: sgRDS
- 8.7.2 - Descrição: SG RDS
- 8.7.3 - VPC: vpc-wordpress
- 8.7.4 - Vá na aba Regras de Entrada (Inbound rules) >  Clique em Adicionar Regra (Add Rule) e preencha:
-- Tipo (Type): MYSQL/Aurora
-- Protocolo (Protocol): TCP
-- Port Intervalo de portas (Range): 3306
-- Origem: selecione Personalizado (Custom)
-- No campo de origem, selecione o grupo de segurança sgInstance
-- Clique em Salvar Regra
- 8.7.5 - Clique em criar grupo de Segurança




 _________________________________________________________________________________________________________________________________________________________________

## Etapa 8 - Criar RDS 

- 9.1 - Vá até o AWS Console

- 9.2 - Pesquise por RDS na barra de busca e clique no serviço

- 9.3 - Clique em Banco de dados (Databases) > Criar Banco de dados (Create database)

- 9.4 - Em “Escolher um método de criação de banco de dados” → Selecione Criação Padrão (Standard Create)

- 9.5 - Em “Engine options”:

-- Engine type: MySQL ou MariaDB

-- Engine version: selecione a mais recente estável (ex: MySQL 8.0.x)

- 9.6 - Em modelos: Escolha o Nível gratuito

- 9.7 - Identificador do cluster de banco de dados (DB instance identifier): wordpress

- 9.8 - Username: wordpress (ou outro nome)

- 9.9 - Desmarque a opção de gerar senha automaticamente se quiser digitar a sua.

- 9.10 - Password: wordpress123

- 9.11 - DB instance class: escolha algo simples para teste, como:

-- db.t3.micro (dentro do free tier)

- 9.12 - Storage: padrão (20 GB ou mais)

- 9.13 - Desmarque a opção de “Enable storage autoscaling” se quiser manter controle de custo

--- Conectividade ( aqui é onde usamos sua VPC)

- 9.14 - Virtual Private Cloud (VPC): selecione a sua VPC (wordpress-vpc)

- 9.15 - Subnet group: Escolha o Default(Padrão).

- 9.16 - Acesso Públic: “Não” (isso é crucial — não queremos acesso público!)

- 9.17 - Grupo de Segurança da VPC(VPC security group): Clique em "Selecionar Existente" e escolha o grupo de segurança que você criou para banco de dados.

- 9.18 - Zona de Disponibilidade (Availability Zone): deixe como “Sem preferência”

- 9.19 - Coloque suas TAG (Se preferir)

- 9.20 - Clique em criar banco de ados

 _________________________________________________________________________________________________________________________________________________________________

## Etapa 10 - Cria o EFS(Elastic File System)

- 10.1 - Acesse o Console da AWS

- 10.2 - Pesquise por EFS e clique em EFS “Elastic File System”

- 10.3 - Clique em Criar sistema de arquivos(“Create file system”)

- 10.4 - Clique em Personalizar

- 10.5 - Em Name, coloque: wordpress-efs

- 10.6 - Deixe a opção “Automatic backups” como preferir (pode desmarcar para testes)

- 10.7 - Clique em Próximo
-- aba Acesso a rede (Network Access) (Mount Targets):
Aqui você irá configurar o EFS para ficar acessível dentro da sua VPC, nas subnets privadas onde as EC2s estão.
A AWS vai sugerir automaticamente as zonas de disponibilidade (AZs)

- 10.8 - Em VPC, selecione a VPC que você criou (vpc-wordpress)

- 10.9 - Para cada Zona de Disponibilidade (AZ):

- 10.10 - Escolha uma subnet privada de EC2 (ex: private-ec2-a e a da outra AZ será private-ec2-b)

- 10.11 - Tipo de endereço IP: Somente IPv4

- 10.12 - Escolha o security group do banco de dados criado para as duas AZ: sgEfs

Importante:
Esse Security Group é o que vai permitir o acesso somente das EC2s ao EFS via porta NFS (2049).

- 10.13 - Finalize a criação do EFS:

- 10.14 - Clique em próximo (Next) até o final

- 10.15 - Clique em Criar Sistema de arquivos (Create File System)

- 10.16 - Aguarde a criação (leva cerca de 1 minuto)

 _________________________________________________________________________________________________________________________________________________________________

## Etapa 11 - Criando o Grupo de Destino

- 11.1 - No console AWS pesquise por EC2 e entre no serviço

- 11.2 - Na barra lateral esquerda procure por Balanceamento de carga > Grupos de Destino

- 11.3 - Clique em criar grupo de destino e preencha:

- 11.4 - Tipo de destiono: Instâncias

- 11.5 - Nome do grupo de destino: grupoDeDestino

- 11.6 - Protocolo: HTTP e Porta: 80

- 11.7 - Tipo de endereço: IPv4

- 11.8 - VPC: sua vpc cria (vpc-wordpress)

- 11.9 - Protocolo de verificação de integridade: HTTP

- 11.10 - Caminho de verificação de integridade: /

- 11.11 - Clique em próximo e depois em criar grupo de destino


 _________________________________________________________________________________________________________________________________________________________________

## Etapa 12 - Criando o Load Balancer

- 12.1 - No console AWS pesquise por EC2 e entre no serviço

- 12.2 - Na barra lateral esquerda procure por Balanceamento de carga > Load balancers > Criar Load Balancer

- 12.3 - Tipo de Load Balancer: criar o tipo Application Load Balancer

- 12.4 - Nome: wordpress

- 12.5 - Esquema: Voltado para a Internet

- 12.6 - VPC: escolha sua vpc criada (vpc-wordpress)

- 12.7 - Zonas de disponibilidade(AZ) e sub redes:

- 12.7.1 - Selecione suas AZ

- 12.7.2 - Em suas AZ selecione as suas subnets públicas ( subnet-public-a e subnet-public-b para a segunda AZ)

- 12.8 - Grupo de Segurança: escolha o do load Balancer (sgLoadBalancer)

- 12.9 - Protocolo: HTTP e Porta: 80

- 12.10 - Grupo de Destino: Selecione o grupo de destino criado (grupoDeDestino)

- 12.11 - Clique em criar o load balancer

 _________________________________________________________________________________________________________________________________________________________________

## Etapa 13 - Configurando User-data para usar no modelo de execução: 

- 13.1 - No console AWS pesquise por EC2 e entre no serviço

- 13.2 - Na barra lateral esquerda procure por Balanceamento de carga > Clique no seu balanceador de carga criado

- 13.3 - Copie o nome de DNS e guarde em algum lugar

- 13.4 - No console AWS pesquise por RDS e entre no serviço

- 13.5 - Na barra lateral esquerda procure por banco de dados > Clique no seu banco de dados criado

- 13.6 - Copie o Endpoint e guarde em algum lugar

- 13.7 - No console AWS pesquise por EFS e entre no serviço

- 13.8 - Na barra lateral esquerda procure por Sistemas de Arquivos > Clique no seu EFS criado > Clique em Anexar

- 13.9 - Copie o código que está escrito abaixo de "Usando o cliente do NFS" e guarde em algum lugar

- 13.10 - Exemplo de user-data: 


------------------
#!/bin/bash

sudo yum update -y
#Install Docker
sudo yum install docker -y
sudo service docker start
sudo systemctl enable docker
sudo usermod -a -G docker ec2-user
sudo chmod 666 /var/run/docker.sock

#Install Docker Compose
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

mkdir /my-compose

sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-5.noarch.rpm
sudo dnf install -y https://dev.mysql.com/get/mysql80-community-release-el9-5.noarch.rpm
sudo dnf install -y mysql-community-server

RDS_HOST="wordpress.c6tii066matn.us-east-1.rds.amazonaws.com"
RDS_ADMIN_USER="wordpress"
RDS_ADMIN_PASSWORD="wordpress123"
WP_DB_NAME="wordpress"

mysql -h $RDS_HOST -u $RDS_ADMIN_USER -p$RDS_ADMIN_PASSWORD <<EOF
CREATE DATABASE IF NOT EXISTS $WP_DB_NAME CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
EOF

sudo mkdir -p /efs/wp-content
sudo chmod 777 /efs

until sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-036b1a81156a6ddd0.efs.us-east-1.amazonaws.com:/ /efs/wp-content/; do
    echo "Aguardando EFS ficar disponível..."
    sleep 10
done

sudo chown -R 33:33 /efs/wp-content

echo "version: '3.3'" >> /my-compose/docker-compose.yml
echo >> /my-compose/docker-compose.yml
echo "services: " >> /my-compose/docker-compose.yml
echo "  wordpress:" >> /my-compose/docker-compose.yml
echo "    image: wordpress:latest" >> /my-compose/docker-compose.yml
echo "    restart: always" >> /my-compose/docker-compose.yml
echo "    ports:" >> /my-compose/docker-compose.yml
echo "      - \"80:80\"" >> /my-compose/docker-compose.yml
echo "    environment:" >> /my-compose/docker-compose.yml
echo "      WORDPRESS_DB_HOST: wordpress.c6tii066matn.us-east-1.rds.amazonaws.com:3306" >> /my-compose/docker-compose.yml
echo "      WORDPRESS_DB_USER: wordpress" >> /my-compose/docker-compose.yml
echo "      WORDPRESS_DB_PASSWORD: wordpress123" >> /my-compose/docker-compose.yml
echo "      WORDPRESS_DB_NAME: wordpress" >> /my-compose/docker-compose.yml
echo "    volumes:" >> /my-compose/docker-compose.yml
echo "      - /efs/wp-content:/var/www/html/wp-content" >> /my-compose/docker-compose.yml
echo >> /my-compose/docker-compose.yml

cd /my-compose
docker-compose up -d


#Variável do DNS do Load Balancer
LB_DNS="wordpress-207486896.us-east-1.elb.amazonaws.com"


#Atualiza URLs do WordPress para usar  o Load Balancer
mysql -h $RDS_HOST -u RDS_ADMIN_USER -p$RDS_ADMIN_PASSWORD $WP_DB_NAME <<EOF
UPDATE wp_options
  SET option_value='http://$LB_DNS'
  WHERE option_name IN ('siteurl','home');
EOF

------------------------


13.11 - Use as informações copiada para alterar o script

13.12 - O nome do DNS coloque dentro da variável LB_DNS 

13.13 - O código copiado do EFS coloque na linha que começa assim: "until sudo mount -tnfs4..."

13.14 - O endpoint que você copiou cole aonde estiver RDS_HOST e WORDPRESS_DB_HOST

13.15 - também altere aonde ter DB_USER, DB_PASSWORD, e DB_NAME conforme as informações que você utilizou na criação do RDS


 _________________________________________________________________________________________________________________________________________________________________

## Etapa 14 - Criando modelo de execução: 

- 14.1 - No console AWS pesquise por EC2 e entre no serviço

- 14.2 - Na barra lateral esquerda procure por Modelo de execução > Criar Load Balancer e preencha:

- 14.3 - Nome: modeloExec

- 14.4 - Descrição: modelo de execução

- 14.5 - AMI: escolha "Início rápido" e selecione o amazona Linux

- 14.6 - Tipo de Instancia: t2.micro

- 14.7 - Nome de par de chaves: Crie um par de chaves

- 14.8 - Sub-rede: não incluir no modelo de execução

- 14.9 - zona de disponibilidade: não incluir no modelo de execução

- 14.10 - grupo de segurança: escolha o sgInstance

- 14.11 - na parte de configuração avançada de rede > Atribuir ip público automaticamente: Não

- 14.12 - Tag: coloque suas tags

- 14.13 - em detalhes avançados na parte de Dados do usuário: cole seu user-data

 _________________________________________________________________________________________________________________________________________________________________

## Etapa 15 - Criando o auto-scaling: 

- 15.1 - No console AWS pesquise por EC2 e entre no serviço

- 15.2 - Na barra lateral esquerda procure por grupos Auto Scaling > Clique em criar auto scaling e preencha:

- 15.3 - Nome do grupo Auto Scaling: autoScaling1

- 15.4 - Modelo de execução: selecione o modelo criado (modeloExec)

- 15.5 - versão: Latest (ultima) 

- 15.6 - Clique em próximo

- 15.7 - vpc: selecione a vpc criada (vpc-wordpress)

- 15.8 - Zona de disponibilidade: escolhas as privadas (subnet-ec2-private-a e subnet-ec2-private-b)

- 15.9 - Clique em próximo

- 15.10 - Clique em Anexar um balanceador de carga existente e selecione o grupo de destino criado (grupoDeDestino)

- 15.11 - Clique em próximo

- 15.12- Capacidade desejada = 2, capacidade mínima desejada = 2, capacidade máxima desejada = 4

- 15.13 - selecione Política de dimensionamento com monitoramento do objeto (Opcional)

- 15.14 - Monitoramento: habilitar coleta de métricas de grupo no CloudWatch

- 15.15 - Clique em próximo até chegar no botão para criar o auto-scaling

- 15.16 - Crie o auto-scaling


# 4. Conclusão do Projeto

Este projeto proporcionou uma visão prática e completa da criação de uma infraestrutura escalável e segura para aplicações WordPress utilizando os principais serviços da AWS. Foi possível aplicar conceitos fundamentais como:

- Segmentação de redes com VPC.

- Alta disponibilidade via Load Balancer e Auto Scaling.

- Armazenamento persistente com EFS.

- Banco de dados gerenciado com RDS.

- Automatização do provisionamento com User Data.

- Além de reforçar conhecimentos em Docker, orquestração com Docker Compose e a importância do isolamento de ambientes e segurança de rede.

# 5. Considerações Finais

Durante o desenvolvimento, alguns pontos foram essenciais para o sucesso do projeto:

- Planejamento da arquitetura antes de iniciar a criação dos recursos.

- Entendimento da relação entre serviços da AWS, especialmente em termos de rede e permissões.

- Testes em ambiente local com Docker antes de escalar para a nuvem.

- Automação usando User-data, que agilizou a criação de instâncias consistentes.


