# 一个布尔值
parser.add_argument('-n', '--dry-run', dest='dry_run',
action='store_true', default=False,
help='打印将要执行的操作，'
'但不实际执行。')
