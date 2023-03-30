
# EC2 Basics
* Elastic Compute Cloud
* Criar máquinas virtuais (EC2)
* Guardar dados em bancos de dados virtuais (Elastic Load Balance, EBS)
* Distribuir carga entre máquinas (Auto-scaling group, ASG)
* Linux (Mais popular), Windows, Mac
* Quantidade de CPU que queres/ potencia (CPU)
* Quantidade de memória para a máquina (RAM)
* Armazenamento
	* Network-attached (EBS & EFS)
	* hardware (EC2 Instance Store)
* Tipo de rede que iremos conectar na máquina
* regras de Firewall : grupo de segurança
* Bootstrap script(configurado no primeiro uso): EC2 User Data
* EC2 User Data é um script que serve para configurarmos o que a máquina irá executar quando ela iniciar como um meio de automatizar tudo, por exemplo, instalar softwares como python, docker, java dentre outras coisas.

# EC2 Instance Types Basics
* Atualmente temos 8 tipos de instâncias do EC2, dentre elas: General Purpose, Compute Optimized, Memory Optimized, Accelerated Computing, Storage Optimized, HPC Optimized, Instance Features, Measuring Instance Performance. (https://aws.amazon.com/ec2/instance-types/?nc1=h_ls)
* Para cada tipo temos diferentes famílias
* Convenção de nomenclatura:
![[Pasted image 20230330193552.png]]
* **General Purpose** serve para atividades de alta escala como servidores web ou repositórios de códigos e tem um bom equilibrio em questão de performance, memória e conexão de rede. A letra será T ou M
* **Compute Optimized** serve para tarefas intensivas/repetitivas e que requerem um processador de alto nível, por exemplo, machine learning, transferencia de dados, servidor para jogos, High performance computing (HPC), etc... A letra será o C
* **Memory Optimized** serve para trabalhos com alta demanda de memória RAM, por exemplo, banco de dados relacionais ou não relacionais, banco de dados inteligentes (BI) e processamentos em tempo real de grandes dados não estruturados. A letra será R, X ou Z, X e Z para maiores memórias.
* **Storage Optimized** serve para acessar muitos conjunsto de dados no armazenamento local, por exemplo, Bancos de dados Relacionais e não relacionais, aplicativos de armazenamento e High frequency online transaction processing (OLTP) systems. A letra será I, D ou H1.

# Security Groups & Classic Ports Overview
* São fundamentais para a segurança da rede na nuvem AWS
* Controlarão como será o tráfego dentro ou fora das instancias EC2
* São simples pois apenas dizemos o que é permitido entrar e sair
* Grupos de Segurança agem como um firewall nas instâncias EC2
* E vão regular o acesso às portas, IP's autorizados (IPv4 e IPv6), Controlam a entrada de rede (De outra instância para a nossa instância) e a saída (Da nossa instância para outra)
* Bom saber:
	* Grupos de segurança podem ser utilizados em várias intâncias e também um instância pode ter vários grupos de segurança
	*  São fechados para regiões/VPC especificas, ou seja se mudarmos de região teremos que criar um novo grupo de segurança
	* É bom manter os grupos de segurança separados para o acesso com a SSH
	* Se a aplicação não está acessando ou está dando time out, isso é problema de gurpo de segurança
* Classic Ports to know:
	* 22 = SSH(Secure Shell) - log into a Linux instance
	* 21 = FTP (File Transfer Protocol) - upload files into a file share
	* 22 = SFTP (Secure File Transfer Protocol) - upload files using SSH
	* 80 = HTTP - access unsecured websites (Sites não seguros)
	* 443 = HTTPS - access secured websites (Sites seguros)
	* 3389 = RDp (Remote Desktop Protocol) - log into a Windows instance
* 