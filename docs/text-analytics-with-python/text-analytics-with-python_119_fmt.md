# 查看相似度矩阵

`bm25_wts_df` = `pd.DataFrame`(`wts`)

`bm25_wts_df`.`head`()

```python
```

现在我们使用电影推荐函数，为我们之前选定的热门电影获取前 5 个推荐结果：

```python
for movie in popular_movies:
    print('Movie:', movie)
    print('Top 5 recommended Movies:', `movie_recommender`(`movie_title`=movie,
                                                         `doc_sims`=`bm25_wts_df`))
    print()
```