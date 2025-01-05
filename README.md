

<h1>Spotify Wrapped: A Deep Dive Into 8 Years of Data
<br />
Part 2. Analyzing Spotify Data Using SQL
</h1>



<h2>Description</h2>

<p><b>Objective:</b> To analyze my Spotify listening habits over the past 8 years, uncovering how they have evolved over time, what influences these changes, and what they reveal about my music preferences and behaviors.</p>

<p><b>Questions to answer:</b></p>
<p><b>Listening trends over time</b></p>

- How much time did I spend listening to Spotify, and how have my habits changed?<br />
- How much time did I spend listening to music versus podcasts?<br />
- What days of the week and times of the day do I listen the most?

<p><b>Artists, songs, gernes, and podcasts</b></p>

- Which artists and songs have been my favorites, and how have they changed over time?<br />
- What are my top podcasts over the years?<br />
- Do I skip songs often? What genres do I skip the most?<br />
- How often do I explore new music versus sticking with familiar favorites?

<p><b>Device preferences and lifestyle</b></p>

- What device do I use the most: desktop, mobile, or maybe TV?<br />
- How has my device usage evolved alongside lifestyle changes, like working remotely or pandemic?<br />
- How does Spotify fit into my daily routine - background noise, focused listening, or exploring new music?

## Table of Contents



1. [Listening Trends Over Time](#listening-trends-over-time----------yearly-spotify-listening-trends)
    1. [Yearly Spotify Listening Trends](#listening-trends-over-time----------yearly-spotify-listening-trends)
    2. [Monthly Listening Patterns](#listening-trends-over-time----------yearly-spotify-listening-trends)
    3. [Weekly Listening Habits](#listening-trends-over-time----------yearly-spotify-listening-trends)
    4. [Listening Habits by Time of Day](#listening-trends-over-time----------yearly-spotify-listening-trends)     
2. [Artists, Songs, and Genrese](#listening-trends-over-time----------yearly-spotify-listening-trends)
    1. [Skipping Behavior](#listening-trends-over-time----------yearly-spotify-listening-trends)
        1. [When Do I Skip?](#listening-trends-over-time----------yearly-spotify-listening-trends)
        2. [Most Skipped Genres](#listening-trends-over-time----------yearly-spotify-listening-trends)
        3. [Songs I Skip the Most](#listening-trends-over-time----------yearly-spotify-listening-trends)
    2. [My Top Artists Over the Years](#listening-trends-over-time----------yearly-spotify-listening-trends)
        1. [Top Artists Rankings](#listening-trends-over-time----------yearly-spotify-listening-trends)
        2. [Listening Time for Top Artists](#listening-trends-over-time----------yearly-spotify-listening-trends)
        3. [Favourite Songs Through the Years](#listening-trends-over-time----------yearly-spotify-listening-trends)
        4. [Artists: Tracks vs. Listening Hours Correlation](#listening-trends-over-time----------yearly-spotify-listening-trends)
    3. [ My Top Podcast Over the Years](#listening-trends-over-time----------yearly-spotify-listening-trends)
        1. [Most-Listened Podcasts](#listening-trends-over-time----------yearly-spotify-listening-trends)
        2. [ Podcast Episodes vs. Listening Minutes Correlation](#listening-trends-over-time----------yearly-spotify-listening-trends)
3. [Device Analysis and Lifestyle Changes](#listening-trends-over-time----------yearly-spotify-listening-trends)
    1. [Device Usage Breakdown](#listening-trends-over-time----------yearly-spotify-listening-trends)
1. [Conclusion](#listening-trends-over-time----------yearly-spotify-listening-trends)
  


<h2>Project walk-through:</h2>

<h3> 
1. Listening Trends Over Time <br />
&nbsp;&nbsp;&nbsp;&nbsp; 1.1. Yearly Spotify Listening Trends
</h3>



<blockquote style="background-color: #f0f0f0; padding: 15px; border-left: 5px solid #ccc; font-style: italic;">
<i> <b> Disclaimer: </b> I analyzed 8 years of Spotify data, but since the data was requested on December 6th, 2024, I didn't have any information for the remaining days of December. Rather than excluding December entirely and limiting the analysis to overlapping months across all years, I chose to use metrics like average listening time per day. This approach accounts for variations in the number of active days, normalizes the data, and ensures fair comparisons between years.</i>
</blockquote>

<h5>Script to pull yearly stats:</h5> 

```sql
SELECT  YEAR,
        COUNT(*) AS nb_tracks_total,
        COUNT(SPOTIFY_TRACK_URI) AS nb_music_tracks_total,
        COUNT(SPOTIFY_EPISODE_URI) AS nb_podcasts_total,
        ROUND(SUM(MIN_PLAYED)) AS minutes_played,
        ROUND(SUM(MIN_PLAYED)/60,2) AS hours_played,
        ROUND(SUM(MIN_PLAYED)/ COUNT(DISTINCT date),2) AS minutes_played_per_day,
        ROUND(SUM(MIN_PLAYED)/60 / COUNT(DISTINCT date),2) AS hours_played_per_day,
        COUNT(DISTINCT ARTIST_NAME) AS unique_artists, 
        COUNT(DISTINCT SPOTIFY_TRACK_URI) AS unique_music_tracks,
        COUNT(DISTINCT EPISODE_SHOW_NAME) AS unique_podcast_shows,
        COUNT(DISTINCT SPOTIFY_EPISODE_URI) AS unique_podcast_episodes,
        ROUND(COUNT(SPOTIFY_TRACK_URI) / NULLIF(COUNT(DISTINCT SPOTIFY_TRACK_URI),0),2) AS avg_plays_per_music_track,
        ROUND(COUNT(SPOTIFY_EPISODE_URI) / NULLIF(COUNT(DISTINCT SPOTIFY_EPISODE_URI),0),2) AS avg_plays_per_podcast_episode,
        ROUND(SUM(CASE WHEN SPOTIFY_TRACK_URI IS NOT NULL THEN MIN_PLAYED END)) AS minuted_played_music,
        ROUND(SUM(CASE WHEN SPOTIFY_EPISODE_URI IS NOT NULL THEN MIN_PLAYED END)) AS minuted_played_podcasts,
        ROUND(SUM(CASE WHEN SPOTIFY_TRACK_URI IS NOT NULL THEN MIN_PLAYED END)/ NULLIF(ROUND(SUM(MIN_PLAYED)),0)*100,2) AS perc_minuted_played_music_of_total,
        ROUND(SUM(CASE WHEN SPOTIFY_EPISODE_URI IS NOT NULL THEN MIN_PLAYED END)/NULLIF(ROUND(SUM(MIN_PLAYED)),0)*100,2) AS perc_minuted_played_podcasts_of_total
FROM "SAMPLE_SCHEMA"."PUBLIC"."SPOTIFY_WRAPPED"
GROUP BY YEAR
ORDER BY YEAR ASC;
```

<h5>Script to pull year-over-year changes:</h5> 

```sql
WITH yearly_metrics AS (
SELECT  YEAR,
        COUNT(*) AS nb_tracks_total,
        COUNT(SPOTIFY_TRACK_URI) AS nb_music_tracks_total,
        COUNT(SPOTIFY_EPISODE_URI) AS nb_podcasts_total,
        ROUND(SUM(MIN_PLAYED)) AS minutes_played,
        ROUND(SUM(MIN_PLAYED)/60,2) AS hours_played,
        ROUND(SUM(MIN_PLAYED)/ COUNT(DISTINCT date),2) AS minutes_played_per_day,
        ROUND(SUM(MIN_PLAYED)/60 / COUNT(DISTINCT date),2) AS hours_played_per_day,
        COUNT(DISTINCT ARTIST_NAME) AS unique_artists, 
        COUNT(DISTINCT SPOTIFY_TRACK_URI) AS unique_music_tracks,
        COUNT(DISTINCT EPISODE_SHOW_NAME) AS unique_podcast_shows,
        COUNT(DISTINCT SPOTIFY_EPISODE_URI) AS unique_podcast_episodes,
        ROUND(COUNT(SPOTIFY_TRACK_URI) / NULLIF(COUNT(DISTINCT SPOTIFY_TRACK_URI),0),2) AS avg_plays_per_music_track,
        ROUND(COUNT(SPOTIFY_EPISODE_URI) / NULLIF(COUNT(DISTINCT SPOTIFY_EPISODE_URI),0),2) AS avg_plays_per_podcast_episode,
        ROUND(SUM(CASE WHEN SPOTIFY_TRACK_URI IS NOT NULL THEN MIN_PLAYED END)) AS minuted_played_music,
        ROUND(SUM(CASE WHEN SPOTIFY_EPISODE_URI IS NOT NULL THEN MIN_PLAYED END)) AS minuted_played_podcasts,
        ROUND(SUM(CASE WHEN SPOTIFY_TRACK_URI IS NOT NULL THEN MIN_PLAYED END)/ NULLIF(ROUND(SUM(MIN_PLAYED)),0)*100,2) AS perc_minuted_played_music_of_total,
        ROUND(SUM(CASE WHEN SPOTIFY_EPISODE_URI IS NOT NULL THEN MIN_PLAYED END)/NULLIF(ROUND(SUM(MIN_PLAYED)),0)*100,2) AS perc_minuted_played_podcasts_of_total

FROM "SAMPLE_SCHEMA"."PUBLIC"."SPOTIFY_WRAPPED"
GROUP BY YEAR
)
SELECT
    YEAR,
    ROUND(((nb_tracks_total - LAG(nb_tracks_total) OVER (ORDER BY YEAR)) / NULLIF(LAG(nb_tracks_total) OVER (ORDER BY YEAR), 0)) * 100, 2) AS nb_tracks_total_yoy_change,
    ROUND(((minutes_played - LAG(minutes_played) OVER (ORDER BY YEAR)) / NULLIF(LAG(minutes_played) OVER (ORDER BY YEAR), 0)) * 100, 2) AS minutes_played_yoy_change,
    ROUND(((hours_played - LAG(hours_played) OVER (ORDER BY YEAR)) / NULLIF(LAG(hours_played) OVER (ORDER BY YEAR), 0)) * 100, 2) AS hours_played_yoy_change,
    ROUND(((unique_artists - LAG(unique_artists) OVER (ORDER BY YEAR)) / NULLIF(LAG(unique_artists) OVER (ORDER BY YEAR), 0)) * 100, 2) AS unique_artists_yoy_change,
    ROUND(((unique_music_tracks - LAG(unique_music_tracks) OVER (ORDER BY YEAR)) / NULLIF(LAG(unique_music_tracks) OVER (ORDER BY YEAR), 0)) * 100, 2) AS unique_music_tracks_yoy_change,
    ROUND(((unique_podcast_shows - LAG(unique_podcast_shows) OVER (ORDER BY YEAR)) / NULLIF(LAG(unique_podcast_shows) OVER (ORDER BY YEAR), 0)) * 100, 2) AS unique_podcast_shows_yoy_change,
    ROUND((( unique_podcast_episodes - LAG( unique_podcast_episodes) OVER (ORDER BY YEAR)) / NULLIF(LAG( unique_podcast_episodes) OVER (ORDER BY YEAR), 0)) * 100, 2) AS  unique_podcast_episodes_yoy_change,
    ROUND(((avg_plays_per_music_track - LAG(avg_plays_per_music_track) OVER (ORDER BY YEAR)) / NULLIF(LAG(avg_plays_per_music_track) OVER (ORDER BY YEAR), 0)) * 100, 2) AS avg_plays_per_music_track_yoy_change,
    ROUND(((avg_plays_per_podcast_episode - LAG(avg_plays_per_podcast_episode) OVER (ORDER BY YEAR)) / NULLIF(LAG(avg_plays_per_podcast_episode) OVER (ORDER BY YEAR), 0)) * 100, 2) AS avg_plays_per_podcast_episode_yoy_change,
    ROUND(((minuted_played_music - LAG(minuted_played_music) OVER (ORDER BY YEAR)) / NULLIF(LAG(minuted_played_music) OVER (ORDER BY YEAR), 0)) * 100, 2) AS minuted_played_music_yoy_change,
    ROUND(((minuted_played_podcasts - LAG(minuted_played_podcasts) OVER (ORDER BY YEAR)) / NULLIF(LAG(minuted_played_podcasts) OVER (ORDER BY YEAR), 0)) * 100, 2) AS minuted_played_podcasts_yoy_change
FROM yearly_metrics
ORDER BY YEAR ASC;

```



<h3>💡 Learnings:</h3>
<p> I was most active in Spotify listening around 2018–2019, with a gradual decline in recent years. My peak listening year was 2019, when I spent an average of <b>2.2 hours per day</b> on Spotify. Starting in 2020, with the onset of the pandemic and the shift to remote work, my listening time began to decline steadily, reaching <b>1.24 hours/day in 2024 - a 43.6% drop</b> compared to the peak in 2019.</p>
<p>Podcasts have become an increasingly important part of my listening habits, though they remain secondary to music. Their share of total listening time grew from <b>3.12% in 2018 to 7.16%</b> in 2024, indicating a consistent increase in interest. Notably, the time I spent listening to podcasts <b>skyrocketed by 169%</b> in 2020, likely driven by lifestyle changes during the pandemic.</p>
<p>My listening diversity has expanded over time. In 2023, I explored <b>1 750 unique artists</b> and listened to <b>the highest-ever number of unique music tracks (2 970)</b>. This suggests a growing curiosity and openness to discovering new music.</p>
<p>The average number of plays per music track has decreased steadily, from <b>3.9 in 2019 to 2.05 in 2023</b> - almost 2 times lower. This shift points to an exploratory approach to music, with less emphasis on replaying the same songs. For podcasts, the average plays per episode have remained stable (<b>around 1.0–1.39</b>), indicating that I tend to listen to podcasts only once.
</p>
<p>Overall, my Spotify habits have shifted over the years toward more <b>diverse, and exploratory engagement</b>, with music taking a slightly reduced role and podcasts steadily gaining ground</p>

<p align="center">
<img src="/images/1.1listening_trends_over_time.png" />
<br />

---


<h3>1.2 Monthly Spotify Listening Trends</h3>


<h5>Script to pull monthly listening stats:</h5> 

```sql
SELECT
    YEAR,
    MONTH,
    CASE 
        WHEN MONTH IN (1,2,12) THEN 'Winter'
        WHEN MONTH IN (3,4,5) THEN 'Spring'
        WHEN MONTH IN (6,7,8) THEN 'Summer'
        ELSE 'Autumn'
    END AS season,
    ROUND(SUM(MIN_PLAYED)/60,2) AS hours_played
FROM "SAMPLE_SCHEMA"."PUBLIC"."SPOTIFY_WRAPPED"
GROUP BY YEAR, MONTH, season
ORDER BY YEAR, MONTH, season ASC;

```

<h3>💡 Learnings:</h3>
<p>My listening habits remain fairly consistent across the seasons, but Summer and Winter tend to dominate overall, reflecting increased activity during holidays and colder months. External factors, such as lifestyle changes or major events, also play a significant role. For instance, in 2020, Spring led with 35.05% of my total listening time, likely due to spending more time indoors during the pandemic. In 2024, Winter reclaimed the top spot (32.93% of total listening time), driven by my heavy running load during that season.</p>

<p align="center">
<img src="/images/1.2listening_by_season.png" />
<br />

<h3>1.3 Weekly Listening Habits</h3>


<h5>Script to pull weekly listening stats:</h5> 

```sql
SELECT 
    TO_CHAR(DATE, 'DY') AS abbreviated_day_name,
    YEAR,
    ROUND(SUM(MIN_PLAYED)/60,2) AS hours_played,
    CASE 
        WHEN TO_CHAR(DATE, 'DY') = 'Mon' THEN 1
        WHEN TO_CHAR(DATE, 'DY') = 'Tue' THEN 2
        WHEN TO_CHAR(DATE, 'DY') = 'Wed' THEN 3
        WHEN TO_CHAR(DATE, 'DY') = 'Thu' THEN 4
        WHEN TO_CHAR(DATE, 'DY') = 'Fri' THEN 5
        WHEN TO_CHAR(DATE, 'DY') = 'Sat' THEN 6
        WHEN TO_CHAR(DATE, 'DY') = 'Sun' THEN 7
    END AS day_order
FROM "SAMPLE_SCHEMA"."PUBLIC"."SPOTIFY_WRAPPED"
GROUP BY 1,2,7
ORDER BY day_order, YEAR ASC;

```

<h3>💡 Learnings:</h3>
<p>My listening habits peak on weekdays, with Friday being the most active day. This could be attributed to the lighter workload on Fridays, which allows me to use music as background noise while I focus on less mentally demanding tasks.</p>
<p>However, over time, my listening patterns have shifted significantly:</p>

- <b>2017–2019:</b> Listening was highly concentrated on weekdays, especially Friday, which consistently dominated as the most active day (~ 21% of total listening). Weekends saw minimal activity during this period.<br />
- <b>2020:</b> The pandemic disrupted established patterns. Listening became more evenly distributed across the week, with notable increases on weekends and even weekdays like Tuesday (23.03%), likely reflecting changes in daily routines during the work-from-home period.<br />
- <b>2021–2022: </b>Weekend listening grew significantly, particularly on Saturdays in 2022, which accounted for 24.90% of total listening time. This marked a shift toward leisure-focused listening habits.<br />
- <b>2023–2024: </b>Sunday emerged as the most prominent day, growing from just ~4% of total listening time in 2018 to a striking 17.65% in 2024. This shift can largely be attributed to my regular running sessions on weekends (Sundays - long run days), which started in 2023 and have significantly increased my listening time during these days.<br />
<p>These changes reflect a gradual move away from weekday-focused listening to a more weekend-centered pattern, influenced by lifestyle changes such as running.</p>
<p align="center">
<img src="/images/1.3weekly_listening.png" />
<br />
<img src="/images/1.3weekly_listening_yoy.png" />
<br />
    
---


<h3>1.4 Listening Habits by Time of Day</h3>


<h5>Script to pull listening stats by time of day:</h5> 

```sql
SELECT 
    EXTRACT(HOUR FROM TS) AS hour,
    YEAR,
    MONTH,
    TO_CHAR(DATE, 'DY') AS abbreviated_day_name,
    TYPE,
    CASE 
        WHEN EXTRACT(HOUR FROM TS) BETWEEN 6 AND 11 THEN 'MORNING'
        WHEN EXTRACT(HOUR FROM TS) BETWEEN 12 AND 17 THEN 'AFTERNOON'
        WHEN EXTRACT(HOUR FROM TS) BETWEEN 18 AND 21 THEN 'EVENING'
        ELSE 'NIGHT'
    END AS time_day, 
    ROUND(SUM(MIN_PLAYED)) AS minutes_played,
    ROUND(SUM(MIN_PLAYED)/60,2) AS hours_played,
    ROUND(SUM(MIN_PLAYED)/ COUNT(DISTINCT date),2) AS minutes_played_per_day,
    ROUND(SUM(MIN_PLAYED)/60 / COUNT(DISTINCT date),2) AS hours_played_per_day
FROM "SAMPLE_SCHEMA"."PUBLIC"."SPOTIFY_WRAPPED"
GROUP BY 1,2,3,4,5,6
ORDER BY 1,2,3,4,5,6;

```

<h3>💡 Learnings:</h3>
<p>From 2017 to early 2020, my listening habits were heavily concentrated in the mornings (over 50% of total listening time), driven by my daily commute. Despite the pandemic and working from home in 2020, my morning listening remained strong thanks to walking my new dog. In 2023, as I developed a regular running habit, my mornings became slightly less dominant, with a noticeable shift toward the afternoon and evening. Additionally, my summer trips to the U.S. in 2023 and 2024 led to an increase in night-time listening.</p>

<p align="center">
<img src="/images/1.4listening_by_time_of_the_day.png" />
<br />

---


      
<h3> 
2. Artists, Songs, and Genres <br />
&nbsp;&nbsp;&nbsp;&nbsp; 2.1. Skipping Behavior<br />
&nbsp;&nbsp;&nbsp;&nbsp; 2.1.1 Device-Specific Skipping Patterns 
</h3>

<h5>Script to pull skipping data:</h5> 

```sql
SELECT 
        SKIPPED,
        COUNT(*) AS nb_tracks_total,
        ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) AS percentage_of_total
FROM "SAMPLE_SCHEMA"."PUBLIC"."SPOTIFY_WRAPPED"
GROUP BY 1
ORDER BY 1

```

```sql
SELECT    
       CASE 
            WHEN DEVICE_TYPE NOT IN ('desktop', 'mobile') THEN 'TV'
            ELSE DEVICE_TYPE
        END AS DEVICE_TYPE,
        SKIPPED,
        COUNT(*) AS nb_tracks_total
FROM "SAMPLE_SCHEMA"."PUBLIC"."SPOTIFY_WRAPPED"
GROUP BY 1,2
ORDER BY 1

```

<h3>💡 Learnings:</h3>
<p>I rarely skip songs on Spotify, with only 2% of tracks being skipped. This is likely due to my use of daily playlists, which are usually well-curated to my taste. Additionally, since I often play Spotify as background music while working, I don't pay close attention to every track, making me less likely to skip them.
</p>

<p align="center">
<img src="/images/2.1skipping_behavior.png" />
<br />

---

<h3>2.1.2 When Do I Skip?</h3>

<h5>Script to pull skipping bins data:</h5> 

```sql
SELECT
    CASE 
        WHEN sec_played IS NULL THEN NULL
        WHEN sec_played <= 10 THEN '[0-10]'
        WHEN sec_played BETWEEN 11 AND 20 THEN '[11-20]'
        WHEN sec_played BETWEEN 21 AND 30 THEN '[21-30]'
        WHEN sec_played BETWEEN 31 AND 40 THEN '[31-40]'
        WHEN sec_played BETWEEN 41 AND 60 THEN '[41-60]'
        ELSE '[61 and more]'
    END AS sec_played_bins,
    COUNT(*) AS nb_tracks_total,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) AS percentage_of_total
FROM "SAMPLE_SCHEMA"."PUBLIC"."SPOTIFY_WRAPPED"
WHERE TYPE = 'Music'
AND SKIPPED = 'TRUE'
GROUP BY 1
ORDER BY percentage_of_total DESC

```


<h3>💡 Learnings:</h3>
<p>I usually skip songs within the first 10 seconds or after 1 minute. If I skip right away, it’s because I’ve overplayed the song and lost interest. For new songs, I give them a fair chance by listening to the first verse or chorus before deciding whether to keep it or move on.
</p>

<p align="center">
<img src="/images/2.2skipping_bins.png" />
<br />

---

<h3>2.1.3 Most Skipped Genres</h3>

<h5>Script to pull skipping data by genre:</h5> 

```sql
 SELECT 
        g.GENRES,
        COUNT(*) AS nb_tracks_total,
        SUM(CASE WHEN SKIPPED = 'TRUE' THEN 1 ELSE 0 END) AS total_tracks_skipped,
        ROUND(100.0 * SUM(CASE WHEN SKIPPED = 'TRUE' THEN 1 ELSE 0 END) / COUNT(*), 2) AS skip_rate_percentage
    FROM "SAMPLE_SCHEMA"."PUBLIC"."SPOTIFY_WRAPPED" AS a
    JOIN "SAMPLE_SCHEMA"."PUBLIC"."GENRES" AS g
    ON a.ARTIST_NAME = g.ARTIST_NAME
    WHERE TYPE = 'Music' 
    GROUP BY a.ARTIST_NAME, g.GENRES
    HAVING nb_tracks_total >=10 
    AND SKIP_RATE_PERCENTAGE >=20.00
    ORDER BY skip_rate_percentage DESC

```
<blockquote style="background-color: #f0f0f0; padding: 15px; border-left: 5px solid #ccc; font-style: italic;">
<i> <b> Disclaimer: </b> <br />
1. To ensure a meaningful sample size for the skip rate and minimize noise from one-off listens or edge cases,  I filtered for artists with more than 10 tracks played, avoiding biases from one-off plays.<br />
2. Genres are assigned to artists, not individual tracks, and since artists often perform across multiple genres, calculating the skip rate for each genre without duplicating data is not feasible. Instead, I opted for an observational approach.
</i>
</blockquote>

<h3>💡 Learnings:</h3>
<p>I tend to skip artists from "dance, electronic" genres and various Polish pop subgenres the most. The high skip rates for Polish pop and Polish hip-hop are likely due to my frequent listening to "Top Songs Poland" playlists during my 5 years living in Poland.

</p>

<p align="center">
<img src="/images/genre_skipped.png" />
<br />

---

<h3>2.1.4 Songs I Skip the Mosts</h3>

<h5>Script to retrieve data on my most skipped songs:</h5> 

```sql
SELECT
        TRACK_NAME,
        ARTIST_NAME,
        COUNT(*) AS nb_tracks_total,
        SUM(CASE WHEN SKIPPED = 'TRUE' THEN 1 ELSE 0 END) AS total_tracks_skipped,
        ROUND(100.0 * SUM(CASE WHEN SKIPPED = 'TRUE' THEN 1 ELSE 0 END) / COUNT(*), 2) AS skip_rate_percentage
    FROM "SAMPLE_SCHEMA"."PUBLIC"."SPOTIFY_WRAPPED" 
    WHERE TYPE = 'Music'
    GROUP BY TRACK_NAME, ARTIST_NAME
    HAVING nb_tracks_total >=10 
    ORDER BY TOTAL_TRACKS_SKIPPED DESC

```


<h3>💡 Learnings:</h3>
<p>My top songs are often the ones I skip the most, simply because I get tired of hearing them repeatedly after adding them to my liked songs. This is what happened with "Flowers" by Miley Cyrus.

</p>

<p align="center">
<img src="/images/2.1.4flowers.png" />
<br />

---


<h3> 

&nbsp;&nbsp;&nbsp;&nbsp; 2.2 My Top Artists Over the Years<br />
&nbsp;&nbsp;&nbsp;&nbsp; 2.2.1 Top Artists Rankings
</h3>


<h5>Script to retrieve data on my favorite artists:</h5> 

```sql
SELECT 
    YEAR,
    ARTIST_NAME,
    ranking
    FROM (
        SELECT
            YEAR,
            ARTIST_NAME,
            DENSE_RANK() OVER (PARTITION BY YEAR ORDER BY nb_tracks_total DESC) AS ranking
        FROM (
            SELECT   
                    YEAR,
                    ARTIST_NAME,
                    COUNT(*) AS nb_tracks_total
                FROM "SAMPLE_SCHEMA"."PUBLIC"."SPOTIFY_WRAPPED" 
                WHERE TYPE = 'Music'
                GROUP BY 1,2
            ) AS subquery
    ) AS ranked_data
WHERE ranking<=10
ORDER BY YEAR, RANKING

```


<h3>💡 Learnings:</h3>
<p>Ed Sheeran has appeared in my top 10 artist rankings the most, with 5 total appearances. Notably, he secured the #1 spot twice, in 2017 and 2023.</p>

<p align="center">
<img src="/images/2.2.1fav_artists_top_1.png" />
<br />
<img src="/images/2.2.2fav_artists_top_10.png" />
<br />
---

<h3>2.2.2 Listening Time for Top Artists</h3>

<h5>Script to retrieve listening time data for top artists:</h5> 

```sql
WITH TopArtists AS (
    SELECT 
        ARTIST_NAME,
        ROUND(SUM(MIN_PLAYED) / 60, 2) AS total_hours_played
    FROM "SAMPLE_SCHEMA"."PUBLIC"."SPOTIFY_WRAPPED"
    WHERE ARTIST_NAME IS NOT NULL
    GROUP BY ARTIST_NAME
    ORDER BY total_hours_played DESC
    LIMIT 5
),
ArtistYearTrends AS (
    SELECT
        ARTIST_NAME,
        YEAR,
        ROUND(SUM(MIN_PLAYED) / 60, 2) AS yearly_hours_played
    FROM "SAMPLE_SCHEMA"."PUBLIC"."SPOTIFY_WRAPPED"
    WHERE ARTIST_NAME IS NOT NULL
    GROUP BY ARTIST_NAME, year
)
SELECT 
    t.ARTIST_NAME,
    a.year,
    a.yearly_hours_played
FROM ArtistYearTrends a
JOIN TopArtists t
    ON a.ARTIST_NAME = t.ARTIST_NAME
ORDER BY t.ARTIST_NAME, a.year;

```


<h3>💡 Learnings:</h3>
<p>All of the top artists I listened to experienced a drop in listening hours, reflecting my overall decrease in music consumption over the years.


</p>

<p align="center">
<img src="/images/Listening_Hours_artists.png" />
<br />

---

