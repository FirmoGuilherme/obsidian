
padronização dos indices entre os times

# Esqueleto
```python
import pydantic



class Field(pydantic.baseModel):

	name: str
	value_type: Enum 
	value: str
	mandatory: bool



class ElasticIndex(pydantic.BaseModel):

	name: str
	fields: dict

	def __getitem__(self, key):
		return self.fields[key]

	@classmethod
	def from_elastic_doc(cls, elastic_document: dict):
		instance = cls()
		for key, value in elastic_document.items():
			if getattr(instance[key], 'value_type') == Enum.DATE:
				formatted_date = format_datetime(value)
				setattr(instance[key], 'value', formatted_date)
			else:
				setattr(instance[key], 'value', value)

		return instance


class DatamartVehicle(ElasticIndex):

	def __init__(self):
		fields = [
			Field(name='license_plate', value_type=ValueTypes.STRING),
			Field(name='vehicle_hash', value_type=ValueTypes.STRING),
			Field(name='telemetry_timestamp', value_type=ValueTypes.DATE)
		]
		super().__init__('evi_datamart_vehicle', fields)


class ProfileDriver(ElasticIndex):

	def __init__(self):
		fields = [
			Field(name='full_name', value_type=ValueTypes.STRING, mandatory=True),
			Field(name='short_name', value_type=ValueTypes.STRING),
			Field(name='cpf', value_type=ValueTypes.STRING)
		]
		super().__init__('evi_profile_driver', fields)



# No workflow
profile_driver_joaozinho = self.es.query_elastic(...)
profile_driver = ProfileDriver.from_elastic_doc(profile_driver_joaozinho)
```




# Lógica

``` mermaid
graph TD
A[criar novo ElasticIndex para adicionar os campos de forma dinamica] --> B[Criar Validação de Data Com o pydantic] --> C[Criar um novo tipo de Exception para os campos mais complexos já que para os simples o pydantic já faz] --> D[Converter os valores complexos]
```


