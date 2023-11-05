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

# Instalando e configurando Apache-NiFi

