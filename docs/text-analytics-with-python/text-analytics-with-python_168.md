# 可视化命名实体
displacy.render(text_nlp, style="ent", jupyter=True)
```

在图 8-5 的清晰可视化效果中，我们可以看到所有主要命名实体均已被标记。其中大部分识别得非常准确，但也有一些略有偏差。我们还可以通过编程方式提取这些命名实体，使用以下代码通常更为实用。

```python
named_entities = []
temp_entity_name = " "
temp_named_entity = None
for term, tag in ner_tagged:
    if tag:
        temp_entity_name = ' '.join([temp_entity_name, term]).strip()
        temp_named_entity = (temp_entity_name, tag)
    else:
        if temp_named_entity:
            named_entities.append(temp_named_entity)
        temp_entity_name = " "
        temp_named_entity = None
print(named_entities)
```

输出结果如下：
```
[('Three', 'CARDINAL'), ('Facebook', 'ORG'), ('Mark Zuckerberg', 'PERSON'), ('Brazil', 'GPE'), ('Latvia', 'GPE'), ('Singapore', 'GPE'), ('eight', 'CARDINAL'), ('London', 'GPE'), ('27 November', 'DATE'), ('Zuckerberg', 'PERSON'), ('Cambridge', 'GPE'), ('Facebook', 'ORG'), ('two', 'CARDINAL'), ('American Senate', 'ORG'), ('House of Representatives', 'ORG'), ('European', 'NORP'), ('Facebook', 'ORG'), ('UK', 'GPE'), ('Canadian', 'NORP'), ('Zuckerberg', 'PERSON'), ('the New York Times', 'ORG'), ('Thursday', 'DATE'), ('Facebook', 'ORG'), ('Facebook', 'ORG')]
