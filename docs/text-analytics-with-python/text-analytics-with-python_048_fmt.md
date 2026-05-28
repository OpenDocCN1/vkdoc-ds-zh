# 三元组

```python
from nltk.collocations import TrigramCollocationFinder
from nltk.collocations import TrigramAssocMeasures
finder = TrigramCollocationFinder.from_documents([item.split() for item in norm_alice])
trigram_measures = TrigramAssocMeasures()
```