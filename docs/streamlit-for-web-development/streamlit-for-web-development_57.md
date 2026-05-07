# 创建数据集上传组件
if 'df_train' not in st.session_state:
st.session_state['df_train'] = None
if 'df_real' not in st.session_state:
st.session_state['df_real'] = None
st.sidebar.subheader('训练数据集')
_, st.session_state['df_train'] = file_upload('请上传训练数据集')
st.sidebar.subheader('测试数据集')
_, st.session_state['df_real'] = file_upload('请上传测试数据集')
