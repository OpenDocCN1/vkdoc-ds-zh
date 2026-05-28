# 上下文敏感的情况：在极短的时间窗口内重复说了同一个名字

```python
df_response.output_text = \
    ('抱歉，我们似乎遇到了问题；'
     '也许您的拨号器中没有这个名字？')

elif df_response.explicit_confirmation:
    df_response.output_text = \
        f"您想呼叫 {given_name} {last_name}，对吗？"
    df_request.update_user_context(   # F
        df_request.current_time,
        'requestedGivenName',
        given_name)
    df_request.update_user_context(   # F
        df_request.current_time,
        'requestedLastName',
        last_name)
    df_response.output_contexts.append(
        confirmation_context(given_name,
                             last_name))
else:
    df_response.output_text = df_request.fulfillment_text
    df_request.update_name_history(   # G
        df_request.current_time,
        given_name,
        last_name)
    df_response.end_conversation = True
    df_response.nva["do"] = True
```