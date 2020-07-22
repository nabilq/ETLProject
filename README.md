# US Movie Database 2017-2019
A collection of US Movies from 2017 to almost the end of 2019

## Introduction and Overview
Data source https://www.kaggle.com/stefanoleone992/imdb-extensive-dataset. Used only the movies, title principals and names CSVs. The title principals table contained the IMDb movie id from the movies CSV
and name ids from the namess CSV, which served to act like foreign keys and made the title principals table a junction table to link the other two CSVs. 

## Movies DataFrame
Some columns were removed to trim down the data. Original_title', 'year', 'description',  'votes', 'metascore' , 'production_company', 'reviews_from_users', 'reviews_from_critics', 'writers', 'actors' columns were all removed for several reasons: 1)We wanted to have the database contain mostly main objective information about the movies, so the review and score columns were removed, leaving only the IMDB user score, 2) To remove redundancy, as the year info is already in the 'date_published' field, 3) We wanted to have the cast and crew info separate, so ‘writers’ and ‘actors’ were removed, 4) Lastly, some information like production company we didn't think it was important, so that was removed to trim down the table. 

Since we were taking movies from 2017 onwards, we did a date filter on date published using >2016-12-31. Now, the date_published column had only the published year listed for some of the movies, and these were removed in the filtering process as they didn't have complete publishing info. There were not too many of these movies to make an impact.

Other transformations done:  
-Did a str.contains script on the country field for "USA" to include moves that had some level of       
US production
-The financial columns had the '$' removed to avoid symbols in SQL and also budgets with non-US figures like CAD, CNY, EUR, and GBP replaced with NaN as we wented to keep US figures only.
-renamed column 'worlwide_gross_income' to 'worldwide_gross-income' for proper spelling

## Names DataFrame:    
Names file contained list of details of more than 175,000 people but only a portion of them were needed. Those with dates of death of 2016 onwards and with null figures(ie still alive) were kept as these people were likely to have worked on the films in the database. 

The birth_country column was cleaned up to replace countries such as West Germany and USSR with proper names like Germany and Russia. 

## Title Principles (Junction) DataFrame:  
This contained the primary keys from the two other DataFrames, the name_ids, and title_ids. These are unique identifiers for the movie titles and the cast and crew from the Names table and links both tables.

Only the ordering and job columns were removed. Ordering was subjective, as it ranked each person's importance to the film (actors, then behend the scenes jobs like directors, writers listed next) didn't serve any meaning and was subjective, as director could be just as important as actor. The job column was a repeat of sorts to the profession column. 

The square brackets and apostrophes were removed from characters columns to keep the column clean.

## Data Load  
Data loaded into PostgreSQL movies data base as 'movies', 'junction', and 'name' with following code


```
connection_string = "postgres:postgres@localhost:5432/movies"
engine = create_engine(f'postgresql://{connection_string}')>'

post2017USAmoviesdropped.to_sql(name='movies', con=engine, if_exists='append', index=False)
title_dfdropped.to_sql(name='junction', con=engine, if_exists='append', index=False)
names_data.to_sql(name='name', con=engine, if_exists='append', index=False)
```
