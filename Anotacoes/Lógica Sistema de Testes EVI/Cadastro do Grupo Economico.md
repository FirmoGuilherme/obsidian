
# Validar Tabela
```mermaid
graph TD
	a([Acessar tela])
	a --> exd[\Extrair dados tabela\]
	a --> exe[(Extrair dados Elastic)]
	exd --> vd[(Validar dados)]
	exe --> vd([Validar dados])	
	vd --> dc{{Dados iguais?}}
	dc -.-> |Sim| fz[Fazer nada]
	dc -.-> |Não| lt[Gerar alerta Log]
```
## Extração Dados Elastic
* Fazer consulta com "s_record_status": "ACTIVE"
```python
query = self.server.build_query(size=100)
```

# Validar Cadastro
```mermaid
graph TD
	a([Acessar tela])
	a --> cb[/Clicar Botão Adicionar/]
	cb --> pt[/Preencher Input Texto/]
	cb --> pn[/Preencher Input Numerico/]
	pt --> sd([Cria Dicionario])
	pn --> sd([Cria Dicionario])
	sd --> bs[/Clicar Botão Salvar/]
	bs --> |E-mail| ee[(Extrair dados Elastic)]
	ee --> vd([Validar Dados])
	vd --> dc{{Dados iguais?}}
	dc -.-> |Sim| fz[Fazer nada]
	dc -.-> |Não| lt[Gerar alerta Log]
```
## Extração Dados Elastic
* Fazer consulta com "s_record_status": "ACTIVE" e "admin_email": email_cadastrado
```python
query = self.server.build_query(admin_email=email_cadastrado,size=100)
```
# Validar Edição
```mermaid
graph TD
	a([Acessar tela])
	a --> pd[/Pesquisar pelo e-mail cadastrado anteriormente/]
	pd --> ad[/Alterar a ordernação de Nome para Mais relevante/]
	ad --> ce[/Clicar no botão de editar do primeiro valor da tabela/]
	ce --> pt[/Preencher Input Texto/]
	ce --> pn[/Preencher Input Numerico/]
	pt --> cd([Criar Dicíonario])
	pn --> cd([Criar Dicíonario])
	cd --> cs[/Clicar Botão Salvar/]
	cs --> |E-mail| ee[(Extrair dados Elastic)]
	ee --> vd([Validar Dados])
	vd --> dc{{Dados iguais?}}
	dc -.-> |Sim| fz[Fazer nada]
	dc -.-> |Não| lt[Gerar alerta Log]
```

# Validar Exclusão
```mermaid
graph TD
	a([Acessar tela])
	a --> pd[/Pesquisar pelo e-mail cadastrado anteriormente/]
	pd --> ad[/Alterar a ordernação de Nome para Mais relevante/]
	ad --> ce[/Clicar no botão excluir da primeira linha da tabela/]
	ce --> vd([Validar se o Grupo Economico foi excluido])
	vd --> dc{{O Grupo Economico foi excluido?}}
	dc -.-> |Sim| fz[Fazer nada]
	dc -.-> |Não| lt[Gerar alerta Log]
```
## Validação Dados Elastic
* Fazer consulta com "s_record_status": "DELETED" e com ordenação do "s_record_creation_date" por ordem decrescente(Do mais novo para o mais velho).
```python
query = self.server.build_query(size=1, s_record_status="DELETED", sort="s_record_creation_date": "desc")
query['query']['bool']['must'].pop(0)
```
