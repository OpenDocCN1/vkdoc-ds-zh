# ...... 为聚焦列表而省略的代码 ......

`self.user_context = {}`  # A

def `update_user_context`(`self`, `request_time`, `key`, `val`):   # B

> """
> 
> 使用键/值对逐次更新用户上下文。
> 
> """

if `request_time` not in `self.user_context`:

> `self.user_context`[`request_time`] = {}

`self.user_context`[`request_time`][`key`] = `val`

def `update_name_history`(`self`, `request_time`, `first`, `last`):  # C

> """
> 
> 使用姓名请求更新用户上下文。
> 
> 调用 `update_user_context` 并传入名和姓。
> 
> """

`self.update_user_context`(`request_time`, `'givenName'`, `first`)  #D

`self.update_user_context`(`request_time`, `'lastName'`, `last`)