# 月-小时字典生成器
def month_hours_dict():
    month_hours = {}
    month_names = ['January','February','March','April','May',
                   'June','July','August','September','October','November','December']
    for month_name in month_names:
        month = {month_name: {'12AM': None, '01AM': None, '02AM': None,
                              '03AM': None, '04AM': None, '05AM': None,
                              '06AM': None, '07AM': None, '08AM': None,
                              '09AM': None, '10AM': None, '11AM': None,
                              '12PM': None, '01PM': None, '02PM': None,
                              '03PM': None, '04PM': None, '05PM': None,
                              '06PM': None, '07PM': None, '08PM': None,
                              '09PM': None, '10PM': None, '11PM': None}}
        month_hours.update(month)
    return month_hours

