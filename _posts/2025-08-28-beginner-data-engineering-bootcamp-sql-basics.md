---
title: "Beginner DE Bootcamp: SQL Basics"
date: 2025-08-28 10:00:00 +0000
categories: [Blog, Data-Engineering]
tags: [sql]
excerpt: "Module 3 - Introduction to SQL"
image:
    path: /assets/img/blog/module3.png
    alt: SQL Notes
---

I’ve just started Zac’s beginner data engineering bootcamp on [dataexpert.io](https://dataexpert.io), and I’m making notes as I explore each module. The course is a great introduction to foundational data engineering concepts that every aspiring data engineer should know. Below is a quick rundown of what I’ve learned so far.

## Database Overview

The bootcamp uses a sample **NBA database** that contains multiple tables of player and game data.  
This is a great real-world dataset because it’s large, has relationships across tables, and makes the queries more engaging.

Some of the key tables include:

- **`nba_player_seasons`**  
  Contains season-level statistics for each player, such as points, rebounds, assists, and personal info like age and college.

- **`nba_games`**  
  Stores high-level game information such as game ID, date, season, and teams playing.

- **`nba_game_details`**  
  Includes player-level stats for each game (points, rebounds, minutes played, etc.)

<!--more-->

## Basic Queries: SELECT, FROM, WHERE

The most basic SQL query starts with `SELECT` (columns) and `FROM` (table). You can limit rows and filter with `WHERE`.

```sql
-- All columns, limited rows
SELECT *
FROM bootcamp.nba_player_seasons
LIMIT 50;

-- Players aged 40 or older
SELECT player_name, age, team, college
FROM bootcamp.nba_player_seasons
WHERE age >= 40;

-- Players aged 40+ who played college ball in Florida
SELECT player_name, age, team, college
FROM bootcamp.nba_player_seasons
WHERE age >= 40
  AND college = 'Florida';
```

## Aggregation for Insights

Aggregation functions help summarize data:

- COUNT(*) — number of rows
- AVG(column) — average
- SUM(column) — total
- ARRAY_AGG(DISTINCT column) — array of unique values

Use `GROUP BY` to compute these per group. Example: per country, show count of players, average points, total rebounds, and list players.

```sql
SELECT
  country,
  COUNT(*)                         AS player_count,
  AVG(pts)                         AS avg_points,
  SUM(reb)                         AS total_rebounds,
  ARRAY_AGG(DISTINCT player_name)  AS players
FROM bootcamp.nba_player_seasons
GROUP BY country
ORDER BY player_count DESC;
```

## Joining Tables

Real-world data spans tables. Use joins to connect them. Here we join game details with games on `game_id` to bring stats together with metadata.

```sql
SELECT
  d.player_name,
  d.team_id,
  d.pts,
  g.game_date,
  g.season
FROM bootcamp.nba_game_details AS d
JOIN bootcamp.nba_games AS g
  ON d.game_id = g.game_id
WHERE g.season = 2020
LIMIT 100;
```

## Queries with CTEs

Common Table Expressions (CTEs) let you structure complex queries into readable steps. Example with two CTEs—one deduping details and one deduping games—then joining and summarizing for a specific player.

```sql
WITH
deduped_details AS (
  SELECT DISTINCT game_id, player_id, player_name, team_id, pts
  FROM bootcamp.nba_game_details
),
deduped_games AS (
  SELECT DISTINCT game_id, game_date, season
  FROM bootcamp.nba_games
)
SELECT
  dd.player_name,
  dg.season,
  SUM(dd.pts)                                AS total_points,
  COUNT(DISTINCT dd.game_id)                  AS games_played,
  ARRAY_AGG(dg.game_date ORDER BY dg.game_date) AS game_dates
FROM deduped_details dd
JOIN deduped_games dg
  ON dd.game_id = dg.game_id
WHERE dd.player_name = 'LeBron James'
GROUP BY dd.player_name, dg.season
ORDER BY dg.season;
```
I’m excited to keep learning as the bootcamp dives into various data engineering related cocepts in the upcoming weeks

👉 Huge thanks to **Zac Wilson** for launching this free learning resource for aspiring data engineers!  