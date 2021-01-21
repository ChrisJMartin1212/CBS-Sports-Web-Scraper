
# Intro

CBS-Sports-Web-Scraper is an R program designed to pull ONLY the quarter scores from the final box score of an NFL game. The code only works on historical (non real-time) data on the pages it scrapes.

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
```

## README is incomplete!
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.
