# README
#Overview




There is a new movie studio opening up called Microsoft Studios. In order to anaylze important data we have anaylzed two important datasets. The first data set "imdb", is a dataset with many valuable data points. Included in this movie database is movie basics, directors, known for, movie aka's, movie ratings, persons, princpals, and writers. For the purposes of this project the focus was on movie basics. The subcatagory in movie basics that were dircetly analyzed included runtime, and genre. In order to analyze this data SQL was initaly used. The second database used was tn.movie_budgets.csv'. To import this data PANDAS was used. This data included budget, and worldwide gross. In order to completely anayzle the data one database was created merging the imdb and tn.movie_budgets.csv. 


#Business Understanding


In order to provide feedback to the Microsoft CEO, board and shareholders, project managers on team for Microsoft Studio, CFO and other important department heads, we first had to define what a successful movie studio does. It seemed important to measure a Movie Studios success interms of Worldwide Gross intake. We asked the question, what makes a movie successful in grossing high Worldwide? While there are many factors this analysis will focus on movie genre, runtime, and budget. 

Important questions regarding these variables included the following:
What genre of movies are best for producing high grossing worldwide movies? What movies generes fail to produce high grossing worldwide movies?
The graph shows that genres such as Action, Adventure and Sci-Fi are most likely to produce an outlier that may result in exstremely high grossing movies. Animation's median worldwide gross is also very high at around .25 Biliion. Based on this information I would recommend that Microsoft produce Animation, Action, Adventure, and Sci-Fi movies. Also based on this graph I would recommend that Microsoft avoids making news, war, history, western, documentary and musical movies. 

[image](https://github.com/jguzzo522/presentation/assets/75549456/dc5c81b4-2290-4b88-b1f8-5a6f257c75af)

Important questions regarding runtime:
How long should a movie be to produce the highest possible worldwide gross?

Based on the data provided from the combined databases, the optimal runtime should be over 120 minutes. In order to measure optimal runtime we broke the runtime catagory up into 3 subcatagoires. Short run time was defined as any movie running less than 60 minutes long. Medium runtime was any movie running between 60-120 minutes, and long runtime was anymovie running longer thant 120 minutes. There was not much diffrence between short and medium runtime movies. However, there were more outliers in medium runtime movies that produced movies that grossed upwards of a billion dollars. The shortrun time movies did not produce any movies grosssing over a billion dollars. Long runtime movies median value was around.20 billion dollars. In addtion many movies were outliers in this catagory, including many movies grossing over 1 billion dollars. Based on the data and the graph below, it is recommened that Microsoft invests  in longer runtimes.

![image](https://github.com/jguzzo522/presentation/assets/75549456/295a1048-e8fd-4bc0-aa45-50b6baa0a33c)


Important questions regarding budget:
What type of budget creates the highest grossing films? Is there a big diffrence to justify larger budgets?

In order to address these questions, it was imporant to breakdown the data of budgets into small (bellow 50 million dollars), medium (50-100 million dollars) and large (100+ million). As perdicted small budgets produced the least grossing films. Small budget films median gross was less than .1 billion dollars. While the median gross for large budgeted films was around .5 billion dollars. Medium movies also did much better than small budget films, with a median gross value around .2 billion dollars. Not a single small budget film produced a billion dollar gross. There were a very small amount of medium sized budgets that produced a billion dollar gross. Large budgetg films produced several movies grossing over a billion dollars. Higher budget films also produced a few movies grossing over two billion dollars. Based on this analysis , large budget films are worth the investment.

![image](https://github.com/jguzzo522/presentation/assets/75549456/e4af6fc7-3bc4-442f-9c2d-3becc4895a46)

In order to address these questions, it was imporant to breakdown the data of budgets into small (bellow 50 million dollars), medium (50-100 million dollars) and large (100+ million). As perdicted small budgets produced the least grossing films. Small budget films median gross was less than .1 billion dollars. While the median gross for large budgeted films was around .5 billion dollars. Medium movies also did much better than small budget films, with a median gross value around .2 billion dollars. Not a single small budget film produced a billion dollar gross. There were a very small amount of medium sized budgets that produced a billion dollar gross. Large budgetg films produced several movies grossing over a billion dollars. Higher budget films also produced a few movies grossing over two billion dollars. Based on this analysis , large budget films are worth the investment.
