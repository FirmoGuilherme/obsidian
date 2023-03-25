
# IAM Introduction (User, Groups, Polices)
* IAM = Identity and Acces Management, Global Service
* Configuração de conta
* Grupos contém apenas usuário, não outros grupos

## Em resumo:
IAM serve para gerenciar os usuários de nossa empresa que estão usando o AWS. Podemos ter diversos grupos com diversos usuários, e esses usuários tem acesso as finalidades específicas de seu grupo. Isso serve para que um usuário não tenha o "poder" de fazer tudo dentro da empresa, apenas o necessário para seu trabalho.


# IAM Polices
![[Pasted image 20230315180029.png]]

* Estrura do Police![[Pasted image 20230315180101.png]]
* Legenda:
	* Verison: Versão do Police;
	* Id: Identificação do Police;
	* Statement: Lista de configurações que podem ser feitas (Pode ter uma ou mais);
		* Sid: Identificador da configuração;
		* Effect: Define o aceeso da configuração estar Permitido (Allow) ou Negado (Deny);
		* Principal: Define qual usuário tem acesso a Police
		* Action: Lista de ações que poderão ser chamadas pela API com base no efeito;
		* Resource: Lista de recursos para as ações que serão aplicadas;

# IAM - Password Policy
* Na AWS podemos definir requisistos para a senha que o usuário quer criar, por exemplo, podemos por exemplo exigir que tenha no minímo um caracter especial, ou que tenha 4 numeros, dentre outras coisas.
* Também podemos definir se a senha pode ser alterada depois de salva ou não.
* Ou também exigir que a senha seja alterada de tempos em tempos;
* E ainda bloquear usar uma senha que já foi usada anteriormente.

* MFA serve para termos mais segurança do que apenas um senha;
* MFA -> Senha + dispositivo de segurança (Celular);

# IAM - Roles
* Não são usados por pessoas e sim pelos serviços da AWS
* Roles comuns:
	* EC2 Instance Roles
	* Lambda Function Roles
	* Roles for CloudFormation

# IAM - Best Practices
* Não usar o conta root, exceto para configurar as contas AWS;
* Sempre criar um novo usuário para uma nova pessoa, nunca passar seus dados para ela
* Criar grupos e permissões para esses grupos
* Criar uma politica de senhas fortes
* Usar sempre o MFA
* Criar e usar as Roles para dar permissão aos serviços AWS
* Usar chaves de acesso para CLI e SDK
