
# Regiões
## No mapa é visivél a separação dos servidores da AWS, isso é feito para ter tolerancia a falhas e estabilidade. Cada região também tem determinados recursos vinculados a regiões.

<iframe height="960" width="1080" src="https://aws.amazon.com/pt/about-aws/global-infrastructure/regions_az/" title="Regiões"></iframe>


# Zonas de Disponibilidade (AZs)
## Cada AZ é totalmente isolada de outras AZs dentro da Região, elas estão a quilometros de distância uma da outra. Embora separadas, todas as AZs são interconectadas por meio de fibra totalmente redundante com alta largura de banda e baixa latência.
## Vamos imaginar que temos 3 AZs no Brasil (AZ1, AZ2, AZ3), se hospedarmos um recurso no Brasil e hospedarmos ele em 2 AZs ou mais, teriamos alta disponibilidade suficiente em uma grande variedade de cenários de falha.

# Outras Opções
## Zonas Locais
### São implantações de infraestrutura que posiciona a computação, armazenado e banco de dados e outros produtos AWS, próximo do público geral e centros industriais.
## Zonas do Wavelength
### Incorpora serviços de computação e armazenamento da AWS em redes 5G forncendo suporte para desenvolvimento, implantação e scalling de aplicativos de latência baixa.
## Locais do Direct Connect
### Serviço de Nuvem que conecta sua rede direto a AWS ignorando a internet para oferecer um desempenho mais consistente e de menor latência. Ele oferece locais em todo mundo para garantir que façamos conexões perto de onde precisamos.
### Podemos pensar que é como se tivessemos um cabo de internet em nosso computador e conectado em um computador da AWS.
## CloudFront
### Serviço da WEB que acelera a distribuição de seu conteúdo estático e dinâmico para seus usuários.