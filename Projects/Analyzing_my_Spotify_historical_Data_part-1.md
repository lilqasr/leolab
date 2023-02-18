# Analyzing my Spotify historical Data

When I was learning SQL I read a lot of information about which projects to do in order to show the skills learnt. 
One that attracted a lot my attention was the analysis of your Spotify history. 
It is something interesting because this a very popular platform to listening music, podcast and others; 
also, a lot of my friends has it. And you know that at the end of the year Spotify sends you the resume of your year.

So, I decided to analize this data, and I read this article where the author gave me an idea of how to do it. 
The first thing is to get into your account and ask for your data. 
There is multiple data that this app keep from you (as like every app in the world), 
but you will need the request your “Account Data” which includes your streaming history of the past year. 
You can also request the “Your Extended streaming history”. 
They send you the first one in about 5 days, and the second one about 30 days. So I request both of them.

You will received a zip file with all the information containing multiple json file. 
For the Account Data you get a json file called Streaming history which is the data from the last year. 
That is what you will import to your DBMS, in this case I used mysql workbench.

This first file gives you information about the End Time of the song/track, the artist name, the track name, and the milliseconds played. 

The second file is the extended streaming history, which includes the history for the lifetime of your account.

<h2 align="center"> Analysis of the last year</h2>

First of all, I created a Database, and imported the json file. Then I changed some parts of the table like names.

1.	The first thing was to know how many records the table has and the period of time cover by the data received:

<script src="https://gist.github.com/lilqasr/02cdc00f9f6cfa6164b40c3935cae2b3.js"></script>

<img width="141" alt="image" src="https://user-images.githubusercontent.com/112327873/219882484-3e5636f8-9d4c-4880-96d2-f808f07c1696.png">
 
<img width="359" alt="image" src="https://user-images.githubusercontent.com/112327873/219882612-06165337-3c31-461b-9f0c-6896633f1e9a.png">

It was exactly one year, from Jan 20th, 2022 to Jan 19th 2023.

2.	How many artist did I listen to and Total minutes played. The most played artist, number of times played and minutes

<script src="https://gist.github.com/lilqasr/0c502ecea0e36fcb583c1d969406c3b1.js"></script>

<img width="354" alt="image" src="https://user-images.githubusercontent.com/112327873/219882831-8b4879cf-6126-4bcd-9816-aecec1ebf4db.png">

<img width="344" alt="image" src="https://user-images.githubusercontent.com/112327873/219882835-f797494f-c91a-4734-81b7-a87dc7866a8f.png">

<img width="344" alt="image" src="https://user-images.githubusercontent.com/112327873/219882855-8370c88d-2966-49ab-a1dd-f39f3586566e.png">

<img width="334" alt="image" src="https://user-images.githubusercontent.com/112327873/219882860-ed639d63-5d24-4b18-bb62-1ec9323d99a6.png">

<img width="343" alt="image" src="https://user-images.githubusercontent.com/112327873/219882868-6411fb3c-58ea-4c21-8dad-72c9a6f02269.png">



