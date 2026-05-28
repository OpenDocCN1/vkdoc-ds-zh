# 提取命名实体

```python
named_entities = []

temp_entity_name = ""

temp_named_entity = None

for term, tag in tags:

    if tag != 'O':
        temp_entity_name = ' '.join([temp_entity_name, term]).strip()
        temp_named_entity = (temp_entity_name, tag)
    else:
        if temp_named_entity:
            named_entities.append(temp_named_entity)
        temp_entity_name = ""
        temp_named_entity = None

print(named_entities)
```

```
[('Three', 'NUMBER'), ('Facebook', 'ORGANIZATION'), ('boss', 'TITLE'), ('Mark Zuckerberg', 'PERSON'), ('Brazil', 'COUNTRY'), ('Latvia', 'COUNTRY'), ('Singapore', 'COUNTRY'), ('eight', 'NUMBER'), ('London', 'CITY'), ('27 November', 'DATE'), ('Zuckerberg', 'PERSON'), ('Cambridge Analytica', 'ORGANIZATION'), ('Facebook', 'ORGANIZATION'), ('two', 'NUMBER'), ('American Senate', 'ORGANIZATION'), ('House of Representatives', 'ORGANIZATION'), ('European', 'NATIONALITY'), ('Facebook', 'ORGANIZATION'), ('UK', 'COUNTRY'), ('Canadian', 'NATIONALITY'), ('Zuckerberg', 'PERSON'), ('New York Times', 'ORGANIZATION'), ('Thursday', 'DATE'), ('Facebook', 'ORGANIZATION'), ('Facebook', 'ORGANIZATION')]
```