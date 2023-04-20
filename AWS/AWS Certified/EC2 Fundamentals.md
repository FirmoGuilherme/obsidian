
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


# Instance Purchasing Options 
* On-Demand Instances -> short workload, pay by second
* Reserved (1 & 3 years)
	* Reserved Instance - long workloads
	* Convertible Reserved Instances
* Saving Plans (1 & 3 years) - commitment to an amount of usage, long workload
*  Spot Instances - short workloads, cheap, can lose instances
* Dedicated Hosts - book an entire physical server, control instance placement
* Dedicated Instances - no othe customers will share your hardware
* Capacity Reservations - reserve capacity in a specific AZ for any duration

## On Demand
* Pay for what you use:
	* Linux or Windows - billing per second, after the first minute
	* All othe operating systems - billing per hour
* Has the highest cost but no upfront payment
* No long-term commitment
* Recommended for short-term and up-interruped workloads, where you cant't predict how the application will behave

## Reserved Instances
* Up to 72% discount compared to On-demand
* You reserve a specific instance attributes(Instance Type, Region, Tenacy, OS)
* Reservation Period - 1 year (+discount) or 3 years (+++discount)
* Payment Options - No Upfront (+), Partial Upfront (++), All Upfront(+++)
* Reserved Instance's Scope - Regional or Zonal (reserve capacity in an AZ)
* Recommended for steady-state usage applications (think database)
* You can buy and sell in the Reserved Instance Marketplace

## Convertible Reserved Instance
* Can change the EC2 instance type, instance family, OS, scope and tenancy
* Up to 66% discount

## Savings Plans
* Get a discount base on long-term usage (up to 72% - same as Reserved Instance's)
* Commit to a certain types of usage (U$10/hour for 1 or 3 years)
* Usage beyond EC2 Savings Plans is billed at the On-Demand price
* Locked to a specific instance family & AWS region
* Flexible across:
	* Instance Size (m5.xlarge, m5.2xlarge)
	* OS (Linux, Windows)
	* Tenacy (Host, Dedicated, Default)

## Spot Instances
* Can get a discount of up to 90% compared to On-demand
* Instances that you can "lose" at any point of time if your max price is less than the curret stop price
* The MOST cost-efficient instances in AWS
* Useful for workload that are resilient to failure
	* Batch jobs
	* Data analysis
	* Image processing
	* Any distributed workloads
	* Workloads with a flexible start and end time
* Not sitable for critical jobs or database

## Dedicated Hosts
* A physical server with EC2 instance capacity fully dedicated to your use
* Allows you address compilance requirements and use your existing server-bound software licenses (per-socket, per-core, per VM software licenses)
* Purchasing Options:
	* On-demand - pay per second for active Dedicated Host
	* Reserved - 1 or 3 years (No Upfront, Patial Upfront, All Upfront)
* The most expensive option
* Useful for software that have complicated licensing model (BYOL - Bring Your Own License)
* Or for companies that have strong regulatory or compilance needs

## Dedicated Instances
* Instances run on hardware that's dedicated to you
* May share hardware with other instances in same account
* No control over instance placement (can move hardware after Stop/Start)

## Capacity Reservations
* Reserve On-Demand instances capacity in a specific AZ for any duration
* You always have access to EC2 capacity when you need it
* No time commitment (create/cancel anytime), no billing discounts
* Combine with Regional Reserved Instances and Savings Plans to benefit from billing discounts
* You're charged at On-Demand rate wheter you run instances or not
* Suitable for short-term, uninterrupted workloads that needs to be in a specific AZ

# Which purchasing option is right for me? (Hotel)
* On demand: comming and stayin in resorte whenever we like, we pay the full price
* Reserved: like planning ahead and if we plan to stay for a long time, we may get a good discount
* Savings Plans: pay a certain amount per hour for certain period and stay in any room type (King, Suit, Sea view, ...)
* Spot instances: the hotel allows people to bid for the empty rooms and the highest bidder keeps the rooms. You ger kicked out at any time
* Dedicated Hosts: We book an entire building of the resort (My own resort)
* Capacity Reservations: you book a room for a period with full price even you don't stay in it