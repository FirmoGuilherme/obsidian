
### Serveles

* O IAM é um serviço de segurança da AWS, onde podemos gerenciar o nosso usuário padrão(root) e criar novos usuários por exemplo para desenvolvedores.

* Podemos separar os usuários por grupos, por exemplo:
``` mermaid
graph TD
DEV[Desenvolvedores] --> S[Sid]
DEV[Desenvolvedores] --> R[Régis]
DEV[Desenvolvedores] --> J[João]

INFRA[Infraestrutura] --> L[Lucas]
INFRA[Infraestrutura] --> RI[Ricardo]
```
Ou seja o time de desenvolvedores tem acesso ao lambda e s3 bucket já o time de infraestrutura tem acesso às maquinas EC2, EBS, lambdas, s3 dentre outros... Os grupos serem para separar os usuários, mas lembrando que um usuário pode estar em dois grupos ao mesmo tempo!

* O IAM policy é um JSON criado para dar permissões aos usuários, por exemplo, a permissão de acesso geral apenas ao S3 ficaria dessa forma:
```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:*",
                "s3-object-lambda:*"
            ],
            "Resource": "*"
        }
    ]
}
```
* Mas se quisermos que ele somente possa ler o que tem no s3, sem poder alterar e adicionar nada o JSON ficaria deste modo:
``` JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:Get*",
                "s3:List*",
                "s3-object-lambda:Get*",
                "s3-object-lambda:List*"
            ],
            "Resource": "*"
        }
    ]
```
* Ou seja, a AWS oferece permissões "padrões" mas ainda conseguimos personalizas como quisermos por meio do IAM Policy

* Temos também a autenticação MFA(*Mult-factor authentication*) que é um código gerado por um aplicativo ou token de acesso que nos da 6 números para ter maior segurança ao logar no console da AWS.

* Por fim temos a forma de entrar na AWS por CLI ou API que precisamos de uma AWS_ACCESS_SECRET_KEY e AWS_ACCESS_KEY_ID (Usar como exemplo os QS da EVI)