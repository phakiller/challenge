# Desafio

### Descrição
Desenhar uma solução para um cenário de Micro/Nano Serviços, onde a stack tecnológica é aberta, porém as bases de dados candidatas a serem utilizadas tem que respeitar alguns requisitos.

> Ao longo do texto chamarei as Bases de: Base A, Base B, Base C


### Requisitos das Bases de Dados
| Base | Caracteristicas |
| :---: | :--- |
| *"Base A"* | Deve ter um segurança elevada e não necessita ser muito performática, pois armazenará dados sensíveis. |
| *"Base B"* | Ainda como a *"Base A"* é necessário um nível de segurança elevado, porém deve ser um pouco mais rápida, essa base será utilizada por algoritmos de Machine Learning. |
| *"Base C"* | Necessita ser extremamente rápida e não precisa ter um nível de segurança alta. |

# Solução Proposta

## Bases de Dados
Começando pelo mais importante na construção de um sistema, as Bases de Dados:
> Dei preferencia para Bases de Dados, Open Source, mas não deixando de lado a qualidade.

* Para a *"Base A"*, escolhi o [*PostgreSQL*](https://www.postgresql.org) por ser um **Banco de Dados Relacional** onde se tem um nível de segurança muito perto de BDs pagos, tem várias ferramentas para gerenciamento e uma comunidade ativa nos fóruns, o que facilita achar a solução de problemas.

* Para a *"Base B"*, escolhi o [*MongoDB*](https://www.mongodb.com) por ter um nível de segurança satisfatório e ser **NoSQL**, ele tem uma velocidade superior a Bancos Relacionais e facilita a utilização e extração de dados para fins de Machine Learning. O **MongoDB** trabalha com o armazenamento de dados como documentos.

* Para a *"Base C"*, escolhi o [*Elasticsearch*](https://www.elastic.co) porque além de armazenar dados de forma **não relacional**, ele tem um motor de busca textual extremamente rápido e provê uma infraestrutura interna muito boa para buscas extremamente pesadas. Com ele conseguimos *"inputar"* uma grande massa de dados ao mesmo tempo que consumimos esses dados.

> Alternativas pagas:
> - Base A: [SQL Server](https://www.microsoft.com/pt-br/sql-server/sql-server-2017)
> - Base B: [DynamoDB](https://aws.amazon.com/pt/dynamodb/)
> - Base C: [S3](https://aws.amazon.com/pt/s3/) utilizando [Athena](https://aws.amazon.com/pt/athena/) ou [Spectrum](https://docs.aws.amazon.com/pt_br/redshift/latest/dg/c-getting-started-using-spectrum.html)

## Linguagem

Como estamos falando de Micro e Nano Serviços, temos a vantagem de podermos utilizar linguagens/tecnologias diferentes para cada serviço, no entanto falarei aqui apenas sobre Python :heart:.
Por que, Python? Python é uma linguagem já conceituada que vem crescendo muito nos últimos anos, principalmente por sua facilidade de implementação, prover melhor abstração na hora do desenvolvimento e ter inúmeras bibliotecas e frameworks para diversos fins.

### Framework Sugerido
[*Nameko*](https://nameko.readthedocs.io) é um framework Python, para se criar Microserviços que provê uma gama de ferramentas, para comunicação entre serviços, injeção de dependências e tudo necessário para um desenvolvimento "tranquilo".

## Payloads

#### Serviço A
```json
{
    "cpf": "00000000000",
    "nome": "Fulano",
    "endereco": "Rua XXXX, XXX - XXXX",
    "dividas":[
        {
            "valor": 0.00,
            "empresa": "XXXXXXX",
            "dataVencimento": "XX/XX/XXXX",
            "datePagamento": "XX/XX/XXXX"
        }
    ]
}
```

#### Serviço B
```json
{
    "cpf": "00000000000",
    "dataNascimento": "XX/XX/XXXX",
    "idade": 20,
    "endereco": "Rua XXXX, XXX - XXXX",
    "fonteDeRenda": "XXXXXXXXX",
    "bens": [
        {
            "tipo": "Casa",
            "inicioPosse": "XX/XX/XXXX",
            "quitado": true
        }
    ]
}
```

#### Serviço C
```json
{
    "cpf": "00000000000",
    "ultimaConsulta": "XX/XX/XXXX",
    "ultimaCompraCC": {
        "data": "XX/XX/XXXX",
        "valor": 0.00
    },
    "movimentacaoFinanceira": [
        {
            "tipo": "cartao",
            "valor": 0.00,
            "data": "XX/XX/XXXX"
        }
    ]
}
```

## Ferramentas

### Bitbucket
[BitBucket](https://bitbucket.org) é um excelente sistema, que provê o versionamento de código com **Git** e algumas outras ferramentas como o *Bitbucket Pipilines*, onde conseguimos fazer o deploy(com testes automatizados) diretamente da nossa branch utilizando os servidores deles para fazer o build das imagens, caso esteja trabalhando com **Docker**.

### Docker
[Docker](https://www.docker.com) é uma excelente ferramenta para orquestração de *containers*, com ele conseguimos subir e atualizar serviços em instantes e com um *"downtime"* muito pequeno. Com ele também temos uma padronização de ambientes de Desenvolvimento e Produção.

### Sentry
[Sentry](https://sentry.io) é uma plataforma online que nos dá um visão geral dos erros de nossos serviços, conseguimos ver os valores das variáveis quando ocorreu o erro, assim podemos identificar mais fácil o problema e fazer o *"fix"*. Sua integração com serviços em Python é bastante simples.

### Swagger
O [*Swagger*](https://swagger.io/tools/swagger-ui/) permite que documentemos nossos *End-Points*, é muito importante termos uma documentação para que possamos compartilhar com que irá utilizar nossos serviços.

### Portainer
[Portainer](https://portainer.io) é uma ferramenta para a administração/orquestração de containers por meio de uma interface visual.

### AWS(Amazon Web Services)
A [AWS](https://aws.amazon.com/pt/) provê vários serviços e ferramentas para manter as aplicações em sua infraestrutura na nuvem.

# Overview Simples da Plataforma
![Diagrama](https://raw.githubusercontent.com/phakiller/challenge/master/images/desafio.png "Diagrama")

Na imagem acima se tem um **Overview** simples de como ficaria, com os 3 bancos de dados e os serviços em containers rodando no AWS Fargate.

# Segurança e Sugetões

* Para colocar as bases de dados de pé, pode-se usar um [**EC2**](https://aws.amazon.com/pt/ec2/), porém é altamente recomendado ter as bases de dados em máquinas separadas, ou ainda, para não precisar se preocupar a AWS já oferece a máquina com a base de dados, onde é só pegar e usar.

* Trabalhando com Docker em um ambiente *"Clusterizado"* pode-se colocar informações sensíveis em um mecanismo do próprio Docker, que são as [**Secrets**](https://docs.docker.com/engine/swarm/secrets/), onde estes dados ficam disponiveis em todos os nós e só pode ser acessada dentro dos containers.