
# Intro

CBS-Sports-Web-Scraper is an R program designed to pull ONLY the quarter scores from the final box score of an NFL game. The NFL code only works on historical (non real-time) data on the CBSSportsline.com NFL page.

## Packages/Libraries to install

```R
install.packages('rvest')
library(rvest)
library(stringr)
```

## Find the appropriate URL

```R
season <- 2020
wk <- 18
regular_or_playoffs <- 'regular'
  if (wk > 17) {
  regular_or_playoffs <- 'postseason'
            }
#make sure wk and season are correct before proceeding
surl <- paste0("https://www.cbssports.com/nfl/scoreboard/",season,"/",regular_or_playoffs,"/",wk)
scores_pg <- read_html(surl)
head(scores_pg)
```

## Parse URL

```R
qtr_scores <- html_nodes(scores_pg,'.scores')
qtr_scores

raw_teams <- html_nodes(scores_pg,'.team')
raw_teams

parsed_teams <- html_text(raw_teams,'')
parsed_teams
```

## Remove duplicates
```R
teams_playing <- parsed_teams[-c(1,3,5,7,9,11,13,15,17,19,21,23,25,27,29,31,33,35,37,39,41,43,45,47,49,51,53,55,57,59,61,63)]
teams_playing # creates a list of the teams that are playing that week
```
## Overtimes
Look over the week's scores to see if there are any overtime games. Then determine the indexes of the elements of the OT scores and remove them. Then extract the quarter scores to create a list of all of those.
```R
teams <- qtr_scores #[-c(37,42)] #only if need to remove OT scores
teamsscores <- html_text(teams,"") %>%
  str_extract("[0-9]{1,}") %>%
  as.numeric()
teamsscores
```
## Assemble dataframe
Now create a data frame where each row represents a four-quarter scoreline for each team.
```R
max_length <- 4 # of row
sequence_teamsscores <- seq_along(teamsscores)
split_teamsscores <- split(teamsscores, ceiling(sequence_teamsscores/max_length))
transposed_teamsscores <- t(data.frame(split_teamsscores))
transposed_teamsscores 
```
Now it's important to determine how many NFL teams are playing during the chosen week. If all teams are playing, the nrow number is 32; but if some teams have bye weeks, that number is smaller.
```R
week_teams <- data.frame(matrix(transposed_teamsscores, nrow = 32, ncol = 1))
week_teams # column of teams that are playing
```
Now combine the teams with their correct scorelines.
```R
scorelines <- cbind(week_teams, transposed_teamsscores)
colnames(Weekno_14_2020) <- c("Team", "Q1", "Q2", "Q3", "Q4")
scorelines 
```
Finally, create a new dataframe which lists the week (or date) and the season, and then cbind it to the scorelines dataframe. Again, based on the number of team playing in the week, change the final value to produce the correct number of rows.
```R
week_season<- data.frame("Week" = wk, "Season" = season)
week_season_rows <-week_season[rep(seq_len(nrow(week_season)), 32), ]# check to see if there are 32 teams playing this week
WEEKLY_SCORES <- cbind (scorelines,week_season_rows )
WEEKLY_SCORES 
```
## Save to an Excel file
```R
write.csv(WEEKLY_SCORES,"week_[week number or date]_[season].csv")
```

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## Copyright
[Chris Martin](https://github.com/ChrisJMartin1212)
