

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

<ol>
  <li><h4>Listening Trends Over Time
    <ol type="1">
      <li>Yearly Spotify Listening Trends</h4></li>
    </ol>
  </li>
</ol>



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




---


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





