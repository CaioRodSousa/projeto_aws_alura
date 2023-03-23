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
* [DBeaver](#DBeaver)
* [VScode](#VScode)
<!--te-->
### Tecnologias

As seguintes ferramentas foram usadas na resolução dos questionamentos:

- VScode
- Pandas 
- Python
- SQL
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

Agora tenho que adicionar o ipv4 do meu servidor nas regras de entrada do meu banco de dados:
![print](https://user-images.githubusercontent.com/73916591/227208778-67604765-ddd1-440e-ac11-81b1c06a074b.png)

# DBeaver
Feita a conexão irei acessar o banco através do DBeaver e crio um novo script sql para criar as minhas tabelas no banco:
![image](https://user-images.githubusercontent.com/73916591/227274521-f40762b9-ffef-491a-8477-3de2fb09da9f.png)

Executo o seguinte comando sql no meu script:
```SQL
CREATE TABLE cobranca_paciente(
    id serial PRIMARY KEY,
    definicao VARCHAR(100) NOT NULL,
    identificacao INT NOT NULL,
    nome  VARCHAR(100) NOT NULL,
    endereco VARCHAR(100) NOT NULL,
    cidade VARCHAR(50) NOT NULL,
    estado VARCHAR(50) NOT NULL,
    codigo_postal INT NOT NULL,
    regiao VARCHAR(50) NOT NULL,
    total_cobrancas INT NOT NULL,
    media_custos_cobertos NUMERIC NOT NULL,
    media_pagamento_total NUMERIC NOT NULL,
    media_gastos_cuidados NUMERIC NOT NULL
)
CREATE TABLE dados_analises(
    id INT PRIMARY KEY,
    diagnostico VARCHAR(5) NOT NULL,
    media_raio NUMERIC NOT NULL,
    media_textura NUMERIC NOT NULL,
    media_perimetro NUMERIC NOT NULL,
    media_area NUMERIC NOT NULL,
    media_suavidade NUMERIC NOT NULL,
    media_compactacao NUMERIC NOT NULL,
    media_concavidade NUMERIC NOT NULL,
    media_concavidade_pontos NUMERIC NOT NULL,
    media_simetria NUMERIC NOT NULL,
    media_dimensao_fractal NUMERIC NOT NULL,
    se_raio NUMERIC NOT NULL,
    se_textura NUMERIC NOT NULL,
    se_perimetro NUMERIC NOT NULL,
    se_area NUMERIC NOT NULL,
    se_suavidade NUMERIC NOT NULL,
    se_compactacao NUMERIC NOT NULL,
    se_concavidade NUMERIC NOT NULL,
    se_concavidade_pontos NUMERIC NOT NULL,
    se_simetria NUMERIC NOT NULL,
    se_dimensao_fractal NUMERIC NOT NULL,
    pior_raio NUMERIC NOT NULL,
    pior_textura NUMERIC NOT NULL,
    pior_perimetro NUMERIC NOT NULL,
    pior_area NUMERIC NOT NULL,
    pior_suavidade NUMERIC NOT NULL,
    pior_compactacao NUMERIC NOT NULL,
    pior_concavidade NUMERIC NOT NULL,
    pior_concavidade_pontos NUMERIC NOT NULL,
    pior_simetria NUMERIC NOT NULL,
    pior_dimensao_fractal NUMERIC NOT NULL
)
```
E após a execução podemos ver as tabelas criadas:
![image](https://user-images.githubusercontent.com/73916591/227277916-8be859b6-5d26-4443-a73a-b550d25527c1.png)

# VScode
Agora irei fazer o tratamento da minha database local e importa-la para o meu banco de dados através de um script python:

```python
# -*- coding: utf-8 -*-
#importanto bibliotecas necessárias

import io
import psycopg2
import pandas as pd
```
carregando datasets e realizando tratamento
```python
# dataset cobranca de pacientes
cobranca_pacientes = pd.read_csv('datasets/inpatientCharges.csv')
cobranca_pacientes[' Average Covered Charges '] = cobranca_pacientes[' Average Covered Charges '].str.replace('$','')
cobranca_pacientes[' Average Total Payments '] = cobranca_pacientes[' Average Total Payments '].str.replace('$','')
cobranca_pacientes['Average Medicare Payments'] = cobranca_pacientes['Average Medicare Payments'].str.replace('$','')

# dataset diagnosticos
diagnosticos = pd.read_csv('datasets/datasets_180_408_data.csv')
diagnosticos.drop(diagnosticos.columns[len(diagnosticos.columns)-1], axis=1, inplace=True)
```
criando função para carregar os dados no postgr
```python
def carregar_dados(conn, df, tabela, colunas):
    cur = conn.cursor()
    output = io.StringIO()
    df.to_csv(output, sep='\t', header = False, index = False)
    output.seek(0)
    try:
        cur.copy_from(output, tabela, null = "", columns = colunas)
        conn.commit()
    except Exception as e:
        print(e)
        conn.rollback()
```
fazendo a conexão com o postgre e carregando os datasets utilizando a função criada anteriormente
```python
conn = psycopg2.connect(host='localhost', port='5432', database='postgres', user='admin', password='admin')
        
carregar_dados(conn, cobranca_pacientes, 'cobranca_paciente', ('definicao', 
                                                            'identificacao',
                                                            'nome', 
                                                            'endereco',
                                                            'cidade',
                                                            'estado',
                                                            'codigo_postal',
                                                            'regiao',
                                                            'total_cobrancas',
                                                            'media_custos_cobertos',
                                                            'media_pagamento_total',
                                                            'media_gastos_cuidados'))
 

```
```python
carregar_dados(conn, diagnosticos, 'dados_analises', ('id',
                                                    'diagnostico',
                                                    'media_raio',
                                                    'media_textura',
                                                    'media_perimetro',
                                                    'media_area',
                                                    'media_suavidade',
                                                    'media_compactacao',
                                                    'media_concavidade',
                                                    'media_concavidade_pontos',
                                                    'media_simetria',
                                                    'media_dimensao_fractal',
                                                    'se_raio',
                                                    'se_textura',
                                                    'se_perimetro',
                                                    'se_area',
                                                    'se_suavidade',
                                                    'se_compactacao',
                                                    'se_concavidade',
                                                    'se_concavidade_pontos',
                                                    'se_simetria',
                                                    'se_dimensao_fractal',
                                                    'pior_raio',
                                                    'pior_textura',
                                                    'pior_perimetro',
                                                    'pior_area',
                                                    'pior_suavidade',
                                                    'pior_compactacao',
                                                    'pior_concavidade',
                                                    'pior_concavidade_pontos',
                                                    'pior_simetria',
                                                    'pior_dimensao_fractal'))
```

agora é só verificar no banco se os dados foram importados:
![image](https://user-images.githubusercontent.com/73916591/227285978-c69da432-4a02-41a0-afa5-3db07ff23fdf.png)

podemos ver que deu tudo certo com a solução, obrigado a todos que leram!
