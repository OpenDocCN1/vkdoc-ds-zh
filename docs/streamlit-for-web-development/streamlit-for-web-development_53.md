# 流量洞察数据库凭据字典
database_credentials = {
"username": "",
"password": "",
"port": ""
}
清单 11-14
config.py
```



# 主脚本

在介绍了相关流程之后，我们现在可以开始开发应用程序的主脚本了。此脚本将充当中央枢纽，连接各个页面并处理一些后台维护任务。如代码清单 11-15 所示，我们首先按照第 3.3 节讨论的方法，导入其他页面的脚本。接着，我们使用 Streamlit-Authenticator 组件，基于存储在`config.yaml`文件中的凭据对用户进行身份验证。身份验证通过后，我们使用`st.selectbox`命令实现页面间的导航，该命令从一个键值对字典中为所选页面选择对应的函数，其中键是页面名称，值是其对应的函数。最后，我们从`Utils.py`文件中调用文件上传组件，并将上传的 Pandas 数据帧存储在会话状态中，使其可按需访问。

```
import streamlit as st
from Utils import *
import streamlit_authenticator as stauth
import yaml
from yaml.loader import SafeLoader
import config # config.py 不要与 config.yaml 混淆
with open('../config.yaml') as file:
config = yaml.load(file, Loader=SafeLoader)
authenticator = stauth.Authenticate(
config['credentials'],
config['cookie']['name'],
config['cookie']['key'],
config['cookie']['expiry_days']
)
