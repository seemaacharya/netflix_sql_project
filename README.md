# Netflix Movies and TV Shows Data Analysis SQL![Netflix Logo](https://github.com/seemaacharya/netflix_sql_project/blob/main/netflix_PNG25.png)
## Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives
Analyze the distribution of content types (movies vs TV shows).
Identify the most common ratings for movies and TV shows.
List and analyze content based on release years, countries, and durations.
Explore and categorize content based on specific criteria and keywords.

## Dataset
The data for this project is sourced from the Kaggle dataset:

## Dataset Link: [Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

## Schema
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
	show_id VARCHAR(6),
	type VARCHAR(10),
	title VARCHAR(150),
	director VARCHAR(208),
	casts VARCHAR(1000),
	country VARCHAR(150),
	date_added VARCHAR(50),
	release_year INT,
	rating VARCHAR(10),
	duration VARCHAR(15),
	listed_in VARCHAR(100),
	description VARCHAR(250)
);

## Business Problems & Solutions
SELECT * FROM netflix;



### Q1. Count of records
--A1. There are 8807 rows/records in our dataset

```sql
SELECT COUNT(*) as total_content FROM netflix;
```



### Q2.How many different or unique contents are there in type
--A2. There are 2 unique contents in type- 1.Movie and 2.TV Show


SELECT DISTINCT (type) AS content_type
FROM netflix;


### Q3. To check how many different or unique director are there in our data

SELECT DISTINCT (director) FROM netflix;


### Q4. Count the number of Movies vs TV Shows
--A4. There are 6131 total_content in Movie type and 2676 total_content in TV Show.

## Objective: Determine the distribution of content types on Netflix.



SELECT type,
COUNT(*) as total_content 
FROM netflix
GROUP BY type; 

### Q5. Find the most common rating for movies and TV shows
--A5. 'TV-MA' is the common rating given for movies and TV Shows.


SELECT DISTINCT ON (type) type, rating
FROM netflix
GROUP BY type, rating
ORDER BY type, COUNT(*) DESC;



### Q6. List all the movies released in a specific year (eg.2020)

SELECT * FROM netflix
WHERE 
	type='Movie'
	AND 
	release_year=2020;


### Q7. Find the top 5 countries with the most content on Netflix.
--A7. United States has the higest number of content on Netflix, whereas India holds the 2nd position with the count 1008 contents released on Netflix.
 
 */ARRAY is a kind of LIst that is separated by commas, we see that most of the contries are in
one line separated by commas, so we are using string to array. Then we are unnesting the array using UNNEST function.
*/


SELECT new_country, COUNT(*) AS total_content
FROM (
    SELECT UNNEST(STRING_TO_ARRAY(country, ',')) AS new_country
    FROM netflix
) AS countries
GROUP BY new_country
ORDER BY total_content DESC
LIMIT 5;




### Q8. Identify the longest movie.
--A8. 99 min is the longest duration, so below are the list of movies and TV Shows with the longest duration i.e 99 mins.

SELECT * FROM netflix 
WHERE 
	type= 'Movie'
	AND
	duration=(SELECT MAX(duration) FROM netflix);

 

	
### Q9. Find content added in the last 5 years.
*/
If date_added is not in a date format and contains the month as text, we can use the original
query with TO_DATE */


SELECT
*
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';



### Q10. Find all the movies or TV Shows by director 'Rajiv Chilaka'

SELECT *
FROM (
    SELECT *, UNNEST(STRING_TO_ARRAY(director, ',')) AS director_name
    FROM netflix
) AS directors
WHERE director_name = 'Rajiv Chilaka';




### Q11. List all the TV Shows with more than 5 seasons.


SELECT *
FROM netflix
WHERE 
    type = 'TV Show'
    AND 
    duration LIKE '%Seasons'  -- Ensures we're only looking at entries with "Seasons"
    AND 
    CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER) > 5;

    


### Q12. Count the number of content items in each genres.

SELECT genre, COUNT(*) AS total_content
FROM (
    SELECT UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre
    FROM netflix
) AS genres
GROUP BY genre;

select * from netflix;



### Q13. Find the average release year for content produced in a specified country.
--A13. For India: The average release year for content produced in India is 2012.
--For South Africa: The average release year for content produced in South Africa is 2017.

--For India-
SELECT ROUND(AVG(release_year)) AS average_release_year
FROM netflix
WHERE country = 'India';

--For South Africa
SELECT ROUND(AVG(release_year)) AS average_release_year
FROM netflix
WHERE country = 'South Africa';



### Q14. List all movies that are documentaries.

SELECT * FROM netflix
WHERE listed_in LIKE '%Documentaries';




### Q15. Find all content without a director.

SELECT * FROM netflix
WHERE director IS NULL;



### Q16. Find in how many movie actor 'Salman Khan' appeared in last 10 years.
--A16. Actor 'Salman Khan' has appeared in 2 movies over the last 10 years. The movies are Prem Ratan Dhan Payo and Paharganj.

SELECT * FROM netflix
WHERE 
	casts LIKE '%Salman Khan%'
	AND 
	release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10 

 


### Q17. Find the top 10 actors who have appeared in the highest number of movies produced in India.
--A17. Anupam Kher is the actor with the highest number of movies produced in India, having appeared in 36 such films.

SELECT 
	UNNEST(STRING_TO_ARRAY(casts, ',')) as actor,
	COUNT(*)
FROM netflix
WHERE country = 'India'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;




### */Q18:
Categorize the content based on the presence of the keywords 'kill' and 'violence' in 
the description field. Label content containing these keywords as 'Bad' and all other 
content as 'Good'. Count how many items fall into each category.
*/
--A18. There are 251 Bad movie type, 5880 Good movie type whereas 91 Bad TV Show and 2585 Good TV Show.

SELECT 
    category,
	TYPE,
    COUNT(*) AS content_count
FROM (
    SELECT 
		*,
        CASE 
            WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) AS categorized_content
GROUP BY 1,2
ORDER BY 2



--End of reports


