# star_wars_survey
Project using the Python package "pandas" and various functions to combine, clean, and analyze data in multiple CSV files

# Project Details:
Back in 2014 before Star Wars: The Force Awakens came out, FiveThirtyEight, a website which focuses on statistical anaylysis as the basis for their articles in various subjects, collected data via SurveyMonkey on previous Star Wars films. The 835 responses included information on which movies they've seen, how they ranked those movies, and some personal information such as age, gender, and income. Let's take a look and find out which movie was ranked the highest from these responses.

# Note: Please replace [directory] below in the open() function with the link to your folder of choice where dete_survey.csv & tafe_survey.csv is located

# Opening and Exploring the Data:
```python
import pandas

# the default encoding type "utf-8" can't decode certain characters in this dataset, thus we will use "ISO-8859-1" for this project
star_wars = pandas.read_csv("[directory].star_wars.csv", encoding="ISO-8859-1")

# There originally were 1,186 responses to the original question "Have you seen at least one Star Wars film?". However, we only care about the 835 responses that included that extra details.
star_wars = star_wars[pandas.notnull(star_wars["RespondentID"])]

print(star_wars.columns)
```
Index(['RespondentID',
       'Have you seen any of the 6 films in the Star Wars franchise?',
       'Do you consider yourself to be a fan of the Star Wars film franchise?',
       'Which of the following Star Wars films have you seen? Please select all that apply.',
       'Unnamed: 4', 'Unnamed: 5', 'Unnamed: 6', 'Unnamed: 7', 'Unnamed: 8',
       'Please rank the Star Wars films in order of preference with 1 being your favorite film in the franchise and 6 being your least favorite film.',
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
      
 
