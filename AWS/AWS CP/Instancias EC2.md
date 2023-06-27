
Elastic Compute Cloud

### Vantagens
* Baixo custo
* Descomplicado
* Seguro
* Controle
* AWS Services (Arquivos no S3, dominio de site, etc...)



### ECS = Docker
* Container -> Tasl
* Image -> Register (Elastic Container Register - ECR)
* ECS Service configura os container

Existem dois tipos de ECS, um é o ECS EC2 cluster que é o usuário que manuseia, configura e tudo mais, ou seja, **não é serveless** e temos também o ECS Fargate que é **serveless** ou seja, menos responsabilidade para o usuário(a AWS faz tudo pra você).

Podemos também configurar um código padrão para iniciar máquinas EC2 mais facilmente, isso se chama User Data, criamos um código para por exemplo, quando a máquina for iniciada ele atualizar o sistema, instalar python, docker e react com isso não precisamos ficar criando tudo na mão várias vezes.

Para acessar outros serviços da AWS pela CLI precisamos de uma autenticação do IAM User(AWS_ACCESS_KEY, AWS_ACCESS_SECRET_KEY, etc..) porém esse não é o método mais seguro tendo em vista que um hacker pode entrar na sua máquina e ter todas suas credenciais. Para isso criamos um IAM Roles com acesso necessário.

O AWS LightSail é um EC2 mais simples de ser montado, geralmente as empresas que precisam de apenas 1 ou 2 servidores utilizam eleS

