# 四个选项之一

```
parser.add_argument('--type', dest='interpolation_type', action='store',

choices=['nearest', 'linear', 'bilinear',

'cubic'], type=str, default='nearest',

help='插值类型 ["nearest"]。')

if len(sys.argv) == 1:

```