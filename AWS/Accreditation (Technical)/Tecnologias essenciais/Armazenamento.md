# Serviços de Armazenamento
## Amazon Elastic Blcok Store (EBS)
### Armazenamento persistente em nível de bloco;
### Como um disco rígido para instâncias do EC2.
## Amazon S3
### Armazenamento de objetos durável e minesionável;
### Armazenamento na internet.
## Amazon S3 Glacier
### Arquivamento e backup de dados.
## AWS Storage Gateway
### Integre armazenamento na nuvem com cargas de trabalho no local.
## Amazon Elastic File System
### Armazenamento de aruqivo para instâncias do Amazon EC2;
### O armazenamento é elastico ou seja o tamanho aumenta ou diminuia dependendo da quantidade de arquivos.
## Amazon FSx
### Armazenamento de arquivos para sistemas de arquivo amplamente usados.

# Amazon Elastic Block Store (EBS)
## Armazenamento em bloco anexo à rede para uso com as Instâncias do Amazon EC2
### Persiste independentemente da instância
### Usado como um disco rígido físico
### Replicação automática para evitar perca de dados caso haja alguma eventual falha
### Anexado a qualquer instância na mesma AZ
### Uma instância para vários volumes do EBS
### Os volumes do EBS podem reter dados após o encerramento da instância do EC2

# Amazon Simple Storage Service (S3)
## Dimensionamento infinito, maior análise e recuperação de dados mais rápida
## Para cada dez milhões de objetos armazenados pode-se esperar uma perda média de UM objeto a cada 10 MIL anos
## Casis de uso comuns do S3:
### Data lakes;
### Bakcup e armazenamento;
### Hospedagem de Aplicativos;
### Hospedagem de mídia
### Entrega de software

# Categorias de armazenamento no Amazon S3
## Amazon S3 Standard
### Dados acessados com frequência.
## Amazon S3 Standard-Infrquent Access
### Dados acessados com menos frequência mas que exigem rapidez quando necessário
### Parecido com o Amazon S3 Standard porém com um preço menor de armazenamento por GB
### Ideal para armazenamento de longo prazo, backups e armazenamento de dados para arquivos de recuperação de desastres
## Amazon S3 One Zone-Infrequent Access
### Dados acessados com menos frequência
### Ideal para clientes que não precisam da disponibilidade e da resiliencia das Categorias Standard
### Cópia de backup secundárias
## S3 Glacier storage classes
### Armazenamento seguro, durável e de baixo custo para arquivamento de dados

# Resumo
#### EBS fornece discos para minhas instâncias;
#### Amazon S3 para armazenamento em massa;
#### Glacier com custo mais baixo para armazenamento de backups de longo prazo.