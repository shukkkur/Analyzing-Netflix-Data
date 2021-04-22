<h1 align="center">Analyzing-Netflix-Data</h1>

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/0/08/Netflix_2015_logo.svg"/>
</p>

![Forks](https://img.shields.io/github/forks/shukkkur/Analyzing-Netflix-Data.svg)
![Stars](https://img.shields.io/github/stars/shukkkur/Analyzing-Netflix-Data.svg)
![Watchers](https://img.shields.io/github/watchers/shukkkur/Analyzing-Netflix-Data.svg)
![Last Commit](https://img.shields.io/github/last-commit/shukkkur/Analyzing-Netflix-Data.svg) 

<h2>Hypothesis: The average duration of movies has declined </h2>

<h3>1. Loading the data from a CSV</h3>

```python
# Read in the CSV as a DataFrame
netflix_df = pd.read_csv("datasets/netflix_data.csv")

# Print the first five rows of the DataFrame
display(netflix_df.head())
```

|   | show_id |   type  | title |      director     |                        cast                       |    country    |     date_added    | release_year | duration |                    description                    |       genre      |
|:-:|:-------:|:-------:|:-----:|:-----------------:|:-------------------------------------------------:|:-------------:|:-----------------:|:------------:|:--------:|:-------------------------------------------------:|:----------------:|
| 0 | s1      | TV Show | 3%    | NaN               | João Miguel, Bianca Comparato, Michel Gomes, R... | Brazil        | August 14, 2020   | 2020         | 4        | In a future where the elite inhabit an island ... | International TV |
| 1 | s2      | Movie   | 7:19  | Jorge Michel Grau | Demián Bichir, Héctor Bonilla, Oscar Serrano, ... | Mexico        | December 23, 2016 | 2016         | 93       | After a devastating earthquake hits Mexico Cit... | Dramas           |
| 2 | s3      | Movie   | 23:59 | Gilbert Chan      | Tedd Chan, Stella Chung, Henley Hii, Lawrence ... | Singapore     | December 20, 2018 | 2011         | 78       | When an army recruit is found dead, his fellow... | Horror Movies    |

<h3>2. Filtering for movies!</h3>
<p>Okay, we have our data! Now we can dive in and start looking at movie lengths. Or can we? Looking at the first five rows of our new DataFrame, we notice a column <code>type</code>. Scanning the column, it's clear there are also TV shows in the dataset! Moreover, the <code>duration</code> column we planned to use seems to represent different values depending on whether the row is a movie or a show</p>

```python
# Subset the DataFrame for type "Movie"
netflix_df_movies_only = netflix_df[netflix_df.type == 'Movie']

# Select only the columns of interest
netflix_movies_col_subset = netflix_df_movies_only[["title", 'country', 'genre', 'release_year','duration']]

# Print the first five rows of the new DataFrame
print(netflix_movies_col_subset.head())
```
|   | title |    country    |     genre     | release_year | duration |
|:-:|:-----:|:-------------:|:-------------:|:------------:|:--------:|
| 1 | 7:19  | Mexico        | Dramas        | 2016         | 93       |
| 2 | 23:59 | Singapore     | Horror Movies | 2011         | 78       |
| 3 | 9     | United States | Action        | 2009         | 80       |
| 4 | 21    | United States | Dramas        | 2008         | 123      |
| 6 | 122   | Egypt         | Horror Movies | 2019         | 95       |

<h3>3. Creating a scatter plot</h3>
<p>Okay, now we're getting somewhere. We've read in the raw data, selected rows of movies, and have limited our DataFrame to our columns of interest. Let's try visualizing the data again to inspect the data over a longer range of time.</p>

```python

# Create a scatter plot of duration versus year
plt.scatter(netflix_movies_col_subset.release_year, netflix_movies_col_subset.duration)

# Create a title
plt.title("Movie Duration by Year of Release")

# Show the plot
plt.show()
```

<p align="center">
  <img src="https://github.com/shukkkur/Analyzing-Netflix-Data/blob/4994953bc3f83d801208b17f1c8edd9f8c14bf94/datasets/img1.png"/>
</p>

<h3>4. Digging deeper </h3>
<p>Upon further inspection, something else is going on. Some of these films are under an hour long! Let's filter our DataFrame for movies with a duration under 60 minutes and look at the genres. This might give us some insight into what is dragging down the average.</p>

|     |                       title                       |    country    |     genre     | release_year | duration |
|:---:|:-------------------------------------------------:|:-------------:|:-------------:|:------------:|:--------:|
|  35 | #Rucker50                                         | United States | Documentaries | 2016         | 56       |
|  55 | 100 Things to do Before High School               | United States | Uncategorized | 2014         | 44       |
|  67 | 13TH: A Conversation with Oprah Winfrey & Ava ... | NaN           | Uncategorized | 2017         | 37       |
| 101 | 3 Seconds Divorce                                 | Canada        | Documentaries | 2018         | 53       |
| 146 | A 3 Minute Hug                                    | Mexico        | Documentaries | 2019         | 28       |

<p>Interesting! It looks as though many of the films that are under 60 minutes fall into genres such as "Children", "Stand-Up", and "Documentaries". This is a logical result, as these types of films are probably often shorter than 90 minute Hollywood blockbuster</p>

<h3>5. Marking non-feature films</h3>
<p>We could eliminate these rows from our DataFrame and plot the values again. But another interesting way to explore the effect of these genres on our data would be to plot them, but mark them with a different color.</p>

```python
colors = []

# Iterate over rows of netflix_movies_col_subset
for row, ser in netflix_movies_col_subset.iterrows():
    if ser['genre'] == 'Children':
        colors.append('red')
    elif ser['genre'] == 'Documentaries' :
        colors.append('blue')
    elif ser['genre'] == "Stand-Up" :
        colors.append('green')
    else:
        colors.append('black')
        
# Inspect the first 10 values in your list        
print(colors[:11])
```
<code>['black', 'black', 'black', 'black', 'black', 'black', 'black', 'black', 'black', 'blue', 'black']</code>

<h3>6. Plotting with color! </h3>
<p>We now have a colors list that we can pass to our scatter plot, which should allow us to visually inspect whether these genres might be responsible for the decline in the average duration of movies.</p>

```python
# Set the figure style and initalize a new figure
fig = plt.figure(figsize=(12,8))

# Create a scatter plot of duration versus release_year
plt.scatter(netflix_movies_col_subset.release_year, netflix_movies_col_subset.duration, c=colors)

# Create a title and axis labels
plt.title("Movie duration by year of release")
plt.xlabel("Release year")
plt.ylabel("Duration (min)")

# Show the plot
plt.show()
```
<p align="center">
  <img src="https://github.com/shukkkur/Analyzing-Netflix-Data/blob/8223149cc735de97f0ec57be7679095f739d390c/datasets/img2.png"/>
</p>

<p>Well, as we suspected, non-typical genres such as children's movies and documentaries are all clustered around the bottom half of the plot. But we can't know for certain until we perform additional analyses.</p>

```python
# Are we certain that movies are getting shorter?
are_movies_getting_shorter = "Nope"
```
