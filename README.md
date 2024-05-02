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
This table also combines information about games and participants. But it also has information about the age of the participants.<br>
The table has 180252 rows.<br>
![Games_competitor](https://github.com/julia-urikh/Olympic_games/blob/main/img/games_competitor.jpg?raw=true)
### Games 
The full list of modern Olympic games.<br>
The table has 51 rows.<br>
![Games](https://github.com/julia-urikh/Olympic_games/blob/main/img/games.jpg?raw=true)
### Games_city 
Table combining data from Games and City was created because there were cases when one Olympics was held in different places.<br>
The table has 52 rows.<br>
![Games_city](https://github.com/julia-urikh/Olympic_games/blob/main/img/games_city.jpg?raw=true)
### City 
List of all cities that hosted the Olympics games.<br>
The table has 42 rows.<br>
![City](https://github.com/julia-urikh/Olympic_games/blob/main/img/city.jpg?raw=true)
### Person 
Personal data of all participants of the Olympics games.<br>
The table has 128854 rows.<br>
![Person](https://github.com/julia-urikh/Olympic_games/blob/main/img/person.jpg?raw=true)
### Person_region 
Table combines information about the participant and his NOC. It was created because one participant could represent several NOCs at different Olympic Games.<br>
The table has 130521 rows.<br>
![Person_region](https://github.com/julia-urikh/Olympic_games/blob/main/img/person_region.jpg?raw=true)
### NOC_region 
Is a list of National Olympic Committee codes and the names of the countries represented. This roughly means which country the participant represented.<br>
The table has 231 rows.<br>
![NOC_region ](https://github.com/julia-urikh/Olympic_games/blob/main/img/noc_region.jpg?raw=true)
## Queries to the database
Let's find answers to some questions using SQL

-Identify the number of participants and the total number of medals they brought for each country.
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
ORDER  BY region_name; <br>
```
![]()
