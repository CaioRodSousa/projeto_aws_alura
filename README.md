# curso_aws_alura
## Desafio tratar database local e exportar para um banco de dados em cloud
### Tabela de conteúdos
=================
<!--ts-->
* [Tecnologias utilizadas](#Tecnologias)
* [Arquitetura](#Arquitetura)
* [AWS - Ambiente de produção](#AWS(Ambiente_de_produção))
* [AWS EC2](#AWS_EC2)
* [AWS RDS](#AWS_RDS)
* [Conexão com o EC2](#Conexão_EC2)
* [VScode](#VScode)
* [Dbeaver](#Dbeaver)
<!--te-->
### Tecnologias

As seguintes ferramentas foram usadas na resolução dos questionamentos:

- VScode
- Pandas 
- Python
- Drawio
- Amazon Web Services

### Arquitetura
Ambiente de produção:

![image](https://user-images.githubusercontent.com/73916591/227060691-3ff70721-c5fb-4eeb-a9b8-1217aaca2067.png)

# AWS(Ambiente_de_produção)
# AWS_EC2
Primeiro criei a instância que irei utilizar como servidor para comunicar com o meu banco de dados:
![image](https://user-images.githubusercontent.com/73916591/227062459-24db190c-f194-4869-b9cd-7aea4bdb14e4.png)

# AWS_RDS
Após esse passo criei o meu banco de dados postgresql na aws utilizando o Amazon Relational Database Service(RDS):
![image](https://user-images.githubusercontent.com/73916591/227063500-f64d96c5-85e2-48f7-870b-61e19c57e99f.png)

# Conexão_EC2
Para conectar minha maquina local com o servidor EC2, primeiro localizei onde guardei o meu arquivo de autenticação gerado ao criar a instância e executei este comando para garantir que minha chave não fique visível publicamente:
![image](https://user-images.githubusercontent.com/73916591/227067369-59b3aea4-9664-4f87-9902-4338c853aa35.png)

Depois faço a conexão via ssh tunel utilizando o seguinte comando:
![image](https://user-images.githubusercontent.com/73916591/227070274-ab0c6a87-aa52-4d4b-9ae3-3833109b0797.png)

