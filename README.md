# Olympic Games <img src="https://github.com/julia-urikh/Olympic_games/blob/main/img/olympic%20rings.png?raw=true" width="80" alt='logo'>

The modern Olympic Games are sporting events that happen every four years and are attended by participants from different countries. They are not limited to any one sport, but combine a variety of competitions. That is why, since 1924, they have been divided into winter and summer competitions.

Have you ever been interested in the statistics of the Olympic Games? Let's take a look at the database from a great resource and look for answers to some unobvious questions.

The database was created on the basis of mySQL, DBeaver was used for the database management.

The database has 11 tables that cover all events from the Olympic Games since 1896 and ending in 2016.
## Let's look at the ERD 
![ERD Olympic Games](https://github.com/julia-urikh/Olympic_games/blob/main/img/er%20diagram%20olympic%20games.jpg?raw=true)

## Structure of the tables

### Sport 
List of all types of sports that can be presented at the Olympics
![sport](https://github.com/julia-urikh/Olympic_games/blob/main/img/sport.jpg?raw=true)
### Event 
Features of competitions for a particular sport
![event](https://github.com/julia-urikh/Olympic_games/blob/main/img/event.jpg?raw=true)
### Medal 
List of possible medals: gold, silver, bronze, and participation
![medal]()
### Competitor_event 
This table consists entirely of foreing key and combines information about participants, games and events they took part in, and awards they received.
![competitor event](https://github.com/julia-urikh/Olympic_games/blob/main/img/competitor_event.jpg?raw=true)
### Games_competitor 
This table also combines information about games and participants. It has information about the age of participants
![Games_competitor](https://github.com/julia-urikh/Olympic_games/blob/main/img/games_competitor.jpg?raw=true)
### Games 
The entire list of modern Olympic games
![Games](https://github.com/julia-urikh/Olympic_games/blob/main/img/games.jpg?raw=true)