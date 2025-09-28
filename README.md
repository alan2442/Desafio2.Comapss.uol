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
