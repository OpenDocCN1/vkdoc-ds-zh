# 创建用户认证对象
authenticator = stauth.Authenticate(config.user_credentials['names'],
config.user_credentials['usernames'], config.user_credentials['passwords'],
'some_cookie_name','some_signature_key', cookie_expiry_days=30)
