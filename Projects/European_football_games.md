In my path to learning SQL, i wanted to apply the knowledge with some topics i love but not necessarily in the business field. 
Since i'm a football lover, I [found this dataset](https://www.kaggle.com/datasets/hugomathien/soccer) in Kaggle where somebody recollect a big dataset 
for the top european leagues. It collects data from Teams, Players, and Matches. The database was in sqlite, so i worked the analysis on DB Browser for SQLite.

The information shown in the database is a little confused because does not describe what's talk about, so i try to make a small analysis 
selecting some fields i understood. The interesting about the database was that i was able to make some queries with joins, Common Tables Expressions (CTE)
and subqueries.

## I. The data exploration
Before starting, i had to make a little research on how to processing the data because i wasn't able to open the database in mysql. So i realise it could 
be done on Python, but it was going to take me more than time that i thought. Then i figured it out that it was this RDBM for SQLite called DB BROWSER FOR
SQLITE, so i did it on this. Also this program, allows to convert the sqlite database into a normal sql database.

### 1 How many tables do i have in this database?

```sql
SELECT 
    name
FROM 
    sqlite_schema
WHERE 
    type ='table' AND 
    name NOT LIKE 'sqlite_%'
```

<img width="250" src="https://user-images.githubusercontent.com/112327873/225099964-319c2c97-845d-4267-89d7-eb3588bbd3b8.png">

As is shown above, this database contain 7 tables, but i used just "Match", "Country" and "Team". According with the database description, the tables 
contains 25,000 matches between 2008 and 2016 seasons for 11 european countries and their lead championship. Lets see if that is right.

### 2. How many matches, countries does this database include?

"Match" table has 115 columns which i couldn't understand all of them, so i tried to use the first 10: 
```sql
SELECT COUNT(*)
FROM pragma_table_info('Match');

SELECT *
FROM pragma_table_info('Match');
```

<img width="250" src="https://user-images.githubusercontent.com/112327873/225137622-986c8f72-8bb0-4731-aed2-a4a2014911b1.png">

Now, I want to know how many games do we have in this table.
```sql
SELECT COUNT(*) FROM Match;

SELECT COUNT(DISTINCT(match_api_id)) AS Quantity_Games FROM Match
-- Each Row is a game
```
<div id="pictures">
<img width="100" src="https://user-images.githubusercontent.com/112327873/225138754-419b72ee-703d-4bae-8f5f-15e786fe82e9.png">
<img width="150" src="https://user-images.githubusercontent.com/112327873/225138903-4d0df371-2a48-4907-ba9c-f76d236221c0.png">
</div>

It has 25,979 games.

**Number of countries/**
```sql
-- How many countries:
SELECT COUNT(DISTINCT(country_id)) NUMBER_OF_COUNTRIES FROM Match

SELECT DISTINCT(name) COUNTRY_NAME FROM Country
```

![image](https://user-images.githubusercontent.com/112327873/225140183-95fdf5f9-e7b3-4b7e-af1c-3586d8d3c017.png)
![image](https://user-images.githubusercontent.com/112327873/225142285-6a6b4461-3bf6-469f-aa49-d15fb0603180.png)

![image](https://user-images.githubusercontent.com/112327873/225140364-65424500-dd36-4172-a4fb-912fe59ed4c5.png)
![image](https://user-images.githubusercontent.com/112327873/225142083-5b285294-cddf-4f11-a1da-c7e62b04c97d.png)


We can confirm that there are games for 11 countries and leagues.

### 3. How many teams per league and season do the Match table has?

```sql
SELECT c.name Country_name, l.name League_name, m.season, COUNT(DISTINCT(m.home_team_api_id)) FROM Match m
INNER JOIN Country c ON m.country_id = c.id
INNER JOIN League l on m.league_id = l.id
GROUP BY 1,2,3
```
<div id="pictures">
<img width="300" height="250" src="https://user-images.githubusercontent.com/112327873/225149477-f1a49d83-86fa-49c2-8b6c-27a38ee34612.png">
<img width="300" height="250"  src="https://user-images.githubusercontent.com/112327873/225149599-c36a6e6c-39b1-46dc-a224-4fe1cc51a42e.png">
<img width="300" height="250"  src="https://user-images.githubusercontent.com/112327873/225149683-5d4a03e5-ba0c-4232-b210-b9894d99b8ca.png">
<img width="300" height="250"  src="https://user-images.githubusercontent.com/112327873/225149801-c98b1c2a-c4a8-465e-bd08-5bf2135c70ea.png">
<img src="https://user-images.githubusercontent.com/112327873/225149901-83b6d240-9118-48fe-849d-ff5aba362487.png">
</div>








