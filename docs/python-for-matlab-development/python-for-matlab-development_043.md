# remove underperforming companies
cursor.execute("delete from companies where revenue_mil < 10;");
