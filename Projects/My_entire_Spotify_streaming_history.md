<h1 align="center"> What My Entire Spotify Streaming History Says about me?</h1>

In the [previous post](https://lilqasr.github.io/leolab/Projects/Analyzing_my_Spotify_historical_Data_part-1.html), 
i show my spotify streaming data analysis for an entire year, but before finish it, i received the entire streaming history, which is SINCE THE FIRST TIME
I PLAYED A SONG/TRACK ON SPOTIFY.

I received a zip file with all the information containing four files: three json files, and one PDF, which contains the explanation of the data sent.
The json files where all the data captured with the streaming information. 

## The process

**The first thing was to import the json files on individuals tables and then make the union.**

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
<img width="430" alt="image" src="https://user-images.githubusercontent.com/112327873/220428351-cebd81ad-23e4-4d95-8f98-6b8c47accb73.png">
<figcaption align = "center"><b> Data technical description.</b> </figcaption>

</figure>


**Once i had the whole table
