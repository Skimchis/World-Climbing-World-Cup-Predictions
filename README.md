# World-Climbing-World-Cup-Predictions
As climbing is becoming as popular a sport as ever, I decided to compile data since 2023 to utilise in predicting the next world cup's results

# The purpose of this Project
The goal is to predict the podium / scores of the athletes participating in a world cup and produce a starting model which will be refined as more competitions are held. I've also compiled data that, to me, wasn't readily available. As a result I hope it will be useful for other projects in the future and hopefully I can get some feedback to improve the model.
As well as this, without seeing the boulders in a competition, it is hard to give a prediction due to the strengths and weaknesses that any individual athlete has. This project aims to compile these factors and run simulations based on previous competitions to provide a guess on the next world cup's results.

# Data
I initially collected 4 data files which were then cleaned and merged to form more data. However, the initial data files are:
* Qual_Boulder_Results.csv : This csv file consists of the 169 athletes registered for boulder world cups in 2026. Along with their attendances over the 2025-2026 seasons, their semi final attendances, final attendances, progression rate to semi finals and progression rate to finals. The Athlete ID is given by the first letter of their first name as well as their last name. E.g Sorato Anraku = SANRAKU
* BoulderStyle.csv : This csv file contains each of the world cups since 2023 that had a finals round, separated by the round they were held (Final vs Semifinal). I've also included their top rate (a difficulty indicator) and individually watched and analysed each boulder to score them on their style from the categories : Slab, Coordination, Power, Compression, Dynamic and Press. These categories are given a number from 0-5 to quantify their prevalence in the individual boulders
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

$$
\text{Blended Rate} = \frac{\text{SemiFinals} + k \cdot \text{Prior Rate}}{\text{Attended} + k}
$$

The prior rate is the population's rate of enterring semi finals. That is:

$$
\text{Prior Rate} = \frac{\displaystyle\sum \text{SemiFinals}}{\displaystyle\sum \text{Attended}}
$$


## Style Affinity Profiles per Athlete
To obtain the style affinity per athlete I used a formula that utilised the athletes PctOfBest score ( e.g if Dohyun lee scored 50 points but the winner scored 100, Dohyun Lee's PctOfBest is 50%) and the recorded style of each boulder to build a style profile for each athlete.

* First I used the formula below to add how much a boulder gives to an athletes profile in a particular style 's':

$$
\text{style weighted}_{s,i} = \text{Pct Of Best}_i \cdot \text{Style}_{s,i}
$$

* Then we assign the affinity as:

$$
\text{Style Affinity}_s = \frac{\sum_{i} \text{Pct Of Best}_i \cdot \text{Style}_{s,i}}{\sum_{i} \text{Style}_{s,i}}
$$

* E.g, if an athlete participated in 2 boulders with power 5. And got a 50% and 80% PctOfBest respectively. Then the style_weighted would be 2.5+4. Which would give an affinity of (6.5/10)

Once again, I applied Bayesian shrinkage to help mediate the issue of lack of information for some athletes. This time I applied the value of k based on the median of each style's total weight for the athletes. For each individual style 's' we have:

$$
\text{Style Affinity Shrunk}_s = \frac{\text{Athlete Style Weighted}_s + k_s \cdot \text{Field Average}_s}{\text{Athlete Style}_s + k_s}
$$

## Scoring Model
I utilised Ridge regression with interaction terms between boulder style ratings and athlete affinity to model the PctOfBest for each athlete and compare them amongst each other. This model has a R^2 value of 0.201 and MAE of 0.276.

I used Ridge regression due to the lack of relevant recent datapoints to compare athletes as precisely as I would want to. So, Ridge regression helps to prevent an extreme model by decreasing the overall variance in the model.

## Monte Carlo Simulations
Resampled boulders amongst the ones I've recorded data for. This prevents unrealistic boulders from being set e.g we would not expect a boulder with 5 power and 5 slab. Hence it ensures the boulders we're testing with are feasible and realistic. 

I introduced residual-based performance noise for the athletes to simulate having good days and bad days and the random fluctuations of athlete performance from event to event. 

Simulated thousands of events through the semi finals round to the finals round to produce finalist probabilities as well as podium probabilities which I then use to predict the podium for the next event as well as the finalists.


# Results
Backtesting against the 2026 events, the model achieved an overall Semifinals prediction precision of 68% and an overall FInals prediction precision of 45%.

As for the podium, across the 5 events (15 total finalist spots) the model had an overlap with 11/15 spots (73%). 

The images below display the resulting podium predictions, finalists predictions and affinity heatmap produced.

<img width="1357" height="335" alt="image" src="https://github.com/user-attachments/assets/d2ef9924-0ad3-4241-96d7-81309d0369b2" />

<img width="1600" height="2000" alt="image" src="https://github.com/user-attachments/assets/905dea79-4048-467f-ac26-e63740722fc4" />

<img width="1580" height="1979" alt="image" src="https://github.com/user-attachments/assets/6f34276e-a25b-465d-8158-6d57fc4ee2dc" />


# Limitations

## Lack of recent data
Bouldering only has 6 competitions in a year and athletes can improve lots over this time. As well as this, the style of route setting can also change. For example, in 2025 there was a clear focus on dynamic coordination boulders. Whereas in 2026 they are including more physical old-style boulders. This makes it difficult to create a very accurate model. Furthermore, there are many more categories of boulders one can add, for example, complexity in the route, crimps, slopers, pinches. All of which are strengths of individual climbers but would require more detailed analysis for each boulder. 

On the topic of boulder style, some boulders only have 1 athlete that tops the boulder, as a result, I've based the style of the boulder on that one top. Which may create bias towards how they've climbed it. And some boulders do not get topped during competitions. This also makes it difficult to judge how it fits into each category as I, myself, am not a top tier competitive climber.

Furthermore, in recent time, Sorato Anraku, Dohyun Lee and Mejdi Schalk have been quite dominant in the bouldering world cups. Almost missing no semi finals/finals between each other. Whilst we see in our simulations, some athletes still have non zero probabilities for gold medals, the dominance of these 3 athletes means our model has given them a significantly higher probability of being in finals/podium/gold medal spots. This is a weakness of the model, though, for the current 2026 season, does not seem to be an issue as these athletes may still continue to dominate. We see this weakness particularly because in our prediction of the podium for each of the 2026 events, the model always predicts that these 3 athletes will be on the podium

## Model Limitations
The Ridge regression model has a low R^2 value of 0.201. Which means still around 80% of the variance is unexplained by the model. This is to be expected since, in competition, there are many more factors such as the pressure and subtle differences in boulders / in the moment situations that may make significant differences in even our finalists.

However, the model still explains about 20% which indicates our model is still clarifying some of the variance.

The model coefficients seem to be unintuitive but this is due to multicolinearity of the variables rather than a weakness in the model. We can see this as an athlete that is strong at power boulders likely possesses a very good strength in compression moves or can use that power to generate the required momentum for coordination/dynamic boulders. But this also means that the affinity scores may reflect general ability rather than fully distinct style specialisations between athletes. I say this because we observe top athletes tend to score uniformly well in all 6 styles and weaker athletes uniformly low. 

## Simulation Limitations
The residual noise used to mimic good/bad days for each athlete is drawn from one pooled distribution across all athletes rather than giving each individual athlete their own consistency profile. This is an issue since some athletes are more consistent than others and may be an improvement for the future.

The simulated rounds are ranked by 'Pct_Of_Best' rather than an actual absolute points system. This is to avoid a round's overall difficulty from disproportionally affecting rankings and allows the ranking system to stay consistent throughout all rounds without having to take into account low scoring vs high scoring rounds.

## Evaluation Limitations
The semifinalists and finalist progression rates used for backtesting were computed by pooling all events in the data together including the 2026 events that we used to validate against. A proper backtest would've recomputed all these using only chronologically prior data which hasn't been implemented. This is a point to take note of when looking at the precision figures as they probably overstate the true accuracy to some degree as it has been biased using the actual data within the model.

We've only been able to use 5 events in the 2026 season to backtest the model. This is not a big sample to use to test the model against and whilst I've achieved a 73% overlap in the prediction of the podium, the model consistently predicts the same 3 names for the podium spots which is not ideal.
