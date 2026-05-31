# get movies belonging to each cluster
for cluster_num in range(NUM_CLUSTERS):
    key_features = [feature_names[index]
                    for index in ordered_centroids[cluster_num, :topn_features]]
    movies = movie_clusters[movie_clusters['kmeans_cluster'] ==
                            cluster_num]['title'].values.tolist()
    print('CLUSTER #'+str(cluster_num+1))
    print('Key Features:', key_features)
    print('Popular Movies:', movies)
    print('-'*80)
```

上述代码的输出如下：

```
CLUSTER #1
Key Features: ['film', 'movie', 'story', 'first', 'love', 'making', 'director', 'new', 'time', 'feature', 'made', 'young', '3d', 'american', 'america']
Popular Movies: ['Contact', 'Snatch', 'The Pianist', 'Boyhood', 'Tropic Thunder', 'Movie 43', 'Night of the Living Dead', 'Almost Famous', 'My Week with Marilyn', 'Jackass 3D', 'Inside Job', 'Grindhouse', 'The Young Victoria', 'Disaster Movie', 'Jersey Boys', 'Seed of Chucky', 'Bowling for Columbine', 'Walking With Dinosaurs', 'Me and You and Everyone We Know', 'Urban Legends: Final Cut']

CLUSTER #2
Key Features: ['young', 'man', 'story', 'love', 'family', 'find', 'must', 'time', 'back', 'friends', 'way', 'years', 'help', 'father', 'take']
Popular Movies: ['Interstellar', 'Guardians of the Galaxy', 'Pirates of the Caribbean: The Curse of the Black Pearl', 'Dawn of the Planet of the Apes', 'The Hunger Games: Mockingjay - Part 1', 'Big Hero 6', 'Whiplash', 'The Martian', 'Frozen', "Pirates of the Caribbean: Dead Man's Chest", 'Gone Girl', 'X-Men: Apocalypse', 'Rise of the Planet of the Apes', 'The Lord of the Rings: The Fellowship of the Ring', 'Pirates of the Caribbean: On Stranger Tides', "One Flew Over the Cuckoo's Nest", 'Star Wars', 'Brave', 'The Lord of the Rings: The Return of the King', 'Pulp Fiction']

CLUSTER #3
Key Features: ['world', 'young', 'find', 'story', 'man', 'new', 'must', 'save', 'way', 'time', 'life', 'evil', 'love', 'family', 'finds']
Popular Movies: ['Minions', 'Jurassic World', 'Captain America: Civil War', 'Avatar', 'The Avengers', "Pirates of the Caribbean: At World's End", 'The Maze Runner', 'Tomorrowland', 'Ant-Man', 'Spirited Away', 'Chappie', 'Monsters, Inc.', 'The Matrix', 'Man of Steel', 'Skyfall', 'The Adventures of Tintin', 'Nightcrawler', 'Allegiant', 'V for Vendetta', 'Penguins of Madagascar']
```



# 集群 #4
关键特征：`['new', 'york', 'new york', 'city', 'young', 'family', 'love', 'man', 'york city', 'find', 'friends', 'years', 'home', 'must', 'story']`
热门电影：`['Terminator Genisys', 'Fight Club', 'Teenage Mutant Ninja Turtles', 'Pixels', 'Despicable Me 2', 'Avengers: Age of Ultron', 'Night at the Museum: Secret of the Tomb', 'Batman Begins', 'The Dark Knight Rises', 'The Lord of the Rings: The Two Towers', 'The Godfather: Part II', 'How to Train Your Dragon 2', '12 Years a Slave', 'The Wolf of Wall Street', 'Men in Black II', "Pan's Labyrinth", 'The Bourne Legacy', 'The Amazing Spider-Man 2', 'The Devil Wears Prada', 'Non-Stop']`

# 集群 #5
关键特征：`['life', 'love', 'man', 'family', 'story', 'young', 'new', 'back', 'years', 'finds', 'hes', 'time', 'find', 'way', 'father']`
热门电影：`['Deadpool', 'Mad Max: Fury Road', 'Inception', 'The Godfather', 'Forrest Gump', 'The Shawshank Redemption', 'Harry Potter and the Chamber of Secrets', 'Inside Out', 'Twilight', 'Maleficent', "Harry Potter and the Philosopher's Stone", 'Bruce Almighty', 'The Hobbit: An Unexpected Journey', 'The Twilight Saga: Eclipse', 'Titanic', 'Fifty Shades of Grey', 'Blade Runner', 'Psycho', 'Up', 'The Lion King']`

# 集群 #6
关键特征：`['war', 'world', 'world war', 'ii', 'war ii', 'story', 'young', 'man', 'love', 'army', 'find', 'american', 'battle', 'first', 'must']`
热门电影：`['The Dark Knight', 'Batman v Superman: Dawn of Justice', 'The Imitation Game', 'Fury', 'The Hunger Games: Mockingjay - Part 2', 'X-Men: Days of Future Past', 'Transformers: Age of Extinction', "Schindler's List", 'The Good, the Bad and the Ugly', 'American Sniper', 'Thor', 'Shutter Island', 'Underworld', 'Indiana Jones and the Kingdom of the Crystal Skull', 'Captain America: The First Avenger', 'The Matrix Revolutions', 'Inglourious Basterds', '300: Rise of an Empire', 'The Matrix Reloaded', 'Oblivion']`

以上输出展示了每个集群的关键特征及所属的电影。每个集群由其主要主题描述，这些主题通过其最突出的特征来定义。你可以看到热门电影根据一些关键共同特征被聚类在一起。你能否发现任何有趣的模式？

我们也可以使用其他特征方案，比如两两文档相似度，将相似电影归入同一集群。以下代码有助于实现这一点。

```python
from sklearn.metrics.pairwise import cosine_similarity
cosine_sim_features = cosine_similarity(cv_matrix)
km = KMeans(n_clusters=NUM_CLUSTERS, max_iter=10000, n_init=50, random_state=42).fit(cosine_sim_features)
Counter(km.labels_)
Counter({4: 427, 3: 724, 1: 1913, 2: 504, 0: 879, 5: 353})
df['kmeans_cluster'] = km.labels_
movie_clusters = (df[['title', 'kmeans_cluster', 'popularity']]
.sort_values(by=['kmeans_cluster', 'popularity'],
ascending=False)
.groupby('kmeans_cluster').head(20))
movie_clusters = movie_clusters.copy(deep=True)
