# World-Climbing-World-Cup-Predictions
As climbing is becoming as popular a sport as ever, I decided to compile data since 2023 to utilise in predicting the next world cup's results

# The purpose of this Project
The goal is to predict the podium / scores of the athletes participating in a world cup and produce a starting model which will be refined as more competitions are held. I've also compiled data that, to me, wasn't readily available. As a result I hope it will be useful for other projects in the future and hopefully I can get some feedback to improve the model.
As well as this, without seeing the boulders in a competition, it is hard to give a prediction due to the strengths and weaknesses that any individual athlete has. This project aims to compile these factors and run simulations based on previous competitions to provide a guess on the next world cup's results.

# Data
I initially collected 4 data files which were then cleaned and merged to form more data. However, the initial data files are:
* Qual_Boulder_Results.csv : This csv file consists of the 169 athletes registered for boulder world cups in 2026. Along with their attendances over the 2025-2026 seasons, their semi final attendances, final attendances, progression rate to semi finals and progression rate to finals. The Athlete ID is given by the first letter of their first name as well as their last name. E.g Sorato Anraku = SANRAKU
* BoulderStyle.csv : This csv file contains each of the world cups in 2023 (that had a final), separated by the round they were held (Final vs Semifinal). I've also included their top rate (a difficulty indicator) and individually watched and analysed each boulder to score them on their style from the categories : Slab, Coordination, Power, Compression, Dynamic and Press. These categories are given a number from 0-5 to quantify their prevalence in the individual boulders
* Events.csv : This csv file has each events ID which includes the location and year. E.g Hachioji 2023 => HAC23. And it contains the best score in the finals as well as the semi finals best score.
* BoulderResults.csv : This csv file contains the full list of athletes across each competition as well as the round they participated in (finals or semi finals), the event, and the score the athlete received for that particular boulder. There is also a column with a binary 1 for Yes and 0 for No for whether or not that athlete topped that boulder.

The following data files are those that have been cleaned/merged and used in the code (The methodology of obtaining these files are explained later on)
* SemiFinalists.csv : This contains my predicted semifinalist roster
* Athlete_Style_Affinity.csv : To quantify how much an athlete leans into their strengths, I've made this csv which contains score-like figures to quantify how much an athlete benefits from a boulder of their strengths
* Athlete_Style_Affinity_Shrunk.csv : The same data as the previous one just with Bayesian shrinkage applied to prevent bias towards athletes with more data points
* Boulder_Results_with_PctOfBest.csv : This data is produced initially in the code to compare athletes with the best score of a round rather than a full score. This is because, in an easy round many athletes may obtain higher scores than usual, or in a harder round many athletes may obtain lower scores than usual. I did not think it was a good idea to use score as a metric for placements because of this reason and so recorded the proportion of each athletes score to the best score.

# Methodology
## Semi Finalist Prediction
* The semifinalist prediction was done via Bayesian Shrinkage on the Q-SF progression rates recorded in the Qual_Boulder_Results data. This may seem quite lazy as a way to predict the semifinalists, however, I thought it to be the best method as footage of the qualifier boulders are not available and the athletes are separated into two groups. This makes it more difficult to predict the exact list of athletes whilst also replicating the way in which they are selected.
* The formula for Bayesian Shrinkage was utilised with a constant of k = 5. The formula is shown below:
