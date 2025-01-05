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


