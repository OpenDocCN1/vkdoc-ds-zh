# 同时设置用户上下文

```
if context_time > df_request.most_recent_intent['time']:
    df_request.most_recent_intent["time"] = context_time
```