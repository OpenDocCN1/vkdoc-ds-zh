# 字体样式，背景颜色

`ft_title = Font(name='Arial', size=14, bold=True)`

`ft_red = Font(color='00FF0000')`

`ft_italics = Font(bold=True, italic=True)`

`bg_green = PatternFill(fgColor='C5FD2F', fill_type='solid')`

`sheet.merge_cells('B2:D3')`

`sheet['B2'] = 'My Pets'`

`sheet['B2'].font = ft_title`

`sheet['B2'].alignment = Alignment(horizontal="center", vertical="center")`