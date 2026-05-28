# 即 webhook 不修改 Dialogflow 原本要执行的操作。

```python
if (given_name == df_request.repeat_information['recent_given_name'] and
    last_name == df_request.repeat_information['recent_last_name']):
```