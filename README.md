## Introduction
This code models the difficulties of different cross country (XC) courses. 
The goal is to provide a single number that models each course's difficulty 
and can be used to adjust expected race times across courses.
For example, course A might be 1.1 times harder than course B,
and thus the expected times for course A will be 10% higher.

Using XC race results over multiple years and courses,
it builds a model that takes into account these different factors:

  * Course difficulty
  * Runner's inate ability
  * Runner's month-to-month improvement over the season
  * Runner's year-to-year improvement over their career

The model estimates a single number (ability or difficulty) for 
each runner and each course.
While the month-to-month and year-to-year parameters
are averages that apply to all runners.

To be more specific, given each runner's race times, 
the model fits parameters
to a mathematical model that looks like this:

```
race_time = (average_race_time - race_month*month_slope - student_year*year_slope)
            * runner_ability * course_difficulty
``````
Here, the race times are in seconds.
The race_month is the numerical month, starting with September is 0.
The student_year is the high-school year of the student, where freshman is 0.
Thus the slopes are in terms of seconds per month or year, 
to make the results easier to interpret.

The runner_ability and course_difficulty parameters are multiplicative factors
that modify the expected race time during the season. Both factors adjust the
expected times, but in different fashions.  So higher (>1) course difficulties
represent *harder* courses.  While lower (<1) runner abilities represent *faster*
runners. In both cases, higher numbers translate to longer finish times.

Note, the outputs from this model are unnormalized and should be considered
*relative* results. 
While both the ability and difficulty numbers tend to be close to 1, 
their baselines are arbitrary.
Thus an average course_difficulty of 0.5 and an average runner ability of 2 will
produce the same overall race-time predictions as the reverse.

## Bayesian Modeling
We use a Bayesian framework to fit a model that explains
the observed data (runner's race times).
In a Bayesian model all the parameters of the model are considered random
variables. We don't know Kent's true ability, so it is a random variable. 
Likewise, the difficulty of Crystal changes with the weather and other variables
we do not have control over. Our goal is to find probability distributions that
are as narrow as possible that explain the observed data.

By way of contrast, a deterministic model such as linear regression finds the
model parameters that produce the smallest possible error
(in the mean-squared sense)
when predicting the observed times.
Instead, here we use a Bayesian model so we can model and 
describe the uncertainties in our predictions.

An important part of Bayesian modeling is providing information about 
expectations of the parameters. In this case, we wish the course and runner
parameters to be approximately 1.0. This kind of constraint is added to the 
Bayesian model in the form of prior distributions.

We have 4 years of high school race results from the subscribers to
[XCStats.com](xcstats.com).
This represents 70k boys results and 63k girls results.
For the analysis presented here, we use the runners with times in the top 25\%
of each race, hypothesizing that these are likely to be the more serious runners
and will show less variance in their performance.
This left us with 22k boys
results and 19k girls results.
Our boy's model was traimed with 3919 runners 
running 443 courses.
While the girl's model was trained with 3229 runners running
432 courses.


Given our race results, we find the probability distribution for the parameters
that best explains the data using a
Python package called [PyMC](https://www.pymc.io/welcome.html).


## Results
MAP estimate is 11.02s, not sure why it doesn't match the MCMC sample trace histogram.
![Varsity Boys Monthly Slope (s/month)](Results/vb_monthly_slope.png)

MAP estimate is -16.1s, not sure why it doesn't match the MCMC sample trace histogram.
![Varsity Boys Yearly Slope (s/month)](Results/vb_yearly_slope.png)

![Varsity Boys Abilities (AU)](Results/vb_runner_abilities.png)

![Varsity Boys Course Difficulties (A. U.)](Results/vb_course_difficulty_comparison.png)

![Varsity Girls Course Difficulties (A. U.)](Results/vg_course_difficulty_comparison.png)

![Varsity Boys Stanford Course Difficultes (A. U.)](Results/vb_stanford_course_difficulty_histogram.png)


Interactive course difficulty renderer.  
[Use the buttons on the side to zoom and pan around the image.]

[Course Difficulty Comparison Viewer - probably have to download to render](Results/varsity_difficulties_comparison.html)
