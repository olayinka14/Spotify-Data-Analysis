# Spotify-Data-Analysis
Project Category: Advanced

![Spotify Logo](https://github.com/olayinka14/Spotify-Data-Analysis/blob/main/spotify_logo.jpg)

## Schema

```sql
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);

```

### 1. Retrieve the names of all tracks that have more than 1 billion streams.

```sql
select * from spotify
where stream > 1000000000;
```

### 2. List all albums along with their respective artists.

```sql
select artist, album, album_type from spotify
where album_type = 'album';
```


### 3. Get the total number of comments for tracks where licensed = TRUE.

```sql
select sum(comments) from spotify
where licensed = 'True';
```


### 4. Find all tracks that belong to the album type single.

```sql
select * from spotify
where album_type = 'single';
```


### 5. Count the total number of tracks by each artist.

```sql
select artist, count(*) as nums from spotify
group by artist
order by nums desc;
```


### 6. Calculate the average danceability of tracks in each album.

```sql
select album, avg(danceability) dance_ability from spotify
group by album
order by dance_ability desc;
```


### 7. Find the top 5 tracks with the highest energy values.

```sql
select track, energy from spotify
order by energy desc
limit 5;
```


### 8. List all tracks along with their views and likes where official_video = TRUE.

```sql
select track, sum(views), sum(likes)
from spotify
where official_video = 'True'
group by track;
```


### 9. For each album, calculate the total views of all associated tracks.

```sql
select distinct album, track, sum(views) as nums
from spotify
group by album, track
order by nums desc;
```


### 10.Retrieve the track names that have been streamed on Spotify more than YouTube.select * from spotify;

```sql
with tab as (
select 
	track, 
	most_played_on,
	coalesce(case when most_played_on = 'Youtube' then stream end, 0) as youtube_stream,
	coalesce(case when most_played_on = 'Spotify' then stream end, 0) as spotify_stream
from spotify)
select 
	track, 
	most_played_on,
	spotify_stream
from tab
where spotify_stream > youtube_stream;
```


### 11. Find the top 3 most-viewed tracks for each artist using window functions.

```sql
with rankings as 
(
	select artist, track, views, rank()over(partition by artist order by views desc) as ranks
	from spotify
)
select * from rankings
where ranks <= 3;
```


### 12. Write a query to find tracks where the liveness score is above the average.

```sql
select * from
(
	select artist, track, liveness, avg(liveness)over() as average_liveness
	from spotify
) 
where liveness > average_liveness;
```


### 13. Use a WITH clause to calculate the difference between the highest and lowest energy values for tracks in each album.

```sql
with energy_table as 
(
	select album, max(energy) as max_energy, min(energy) as min_energy 
	from spotify
	group by 1
)
select *, cast(max_energy - min_energy AS DECIMAL(10,2)) AS difference
from energy_table
order by difference desc;
```
