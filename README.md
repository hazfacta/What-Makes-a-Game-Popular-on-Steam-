# What-Makes-a-Game-Popular-on-Steam?

## **How to define popularity?**

Defining a game's popularity can be tricky. It can be measured in multiple ways, such as player count (or copies sold) or the percentage of positive reviews. In this project, I combined both metrics to create a weighted popularity score.
I started by cleaning the estimated_owners column, which contained ranges like "10000 - 50000" as the object datatype. I split these into lower and upper bounds so I could calculate a single average number of owners per game:

``` python
#Cleaning estimated_owners to be usable as an average(float)
df[['owners_lower', 'owners_upper']] = df['estimated_owners'].str.split('-', expand=True)
df['owners_lower'] = pd.to_numeric(df['owners_lower'].str.strip())
df['owners_upper'] = pd.to_numeric(df['owners_upper'].str.strip())
df['average_owners'] = (df['owners_lower'] + df['owners_upper'])/2

#Combining average_owners with % positive reviews for a more representative measure of popularity
df['popularity'] = df['average_owners'] * (df['pct_pos_total']/100)
```
To account for review quality, I defined popularity as the product of average owners and the percentage of positive reviews.
This approach ensures that games with many owners and high positive reviews rank as the most popular.


## **What are the most popular genres?**

When analysing popularity by genre, an important distinction emerges between total (raw) popularity and average popularity per game.

```python
#Bar chart Visualisation of most popular genres (Raw)
top_genres = df_exploded.groupby('genres')['popularity'].mean().sort_values(ascending=False).head(20)
top_genres.plot(kind='bar', title='Top 20 Genres by Average Popularity')
```
```python
#Bar chart Visualisation of most popular genres (Average)
top_genres = df_exploded.groupby('genres')['popularity'].sum().sort_values(ascending=False).head(20)
top_genres.plot(kind='bar', title='Top 20 Genres by Raw Popularity')
```

<img width="1454" height="770" alt="image" src="https://github.com/user-attachments/assets/4f2cfd3c-a2b2-42b4-919e-e1b5a41f0d6c" />


### **Initial Observations:**
* Action clearly dominates raw popularity.
* Massively Multiplayer clearly dominates average popularity.
* Movie has surprisingly high average popularity.
* Indie drops noticeably in ranking between the two graphs.
* Free-to-Play scores well on both graphs


### **Take-aways:**

* Action leads in total popularity because it contains a large number of releases. However, it's average popularity per game is lower, likely reduced by many low quality or low visibility titles. Indie follows a similar pattern, with high volume and wide variation in performance.

* In contrast, resource intensive genres such as MMO achieve higher average popularity per title. Movie is the most surprising outlier, also ranking highly despite having relatively few releases. These genres are less saturated and typically developed by larger studios, resulting in more consistent performance. 

* Free to play games benefit from having no financial barrier to entry, which increases ownership and boosts measured popularity.

* It's also worth noting that most of these genres are not mutually exclusive, and most games contain a mixture of atleast two of these genres. 




### ***Sweet-Spot:***
* *An _Action, FTP, MMO_ seems to be a safe bet, as this combines high accessability with broad appeal and longevity. This would be consistant with some of the most popular games on the market, such as Counter Strike 2*


## **Is there a correlation between achievement count and popularity?**

``` python
#Scatter plot to represent correlation between popularity and achievements
df_filtered.plot(kind = 'scatter', 
                 x = 'achievements', 
                 y = 'popularity', 
                 xlabel='Number of Achievements', 
                 ylabel='Popularity', 
                 title='Scatter Plot of Achievements vs Popularity', 
                 s=20, 
                 c='blue', 
                 alpha=0.4, 
                 figsize=(10,6), 
                 logy=True, 
                 logx=True)
```

<img width="849" height="552" alt="image" src="https://github.com/user-attachments/assets/d5bafa89-1fbb-4025-8459-2306c1940672" />


**Initial Observations:**
* Slight positive correlation
* Sweet spot of 20-100 achievements
* Single outlier at 1 achievement
* Beyond ~100(10^2) achievements, popularity becomes highly dispersed.

**Take-aways:**

Whilst there is a clear correlation between achievement count and popularity, high achievement counts do not guaruntee success. Futhermore, I've realised that achievement count may be a proxy variable for other factors such as: 
* Developmental scale/budget
* Game length
* Structured progression

*In essense, larger + more polished games = more players/better reviews/more achievements*

So perhaps directly correlating achievements to popularity is not the most useful thing to do.

## **What I'm working on:**
* Release Window section -best dates to release games
* Pricing section -how much can you charge before popularity drops off
* More detailed visualisations (trend lines etc.)
* Individual Case-studies





