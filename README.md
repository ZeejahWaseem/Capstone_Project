# Overview

The business problem we were presented with was that Computing Vision is interested in entering the film making industry but lacks industry knowledge and is looking for recommendations. We have come up with solutions to provide the what, the how and the who to provide Computing Visions with the necessary tools to make a successful entrance into the film making industry.

## Business Understanding

Our stakeholder is Computing Vision.

These are the key business questions we analyzed:

### Genre 
- What is sample size for each genre?
- Which genre has the highest avg rating?

### Runtime
- How do movie run-time compare to movie ratings?
- Do shorter run-time movies perform better than others?

### Directors
- What was the director experience level?
- What was the overall movie rating each director received?

## Data Understanding and Analysis

### Source of data
- [Box Office MojoLinks to an external site](https://www.boxofficemojo.com/)
- [IMDBLinks to an external site](https://www.imdb.com/)
- [Rotten TomatoesLinks to an external site](https://www.rottentomatoes.com/)
- [TheMovieDBLinks to an external site](https://www.themoviedb.org/)
- [The NumbersLinks to an external site](https://www.the-numbers.com/)

### Description of data

We were presented with multiple csv, tsv files and a database. 

For Genre analysis we used movie_basics and movie_ratings tables from the SQL database because it had list of genres, avg ratings, and number of votes for each rating that helped us analyse the genre with the highest average rating.

For Run-time analysis we used movie_basics and movie_ratings tables from the SQL database so we could analyse run-time based on genre and ratings.

For Directors analysis persons, directors and movie-ratings tables from the SQL database so we could analyse the level of experience each director had along with the avg movie rating each director received.

Avg ratings is the key metrics we used for our all three of our recommendations.

### Three visualizations (the same visualizations presented in the slides and notebook)

Genre: 
![image.png](attachment:image.png)

Run-time:
![image.png](attachment:image.png)
![image.png](attachment:image.png)
![image.png](attachment:image.png)

Directors
![image.png](attachment:image.png)

### Statistical Communication

#### Process and Results of statistical inference

We first imported all of the necessary libraries and files (SQL Database) in order to be able to perform our work and do our ERDs. Also, for all recommendations we used Avg ratings as the key metrics.

##### Genres
Firstly, we needed to investigate genre as it is often the first categorization a consumer does when choosing a movie. Do they want to watch a comedy movie, an action thriller or etc. Moreover, film genre also influences the film making techniques and styles that are used on set and by directors. So, to investigate the most popular genre we started with joining the movie-basics and movie-ratings table. But during the joining step we noticed that movie-basics table had more rows than the movie_ratings table, however, there were rows in the movie_basics table with null values for avgratings column that disappeared when we joined the tables which led us to assumig that the movie_basics table had movies with no ratings. While joining the tables we just chose the rows with avgrating >=8 and numvotes >1000 for precise analysis. 

We also noticed that there were many genres listed under the genre column for each movie. Due to the way the string values were attached in the genre column it was not possible to perform any operation without first seperating them. So we used the split and explode functions that first addded a space and then transformed each genre of a list-like to a separate row. We saved this upadated dataframe with the name genres_split_df.

Then we used value_counts function on genres_split_df to check the total number of the genre categories and their counts. Then we saved the keys and values of the value_counts function under the genre_keys and genre_values varibles and plotted a bar graph (using seaborn) of top ten genre categories against its counts. The bar graph showed that with respect to the number of times each genre popped up under a movie name - drama, documentary, and comedy were the most successful genres. But then, we also accounted for the fact that there could have been more movies of those genres produced and that does not necessarily make that genre the most popular one. To further prove our point we plotted a pie chart that clearly showed that more movies of those genres were produced.

So we digged deep to find the most popular genre and again joined the tables (with the variable name jt_df) but this time we did not restrict the avgrating to >=8, instead chose the complete population set. Then we performed the same split/explode steps to separate the genres. This time as we were looking for the avg rating, so we also rouned the rating float to 2 decimal place to avoid clutter. Next we sorted genres from highest to lowest and saved it as sorted_avg_genres variable. We again perfomed the value_counts function to the newly joined tables dataframe and during analysis we noticed that even though some genres were highly rated they did not have enough samples of them to help us make a definitive decision as they could have been outliers. Therefore, we dropped genres with less than 50 samples and saved the new series of genre categories and its ratings under the variable named cleaned. Again, repeating the same process assinged keys and value as x and y variable and plotted a bar graph using seaborn. 

Once we ploted our data we noticed that "documentary" genre had the highest average rating among all the genres and also had fair amount sample size to back up our recommendation.

#### Hypothesis Testing for Genres (Z-Test)
To further support our recommendation we decided to run Hypothesis Testing, specifically Z-Test as our sample size was more than 30 to ensure that Documentary genre had higher avg rating than the rest of the population. We decided our alpha value to be 0.05 and go ahead with 95% confidence level. 

Next we stated out Ho and Ha:
- Ho : the Documentary genre has the same or less average ratings compared to the rest of the population 
- Ha : the Documentary genre has higher average ratings than the rest of the population

Then we computed the mu, sigma, x_bar, n values for our joined dataframe (jt_df) using functions like mean() and std(). Finally we used the mu, sigma, x_bar and n to find the value z and p values. As our p_value came out to be less than our alpha value so we rejected the Null Hypothesis that the Documentary genre had the same or less average ratings compared to the rest of the population.

##### Run-time

##### Directors
Before we were able to provide the proper director recommendation we had to preform several data cleaning and prepping tactics. First we joined movie_ratings and directors using movie_id. Then we joined the persons table by using person_id from the persons and directors table. We used a WHERE function so that only columns with number of votes over a 1000 would be displayed. After the tables were joined we started cleaning the data, where we dropped any duplicate movies by using the .drop_duplicates() function in pandas. This removed any movie_id that was listed more than once. Once that was complete we realized that some of the directors were no longer alive so we had to remove any row that had a date entered in the death_year column, leaving only rows with an NaN indicating the directors was still alive as of 2019. Once that was completed we no longer needed the death_year column along with the birth_year, person_id, and primary_profession so we dropped all four of them. This resulted in movie_id, averagerating, numvotes, and primary_name being the only columns left on the chart.

In order to provide a competitive director recommendation we have decided to provide two main criteria points as a basis for our final decision. These two points included the directors experience level along with the movie rating they received, which was an average rating of all the movies they directed.

Our first point led us to uncover the top 10 directors listed in the data. This value was calculated based on the number of movies that specific director created. In order to further support our data finding we decided to bring in outside data research. This data was presented by Stephen Follows film data and education and provided evidence of a significant decrease in the number of movies produced by a single producer between the years of 1949 and 2017. Even more importantly to note is the fact that between the years of 1949 and 2018, 60% of movie directors only released one film and less than 10% created over 10 films. This data was important to note because based on the data we presented, the top 10 directors all have directed over 7 movies. This means that they have a significant amount of experience in the industry, when compared to the industry as a whole.

Our second point dives into the next criteria point we believe makes a competitive director and that is their average movie rating. We believe this was important because if a director has a lot of movies that means they have experience but it can only be labeled as credible if those movies have received a good rating and have been seen by a wide range of people. This is why we only took into account movies made by a director that received more than 1000 votes towards the rating. With this information we calculated the mean rating for each director. The top three directors all had overall rating of above a 6.7. With this being said we wanted to check this values against an overall average movie rating. Based on a poll produced by imbd.com it was stated that the average movie rating is a 7.0, meaning that our average rating of above a 6.7 of a director is very competitive. We do need to take into consideration that this rating is the average amongst over 8 different movies not just one single movie.

Overall our final recommendation for the most competitive director would be either Alex Gibney, Anurag Kashyap, or Sang-soo Hong as they all have very high overall movie ratings and high levels of experience. It is import to note that this data is based off data that is as of 2019.

#### Interpretation of these results in the context of the problem

##### Genre
'Documentary' genre should be chosen when developing a film since it has the highest average rating

##### Run-time
The Documentary film runtime can be of any length since there is no correlation in runtime and rating 

##### Directors
The Documentary film should be directed by people of similar background to:
- Alex Gibney
- Anurag Kashyap
- Sang-soo Hong   






