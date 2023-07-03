
# DNS
* DNS = Domain Name Sistem
* Em resumo o DNS é uma "máscara" para o endereço ip do site, por exemplo o site da work é um ip (123.4.5.67) porém para os clientes seria feio e complicado ficar acessando o site 123.4.5.67 é muito mais fácil digitarem workdb.com.
* Quando vamos acessar um site pela primeira vez percebemos que é um pouquinho mais lento, pois estamos mandando uma requisição para diverrsos serviços de pesquisa de DNS para ver se eles encontra essa "máscara" em seu cadastro.

# Route 53
* O route 53 é um sistema da AWS para automatizar e descomplicar esse processo do DNS, vamos ao exemplo da Work novamente, mas agora ela está na AWS com o Route 53 e com três ip's(123BR, 456US, 789UK) e um site(workdb.com). 
* Com o Route 53 nós podemos fazer uma policy de fail over, ou seja, se um usuário acessa a workdb.com no ip 123BR e esse servidor por algum motivo cai, ele automaticamente é redirecionado para o ip 456US(mantendo a URL);
* Também podemos ter políticas de localização, ou seja eu estou no Brasil, logo o Route 53 irá me colocar no site com ip 123BR, já o Lucas que está na alemanha o site irá pegar o ip 789UK pelo fato da inglaterra estar mais próximo da alemanha do que o Brasil ou EUA.
* Registros/Registers -> 123BR, 456US, 789UK 


# Scaling
* Scaling up -> Aumentar o hardware da sua instancia, por exemplo se um site está sendo muito acessado ele pode aumentar a instancia e evitar bugs/lentidões. Aumentando verticalmente
* Scaling out -> Criar novas instancias para que uma pare as outras substituam, ou seja, se um site cair ele terá mais 3 instancias para substituir, isso aumenta a redundancia. Aumentando horizontalmente (melhor).

# EC2 Auto Scaling
* O auto scaling faz um scaling out automaticamente baseando na configuração que você fizer, ou seja se a memória da sua EC2 atingir 70% o **CloudWatch** irá mandar um request pro **Auto Scaling Group** para ele criar uma nova instância com base num **Launch Template** de EC2
* O passo de criação do auto-scaling é criar um Template EC2(Modelo de Template) e após isso criar um Auto Scaling Group

# Elastic Load Balancer (ELB)
* O load balancer ele basicamente distribui as requisições entre as instancias ec2, ou seja se temos um site com 4 maquinas ec2 rodando e 4 acessos de pessoas, o Load Balancer vai separar uma instancia para cada pessoa, ou se tivermos 8 pessoas usando, vai separar 1 instancia para duas pessoas, e assim por diante. Ele é muito bem usado com o Auto Scaling, para que caso uma instancia falhe, já suba uma nova e esse balanceamento seja feito, deixando o site ou aplicação mais rápido e dinamico.

# Tipos de Load Balancer
* ALB = Application Load Balancer -> Layer 7 (OSI)
	* Routing -> Path-based
		*       -> Host-based
	* Apontar -> Instancias
	*               -> Ip's
	*               -> Lambdas
	*               -> Containers
	*               -> Targets
* NLB = Network Load Balancer -> Layer 4 (OSI)
	*  Routing -> IP
	*                -> TCP/UDP

* ALB -> Detalhado e Inteligente
* NLB -> Rápido (High Performance + Low Latency)

# Scaling Policies
* Target Tracking
* Step/Simple Scaling

* Target Tracking -> Supondo que sua instancia chegue a 100% por uma alta demanda, ele não vai conseguir ficar criando instancias dinâmicas e todas começarão a estar 100% (?????? Help do Lucas)
* Step/Simple Scaling -> É mais inteligente pois usa o cloudwatch e vai criando mais instâncias dependendo dos alertas enviados