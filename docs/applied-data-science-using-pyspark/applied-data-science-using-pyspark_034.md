# 通过计算轮廓系数评估聚类效果
evaluator = ClusteringEvaluator()
silhouette = evaluator.evaluate(predictions)
print("使用平方欧氏距离的轮廓系数 = " + str(silhouette))
