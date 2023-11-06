# Referencia
* Esse projeto é do site project pro e tudo o que está aqui pode ser encontrado em [Projeto](https://www.projectpro.io/project/hackerday-project/project-title/gcp%20iac%20project%20to%20build%20etl%20pipeline%20for%20financial%20data%20analytics#sub-hackerday-video-1)

# Objetivo
* O objetivo deste projeto é construir um pipeline ETL para o conjunto de dados financeiros no GCP, permitindo a extração, transformação e carregamento de dados de um servidor SQL para o BigQuery para fins analíticos.

# Arquitetura
![](https://github.com/Antonio-Borges-Rufino/Build-an-ETL-Pipeline-for-Financial-Data-Analytics-on-GCP-IaC/blob/main/Architecture.webp)

# Banco de Dados (AWS-RDS SQLServer)
* Nesse projeto vamos trabalhar com uma instancia SQL-Server da aplicação RDS da AMAZON AWS.
* Configure de forma padrão a instancia RDS e ative apenas quando necessário.
* Quando voce criar uma instancia MSSQL no RDS da amazon, você precisa alterar suas regras de segurança VPC para aderir ou ao seu ip especifico ou para todos os ips que se conectarem.
* Para fazer isso, siga redes e segurança/security groups/editar regras de entrada.
* O Próximo passo e se conectar ao RDS da AWS através do seu cliente SQL, eu usei o SQL Server Management Studio.
* Depois você escolhe o banco de dados que quer inserir as informações e importa os dados, no meu caso, os dados foram informações de ações de empresas indianas. A imagem abaixo demonstra isso:
* ![](https://github.com/Antonio-Borges-Rufino/Build-an-ETL-Pipeline-for-Financial-Data-Analytics-on-GCP-IaC/blob/main/import_dados_sql1.png)
* Depois verifica se os dados estão ok.
* ![](https://github.com/Antonio-Borges-Rufino/Build-an-ETL-Pipeline-for-Financial-Data-Analytics-on-GCP-IaC/blob/main/import_dados_sql2.png)
* Agora e só conectar com o drive da sua preferencia e executar o comando que ele vai importar para o RDS a tabela.
* ![](https://github.com/Antonio-Borges-Rufino/Build-an-ETL-Pipeline-for-Financial-Data-Analytics-on-GCP-IaC/blob/main/import_dados_sql3.png)
* No final, verificamos se houve a importação da maneira correta.
*  ![](https://github.com/Antonio-Borges-Rufino/Build-an-ETL-Pipeline-for-Financial-Data-Analytics-on-GCP-IaC/blob/main/import_dados_sql4.png)

# Configurando VM Ubunto no GPC (Fase compute engine)
* Para criar uma maquina virtual no GPC eu utilizei a ferramenta compute engine
* A imagem abaixo mosta a instancia vm criada. Esse processo é simples e de livre implementação, apenas clicando em nex e escolhendo qual SO vai ser usado na sua instancia.
* ![](https://github.com/Antonio-Borges-Rufino/Build-an-ETL-Pipeline-for-Financial-Data-Analytics-on-GCP-IaC/blob/main/imagem_2023-11-04_205914879.png)
# Criando armazenamento do cloud storage
* Para criar o armazenamento do cloud storage também é simples, é lá onde vão ficar guardados os registros que virão do AWS.
* Para isso, apenas crie com as funções padrões, apenas colocando o nome e dando next, o resultado deve parecer com o da imagem abaixo:
* ![](https://github.com/Antonio-Borges-Rufino/Build-an-ETL-Pipeline-for-Financial-Data-Analytics-on-GCP-IaC/blob/main/imagem_2023-11-05_013452593.png)
# Instalando e configurando Apache-NiFi
* Primeiro, inicie a máquina virtual do GPC e entre nela por SSH, no meu caso, escolhi entrar por SSH pelo próprio serviço do GPC.
* Após entrar na VM, selecione o usuario root da máquina
* ```
  sudo su
  ```
* Atualize todo o conjunto de pacotes
* ```
  apt update
  ```
* Baixe a versão do nifi, no meu caso, vou seguir o projeto e vou baixar a versão 1.16
* ```
  wget https://archive.apache.org/dist/nifi/1.16.0/nifi-1.16.0-bin.tar.gz
  ```
* Instale o JDK
* ```
  apt install openjdk-8-jdk
  ```
* Descompacte o nifi
* ```
  tar -xzvf nifi-1.16.0-bin.tar.gz
  ```
* Edite o arquivo de propiedades
* ```
  nifi-1.16.0/conf/nifi.properties
  ```
* Atualize os seguintes parâmetros
* ```
  nifi.remote.input.http.enabled = false
  # web properties #
  nifi.web.http.host=
  nifi.web.http.port=8080
  nifi.web.https.host=
  nifi.web.https.port=
  # security properties #
  nifi.security.keystore=
  nifi.security.keystoreType=
  nifi.security.keystorePasswd=
  nifi.security.keyPasswd=
  nifi.security.truststore=
  nifi.security.truststoreType=
  nifi.security.truststorePasswd=
  ```
* Start o serviço do nifi
* ```
  ./nifi-1.16.0/bin/nifi.sh start
  ```
* Verifique a partir do seu ip externo da VM na porta configurada se o nifi está funcionando
* Caso o nifi não funcione, atualize a regra de firewall para abrir para todas as conexões tcp na porta 8088. Isso não é adequado para servidor de produção, mas como e so um projeto, não tem problema. A sua regra tem que ficar assim:
* ```
  gcloud compute --project=mythic-method-404223 firewall-rules create nifi-regraprojeto3 --direction=INGRESS --priority=1000 --        
  network=default --action=ALLOW --rules=tcp:8088 --source-ranges=0.0.0.0/0
  ```
* Agora nifi pode ser acessado com tranquilidade direto da porta 8088 no link: ip_externo:8088/nifi
# Implementando o NiFi no Pipeline
* Acesse o NiFi e crie um novo grupo com nome do projeto.
* Agora baixe o driver de acesso ao SqlServer
* ```
  wget https://github.com/microsoft/mssql-jdbc/releases/download/v12.4.2/mssql-jdbc-12.4.2.jre8.jar
  ```
* Agora inicie um novo processor (quem vai fazer a conexão com o banco de dados) e adicione o processo DBCPConnectionPool 1.16.0 como na imagem abaixo.
* ![](https://github.com/Antonio-Borges-Rufino/Build-an-ETL-Pipeline-for-Financial-Data-Analytics-on-GCP-IaC/blob/main/add_control_service.png)
* Seta as configurações de conexão com o AWS RDS
* ```
  Database Connection URL: jdbc:sqlserver://{endpointAWS_RDS};port=1433;database={Data_Base_Name_AWS_RDS};authentication=NotSpecified;encrypt=true;trustServerCertificate=true
  Database Driver Class Name: com.microsoft.sqlserver.jdbc.SQLServerDriver
  Database Driver Location(s): /home/borgesantonio31/{driver_name}
  Database User: {user_RDS_Admin}
  Password: {senha}
  ```
* Da mesma forma, crie outro serviço, mas dessa vez de escrita chamado ParquetRecordSetWriter 1.16.0 com compressão SNAPPY.
* Ainda na parte de serviços de controle, crie um serviços de controle chamado GCPCredentialsControllerService 1.16.0. Nele vamos controlar as credencias GPC. As configurações são descritas abaixo.
* ![](https://github.com/Antonio-Borges-Rufino/Build-an-ETL-Pipeline-for-Financial-Data-Analytics-on-GCP-IaC/blob/main/imagem_2023-11-05_201441752.png)
* Para incluir as chaves de aplicação você tem que ir no GPC/ IAM/Contas de Serviço/Default Compute Service Acount/Chaves/Adicionar Chaves/JSON
* Agora você tem uma chave de acesso, copie tudo que tem na chave e cole no campo do serviço de controle chamado Service Account JSON
* Agora vamos criar o processor de leitura da AWS RDS. Para isso, adicione o processor QueryDatabaseTableRecord 1.16.0. Nele vou configurar 3 configurações.
* ```
  1° configuração é relacionada a conexão com o AWS RDS, essa configuração é a Database Connection Pooling Service que eu adiciono o serviço de controle DBCPConnectionPool 1.16.0 que criei acima
  2° configuração é o tipo, no qual coloco MS SQL 2012+
  3° configuração é a Record Writer onde adicionamos o serviço de controle ParquetRecordSetWriter 1.16.0 que vai gravar os resultados das pesquisas em parquet no bucket do google
  ```
* Abaixo está a imagem das configurações 
* ![](https://github.com/Antonio-Borges-Rufino/Build-an-ETL-Pipeline-for-Financial-Data-Analytics-on-GCP-IaC/blob/main/querydatabase.png)
* {Espaço para criação do processo de leitura do aws rds}
* Agora vamos criar o processo de escrita no GPC-Storage. Para isso, inclua o processo PutGCSObject 1.16.0. Nele vamos inserir algumas configurações relacionadas ao bucket do gpc onde vão ser armazenados os dados
* ```
  Project ID: É a configuração de ID do seu projeto no GPC, Você pode no selecionador de projetos do lado do nome do google cloud. Lá vai ter um ID associado pra cada projeto.
  GCP Credentials Provider Service: É a configuração onde vou colocar o serviço de controle GCPCredentialsControllerService 1.16.0. que criei para acessar meu gpc
  Bucket: É o nome do Bucket que criei anteriormente
  ```
* Abaixo está uma imagem demonstrando as configurações
* ![](https://github.com/Antonio-Borges-Rufino/Build-an-ETL-Pipeline-for-Financial-Data-Analytics-on-GCP-IaC/blob/main/putgcsobj.png)
* É importante ressaltar que é preciso observar os formatos das tabelas que usamos, no meu caso, deu um erro porque as tabelas que usei tinham colunas com nomes espaçados, então deu erro no meu pipeline. Para isso modifiquei as tabelas, o que não pode ser suficiente em um ambiente de produção
* Agora é só conectar e executar o pipeline. Como na imagem abaixo. Depois de executar o pipeline, eu parei e atualizei o bucket GPC, e o resultado também está na imagem abaixo
* ![](https://github.com/Antonio-Borges-Rufino/Build-an-ETL-Pipeline-for-Financial-Data-Analytics-on-GCP-IaC/blob/main/execebuckt.png)
* Aqui é importante lembrar, que dependendod as configurações, toda vez que você fizer alterações na sua base de dados AWS, esse pipeline vai realizar a execução passando os arquivos para o bucket do GPC cloud storage
# Big Query
