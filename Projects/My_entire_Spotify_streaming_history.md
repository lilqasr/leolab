<h1 align="center"> What My Entire Spotify Streaming History Says about me?</h1>

In the [previous post](https://lilqasr.github.io/leolab/Projects/Analyzing_my_Spotify_historical_Data_part-1.html), 
i show my spotify streaming data analysis for an entire year, but before finish it, i received the entire streaming history, which is SINCE THE FIRST TIME
I PLAYED A SONG/TRACK ON SPOTIFY.

I received a zip file with all the information containing four files: three json files, and one PDF, which contains the explanation of the data sent.
The json files where all the data captured with the streaming information. 

## The preprocessing

**The first thing was to import the json files and get it into individuals tables, and then make the union.**

```sql
CREATE TABLE spotihist (
SELECT * FROM historyfirst
UNION 
SELECT * FROM historyfirst1
UNION 
SELECT * FROM historyfirst2);

SELECT * FROM spotihist;
```


<figure>
<img alt="image" src="https://user-images.githubusercontent.com/112327873/220428351-cebd81ad-23e4-4d95-8f98-6b8c47accb73.png" style="width:100%">
<figcaption align = "center"><b> Data technical description.</b> </figcaption>

</figure>


**Once i had the whole table, it was necessary to make some changes.**

```sql
ALTER TABLE spotihist
CHANGE COLUMN ts when_played VARCHAR(100),
CHANGE COLUMN master_metadata_track_name track_name VARCHAR(200),
CHANGE COLUMN master_metadata_album_artist_name artist_name VARCHAR(200),
CHANGE COLUMN master_metadata_album_album_name album_name VARCHAR(200);
```

I realize there was some characters that did not allow me to convert the datetime column (ts/when_played).
So i had to replace this characters:

```sql
UPDATE spotihist
SET when_played = REPLACE(REPLACE(when_played, 'T', ' '),'Z', ''); 

UPDATE spotihist
SET when_played = REPLACE (when_played, 'Z', '');

ALTER TABLE spotihist
MODIFY when_played DATETIME;
```
**After this part, i had the final table to make my analysis.**

## **I. The General analysis**

For this analysis i used 6 of the variables/columns/attributes (as you wish call them), but then i created others, extracted from my datetime column.

### 1. The first thing to know is the period of time covered by the data:

```sql
SELECT MIN(when_played) start_date,
MAX(when_played) end_date,
DATEDIFF(MAX(when_played),MIN(when_played)) total_days
FROM spotihist;
```

<img width="450" alt="image" src="https://user-images.githubusercontent.com/112327873/220452828-76836401-3b45-4a72-b953-0d04a97e1887.png">

According to this, the first time i played a song/track on spotify was on August 8th, 2014, like 8.5 years ago; **but, which song did i played?**

```sql
SELECT when_played, track_name, artist_name, ms_played, conn_country, reason_end 
FROM spotihist
WHERE when_played = (SELECT min(when_played) FROM spotihist);
```

<img width="600" alt="image" src="https://user-images.githubusercontent.com/112327873/220454368-fe5758ce-ce3b-438d-8639-bea2c15245fe.png">

_I actually don't remember that song, but it's fine!!!_

### 2. How many artist i've listened to it; how many songs; how many hours in all of these years; which countries from; 

```sql
-- HOW MANY ARTISTS
SELECT COUNT(DISTINCT(artist_name)) AS `Q. ARTIST PLAYED`
FROM spotihist;
```
<img width="450" alt="image" src="https://user-images.githubusercontent.com/112327873/220456807-7f9d53b7-3fff-43fa-98ad-b2ccab9c6998.png">

_**Looks like less than an artist by day (0.88)**_

```sql
-- HOW MANY HOURS
SELECT ROUND(SUM(ms_played/3600000),2) as `TOTAL HOURS PLAYED`
FROM spotihist;
```

<img width="450" alt="image" src="https://user-images.githubusercontent.com/112327873/220458733-3e6f2da1-b27e-4d70-adf2-ca119fae357a.png">

_**Something like 0.63 hours per day**_

```sql
-- HOW MANY SONGS
SELECT COUNT(DISTINCT(track_name)) AS `TOTAL # OF SONGS`
FROM spotihist;
```
<img width="450" alt="image" src="https://user-images.githubusercontent.com/112327873/220460647-0f8e5a43-9390-452c-9d5a-2c24ec0e2562.png">

_**It was like 2.62 songs per day**_

```sql
-- TOTAL HOURS PER COUNTRY?
SELECT conn_country, ROUND(SUM(ms_played/3600000),2) AS `# HOURS BY COUNTRY`
FROM spotihist
GROUP BY conn_country ORDER BY 2 DESC;
```

<img width="450" alt="image" src="https://user-images.githubusercontent.com/112327873/220463887-f4cac670-c3be-4d02-884e-72d8da9f863b.png">

_**I played spotify from 14 differents country (i haven't visited that many countries, but it is because of sometimes i play it with vpn)**_

### 3. What is my top 10 most listened artists, and songs?

```sql
-- TOP 10 MOST LISTENED ARTIST AND SONGS:

SELECT artist_name, ROUND(SUM(ms_played/3600000),2) AS `HOURS PLAYED`
FROM SPOTIHIST
GROUP BY ARTIST_NAME ORDER BY 2 DESC LIMIT 10;
```

<img width="450" alt="image" src="https://user-images.githubusercontent.com/112327873/220754521-4c129b82-cc7f-459e-895b-086d52cb83cc.png">

```sql
SELECT TRACK_NAME, ROUND(SUM(ms_played/3600000),2) AS `HOURS PLAYED`
FROM SPOTIHIST
GROUP BY TRACK_NAME ORDER BY 2 DESC LIMIT 10;
```

<img width="450" alt="image" src="https://user-images.githubusercontent.com/112327873/220754595-fc6ce17d-d134-4086-9ade-1a2f5930e841.png">

**As you can see, there is an artist and track name called 'None', so i had to figure it out, who is this; besides that, looks like my favourite artist
is Kings Of Leon, and my favourite song "En la ciudad de la furia" by Soda Stereo.**

```sql
SELECT *
FROM spotihist
where artist_name = 'None';

SELECT spotify_track_uri, episode_name, episode_show_name, spotify_episode_uri
FROM spotihist
where episode_show_name <> 'None';
```

<img width="100%" alt="image" src="https://user-images.githubusercontent.com/112327873/220752792-baf41758-042d-4d86-845e-b11ba7b497d0.png">

<img width="100%" alt="image" src="https://user-images.githubusercontent.com/112327873/220753264-f0f214f8-8c41-4781-9058-279802d57f0f.png">


**I realize it was all the podcasts i played, and others that didn't have any kind of information; so the last thing I wanted to know here was 
my favourites podcast:**
```sql
SELECT episode_show_name, ROUND(SUM(ms_played/3600000),2) AS `HOURS PLAYED`
FROM spotihist
where episode_show_name <> 'None'
GROUP BY episode_show_name ORDER BY 2 DESC LIMIT 10;
```
<img width="450" alt="image" src="https://user-images.githubusercontent.com/112327873/220753748-691bc699-1c36-4e00-ade0-74d1aeaf55b2.png">

## **II. A time series analysis**

As you can see at the beggining, this dataset is a collection of information from 2014 to beggining 2023. That means that i can do a time series analysis.

### 1. Hours listened to per year:

```sql
WITH YEARS_PLAYED AS (
SELECT EXTRACT(YEAR FROM WHEN_PLAYED) AS YEAR_PLAYED, SUM(MS_PLAYED/3600000) AS `HOURS_PLAYED`,
LAG(SUM(MS_PLAYED/3600000)) OVER (ORDER BY EXTRACT(YEAR FROM WHEN_PLAYED)) AS PREV_HR_PLAYED
FROM SPOTIHIST
GROUP BY YEAR_PLAYED)
SELECT *, 
(HOURS_PLAYED-PREV_HR_PLAYED) as HOURS_CHANGE,
ROUND(((HOURS_PLAYED/PREV_HR_PLAYED)-1)*100,2) PCT_CHANGE
FROM YEARS_PLAYED;
```
<img width="450" alt="image" src="https://user-images.githubusercontent.com/112327873/220896261-d3834bfd-61e5-4bea-9a09-4c55797112e4.png">

_**This table gives me a lot of information (I can discard the first year (2014 because it recorded just 4 months):**_

  a. First of all, the year which I listened the most hours was 2021, and the least was 2020;
  b. Between 2017 and 2020 i reduced a lot my time listening to spotify;
  c. In 2021 I increased to the most my listened hours, but then in 2022 I listened it to few hours.
  

