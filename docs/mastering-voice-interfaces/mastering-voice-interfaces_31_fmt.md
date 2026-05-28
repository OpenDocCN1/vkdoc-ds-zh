# 如有需要，设置用户上下文

```
user_context = context['parameters'].get('userContext')
if user_context:
    df_request.user_context = user_context  # E
```

```
def ar_call_by_name(df_request, df_response):
```