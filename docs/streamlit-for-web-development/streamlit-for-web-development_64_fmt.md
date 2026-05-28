# 超参数展开函数

```python
def lr_hyperparameters():

    with st.expander('高级参数'):

        col2_1, col2_2 = st.columns(2)

        with col2_1:

            penalty = st.selectbox('惩罚项', ['l2', 'l1', 'elasticnet', 'none'])

            tol = st.number_input('容忍度 (1e-4)', value=1) / 10000

            fit_intercept = st.radio('截距', [True, False])

            class_weight = st.radio('类别权重', [None, 'balanced'])

            solver = st.selectbox('求解器', ['lbfgs', 'newton-cg', 'liblinear', 'sag', 'saga'])

            multi_class = st.selectbox('多类别', ['auto', 'ovr', 'multinomial'])

            warm_start = st.radio('热启动', [False, True])

        with col2_2:

            dual = st.radio('对偶或原始形式', [False, True])

            C = st.number_input('逆正则化强度', 0.0, 99.0, 1.0, 0.1)

            intercept_scaling = st.number_input('截距缩放', 0.0, 99.0, 1.0, 0.1)

            random_state = st.radio('随机状态', [None, '自定义'])

            if random_state == '自定义':

                random_state = st.number_input('自定义随机状态', 0, 99, 1, 1)

            max_iter = st.number_input('最大迭代次数', 0, 100, 100, 1)

            verbose = st.number_input('详细程度', 0, 99, 0, 1)

            l1_ratio = st.radio('L1 比率', [None, '自定义'])

            if l1_ratio == '自定义':

                l1_ratio = st.number_input('自定义 L1 比率', 0.0, 1.0, 1.0, 0.01)

    # 下载超参数功能

    hyperparameters = {'penalty': [penalty], 'dual': [dual], 'tol': [tol], 'C': [C],

                       'fit_intercept': [fit_intercept], 'intercept_scaling': [intercept_scaling],

                       'class_weight': [class_weight],

                       'random_state': [random_state],

                       'solver': [solver], 'max_iter': [max_iter], 'multi_class': [multi_class],

                       'verbose': [verbose], 'warm_start': [warm_start], 'l1_ratio': [l1_ratio]}

    st.download_button(

        label='下载超参数',

        data=pd.DataFrame(hyperparameters).to_csv(index=False).encode('utf-8'),

        file_name='超参数.csv',

    )

    return (penalty, tol, fit_intercept, class_weight, solver, multi_class, warm_start, dual, C, intercept_scaling,

            random_state, max_iter, verbose, l1_ratio)
```