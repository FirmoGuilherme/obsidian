
* S3
* Glacier
* EFS
* Snowball
* CloudFront
* Storage Gateway
* EBS
* Instance Store (EC2)

**throughput -> taxa de transferencia**


### Block Storage
* Baixa latência, blocos, volumes(hd)
* Comparado com a DAS (Attatched Storage)
* Blocos conectados a uma instancia única

### File Storage
* Armazenar um arquivo para depois serem compatilhados
* Dados -> Compartilhamento
* NAS (Network Attached Storage)

### Object Storage
* Obejtos
	* ID
	* Flat Addres (URL)
	* Metadata (Meta-dados)


# EBS
* O EBS precisa sempre estar na mesma AZ da instância EC2;
* Para "copiar" um EBS em outra AZ nós precisamos fazer uma snapshot, que seria o seguinte passo a passo: Backup -> move o backup para a outra AZ -> e a partir do backup cria um novo EBS;
* Pode conectar 16 intancias no mesmo EBS, porém o tipo do disco tem que ser **IOP1** e as instancias tem que ser do tipo **Nitro**. Se naõ estiver desse modo você só pode conectar o EBS somente a 1 EC2. O nome deste processo é **multi-attach**;
* Tipos:
	* Hdd = Lento e mais barato;
	* Ssd = Rapido e mais caro;

# S3 (Simple Storage Service)
* Bucket (Pastas)
* O bucket tem que ter um nome único, mais ninguém no mundo terá o mesmo nome.
* O espaço do bucket é infinito porém ele só permite uploads até 5 TB, ou seja, se você quiser armazenar subir um arquivo de 10tb você pode porém terá que separálo em duas partes.
* Durabilidade -> 99,999999999%
* Disponiblidade -> 99,95% à 99,99%


# AMI (Amazon Machine Image)
* Public -> AWS/ Empresas terceiras
* Private -> Minhas imagens
* A AMI serve baiscamente para criarmos uma snapshot e usar em outra AZ, por exemplo se intalamos um servidor com diversas configs e precisamos colocar em outro páis vamos apenas criar uma Imagem do servidor e no outro país iremos usar esta imagem, facilitando o trabalho de recriar tudo.
* E se a AMI for "útilizavel" para outras pessoas, podemos coloca-las públicas, por exemplo uma máquina que já vem com python, pycharm, docker, teams, etc... Instalado

# Tipos de S3
## Standard
É o tipo padrão e mais caro, porém mais seguro e rápido, geralmente usado para empresas que movem arquivos e usam-os frequantemente
## Intelligent-Tiering
Se você não usa um arquivo por mais de 30 dias ele é movido para o nível "Infrequent Access" e após 90 dias para o "Archive Instant Access". Com isso você garante 68% de economia no custo, mas você pode obter até 95% de economia se adicionar mais um nível, o "Deep Archive Access" que é configurado para os arquivos que não exigem recuperação imediata e que não são acessados a 180 dias ou mais. 
## Standart IA (Infrequent Access)
Indicado para dados acessados com menos frequencia, mas que exigem acesso rápido quando necessário, ofertado para armazenamento de longa duração, backups e datastores para arquivos de recuperação de desastres.
## One Zone-Infrquent-Access
Simplesmente a mesma coisa que o de cima porém em uma zona apenas, ou seja se a zona está indiponivel, você não conseguirá pegar os dados
## Glacier Instant Retrieval, Glacier Flexible Retrieval
Basicamente a mesma coisa, você paga mais barato para arquivos que você acessa somente 1 ou 2 vezes no ano porém o tempo de recuperação deles é mais longo de 1 minuto a 12 horas
## Gaclier Deep Archive
Serve para guardar arquivos legais, como processos, BO's e outras coisas que precisam ficar armazenadas durante 10 anos ou mais, o deep archive já leva até 12 horas para serem restaurados
## Outposts
Ideal para carga de trabalho com requisitos de residência de dados locais e para satisfazer as necessidades de desempenho exigentes, mantendo os dados próximos aos aplicativos locais.


# Versionamento S3
![[Pasted image 20230629210024.png]]


# S3 life cycle
Você pode criar regras de mudanças de classes, ou seja se você quer que os arquivos sejam alterados para o Infrequent Access após 15 dias sem usar e assim por diante, é basicamento o Intelligent-Tiering mas quem configura é você.

# S3 Glacier
* Archive (Arquivos que não vão ser acessados por muito tempo)
* Muito usado para Backup
* Classes:
	1. Glacier (Você paga barato e você pode pegar os objetos com uma quantidade de tempo menor); Colocar os produtos na geladeira
	2. Glacier Deep Archive (Coloca os objetos e esquece eles lá e é muito mais barato); Colocar os produtos no freezer
* Temos 3 tipos para pegar os dados:
	* Expedited que no Glacier é de 1 a 5 min e não se tem no Deep Archive, consequentemente pela velocidade é a mais cara
	* Standard no Glacier leva de 3 a 5 Horas e no Deep Archive leva até 12 horas, conseguentemente mais barato
	* Bulk no Glacier leva de 5 a 12 horas e no Deep Archive leva até 48h, consequentemente mais barato que as outras duas.
* S3 Obejct Lock e Glacier Vault Lock são configurações que vem com o S3 Glacier, são objetos que não podem ser alterados por conta de um contrato ou algo do tipo (Ex.: Processos).

# AWS Storage Gateway
* Híbrido, tanto no S3 quanto on premessie;
* Backup do on premisse para o S3;
* A AWS fornece um dispositivo (AWS Gateway) para colocar na empresa para fazer uma conexão segura com o S3;
* Precisamos configurar o dispositivo para saber o que será mandado, temos 3 opções, File Gateway, Volume Gateway e Backup Gateway, onde:
	* File Gateway: S3
	* Volume Gateway: Block Storage
	* Backup: Um dos dois, mas por padrão usa o Block