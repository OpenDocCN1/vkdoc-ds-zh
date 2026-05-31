# 文件: code/IO/demo_openpyxl.py
from openpyxl import Workbook
from openpyxl.styles import Font, Alignment, PatternFill
book = Workbook()
sheet = book.active
sheet.title = "Pets by weight"
