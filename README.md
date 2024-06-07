# Nova Drive Motors - Data Engineering


## Sobre o projeto

O projeto aqui desenvolvido utiliza como base os dados de uma empresa fictícia denominada 'Nova Drive Motors', de cujo banco de dados operacional serão extraídos os dados, faremos a carga em um datawarehouse e transformações para prover respostas para as questões analíticas da área de negócios da empresa. 

### Motivações:

Este projeto tem como objetivo a aplicação de conhecimentos de ELT utilizando ferramentas atuais.

### Ferramentas:

Usaremos nesse projeto as seguintes ferramentas:

- Postgres - Ferramenta onde estará nosso banco de dados operacional;

- AWS EC2 - Ferramenta onde nossa máquina virtual irá utilizar o Docker com o Apache Airflow para orquestramento dos dados;

- SnowFlake - Ferramenta onde será criado o Data Warehouse

- DBT - Ferramenta para transformação dos dados para análises da área de negócios;

- Python - Utilizaremos o python para analisar os dados com criação de Dashboards;

## Passo a Passo
### Configurando a VM no AWS EC2
```code
1 - Executar uma instância do tipo Ubuntu com +/- 8gb de ram (tipo t2.large) 

2 - Criar uma key pair (salvar no PC)

3 - Habilitar tráfego HTTP e HTTPS

4 - Dar launch na VM

5 - Modificar as Inbound rules para liberar acesso a porta 8080

   Acessar Security Groups e adicionar uma nova regra:
    - type: Custom TCP
    - Port range: 8080
    - Source: Anywhere-IPv4
```

### Instalando o Airflow no AWS EC2


Para configurar o ambiente iremos                       

```sh
# 1 - Atualizando a lista de pacotes do APT
sudo apt-get update

# 2 - Instalar pacotes necessários para adicionar um novo repositório via HTTPS:
sudo apt-get install ca-certificates curl gnupg lsb-release
 
# 3 - Criar diretório para armazenar as chaves de repositórios 
sudo mkdir -m 0755 -p /etc/apt/keyrings
 
# 4 - Adicionar a chave GPG do repositório do Docker:
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# 5 - Adicionar o repositório do Docker às fontes do APT:
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
 
# 6 - Atualiza a lista de pacotes após adicionar o novo repositório do Docker
sudo apt-get update

# 7 - Instalar o Docker e componentes
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
 
# 8 - Baixar o arquivo docker-compose.yaml do Airflow:
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/stable/docker-compose.yaml'
 
# 9 - Criar diretórios para DAGs, logs e plugins:
mkdir -p ./dags ./logs ./plugins
 
# 10 - Criar um arquivo .env com o UID do usuário, usado pelo docker para permissões
echo -e "AIRFLOW_UID=$(id -u)" > .env
 
# 11 - inicia o airflow
sudo  docker compose up airflow-init
 
# 12 - subir o Airflow em modo desacoplado
sudo docker compose up -d
```

```sh
# Toques finais de configuração no docker
 
# copiar Public IPv4 DNS e colocar porta 8080, 
# EXEMPLO:
http://ec2-35-175-126-189.compute-1.amazonaws.com:8080/
 
# editar o arquivo abaixo para não carregar DAGs de exemplos no Ariflow
nano /home/ubuntu/docker-compose.yaml

AIRFLOW__CORE__LOAD_EXAMPLES: 'false'
 
# reiniciar o container docker
sudo docker compose stop
sudo docker compose up -d
sudo docker ps
```

