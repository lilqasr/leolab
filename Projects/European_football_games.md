In my path to learning SQL, i wanted to apply the knowledge with some topics i love but not necessarily in the business field. 
Since i'm a football lover, I [found this dataset](https://www.kaggle.com/datasets/hugomathien/soccer) in Kaggle where somebody recollect a big dataset 
for the top european leagues. It collects data from Teams, Players, and Matches. The database was in sqlite, so i worked the analysis on DB Browser for SQLite.

The information shown in the database is a little confused because does not describe what's talk about, so i try to make a small analysis 
selecting some fields i understood. The interesting about the database was that i was able to make some queries with joins, Common Tables Expressions (CTE)
and subqueries.

## The content:
- [I. The data exploration](#i-the-data-exploration)
- [II. Exploration part 2](#ii-exploration-part-2)
- [III. Italian and AS ROMA EDA](#iii-italian-and-as-roma-eda)

## I. The data exploration
Before starting, i had to make a little research on how to processing the data because i wasn't able to open the database in mysql. So i realise it could 
be done on Python, but it was going to take me more than time that i thought. Then i figured it out that it was this RDBM for SQLite called DB BROWSER FOR
SQLITE, so i did it on this. Also this program, allows to convert the sqlite database into a normal sql database.

### 1. How many tables do i have in this database?

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

## II. Exploration part 2

After the exploration of the tables, i realized that the important one was the "Match" table and i had to make some joins with the others tables as you just saw. 

### 1. How many teams are recorded by country and league in the match table?

```sql
--How many teams per country and league:
SELECT c.name Country_name, l.name League_name,
COUNT(DISTINCT(m.home_team_api_id)) NUMBER_OF_TEAMS FROM Match m
INNER JOIN Country c ON m.country_id = c.id
INNER JOIN League l on m.league_id = l.id
GROUP BY 1,2
```

<img width="350" alt="image" src="https://user-images.githubusercontent.com/112327873/225371466-6b30a384-fba6-4d51-82a5-b3d1fc0f00d6.png">

If i want to agreggate percentage of total, i have to use CTE, as this:
```sql
WITH TOTAL_TEAMS AS (
SELECT c.name Country_name, l.name League_name,
COUNT(DISTINCT(m.home_team_api_id)) NUMBER_OF_TEAMS FROM Match m
INNER JOIN Country c ON m.country_id = c.id
INNER JOIN League l on m.league_id = l.id
GROUP BY 1,2 ORDER BY 3  DESC)
SELECT *, 
ROUND(NUMBER_OF_TEAMS*100.00/(SELECT sum(NUMBER_OF_TEAMS) FROM TOTAL_TEAMS),2) as 'PERCENTAGE OF TOTAL'
FROM TOTAL_TEAMS
GROUP BY Country_name, League_name
ORDER BY 4 DESC
```
<img width="350" alt="image" src="https://user-images.githubusercontent.com/112327873/225398494-86864bb9-c1e4-48cb-941e-033f89360576.png">

England, Spain, Italy, Germany and France have the Top 5 leagues in Europe. All of them, with the exception of "Germany  1. Bundesliga" have 20 teams every season; but the table match registered 35 teams in France, 34 in England, 33 in Spain, 32 in Italy and 30 in Germany. This is because the worst 3 teams (that get least points in a sesons) are relegated to second division.


### 2. How many games were recorded by country and league in the match table?
We could see that the Match table has 25,979 games, but i wanted to know how many games were registered by league.

```sql
-- TOTAL MATCHES BY COUNTRY AND LEAGUE
SELECT c.name Country_name, l.name League_name, COUNT(*) Q_GAMES FROM Match m
INNER JOIN Country c ON m.country_id = c.id
INNER JOIN League l on m.league_id = l.id
GROUP BY c.name, l.name ORDER BY 3 DESC

-- TOTAL MATCHES AND PERCENTAGE BY LEAGUE
WITH GEN_GAMES AS (
SELECT c.name Country_name, l.name League_name, COUNT(*) Q_GAMES FROM Match m
INNER JOIN Country c ON m.country_id = c.id
INNER JOIN League l on m.league_id = l.id
GROUP BY c.name, l.name ORDER BY 3 DESC)
SELECT *, ROUND(Q_GAMES*100.00/(SELECT SUM(Q_GAMES) FROM GEN_GAMES),2) AS 'PERCENTAGE OF TOTAL'
FROM GEN_GAMES
GROUP BY Country_name, League_name
ORDER BY 4 DESC       

-- Another way for percentage
SELECT  l.name League_name, COUNT(*) Q_GAMES, ROUND(COUNT(*)*100.00/(SELECT COUNT(*) FROM Match),2) as 'PERCENTAGE OF TOTAL'
 FROM Match m
INNER JOIN League l on m.league_id = l.id
GROUP BY l.name ORDER BY 2 DESC
```
<img width="350" alt="image" src="https://user-images.githubusercontent.com/112327873/225405570-2364bd14-6b6d-4c64-845e-24d8e430b0b4.png">

As we can see, Spain, France and England has the same number of games, and Italy a few less... what surprised me because these 4 leages has the same number of teams every season. So, the next step, i will be checking out the Serie A data in the Match table.

### 3. How many Home and Away Goals?
So, the Match table has also the score for every game. We are going to see how totals and average goals scored for the home and away teams in each league, and then by season but just for the 5 top leagues.

```sql
-- Quantity of home and away goals BY league.
SELECT l.name AS League_name, 
SUM(m.home_team_goal) as Home_Goals,
SUM(m.away_team_goal) as Away_Goals,
SUM(m.home_team_goal) + SUM(m.away_team_goal) AS TOTAL_GOALS
FROM Match m
INNER JOIN League l on m.league_id = l.id
GROUP BY 1 ORDER BY 4 DESC

-- AVERAGE GOALS BY LEAGUE
SELECT l.name AS League_name, 
ROUND(AVG(m.home_team_goal),2) as AVG_Home_Goals,
ROUND(AVG(m.away_team_goal),2) as AVG_Away_Goals,
ROUND(AVG(m.home_team_goal) + AVG(m.away_team_goal),2) AS AVG_TOTAL_GOALS
FROM Match m
INNER JOIN League l on m.league_id = l.id
GROUP BY 1 ORDER BY 4 DESC
```

<div id="pictures">
<img width="398" alt="image" src="https://user-images.githubusercontent.com/112327873/225414176-591ca0b1-fb41-4142-8034-c16f7e8d6ed3.png">
<img width="476" alt="image" src="https://user-images.githubusercontent.com/112327873/225414650-25379c74-dd8f-4b25-953e-0031d14fbf38.png">
</div>

As we can see in all of these seasons, the league with more goals was Spain, follow by England and Italy Serie A; in all of them home teams score more goals than away teams... it is kind of the logic. But then, when we look at the average goals, we find that the Netherlands Eredivisie has the highes average of scored goals per game, followed by Switzerland Super League, and Germany 1. Bundesliga. But to check how well distributed is this data we should look at some graphs that we are not going to do in this post.

#### 3.1 Goals by season and league

```sql
-- Quantity of home and away goals BY league and season
SELECT l.name AS League_name, m.season,
SUM(m.home_team_goal) as Home_Goals,
SUM(m.away_team_goal) as Away_Goals,
SUM(m.home_team_goal) + SUM(m.away_team_goal) AS TOTAL_GOALS,
RANK() OVER (PARTITION BY m.season order by SUM(m.home_team_goal) + SUM(m.away_team_goal)  DESC) as Rank_League_Season
FROM Match m
INNER JOIN League l on m.league_id = l.id
WHERE l.name in ('Spain LIGA BBVA','Italy Serie A', 'England Premier League','Germany 1. Bundesliga','France Ligue 1')
GROUP BY 1,2 ORDER BY 2 ASC,  5 DESC
```
<div id="pictures">
<img width="500" alt="image" src="https://user-images.githubusercontent.com/112327873/225446764-96ce9ad4-386d-4c75-a63a-b02a531268e9.png">
<img width="500" alt="image" src="https://user-images.githubusercontent.com/112327873/225446804-1d25f11d-65b3-4f3c-9ee4-bd33718357dc.png">
</div>

```sql
-- AVERAGE GOALS BY LEAGUE AND SEASON
SELECT l.name AS League_name, m.season,
ROUND(AVG(m.home_team_goal),2) as AVG_Home_Goals,
ROUND(AVG(m.away_team_goal),2) as AVG_Away_Goals,
ROUND(AVG(m.home_team_goal) + AVG(m.away_team_goal),2) AS AVG_TOTAL_GOALS_x_Game,
RANK() OVER (PARTITION BY m.season order by AVG(m.home_team_goal) + AVG(m.away_team_goal) DESC) as Rank_League_Season
FROM Match m
INNER JOIN League l on m.league_id = l.id
WHERE l.name in ('Spain LIGA BBVA','Italy Serie A', 'England Premier League','Germany 1. Bundesliga','France Ligue 1')
GROUP BY 1,2 
```

<div id="pictures">
<img width="500" alt="image" src="https://user-images.githubusercontent.com/112327873/225447138-79943f78-6494-446c-8b04-9ddfb90915ba.png">
<img width="500" alt="image" src="https://user-images.githubusercontent.com/112327873/225447172-473c1fe2-421b-4660-96f7-28c161162393.png">
</div>

As we could see, I could get the Total and average Goals by the Top 5 european leagues. Additionally I get the ranking of these leagues considering total and average goals per game. As we can see, in total goals we can see that the top are Premier League and Spain Liga. In average goal per game the league has more goals typically is German Bundesliga.

## III. Italian and AS ROMA EDA

Before, [at the beginning](#2-how-many-games-were-recorded-by-country-and-league-in-the-match-table), I realized there were less games in the Match table for the Serie A than the other 3 top european leagues (Spain, France and England), so i want to find out which season are the games not registered. Remember these four leagues have the same number of team each season (20)

```sql
-- How many games in Serie A:
SELECT l.name League_name, m.season, COUNT(match_api_id) Quantity_GAMES FROM Match m
INNER JOIN League l on m.league_id = l.id
WHERE l.name = 'Italy Serie A'
GROUP BY l.name, m.season ORDER BY 2 
```
<img width='300' src='https://user-images.githubusercontent.com/112327873/225984913-e2997e69-ddbb-42f2-9340-d0ab8390d382.png'>

So, we can see that there are 22 games less in season 2011/2012 and 1 less in 2014/2015.

### 2. AS ROMA EDA
### 2.1 How many games were registered for AS ROMA?

```sql
-- TOTAL AS ROMA GAMES 
SELECT COUNT(*)
FROM Match m
INNER JOIN Team t on m.home_team_api_id = t.team_api_id
INNER JOIN Team t2 on m.away_team_api_id = t2.team_api_id
WHERE t.team_short_name = 'ROM' OR t2.team_short_name = 'ROM'

-- AS ROMA GAMES BY SEASON
SELECT m.season, COUNT(*)
FROM Match m
INNER JOIN Team t on m.home_team_api_id = t.team_api_id
INNER JOIN Team t2 on m.away_team_api_id = t2.team_api_id
WHERE t.team_short_name = 'ROM' OR t2.team_short_name = 'ROM'
GROUP BY 1
```

<img width='300' src='https://user-images.githubusercontent.com/112327873/226181080-842f8bad-ba92-4e08-a2bf-e5813fa7ae79.png'>
<img width='300' src='https://user-images.githubusercontent.com/112327873/226181100-86ac50c4-092e-43b8-a0cb-fb07c437919c.png'>

As can be shown, there are 303 games recorded in the Match table for ASROMA, and according with the number of games per season, is missing one game in 2011/2012 season, because it only counts 37 matches.

### 2.2 Total AS ROMA goals for all seasons and on each one?

```sql
-- ASROMA GOALS SCORED AND ALLOWED 
SELECT 
SUM(CASE WHEN t.team_short_name = 'ROM' THEN m.home_team_goal ELSE 0 END)  + SUM(CASE WHEN t2.team_short_name= 'ROM' THEN m.away_team_goal  ELSE 0 END) TOTAL_SCORED_GOALS,
SUM(CASE WHEN t.team_short_name <> 'ROM' THEN m.home_team_goal ELSE 0 END)  + SUM(CASE WHEN t2.team_short_name<> 'ROM' THEN m.away_team_goal  ELSE 0 END) TOTAL_ALLOWED_GOALS
FROM Match m
INNER JOIN Team t on m.home_team_api_id = t.team_api_id
INNER JOIN Team t2 on m.away_team_api_id = t2.team_api_id
WHERE t.team_short_name = 'ROM' OR t2.team_short_name = 'ROM'

-- ASROMA GOALS BY SEASON 
SELECT m.season, 
SUM(CASE WHEN t.team_short_name = 'ROM' THEN m.home_team_goal ELSE 0 END)  + SUM(CASE WHEN t2.team_short_name= 'ROM' THEN m.away_team_goal  ELSE 0 END) TOTAL_SCORED_GOALS,
SUM(CASE WHEN t.team_short_name <> 'ROM' THEN m.home_team_goal ELSE 0 END)  + SUM(CASE WHEN t2.team_short_name<> 'ROM' THEN m.away_team_goal  ELSE 0 END) TOTAL_ALLOWED_GOALS
FROM Match m
INNER JOIN Team t on m.home_team_api_id = t.team_api_id
INNER JOIN Team t2 on m.away_team_api_id = t2.team_api_id
WHERE t.team_short_name = 'ROM' OR t2.team_short_name = 'ROM'
GROUP BY 1
```
<img width='350' src='https://user-images.githubusercontent.com/112327873/226188036-dd3d00fc-7177-4a75-a589-7d76efc9b784.png'>


<img width='350' src='https://user-images.githubusercontent.com/112327873/226187743-05e31506-4b56-4ec9-99a3-f164eddc575f.png'>


As shown above, I could get the Total goals Scored and Allowed by season. I double checked this data with the [Serie A official website](https://www.legaseriea.it/it/serie-a/classifica) and could confirmed this info (only season 2011/2012 is missing a few goals)

### 2.3 AS ROMA points each season?

```sql
WITH ROMASEASON AS (
SELECT m.season, t.team_short_name AS HOME_TEAM,
t2.team_short_name AS AWAY_TEAM,
m.home_team_goal as Home_Goals,
m.away_team_goal as Away_Goals,
CASE 
WHEN t.team_short_name = 'ROM' AND m.home_team_goal = m.away_team_goal THEN 1
WHEN t2.team_short_name = 'ROM' AND m.home_team_goal = m.away_team_goal THEN 1
WHEN t.team_short_name = 'ROM' AND m.home_team_goal > m.away_team_goal THEN 3
WHEN t2.team_short_name = 'ROM' AND m.home_team_goal < m.away_team_goal THEN 3
ELSE 0 END AS POINTS
FROM Match m
INNER JOIN Team t on m.home_team_api_id = t.team_api_id
INNER JOIN Team t2 on m.away_team_api_id = t2.team_api_id
WHERE t.team_short_name = 'ROM' OR t2.team_short_name = 'ROM')
SELECT season, SUM(POINTS) AS TOTAL_POINTS
FROM ROMASEASON
GROUP BY season
```
<img width='350' src='https://user-images.githubusercontent.com/112327873/226188389-334b88e9-0a48-490d-9223-136d5c95effb.png'>

As displayed above, this is the historic points for AS ROMA since 2008/2009 SEASON.
