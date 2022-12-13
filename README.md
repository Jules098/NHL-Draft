# NHL-Draft
This project will be an attempt at building a machine learning model to predict the likeliest player's to become NHLers prior to the draft.

## Database engineering

The first step of this project was to put together a database where all the relevant information was localized in one place.  While draft information is largely available, biographical information on the player's height and weight are not found with the statistical information of the player's draft year.  Since I wanted every players' height, weight, draft year production, and their draft minus 1 year production, there was a significant amount of time portioned to centralizing and creating the database necessary for the project.

The data was stored in individual csv files, that can be found in this repo.  Of note, the goalies were separated from the skaters and were not worked on for this project as their position requires a different lens of analysis, since the statistics we use to measure their performance are entirely different from the skaters.

## Data Cleaning

The cleaning of the data and the structuring of the database largely took place simultaneously.  A few decisions had to be made.  The sources from which I drew my draft information had enough missing +/- and penalty minute data for me to discard them as relevant statistics.  The Atlanta Thrashers were relocated to Winnipeg in the middle of this draft study.  As such, their picks were combined with the picks of the Winnipeg Jets, but both team's were denoted as Atlanta/Winnipeg to show their relationship while differentiating the management teams and ownerships involved with the two clubs.

The last part of the cleaning process was defining an NHLer.  I have defined it here as a player who has played at least 200 NHL games.  This threshold allows us to be certain the player had an impact on their team/teams for long enough to be considered a full-time NHLer.  Players who played under this threshold were determined to not be NHLers.

## Exploratory Data Analysis and Visualization

<img width="862" alt="Games Played per pick by Team" src="https://user-images.githubusercontent.com/110205528/207431522-93326ada-a21a-4445-a829-cd28bc4bd52b.png">

Visualizing draft pick value and team draft performance is crucial to ultimately being able to evaluate the model later.  Between 2007 and 2014 the Los Angeles Kings drafted players with the most total games played out of their draft picks, with 11 306 total. They also got the most games played per pick. The Tampa Bay Lightning drafted the highest scoring players in the draft, while also drafting the most players with the highest points per pick of the draft.  The New York Islanders were 2nd in all 4 categories.  On these metrics these 3 teams were the most successful drafting teams in the time period of this analysis, and it led to two of the three winning multiple Stanley cups: LA in 2012 and 2014, and Tampa Bay in 2020 and 2021.

<img width="720" alt="Cumulative Points Scored per Draft Round" src="https://user-images.githubusercontent.com/110205528/207433544-84d61428-7a32-4dee-a537-62053bd38846.png">

## Feature Engineering - NHLe

Training any model to predict the probability of a draft pick becoming an NHLer requires a specific type of production standardization, since the drafted players are coming from a number of different leagues with a wide variance in the degree of difficulty they are facing.  The most common junior leagues to draft from, the OHL, QMJHL and WHL, are much more difficult to score in than a smaller high school circuit, and much easier to score in than the men's professional leagues in Sweden, Finland and Russia.  NHLe has become the go to metric to standardize the scoring rates of players across these different leagues so as to make it easier to evaluate their performance relative to the degree of difficulty they are facing.

I followed the NHLe co-efficients of Patrick Bacon of topdownhockey, and the methodology of C.J. Turtoro in developing the NHLe metric I used for my model. You can find the links to their methodologies and thought processes down below.

## Making Pipelines and testing different models

I originally started my model building with the purpose of predicting a player's likelihood of becoming an NHLer by converting the concept of NHLer into a binary classification problem.  Over 200 games played was an NHLer, under was not. My initial results weren't terrific, however after broadening the scope of the dataset the precision scores improved drastically.  A player drafted typically plays in the NHL only 26% of the time, with the vast majority of those players coming in the 1st round of the draft.

I attempted two other models.  The first was changing the problem to a regression problem, and using the draft minus 1 year and draft year production profiles of the drafted players to predict their overall positioning in the draft.  In this problem, the target variable was switched from 'NHLer' to 'Overall'.  The other model I attempted cleaned out the players that didn't make the NHL to try and predict how many games played a drafted player would play in the NHL.  In this model, the target variable was NHL games played.

Overall, the regression solutions were not as intuitive as the classification of NHLer vs non-NHLer.  The original model seems to out-perform the two regression problems.

## Evaluation

There are two notes of import for my evaluation.  The first is that I chose precision scores as the metric to evaluate my model by.  This is because as a drafting strategy, maximizing drafting NHL profile players (True positives) is the likeliest way of coming out of any given draft with players that will have an impact on the team's roster down the line.  A false positive in this case, a player who looked like they would become an NHLer but weren't able to make it, is less of a mistake than using all of a team's draft capital swinging for players that do not have an NHL profile and hoping they become statistical outliers.

The second note of import: in order to evaluate my model, the model needed to be placed in the context of the NHL draft.

The methodoly I used to evaluate the model was to take my outputted probabilities and sort my 2013 and 2014 drafts by highest likelihood to become an NHLer.  Once my dataframe was sorted in this way, I pretended to be the 15th overall team, picking at positions 15, 45, 75, 105 135, 165, and 205 respectively.  My first pick was the highest probability player in my dataframe that hadn't been picked before 15th overall in the actual draft.  My second pick was the highest probability player after that not picked prior to 46th overall, etc.

The model's performance on the training data significantly out-performed all NHL teams, drafting players with over a combined 13 000 games played - almost 2000 more games played than the second best team in that time interval.  It performed less impressively on the testing data (2013 and 2014 drafts).  While it still drafted players that ended up becoming NHLers, the results weren't drastically different from an average NHL team.


Sources:

https://www.hockey-reference.com/draft/NHL_2007_entry.html

https://www.thedraftanalyst.com/draft-results-2011/ (for Biographical details)

https://www.eliteprospects.com/draft/nhl-entry-draft/2007

https://cj-turtoro.shinyapps.io/NNHLe-writeup/

https://towardsdatascience.com/nhl-equivalency-and-prospect-projection-models-building-the-nhl-equivalency-model-part-2-6f275a45e22
