<h1 align="center"> What My Entire Spotify Streaming History Says about me?</h1>

In the [previous post](https://lilqasr.github.io/leolab/Projects/Analyzing_my_Spotify_historical_Data_part-1.html), 
i show my spotify streaming data analysis for an entire year, but before finish it, i received the entire streaming history, which is SINCE THE FIRST TIME
I PLAYED A SONG/TRACK ON SPOTIFY.

I received a zip file with all the information containing four files: three json files, and one PDF, which contains the explanation of the data sent.
The json files where all the data captured with the streaming information. 

## The process

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

/* I realize there was some characters that did not allow me to convert the datetime column (ts/when_played).
So i had to replace this characters:*/

UPDATE spotihist
SET when_played = REPLACE(REPLACE(when_played, 'T', ' '),'Z', ''); 

UPDATE spotihist
SET when_played = REPLACE (when_played, 'Z', '');

ALTER TABLE spotihist
MODIFY when_played DATETIME;
```


