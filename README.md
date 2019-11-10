# star_wars_survey
Project using the Python package "pandas" and various functions to combine, clean, and analyze data in multiple CSV files

# Project Details:
Back in 2014 before Star Wars: The Force Awakens came out, FiveThirtyEight, a website which focuses on statistical anaylysis as the basis for their articles in various subjects, collected data via SurveyMonkey on previous Star Wars films. The 835 responders provided information such as which movies they've seen, how they ranked those movies, and some personal information like age, gender, and income. There's actually over 1,000 responses in the dataset but a few hundred will end up disregarded as they didnt rank any of the movies nor list which they've seen. Let's take a look and find out which movie was ranked the highest from these responses.

# Note: Please replace [directory] below in the open() function with the link to your folder of choice where dete_survey.csv & tafe_survey.csv is located

# Opening and Exploring the Data:
```python
import pandas

# the default encoding type "utf-8" can't decode certain characters in this dataset, thus we will use "ISO-8859-1" for this project
star_wars = pandas.read_csv("[directory].star_wars.csv", encoding="ISO-8859-1")

# Let's remove any responses that lack the unique ID
star_wars = star_wars[pandas.notnull(star_wars["RespondentID"])]

print(star_wars.columns)
```
Index(['RespondentID',
       'Have you seen any of the 6 films in the Star Wars franchise?',  
       'Do you consider yourself to be a fan of the Star Wars film franchise?',  
       'Which of the following Star Wars films have you seen? Please select all that apply.',  
       'Unnamed: 4', 'Unnamed: 5', 'Unnamed: 6', 'Unnamed: 7', 'Unnamed: 8',  
       'Please rank the Star Wars films in order of preference with 1 being your favorite film in the franchise and 6 being your least   favorite film.',  
       'Unnamed: 10', 'Unnamed: 11', 'Unnamed: 12', 'Unnamed: 13',  
       'Unnamed: 14',  
       'Please state whether you view the following characters favorably, unfavorably, or are unfamiliar with him/her.',  
       'Unnamed: 16', 'Unnamed: 17', 'Unnamed: 18', 'Unnamed: 19',  
       'Unnamed: 20', 'Unnamed: 21', 'Unnamed: 22', 'Unnamed: 23',  
       'Unnamed: 24', 'Unnamed: 25', 'Unnamed: 26', 'Unnamed: 27',  
       'Unnamed: 28', 'Which character shot first?',  
       'Are you familiar with the Expanded Universe?',  
       'Do you consider yourself to be a fan of the Expanded Universe?ÂÃ¦',  
       'Do you consider yourself to be a fan of the Star Trek franchise?',  
       'Gender', 'Age', 'Household Income', 'Education',  
       'Location (Census Region)'],  
      dtype='object')  
      
If we take a closer look at the data within the columns, we notice that 2 columns, "Have you seen any of the 6 films in the Star Wars franchise?" and "Do you consider yourself to be a fan of the Star Wars film franchise?" have either "Yes", "No", or doesn't have a value (Null/NaN). In Python & pandas, we prefer to go by a Boolean value instead of Yes or No. Let's change the values of these columns to a boolean type:

```python
yes_no = {"Yes": True, "No": False}

star_wars["Have you seen any of the 6 films in the Star Wars franchise?"] = star_wars["Have you seen any of the 6 films in the Star Wars franchise?"].map(yes_no)
star_wars["Do you consider yourself to be a fan of the Star Wars film franchise?"] = star_wars["Do you consider yourself to be a fan of the Star Wars film franchise?"].map(yes_no)

print(star_wars["Have you seen any of the 6 films in the Star Wars franchise?"].value_counts())
print(star_wars["Do you consider yourself to be a fan of the Star Wars film franchise?"].value_counts())
```
True     936  
False    250  
Name: Have you seen any of the 6 films in the Star Wars franchise?, dtype: int64  
True     552  
False    284  
Name: Do you consider yourself to be a fan of the Star Wars film franchise?, dtype: int64  

We need to do something similiar with the next six columns, each one representing if the respondent has seen that Star Wars film. These columns are a little weird as the column headers are a bit ambiguous and if the respondent did see the movie, the value would be the title of the film and not a typical Yes/No or True/False response.
For example: one of the headers is "Unnamed: 4", this column represents if the respondent has seen Star Wars II. If they have, the value would be "Star Wars: Episode II  Attack of the Clones" and if they haven't, the value would just be empty.
It's a little weird compared to the Yes/No response but at least the responses have the same value so it's not too difficult to work with:

```python
import numpy

# The jist is if the title comes up, that means the respondent has seen the movie. Thus we can replace the value with True. If otherwise # empty, the value is now False
movie_mapping = {
    "Star Wars: Episode I  The Phantom Menace": True,
    "Star Wars: Episode II  Attack of the Clones": True,
    "Star Wars: Episode III  Revenge of the Sith": True,
    "Star Wars: Episode IV  A New Hope": True,
    "Star Wars: Episode V The Empire Strikes Back": True,
    "Star Wars: Episode VI Return of the Jedi": True,
    numpy.nan: False,
}

# We run the mapping through each of the 6 columns
for col in star_wars.columns[3:9]:
    star_wars[col] = star_wars[col].map(movie_mapping)
```

Now lets revise the column headers as well to make it a little easier to work with:  
```python
star_wars = star_wars.rename(columns={
        "Which of the following Star Wars films have you seen? Please select all that apply.": "seen_1",
        "Unnamed: 4": "seen_2",
        "Unnamed: 5": "seen_3",
        "Unnamed: 6": "seen_4",
        "Unnamed: 7": "seen_5",
        "Unnamed: 8": "seen_6"
        })
 
 print(star_wars.columns[3:9])
```
Index(['seen_1', 'seen_2', 'seen_3', 'seen_4', 'seen_5', 'seen_6'], dtype='object')  

Moving on to the next six columns, it looks like each represent how each respondent ranked that particular film. The headers of these are also a bit ugly so let's revise them and then check each column for their average rank:
```python
star_wars = star_wars.rename(columns={
        "Please rank the Star Wars films in order of preference with 1 being your favorite film in the franchise and 6 being your least favorite film.": "ranking_1",
        "Unnamed: 10": "ranking_2",
        "Unnamed: 11": "ranking_3",
        "Unnamed: 12": "ranking_4",
        "Unnamed: 13": "ranking_5",
        "Unnamed: 14": "ranking_6"
        })
```
```python
# changing their rank values from integer to float before finding the mean
star_wars[star_wars.columns[9:15]] = star_wars[star_wars.columns[9:15]].astype(float)

print(star_wars[star_wars.columns[9:15]].mean())
```
ranking_1    3.732934  
ranking_2    4.087321  
ranking_3    4.341317  
ranking_4    3.272727  
ranking_5    2.513158  
ranking_6    3.047847  
dtype: float64  

Now that we have the average ratings, let's create a plot to visualize it:  
Note: The following was run on Jupyter Notebook, which has a "maigc command" (%matplotlib inline) that will print the graphs in order of creation. If not using Jupyter Notebook, you will use the "matplotlib.pyplot.show()" function under the matplotlib package at the end. This will print out all of the created graphs at once.  

```python
% matplotlib inline # Magic command used only in Juniper Notebook. Delete this line otherwise or it will cause an error

import matplotlib.pyplot # no need to import if using Juniper Notebook

star_wars[star_wars.columns[9:15]].mean().plot.bar(rot=0)
```

![image](https://user-images.githubusercontent.com/57373723/68537700-57313900-031d-11ea-8f32-441c1b0efdc3.png)

Remember, a lower number is better because this is a ranking system. Thus it's preferable to be closer to 1 (best possible rank) than it is to 6 (worst possible rank). Looking at the graph, it seems the original trilogy (Episode 4-6) is rated higher than the second trilogy (1-3). The data also isn't perfect as it seems the responders had to rank every movie, despite possibly not seeing all of them.

Let's take a look at which films have been seen the most by our responders:
```python
print(star_wars[star_wars.columns[3:9]].sum())

star_wars[star_wars.columns[3:9]].sum().plot.bar(rot=0)
```
seen_1    673  
seen_2    571  
seen_3    550  
seen_4    607  
seen_5    758  
seen_6    738  
dtype: int64  

![image](https://user-images.githubusercontent.com/57373723/68537702-60baa100-031d-11ea-9c8d-cd4b7af78667.png)

Not only does the original trilogy have higher rankings, more have also seen it. It's possible that this is a direct correlation and they were ranked higher only because more responders have actually seen it.

Let's have some fun with the data and see if there's a difference in rankings & views between both genders.

```python
males = star_wars[star_wars["Gender"] == "Male"]
females = star_wars[star_wars["Gender"] == "Female"]

print(males[males.columns[9:15]].mean())
males[males.columns[9:15]].mean().plot.bar(rot=0)

print(females[females.columns[9:15]].mean())
females[females.columns[9:15]].mean().plot.bar(rot=0)
```

Male Rankings:  
ranking_1    4.037825  
ranking_2    4.224586  
ranking_3    4.274882  
ranking_4    2.997636  
ranking_5    2.458629  
ranking_6    3.002364  
dtype: float64
![image](https://user-images.githubusercontent.com/57373723/68537704-687a4580-031d-11ea-8108-ab8ddc186b10.png)

Female Rankings:  
ranking_1    3.429293  
ranking_2    3.954660  
ranking_3    4.418136  
ranking_4    3.544081  
ranking_5    2.569270  
ranking_6    3.078086  
dtype: float64  
![image](https://user-images.githubusercontent.com/57373723/68537705-729c4400-031d-11ea-9a29-3f99dba9b2e9.png)
```python
print(males[males.columns[3:9]].sum())
males[males.columns[3:9]].sum().plot.bar(rot=0)

print(females[females.columns[3:9]].sum())
females[females.columns[3:9]].sum().plot.bar(rot=0)
```

Male Views:  
seen_1    361  
seen_2    323  
seen_3    317  
seen_4    342  
seen_5    392  
seen_6    387  
dtype: int64  
![image](https://user-images.githubusercontent.com/57373723/68537708-7af47f00-031d-11ea-8055-4475fd91c766.png)

Female Views:  
seen_1    298  
seen_2    237  
seen_3    222  
seen_4    255  
seen_5    353  
seen_6    338  
dtype: int64  
![image](https://user-images.githubusercontent.com/57373723/68537714-834cba00-031d-11ea-8adf-e1cd5658da5b.png)

Based on the new information/graphs, despite more males having watched episodes 1-3, they tend to have liked them much less than females.

Rmmeber how we stated that the respondants had to rank every movie even if they had not seen all of them? Let's cleanup the data so that for the respondants, we delete their ranks for the movies they have not seen. We had also previously change the column names for better clarity and can now use it to our advantage. We can plot then plot and compare the graph to the previous ratings:
```python
watched_ratings = star_wars.copy()

for number in range(1, 7):
    seen_col = 'seen_' + str(number)
    rank_col = 'ranking_' + str(number)
    watched_ratings.loc[watched_ratings[seen_col] == False, rank_col] = numpy.nan
    
print(watched_ratings[watched_ratings.columns[9:15]].mean())
watched_ratings[watched_ratings.columns[9:15]].mean().plot.bar(rot=0)
```
ranking_1    3.787519  
ranking_2    4.126095  
ranking_3    4.158470  
ranking_4    2.650741  
ranking_5    2.299472  
ranking_6    2.738482  
dtype: float64  

![image](https://user-images.githubusercontent.com/57373723/68552000-2279bc00-03c7-11ea-8934-5d7c67389af5.png)

When looking at the new ratings graph, there doesn't seem to be much of a change for the second triogy (1-3) but the original trilogy (4-6) seems to have even better rankings this time around. Remember, closer to 1 in rankings the better. The movie with a real noticeable difference in their rating is movie 4, "Star Wars: Episode IV - A New Hope". Those who have seen it may have rated it higher since it was the first movie to release in the Star Wars series, providing a nostalgic feeling.

Lastly, we can use show() to get all the bar graphs above to print:
```python
matplotlib.pyplot.show()
```
