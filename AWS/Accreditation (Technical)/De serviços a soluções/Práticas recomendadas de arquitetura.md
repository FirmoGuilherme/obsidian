# Práticas recomendadas de arquitetura na nuvem
1. Fazer um design à prova de falhas e nada vai falhar
2. Criar segurança em cada camada
3. Aproveitar diferentes opções de armazenamento
4. Implementar a elasticidade
5. Pensar paralelo
6. O acomplamento fraco liberta você
7. Não temer restrições

## 1
### Evitar pontos únicos de falha
### Várias instâncias
### Várias zonas de disponibilidade
### Separar um único servidor em vários aplicativos em camadas
### Para o Amazon RDS, usar o recurso Multi-AZ

## 2
### Criptografar dados em repouso e em trânsito
### Aplicar o princípio de menor privilégio no [IAM](Segurança.md)
### Implementar grupos de segurança e lista de controle de acesso à rede [(NACL)](Redes.md)
### Considere recursos e serviços avançados de segurança

## 3
### Mover ativos estáticos da web para o [Amazon S3](Armazenamento.md)
### Usar o Amazon CloudFront para servir globalmente
### Armazenar o estado da sessão no DynamoDB
### Usar o ElasticCache entre hosts e banco de dados

## 4
### Implementar políticas de Auto Scalling
### Arquitetar resiliência para reinicializar e relançar
### Usar serviços gerenciados como o Amazon S3 e o Amazon DynamoDB

## 5
### Dimensionar horizontalmente, não verticalmente
### Desacoplar a computação da sessão/estado
### Usar o [Elastic Load Balancig](Computação.md)
### Dimensionar corretamente a infraestrutura

## 6
### Em vez de um único fluxo de trabalho ordenado, usar várias filas
### Usar o Amazon Simple Queue Service e o Simple Notification Service (SQS e SNS)
### Aproveitar os serviços existentes

## 7 
### Repensar restrições tradicionais
### Precisa de mais RAM? (Instalar mais RAM)
### Melhor IOPS para banco de dados? (Retrabalhar meticulosamente um esqueme para aumentar o IOPS)

# O que é Well-Architected Framework?
## Ajudar os clientes a criar a infraestrutura mais segura, de alto desempenho, resiliente, sustentável e eficiente possível de modo nativo na nuvem.
## Práticas desenvolvidas por meio da revisão das arquiteturas dos clientes na AWS
## Abordagem sistemática para avalaiar e implementar arquiteturas

# Os seis pilares
## Excelência operacional
## Segurança
## Confiabilidade
## Eficiência de desempenho
## Otimização de custos
## Sustentabilidade

# Dimensionamento correto de instâncias
## O dimensionamento correto é o processo de avaliar os recursos implantados e buscar oportunidades de redução sempre que possível. Por exemplo, se uma instância de aplicativo estiver subutilizando consistentemente sua RAM e CPU, alternar para uma instância menor poderá oferecer economias significativas enquanto mantém o mesmo desempenho.

# Aumento da elasticidade do aplicativo
##  Um exemplo é usar o auto scaling para garantir que o número correto de instâncias esteja disponível para lidar com a carga de trabalho de um aplicativo. Aumentar a quantidade durante a demanda alta e reduzi-la durante a demanda baixa.

# Escolha do modelo correto de preço
## Um exemplo é usar instâncias reservadas para cargas de trabalho que precisam ser executadas a maior parte ou o tempo todo, como ambientes de produção. Isso pode ter um impacto significativo na economia em comparação com o modelo sob demanda; em alguns casos, até 75 por cento.

# Otimização do armazenamento
## Um exemplo é a classe de armazenamento S3 Intelligent-Tiering, projetada para otimizar os custos ao mover os dados automaticamente para a camada de armazenamento mais econômica.

# Estrutura de adoçao da nuvem AWS ( AWS Cloud Adoption Framework)
## Migrar para a nuvem é um processo
## A migração bem-sucedida para a nuvem requer conhecimento especializado
## Aproveitar diferentes perpectivas
## Garantir que você tenha o talento certo
## AWS CAF permite uma migração para a AWS sem problemas
## Perspectiva empresarial:
### Garantir que a TI esteja alinhada com os negócios
### Cria um caso de negócios robusto para a adoção da nuvem
### Garantir o alinhamento dos negócios com a TI
### Funcções comuns:
#### Gerentes de negócios
#### Gerentes financeiros
#### Responsáveis pelo orçamento
#### Stakeholders estratégicos
## Perspectiva das pessoas:
### Apoiar a estratégia de gerenciamento de alterações
### Avaliar estruturas organizacionais e funções
### Avaliar novas habilidades e requisistos de processo
### Identificar pontos para melhorias
### Priorizar o treinamento 
### Funções comuns:
#### Recursos humanos
#### Pessoal
#### Gerente de pessoas
## Perspectiva de governança:
### Foco em habilidades e processos
### Garantir que os valores comerciais sejam maximizados e os ricos sejam minimizados
### Atualizar as habilidades e os processos da equipe
### Mensurar os investimentos em nuvem para avaliar os resultados de negócios
### Funções comuns:
#### Chief Information Officer (CIO)
#### Gerentes de Programas
#### Arquitetos Empresariais
#### Analistas de Negócios
#### Gerentes de Portfólio
## Perspectiva de plataforma:
### Implementar novas soluções na nuvem
### Migrar cargas de trabalho on-premises para a nuvem
### Compreender e comunicar a estrutura dos sistemas de TI e as relações entre eles
### Descrever a arquitetura do ambiente de estado de destino em detalhes
### Funções comuns:
#### Diretor de Tecnologia (CTO)
#### Gerentes de TI
#### Arquitetos de soluções

## Perspectiva de segurança:
### Atender aos objetivos de segurança para visibilidade, auditabilidade, controle e agilidade
### Estruturar a seleção e implementação de controles de segurança
### Funções comuns:
#### Diretor de segurança de informação (CISO)
#### Gerentes e analistas de segurança de TI

## Perspectiva de operações:
### Habilitar, executar, usar, operar e recuperar cargas de trabalho de TI.
### Definir como os negócios são conduzidos
### Alinhar e apoiar as operações de negócios
### Definir os procedimentos operacionais atuais
### Funções comuns:
#### Gerentes de operações de TI
#### Gerentes de suporte de TI