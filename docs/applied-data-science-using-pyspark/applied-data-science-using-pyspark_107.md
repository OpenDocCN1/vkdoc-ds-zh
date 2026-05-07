# 生成合成数据
num_customers = 100
num_transactions = 1000
customers = [f"customer_{i}" for i in range(1, num_customers + 1)]
transaction_dates = [datetime.date.today() - datetime.timedelta(days=random.randint(1, 365)) for _ in range(num_transactions)]
transaction_amounts = [random.uniform(10.0, 1000.0) for _ in range(num_transactions)]
data = [(random.choice(customers), transaction_dates[i], transaction_amounts[i]) for i in range(num_transactions)]
columns = ["CustomerID", "TransactionDate", "Amount"]

