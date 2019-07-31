# sizing-v2

### Purpose
This is the second version of an application created to decrease returns in e-commerce sales by providing users with a more realistic idea of how a piece of clothing will fit, decreasing the frequency of a user ordering multiple sizes of the same article of clothing with the intent to return whichever pieces do not fit. The end goal is to provide the user with numerical representation, in the form of a percent match score, of how similarly the article of clothing will fit them compared to a picture of another shopper dressed in the item. This will allow customers to order a single size with an increased level of confidence that it will fit.

### Reasoning for a Second Version
The previous version was based on human measurements being generally scaled on a bell curve. However, a z-scores based model proved to not be the most accurate method of either comparison or estimation because comparisons close to the average appeared to be more dramatic despite the differences in the measurements being the same physical distance as a comparison far out on the bell curve's tail.  

This version instead takes the available client data and matches it to the most similarly sized individual in a large base dataset and uses their measurements to fill in any missing data points. For the match to be reasonably accurate, the user needs to input at least three data points out of the six possibilities: hip circumference, waist circumference, bust circumference, height, weight, and age.

### Data Sources
The base dataset in use is the 2012 Anthropometric Survey of US Army Personell (ANSUR). It contains 108 different measurements from 1,986 females enlisted in the United States Army at the time. Since the majority of people only know a basic few, if any, of their measurements, it would have been neither plausible or efficient to set up a system using all 108 parameters. Instead, the data from a select few rudimentary measurements were pulled and put into use: *buttock circumference (mm)* was used as a measurement for hip circumference, *chest circumference (mm)* was used as a measurement for bust, *waist circumference (mm)* was used for waist, as well as the recorded measurements of height (in), weight (lbs), and age (years). While it could be argued that the US Army does not provide a representative data set, when the values are put into a histogram, it creates a relatively normal shape. Unfortunately, like almost all bodily measurements, the chart does have to be skewed right because there is a physical limit to how tiny a body part can be that is comparatively small to the limit for how large a body part can be. For reference, below is a histogram of waist circumferences in the dataset:

![Histogram](https://www.dropbox.com/s/hg4by5twkp0jaa8/waistcircumference.png?raw=true)

Thus, although the data came from a relatively concentrated soucre it can act as a decent starting point and the data set will be able to grow organically each time a user comes armed already knowing all six of their data points.

### Process
When a user inputs their measurements into the system it feeds into a CSV. Any measurements not entered by the user are represented by zeros. The code represented here shows what occurs after this transfer and the current user's data is present in the last row of data in the CSV.
If a user enters incomplete information, meaning there are zeros in the last row of the CSV of client inputs, two loops are initialized that temporarily delete the columns from both the input and base data that correspond to the missing values. The remaining values are entered into a third loop that takes the euclidean distance between each datapoint in the base set and the user input data. For instance, if the user only entered waist circumference, height, and age, the calculation for a single euclidean distance would be:
  sqrt((waist<sub>1</sub>-waist<sub>2</sub>)<sup>2</sup> + (height<sub>1</sub>-height<sub>2</sub>)<sup>2</sup> +    (age<sub>1</sub>-age<sub>2</sub>)<sup>2</sup>)
  The distance values are entered into a list, and the minimum function is then used to find the row that is most similar to the input data. Values from this row of similar data from the base dataset are then used to substitute in for any unknown measurements from the user. In the example above, values for hips, bust, and weight would be borrowed from the closest matching datapoint in the base dataset to create a complete set of six measurements for the user. If a user inputs all six measurements, the program simply bypasses the estimation process.
  
  After a complete set of measurements is collected for the current user, the euclidean distance is used once again, but this time between the current user and all other previous reviewers of the article of clothing in question who have previously had their measurements recorded. 
  
  The percent match is created by comparing the above distance values to the farthest possible distance for the user in question. This is found by comparing the new complete set of datapoints back to the original base set, and this time finding the maximum Euclidean distance. This becomes the denominator of a fraction where the numerator is the distance between the current user and the reviewers. This entire fraction represents the difference between the user and the reviewer, so to get the percent match, it is subtracted from 1 like below:

![Fraction](https://www.dropbox.com/s/k9s7kodaemgu3hk/Screen%20Shot%202019-07-30%20at%204.25.25%20PM.png?raw=true)

This equation, the last in the code, calculates the percent match between any two users of the platform.

### Alterations to Make Code "Demo-able"
The version of social sizing uploaded can be run in a single click by not feeding the full client results into a new CSV, and instead just keeping them as an array labeled "clientdata1". To actually put this into use, the CSV would be necessary to save the results for future use so the original version of the code was split into two parts. One that finds the missing values for the entered user data, and a second that actually creates the comparison to any previous reviews.  
