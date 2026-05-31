# 一个字符串
parser.add_argument('-o', '--out', dest='csv_file',
action='store', type=str, default='sales_data.csv',
help='要创建的 CSV 文件的名称 ["sales_data.csv"]。')
