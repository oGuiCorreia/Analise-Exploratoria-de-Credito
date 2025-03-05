# Análise de Crédito em SQL  

Este repositório contém a exploração e análise de dados de crédito de clientes de um banco, utilizando SQL no AWS Athena.  

Dados:

Nesta base de dados contamos com as informaçoes do cliente do banco com as seguintes colunas :

    idade = idade do cliente
    sexo = sexo do cliente (F ou M)
    dependentes = número de dependentes do cliente
    escolaridade = nível de escolaridade do clientes
    estado_civil = estado civil do cliente
    salario_anual = faixa salarial do cliente
    tipo_cartao = tipo de cartao do cliente
    qtd_produtos = quantidade de produtos comprados nos últimos 12 meses
    iteracoes_12m = quantidade de iterações/transacoes nos ultimos 12 meses
    meses_inativo_12m = quantidade de meses que o cliente ficou inativo
    limite_credito = limite de credito do cliente
    valor_transacoes_12m = valor das transações dos ultimos 12 meses
    qtd_transacoes_12m = quantidade de transacoes dos ultimos 12 meses

📌 Nota: Esta análise foi realizada para fins educacionais e não utiliza o dataset completo. O objetivo é explorar conceitos de SQL e análise de dados de forma didática, evitando custos adicionais com processamento. Caso deseje trabalhar com o conjunto de dados completo, ele está disponível em [Dataset de Crédito](https://github.com/andre-marcos-perez/ebac-course-utils/tree/main/dataset) 


## Exploração dos Dados 

### Quantidade total de itens na base de dados 

**Query:** SELECT COUNT(*) FROM credito;

Saída: *A base de dados contém 2564 linhas.*

## Primeiras Linhas da Tabela

**Query:** SELECT * FROM credito LIMIT 8;

![Primeiras Linhas](https://github.com/oGuiCorreia/EbacSql-/blob/main/primeiras_linhas.png)

Saída: Observamos que há valores nulos (NA) em algumas colunas.

## Valor aceito em cada coluna na tabela

**Query:**  Describe credito;

![Descrição Tabela](https://github.com/oGuiCorreia/EbacSql-/blob/main/DescricaoDados.png)

## Tipos de escolaridades disponiveis no dataset

**Query:** Select distinct escolaridade from credito;

![Tipos Escolaridade](https://github.com/oGuiCorreia/EbacSql-/blob/main/Escolaridade.png)

**Saída: Há variedade nos níveis de escolaridade cadastrados, mas também há valores nulos (NA).**

## Tipos de Cartão

**Query:** SELECT DISTINCT tipo_cartao FROM credito;

![Tipos Cartões](https://github.com/oGuiCorreia/EbacSql-/blob/main/Tipo_Cartao.png)

**Saída: Há 4 tipos de cartões disponíveis (gold, blue, silver e platinum).**

## Estimativa Salarial 

**Query:** Select distinct salario_anual from credito;

![Estimativa Salarial](https://github.com/oGuiCorreia/EbacSql-/blob/main/Salario_anual.png)

**Saída: A faixa salarial é categorizada em intervalos anuais. Também há registros com valores nulos.**

# Análises

Agora como sabemos como o dataset estra estruturado e suas colunas, podemos analisar suas informações:

**Variação de Idade dos Usuários**

**Query:** 
        SELECT AVG(idade) AS media_idade, 
               MIN(idade) AS idade_minima, 
               MAX(idade) AS idade_maxima 
        FROM credito;

![Variação de idade](https://github.com/oGuiCorreia/EbacSql-/blob/main/Variacao_Idade.png)

Saída: A idade dos clientes varia de 26 a 73 anos, com média de 45 anos.

## Quantos clientes temos de cada faixa salarial

**Query:** 
        Select count(sexo) as Quantidade_Usuarios, 
        salario_anual 
        from credito 
        GROUP BY salario_anual;

![Clientes por faixa salarial](https://github.com/oGuiCorreia/EbacSql-/blob/main/Quantidade_Usuarios_Salario_Anual.png)

**Saída: Podemos perceber que temos uma variação entre a faixa salarial, mas a maior quantidade de usuários tem o salario menor que   R$ 40K tendo no total 701 usuários**

## Quantidade de usuarios em cada tipo de cartão

**Query:** 
        Query: Select count(sexo) as Quantidade_Usuarios, 
        tipo_cartao 
        from credito 
        GROUP BY tipo_cartao;

![Clientes por cartao](https://github.com/oGuiCorreia/EbacSql-/blob/main/Quantidade_Usuarios_Tipo_Cartao.png)

**Saída: A maioria dos clientes possui o cartão "Blue".**

 ## Limite minimo e o maximo de crédito no dataset

 **Query:** 
         Select min(limite_credito) as min_limite_credito, 
         max(limite_credito) 
         from credito limit 1;

![Limite max e min de creidito](https://github.com/oGuiCorreia/EbacSql-/blob/main/Min_max_limite_credito.png)

## Maiores limites de Crédito

**Query:**  
        Select max(limite_credito) as max_limite_credito, 
        salario_anual, 
        tipo_cartao 
        from credito 
        where tipo_cartao != 'na' and salario_anual != 'na' 
        GROUP BY salario_anual, tipo_cartao 
        order by max_limite_credito desc limit 10

![Maiores limites credito](https://github.com/oGuiCorreia/EbacSql-/blob/main/Max_credito_Descricao.png)

**Saída: O maior limite de crédito pertence a um cliente com cartão "Blue". Apenas um cartão "Platinum" aparece no top 10.**

## Quantidade de Produtos Comprados x Meses Inativos por Tipo de Cartão

**Query:** 
          select tipo_cartao, 
          sum(qtd_produtos) as total_produtos_comprados, 
          sum(meses_inativo_12m) as total_meses_inativos, 
          count(*) as qnt_usuarios 
          from credito 
          group by tipo_cartao 
          order by total_produtos_comprados desc

![Produtos Comprados x Meses Inativos](https://github.com/oGuiCorreia/EbacSql-/blob/main/Media_prod_comprados_Meses_inativos.png)

**Saída: Cartões "Blue" e "Silver" lideram em média de produtos comprados por usuário.**

## Valor das Transações Anuais x Quantidade de Transações por Tipo de Cartão

**Query:** 
          select tipo_cartao, 
          sum(valor_transacoes_12m ) as total_valor_transacoes, 
          avg(valor_transacoes_12m ) as media_valor_transacoes, 
          sum(qtd_transacoes_12m) as total_quantidade_transacoes, 
          avg(qtd_transacoes_12m) as media_qnt_transacoes, 
          count(*) as qnt_usuarios 
          from credito 
          GROUP BY tipo_cartao 
          ORDER BY total_quantidade_transacoes desc

![Valor Transações Anuais x Qnt. Transações](https://github.com/oGuiCorreia/EbacSql-/blob/main/Valor_transacoes_e_Total_de_Transacoes.png)

**Embora cartões "Blue" e "Silver" dominem em volume de transações, o cartão "Gold" destaca-se pelo maior valor médio por transação.**

# CONCLUSÃO 

Essas foram algumas das analises possiveis com o dataset

*Insights:*

> Podemos ver que a maior parte dos clientes tem renda menor que 40K

> Temos cartões blue e silver dentre maiores limites de credito

> Possuimos poucos cartoes platinum e um deles não aparece no top 10 de limite de credito

> O cartão gold possui as melhores medias no quesito de valores e quantidade de transações

> O cartão com o maior limite é um blue

*Uma otima opção para o banco em questão para melhoria seria uma análise mais profunda e detalhada sobre a oferta de cartões melhores para seus clientes. Isso se baseia no fato que metade de seus maiores limites de credito sao cartões blue(3) e silver(3). Assim poderia ser uma estratégia interessante para atrair clientes de maior potencial de crédito oferecer cartões melhores e com mais vantagens para usuários com limites altos*


## Como Executar as Consultas
*As consultas SQL podem ser executadas no AWS Athena ou em outro banco de dados compatível.*


📌  Autor: Guilherme Quirino Correia

📌  Contato: www.linkedin.com/in/guilherme-quirino-correia



