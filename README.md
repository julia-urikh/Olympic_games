# Olympic Games <img src="https://github.com/julia-urikh/Olympic_games/blob/main/img/olympic%20rings.png?raw=true" width="70" alt='logo'>

The modern Olympic Games are sporting events that happen every four years and are attended by participants from different countries. They are not limited to any one sport, but combine a variety of competitions. That is why, since 1924, they have been divided into winter and summer competitions.

Have you ever been interested in the statistics of the Olympic Games? Let's take a look at the database from a great resource [databasestar.com](https://www.databasestar.com/sample-data-sql/) and look for answers to some unobvious questions.

The database was created on the basis of mySQL, DBeaver was used for the database management.

The database has 11 tables that cover all events from the Olympic Games since 1896 and ending in 2016.
## Let's look at the ERD 
![ERD Olympic Games](https://github.com/julia-urikh/Olympic_games/blob/main/img/er%20diagram%20olympic%20games.jpg?raw=true)

## Structure of the tables

### Sport 
List of all types of sports that can be presented at the Olympic Games.<br>
The table has 66 rows.<br>
![sport](https://github.com/julia-urikh/Olympic_games/blob/main/img/sport.jpg?raw=true)
### Event 
Features of competitions for the relevant sport.<br>
The table has 757 rows.<br>
![event](https://github.com/julia-urikh/Olympic_games/blob/main/img/event.jpg?raw=true)
### Medal 
List of possible medals: gold, silver, bronze, and participation.<br>
The table has 4 rows.<br>
![medal](https://github.com/julia-urikh/Olympic_games/blob/main/img/medal.jpg?raw=true)
### Competitor_event 
This table consists of a foreing key and combines information about participants, games and events they took part in, and awards they received.<br>
The table has 260971 rows.<br>
![competitor event](https://github.com/julia-urikh/Olympic_games/blob/main/img/competitor_event.jpg?raw=true)
### Games_competitor 
This table also combines information about games and participants. It also has information about the age of the participants.<br>
The table has 180252 rows.<br>
![Games_competitor](https://github.com/julia-urikh/Olympic_games/blob/main/img/games_competitor.jpg?raw=true)
### Games 
The full list of modern Olympic games.<br>
The table has 51 rows.<br>
![Games](https://github.com/julia-urikh/Olympic_games/blob/main/img/games.jpg?raw=true)
### Games_city 
Table that include information both from Games and City tables. It describes information about cases when one Olympics Games was held in different places.<br>
The table has 52 rows.<br>
![Games_city](https://github.com/julia-urikh/Olympic_games/blob/main/img/games_city.jpg?raw=true)
### City 
List of cities that are hosted the Olympics games.<br>
The table has 42 rows.<br>
![City](https://github.com/julia-urikh/Olympic_games/blob/main/img/city.jpg?raw=true)
### Person 
Personal information about participants of the Olympics games.<br>
The table has 128854 rows.<br>
![Person](https://github.com/julia-urikh/Olympic_games/blob/main/img/person.jpg?raw=true)
### Person_region 
Table combines information about the participant and their NOC. It consist of participants that could represent several NOCs at different Olympic Games.<br>
The table has 130521 rows.<br>
![Person_region](https://github.com/julia-urikh/Olympic_games/blob/main/img/person_region.jpg?raw=true)
### NOC_region 
Is a list of National Olympic Committee codes and the names of their countries.<br>
The table has 231 rows.<br>
![NOC_region ](https://github.com/julia-urikh/Olympic_games/blob/main/img/noc_region.jpg?raw=true)
## Queries to the database
Let's find answers to some questions using SQL

- <b>Identify the number of participants and the total number of medals they brought for each country.</b>
```
SELECT region_name,
       Count(p.id) AS participants,
       Count(CASE medal_name
               WHEN 'Gold' THEN 1
               ELSE NULL
             END)  AS gold,
       Count(CASE medal_name
               WHEN 'Silver' THEN 1
               ELSE NULL
             END)  AS silver,
       Count(CASE medal_name
               WHEN 'Bronze' THEN 1
               ELSE NULL
             END)  AS bronze
FROM   noc_region nr
       LEFT JOIN person_region pr
              ON nr.id = pr.region_id
       LEFT JOIN person p
              ON pr.person_id = p.id
       LEFT JOIN games_competitor gc
              ON p.id = gc.person_id
       LEFT JOIN competitor_event ce
              ON gc.id = ce.competitor_id
       LEFT JOIN medal m
              ON ce.medal_id = m.id
GROUP  BY region_name
ORDER  BY region_name; 
```
 In the results 230 rows<br>
![](https://github.com/julia-urikh/Olympic_games/blob/main/img/1%20partisi.%20of%20country.jpg?raw=true)
- <b>Which of the participants won medals at their first Olympic Games?</b>
```
WITH winner
     AS (SELECT DISTINCT p.full_name,
                         g.games_year,
                         g.games_name,
                         m.medal_name,
                         Row_number()
                           OVER (
                             partition BY p.id
                             ORDER BY g.games_year) AS order_games
         FROM   person p
                LEFT JOIN games_competitor gc
                       ON p.id = gc.person_id
                LEFT JOIN games g
                       ON g.id = gc.games_id
                LEFT JOIN competitor_event ce
                       ON gc.id = ce.competitor_id
                LEFT JOIN medal m
                       ON ce.medal_id = m.id)
SELECT full_name,
       games_year,
       games_name,
       medal_name
FROM   winner
WHERE  order_games = 1
       AND medal_name IN ( 'Bronze', 'Silver', 'Gold' )
ORDER  BY games_name; 
```
 In the results 18506 rows<br>
![](https://github.com/julia-urikh/Olympic_games/blob/main/img/2%20first%20win.jpg?raw=true)
- <b>The youngest and oldest champions in each sport.</b>
```
WITH age
     AS (SELECT DISTINCT s.sport_name,
                         p.full_name,
                         gc.age,
                         Dense_rank ()
                           OVER (
                             partition BY s.sport_name
                             ORDER BY gc.age ASC)  AS young,
                         Dense_rank ()
                           OVER (
                             partition BY s.sport_name
                             ORDER BY gc.age DESC) AS old
         FROM   person p
                LEFT JOIN games_competitor gc
                       ON p.id = gc.person_id
                LEFT JOIN competitor_event ce
                       ON gc.id = ce.competitor_id
                LEFT JOIN event e
                       ON ce.event_id = e.id
                LEFT JOIN sport s
                       ON e.sport_id = s.id) 
SELECT sport_name,
       full_name,
       age,
       'youngest' AS status
FROM   age
WHERE  young = 1
UNION
SELECT sport_name,
       full_name,
       age,
       'oldest' AS status
FROM   age
WHERE  old = 1
ORDER  BY sport_name; 
```
 In the results 203 rows<br>
![](https://github.com/julia-urikh/Olympic_games/blob/main/img/3%20old%20young.jpg?raw=true)
- <b>Count the number of medals by type for each participant</b>
```
SELECT p.full_name,
       m.medal_name,
       Count(ce.medal_id) AS cnt_medal
FROM   games_competitor gc
       LEFT JOIN competitor_event ce
              ON gc.id = ce.competitor_id
       LEFT JOIN medal m
              ON ce.medal_id = m.id
       LEFT JOIN person p
              ON gc.person_id = p.id
GROUP  BY p.full_name,
          m.medal_name
HAVING m.medal_name IN ( 'Bronze', 'Silver', 'Gold' )
ORDER  BY p.full_name,
          cnt_medal DESC; 
```
 In the results 32828 rows<br>
![](https://github.com/julia-urikh/Olympic_games/blob/main/img/4%20cnt_medal.JPG?raw=true)
- <b>Participants who took part for two or more countries during different Olympic Games</b>
```
WITH rank_reg
     AS (SELECT DISTINCT p.full_name,
                         nr.region_name,
                         Dense_rank ()
                           OVER (
                             partition BY p.id
                             ORDER BY nr.id) AS region_rank
         FROM   person p
                LEFT JOIN person_region pr
                       ON p.id = pr.person_id
                LEFT JOIN noc_region nr
                       ON pr.region_id = nr.id),
     last_reg
     AS (SELECT full_name,
                region_name,
                region_rank
         FROM   rank_reg
         WHERE  region_rank >= 2),
     first_reg
     AS (SELECT full_name,
                region_name,
                region_rank
         FROM   rank_reg
         WHERE  region_rank = 1) 
SELECT *
FROM   last_reg
UNION ALL
SELECT first_reg.full_name,
       first_reg.region_name,
       first_reg.region_rank
FROM   first_reg
       INNER JOIN last_reg
               ON first_reg.full_name = last_reg.full_name
ORDER  BY full_name,
          region_rank; 
```
 In the results 3348 rows<br>
![](https://github.com/julia-urikh/Olympic_games/blob/main/img/5%20diff_games.JPG?raw=true)
- <b>Who more (women or men) took part in mixed sports and won medals?</b>
```
WITH gender
     AS (SELECT e.event_name,
                p.gender,
                Count(p.id) AS cnt_partesipents,
                Count(CASE m.medal_name
                        WHEN 'Gold' THEN 1
                        WHEN 'Silver' THEN 1
                        WHEN 'Bronze' THEN 1
                        ELSE NULL
                      END)  AS cnt_medal
         FROM   person p
                LEFT JOIN games_competitor gc
                       ON p.id = gc.person_id
                LEFT JOIN competitor_event ce
                       ON gc.id = ce.competitor_id
                LEFT JOIN event e
                       ON ce.event_id = e.id
                LEFT JOIN medal m
                       ON m.id = ce.medal_id
         WHERE  event_name LIKE ( '%Mixed%' )
         GROUP  BY e.event_name,
                   p.gender)
SELECT *,
       Round(cnt_medal / cnt_partesipents * 100, 1) AS prcnt_winners
FROM   gender; 
```
 In the results 147 rows<br>
![](https://github.com/julia-urikh/Olympic_games/blob/main/img/6%20woman_man.JPG?raw=true)
- <b>What is the biggest difference in years between each competitor's first and last Olympic Games?</b>
```
WITH game_diff
     AS (SELECT DISTINCT p.id,
                         p.full_name,
                         First_value(g.games_year)
                           OVER (
                             partition BY p.id
                             ORDER BY g.games_year ASC)  AS first_games_year,
                         First_value(g.games_year)
                           OVER (
                             partition BY p.id
                             ORDER BY g.games_year DESC) AS last_games_year
         FROM   person p
                LEFT JOIN games_competitor gc
                       ON p.id = gc.person_id
                LEFT JOIN games g
                       ON gc.games_id = g.id
         ORDER  BY p.id)
SELECT *,
       last_games_year - first_games_year AS diff_year
FROM   game_diff
WHERE  last_games_year - first_games_year > 0
ORDER  BY diff_year DESC; 
```
 In the results 36688 rows<br>
![](https://github.com/julia-urikh/Olympic_games/blob/main/img/7%20diff_year.JPG?raw=true)
- <b>Information about Ukrainian Olympic champions. In which city, event and games they won their medals?</b>
```
SELECT p.full_name,
       e.event_name,
       m.medal_name,
       g.games_name,
       c.city_name
FROM   person p
       LEFT JOIN person_region pr
              ON p.id = pr.person_id
       LEFT JOIN noc_region nr
              ON pr.region_id = nr.id
       LEFT JOIN games_competitor gc
              ON p.id = gc.person_id
       LEFT JOIN competitor_event ce
              ON gc.id = ce.competitor_id
       LEFT JOIN event e
              ON e.id = ce.event_id
       LEFT JOIN medal m
              ON ce.medal_id = m.id
       LEFT JOIN games g
              ON g.id = gc.games_id
       LEFT JOIN games_city gcity
              ON gcity.games_id = g.id
       LEFT JOIN city c
              ON c.id = gcity.city_id
WHERE  nr.region_name = 'Ukraine'
       AND m.medal_name IN ( 'Gold', 'Silver', 'Bronze' )
ORDER  BY full_name,
          g.games_name; 
```
 In the results 245 rows<br>
![](https://github.com/julia-urikh/Olympic_games/blob/main/img/8%20ukr_inform.JPG?raw=true)
- <b>The Olympic Games have the most Ukrainian participants</b>
```
SELECT g.games_name,
       c.city_name,
       Count(p.id) AS ukr_participant
FROM   games g
       LEFT JOIN games_city gcity
              ON gcity.games_id = g.id
       LEFT JOIN city c
              ON c.id = gcity.city_id
       LEFT JOIN games_competitor gc
              ON g.id = gc.games_id
       LEFT JOIN person p
              ON p.id = gc.person_id
       LEFT JOIN person_region pr
              ON p.id = pr.person_id
       LEFT JOIN noc_region nr
              ON nr.id = pr.region_id
GROUP  BY g.games_name,
          nr.region_name,
          c.city_name
HAVING nr.region_name = 'Ukraine'
ORDER  BY ukr_participant DESC; 
```
 In the results 17 rows<br>
![](https://github.com/julia-urikh/Olympic_games/blob/main/img/9%20ukr_games.JPG?raw=true)
<!-- - <b></b>
```

```
 In the results  rows<br>
![]() -->