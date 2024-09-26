# Spotify_Data_Analysis_Using_SQL

![2024-spotify-brand-assets-media-kit](https://github.com/user-attachments/assets/63430f55-bfbf-465b-b2c0-0ea97b96f4ca)


Project Category: Advanced
[Click Here to get Dataset](https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset)



## Overview
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using **SQL**. It covers an end-to-end process of normalizing a denormalized dataset, performing SQL queries of varying complexity (easy, medium, and advanced), and optimizing query performance. The primary goals of the project are to practice advanced SQL skills and generate valuable insights from the dataset.

```sql
-- create table
DROP TABLE IF EXISTS spotify;
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
## Project Steps

### 1. Data Exploration
Before diving into SQL, it’s important to understand the dataset thoroughly. The dataset contains attributes such as:
- `Artist`: The performer of the track.
- `Track`: The name of the song.
- `Album`: The album to which the track belongs.
- `Album_type`: The type of album (e.g., single or album).
- Various metrics such as `danceability`, `energy`, `loudness`, `tempo`, and more.

### 2. Querying the Data
After the data is inserted, various SQL queries can be written to explore and analyze the data. Queries are categorized into **easy**, **medium**, and **advanced** levels to help progressively develop SQL proficiency.

#### Easy Queries
- Simple data retrieval, filtering, and basic aggregations.
  
#### Medium Queries
- More complex queries involving grouping, aggregation functions, and joins.
  
#### Advanced Queries
- Nested subqueries, window functions, CTEs, and performance optimization.

### 3. Query Optimization
In advanced stages, the focus shifts to improving query performance. Some optimization strategies include:
- **Indexing**: Adding indexes on frequently queried columns.
- **Query Execution Plan**: Using `EXPLAIN ANALYZE` to review and refine query performance.
  
---

## 15 Practice Questions

-- 1. Retrieve the names of all tracks that have more than 1 billion streams.

```sql
SELECT * FROM spotify ;

SELECT * 
FROM spotify 
WHERE Stream > 1000000000 ;      -- ( 385 Rowes)
```
-- 2. List all albums along with their respective artists.

```sql
SELECT  DISTINCT Album ,  Artist
FROM spotify
ORDER BY 1 ;
```
-- 3. Get the total number of comments for tracks where `licensed = TRUE`.

```sql
SELECT * FROM spotify ;

SELECT SUM(S.comments) AS total_no_of_comments
FROM spotify S
WHERE S.licensed = 'True' ;
```
-- 4. Find all tracks that belong to the album type `single`.

```sql
SELECT * FROM spotify ;

SELECT S.track
FROM spotify S
WHERE album_type = 'single' ;
```

-- 5. Count the total number of tracks by each artist.

```sql
SELECT * FROM spotify ;

SELECT S.artist, COUNT (*) AS total_no_of_tracks
FROM spotify S
GROUP BY S.artist 
ORDER BY 1 ;                          -- ( 1 for desc / 2 for asc )
```

-- 6. Calculate the average danceability of tracks in each album.

```sql
SELECT * FROM spotify ;

SELECT S.album, AVG (S.danceability) AS average_danceability
FROM spotify S
GROUP BY S.album 
ORDER BY 1 ;
```

-- 7. Find the top 5 tracks with the highest energy values.

```sql
SELECT * FROM spotify ;

SELECT TOP 5 S.track, MAX (S.energy) AS highest_energy_values
FROM spotify S 
GROUP BY S.track
ORDER BY MAX (S.energy) DESC ;
```

-- 8. List all tracks along with their views and likes where `official_video = TRUE`.

```sql
SELECT * FROM spotify ;

SELECT S.track, 
SUM(S.views) AS total_views,
SUM(S.likes) AS total_views
FROM spotify S 
WHERE S.official_video = 1 
GROUP BY S.track ;
```

-- 9. For each album, calculate the total views of all associated tracks.

```sql
SELECT * FROM spotify ;

SELECT S.album, S.track , SUM(S.views) AS totaL_views
FROM spotify S 
GROUP BY S.album, S.track 
ORDER BY SUM(S.views) DESC ;
```

-- 10. Retrieve the track names that have been streamed on Spotify more than YouTube.

```sql
SELECT * FROM spotify ;

SELECT * FROM 
	(
	SELECT S.track, 
	COALESCE (SUM (CASE WHEN S.most_playedon = 'Spotify' THEN S.stream END),0)  AS streamed_on_Spotify,
	COALESCE (SUM (CASE WHEN S.most_playedon = 'Youtube' THEN S.stream END),0)  AS streamed_on_Youtube
	FROM spotify S 
	GROUP BY S.track 
	) AS SP
WHERE sp.streamed_on_Spotify > sp.streamed_on_Youtube 
AND sp.streamed_on_Youtube <> 0 
ORDER BY sp.streamed_on_Spotify DESC ;
```

-- 11. Find the top 3 most-viewed tracks for each artist using window functions.

```sql
SELECT * FROM spotify ;

WITH most_viewed_tracks
AS
(
	SELECT 
	S.artist, 
	S.track, 
	SUM(S.views) AS most_viewed,
	DENSE_RANK() OVER (PARTITION BY S.artist ORDER BY SUM(S.views) DESC) AS D_RNK
	FROM spotify S 
	GROUP BY S.artist,S.track
)
SELECT * FROM most_viewed_tracks
WHERE D_RNK <= 3 ;
```

-- 12. Write a query to find tracks where the liveness score is above the average.

```sql
SELECT * FROM spotify ;

SELECT AVG(S.liveness) avg_liveness
FROM spotify S ;                            -- 0.193653277653686

SELECT S.artist, S.track, S.liveness
FROM spotify S 
WHERE S.liveness > (SELECT AVG(S.liveness) avg_liveness FROM spotify S ) 
ORDER BY S.liveness ;
```				 

-- 13. Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.

```sql
SELECT * FROM spotify ;

WITH CTE
AS
(
	SELECT 
	album,
	MAX(energy) as highest_energy,
	MIN(energy) as lowest_energery
	FROM spotify
	GROUP BY 1
)
SELECT 
album,
highest_energy - lowest_energery as energy_diff
FROM cte
ORDER BY 2 DESC
```
   
14. Find tracks where the energy-to-liveness ratio is greater than 1.2.

```sql
SELECT * FROM Spotify ;

SELECT 
	track,
	energy / liveness AS energy_to_liveness_ratio
FROM Spotify 
	WHERE energy / liveness > 1.2 ;
```

15. Calculate the cumulative sum of likes for tracks ordered by the number of views, using window functions.

```sql
SELECT * FROM Spotify ;

SELECT 
	track,
	SUM(likes) OVER (ORDER BY views) AS cumulative_sum
FROM Spotify
	ORDER BY SUM(likes) OVER (ORDER BY views) DESC ;
```


Here’s an updated section for your **Spotify Advanced SQL Project and Query Optimization** README, focusing on the query optimization task you performed. You can include the specific screenshots and graphs as described.

---

## Query Optimization Technique 

To improve query performance, we carried out the following optimization process:

- **Initial Query Performance Analysis Using `EXPLAIN`**
    - We began by analyzing the performance of a query using the `EXPLAIN` function.
    - The query retrieved tracks based on the `artist` column, and the performance metrics were as follows:
    - Below is the **screenshot** of the `EXPLAIN` result before optimization:
    ![EXPLAIN Before Index]()
 
      

- **Index Creation on the `artist` Column**
    - To optimize the query performance, we created an index on the `artist` column. This ensures faster retrieval of rows where the artist is queried.
    - **SQL command** for creating the index:
      ```sql
      CREATE INDEX idx_artist ON spotify_tracks(artist);
      ```

- **Performance Analysis After Index Creation**
    - After creating the index, we ran the same query again and observed significant improvements in performance:
    - Below is the **screenshot** of the `EXPLAIN` result after index creation:
      
      ![EXPLAIN After Index](https://github.com/AshutoshSahu54/Spotify_Data_Analysis_Using_SQL/blob/main/Spotify_explain_before_index.png)

- **Graphical Performance Comparison**
    - A graph illustrating the comparison between the initial query execution time and the optimized query execution time after index creation.
    - **Graph view** shows the significant drop in both execution and planning times:
      ![Performance Graph](https://github.com/najirh/najirh-Spotify-Data-Analysis-using-SQL/blob/main/spotify_graphical%20view%203.png)
      ![Performance Graph](https://github.com/najirh/najirh-Spotify-Data-Analysis-using-SQL/blob/main/spotify_graphical%20view%202.png)
      ![Performance Graph](https://github.com/najirh/najirh-Spotify-Data-Analysis-using-SQL/blob/main/spotify_graphical%20view%201.png)

This optimization shows how indexing can drastically reduce query time, improving the overall performance of our database operations in the Spotify project.
---

## Technology Stack
- **Database**: PostgreSQL
- **SQL Queries**: DDL, DML, Aggregations, Joins, Subqueries, Window Functions
- **Tools**: pgAdmin 4 (or any SQL editor), PostgreSQL (via Homebrew, Docker, or direct installation)

## How to Run the Project
1. Install PostgreSQL and pgAdmin (if not already installed).
2. Set up the database schema and tables using the provided normalization structure.
3. Insert the sample data into the respective tables.
4. Execute SQL queries to solve the listed problems.
5. Explore query optimization techniques for large datasets.

---

## Next Steps
- **Visualize the Data**: Use a data visualization tool like **Tableau** or **Power BI** to create dashboards based on the query results.
- **Expand Dataset**: Add more rows to the dataset for broader analysis and scalability testing.
- **Advanced Querying**: Dive deeper into query optimization and explore the performance of SQL queries on larger datasets.



