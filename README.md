## Recipes & Ratings Exploratory Data Analysis
Analysis done by Paul J. Losiewicz
### **Introduction**
  In the modern age of technology, the landscape for creating and sharing culinary arts and traditions has been made more accessible than ever.  No longer do amateur and professional cooks have to pass down age-old recipes by word-of-mouth or scribbles jotted down on a pad of paper. The internet allows cooks of all disciplines to share their own unique work and recieve widespread and almost instantaneous feedback; for this reason, we can readily explore the trends and discover insights on what make certain recipes popular as opposed to others and what sets a group of digital culinary publishers from the rest.

  Our dataset in question comes from food.com and originally featured two separate sections of raw data detailing recipes and reviews posted since 2008.  The raw recipe data originally featured 83,782 unique rows of recipes including column-values of recipe-names, recipe ID, preparation time, contributor id, date of submission, related tags, nutrition information, number of steps, and the detailed instructions and descriptions for each given recipe.  On the other hand, the raw ratings datatset originally came with 731,927 row-values which all feature a unique rating for any given recipe in the recipes-dataset.  The ratings dataset included information on user ID, recipe ID for the specified rating, posting-date, rating, and the actual review text.  

  This project consists of several steps, all detailed in the respective sections below.  We start by cleaning the dataset: there will be a more detailed description of this process further down the page.  Generally, I combined the ratings and reviews data-sets on the recipe ID column, essentially merging the recipe data onto each unique review. Afterwards, analysis is done on certain isolated column values of our combined-dataset, exploring the distrobution of given columns and the potential relationships that may exist between independent column values. Additionally, I conduct an analysis on the missingness of the "ratings" collumn, essentially focusing on the reasons why certain row-values may appear as N/A.  Finally, we round this project off by conducting permutation-tests on the research question posed-below.

  As mentioned earlier in this section, the exploration and accessibility of the culinary arts has changed significantly since the emergence of websites like food.com.  Websites and repositories like food.com allow anyone from amateurs to educated culinary-professionals to share whatever they want for willing consumers to make their own personal-spins of such recipes at home.  With such widespread accesibility and willing participation of both aspiring cooks on both-ends, this begs the question: *do more experienced food.com users recieve higher-ratings than those who publish in passing?*  By "more experienced food.com users", I am generally referring to contributors who have published at least 5 unique recipes to food.com, and by "those who publish in passing", I refer to those who have not.  This question bears a few observations to consider when framing a hypothesis with this data.  First, I wonder if those who just post a couple recipes recieve higher ratings due to uniqueness; for example, I can imagine that certain food.com users publish recipes that have been passed down through generations, and as a result, they share their recipe in passing as opposed to publishing myriads of recipes.  In addition, I wonder if food.com contributors who have published greater than 100 recipes do so as the loss of quality.  The over-arching question being explored here is *quality vs. quantity*, and by using the hundreds of thousands of unique-rows of data, I hope to draw some meaningful conclusions on this topic.

### **Cleaning and EDA**

#### Cleaning

  > Merging the raw data-frames
  To start the data-cleaning process, the first step, as mentioned in the introduction, was to merge the raw recipe and review data-sets on the 'recipe_id' column, merging on to the raw-review DataFrame.  After this, the next-step is to check the dtypes of all of the corresponding columns in our new, merged dataframe and make all necessary adjustments.  
  > Replacing 0 values with 'np.nan'
  The rating scale on food.com is measures from 1-5.  As a result, all 'rating' values stored as zeroes were appropriately treated as missing, and thus, replaced with np.nan
  > Creating 'average_rating'
  As mentioned earlier, we merged our data-frames onto the 'ratings' data-frame; as a result, there are multiple row values that represent the same recipe but with different reviews.  To simplify things, I created a 'average_rating' column which details the average-rating for each respective recipe on a given row.
  > Separating nutrition into separate columns
  First of all, the nutrition column came originally as a column of strings, all in the format of a list.  As a result, I had to convert this column into a list by using the '.split()' method, and I subsequently separated the list-values into their own, appropriate columns: 'calories', 'total_fat_pdv', 'sugar', 'sodium', 'protein', 'saturated_fat_pdv', and 'carbohydrates'.
  > Converting tags, steps, and ingredients into lists
  Similar to the 'nutrition' column, 'tags', 'steps', and 'ingredients' all came as strings formatted as lists.  As a result, I followed the same steps detailed above to convert each respective value in each column into lists of strings.
  > Converting 'submitted' into datetime
  This is pretty straight-forward, but I converted the column regarding time of submission into datetime objects to further simplify analysis.
  > Creating 'num_tags' column
  Data in the 'tags' column is stored as a list, so I found it necessary to include a 'num_tags' column which details the # of tags in each respective recipe for EDA.
  > Cleaned DataFrame
  After completing the data-cleaning, below is the '.head()' of our resulting data-frame, only showing necessary columns for convenience.

|    | name                                 |   minutes |   contributor_id |   average_rating |   calories |   num_tags |
|---:|:-------------------------------------|----------:|-----------------:|-----------------:|-----------:|-----------:|
|  0 | 1 brownies in the world    best ever |        40 |           985201 |                4 |      138.4 |         14 |
|  1 | 1 in canada chocolate chip cookies   |        45 |          1848091 |                5 |      595.1 |          9 |
|  2 | 412 broccoli casserole               |        40 |            50969 |                5 |      194.8 |         10 |
|  3 | 412 broccoli casserole               |        40 |            50969 |                5 |      194.8 |         10 |
|  4 | 412 broccoli casserole               |        40 |            50969 |                5 |      194.8 |         10 |

#### **EDA**
#### **Univariate Analysis**
For the univariate analysis section, I seek to explore the distribution of the number of recipes submitted per unique contributor.  In this visualization, I only include contributors who have submitted at-most 50 unique recipes to food.com.

<iframe src="assets/univariate1.html" width=800 height=600 frameBorder=0></iframe>
This visulization shows that an overwhelming majority of contributors on food.com have only submitted 1 recipe and to a larger degree <5 recipes.  Although it seems as if there are few contributos who have submitted ~50 recipes, it can be be demonstrated in the histogram below that there are still a significant number of contributors who have submitted >50 unique recipes.

<iframe src="assets/univariate2.html" width=800 height=600 frameBorder=0></iframe>
This visualization shows the same quantitative information as the histogram above; the only difference is the framing.  Here, I display only contributors with >=50 recipes submitted, and as you can see, there are still a significant # of contributors who have submitted a large number of recipes to food.com.  This histogram follows the same shape as the visualization only displaying <50 recipes, and I find it significant to observe that there exist users that have submitted as many as 500 to even 1000 recipes on food.com

<iframe src="assets/univariate3.html" width=800 height=600 frameBorder=0></iframe>
My final univariate analysis displays the distribution of average-rating per unique contributor.  Essentially, I was looking to visualize how many contributors were averagely getting rated at any given score, and similarly to the visualizations above, this histogram is skewed heavily torwards average perfect scores of 5.0.  It's also probably important to recognize that contributors who have only submitted 1 recipe likely recieve less reviews, and as a result, makes it easier to score an averge score of 5.0.

#### **Bivariate Analysis**
To kick off bivariate analysis, we expand upon some of our visualizations earlier by looking at the relationship between the count of unique recipes published per contributor and the average rating across all recipes for the same respective contributor

<iframe src="assets/bivariate3.html" width=800 height=600 frameBorder=0></iframe>
This scatter-plot is meant to help visualize the relationship between the average rating and number of recipes submitted per contributor.  A trend here that is notable to point-out is that almost all contributors who have submitted >100 recipes rate, on average, in between 4 and 5 points.  Although we will conduct further tests on this obsevation later, it seems that contributors whom publish more-often almost always recieve reviews at a high-rate.

<iframe src="assets/bivariate4.html" width=800 height=600 frameBorder=0></iframe>
Another relationship which I found quite interesting to visualize was the correlation between the # of tags associated with a given recipe and the average score of such recipe.  In this visualization, I was wondering if a higher "accessibility" was causing recipes to be scored at higher rates.  By "accesibility" here, I mean that recipes with a greater number of tags can theoretically be discovered more easily by food.com users.  There does seem to be some type of positive relationship here as recipes with a high # of tags tend to score at a higher rate.

#### **Interesting Aggregates**

|   contributor_id |   num_recipes |   avg_rating |
|-----------------:|--------------:|-------------:|
|             1533 |             8 |      4.88889 |
|             1535 |            34 |      4.34667 |
|             1634 |             1 |      4.66667 |
|             2129 |             1 |      4       |
|             2312 |             1 |      5       |

The interesting aggregate I decided to showcase was actually a DataFrame I featured and visualized heavily up to this point.  This table is indexed on to unique contributor_ids, and the 'num_recipes' features the # of unique recipes submitted by each given contributor while 'avg_rating' features the average rating score across all recipes for each respective contributor.  We are going to use this aggregated data to conduct our permutation tests later in the analysis.

### Assessment of Missingness
#### **NMAR Analysis**
It should be noted that the missingness in the 'rating' column could potentially be dependent on data not observable in our dataset; for example, unsatisfied reviewers may be less likely to leave a review rating at all due to frustration.  Another possible reason for the 'rating' column to be NMAR are user-demographics; I find it possible that less-experienced or older food.com users are tehcnologically challenged, and as a result, they forget to leave a rating.

#### Missingness Dependency
In this part of the analysis, we look to explore the dependency of the missingness in the 'rating' column with data values in other respective columns.
> Rating and Preparation Time
Our null hypothesis here is that the distribution of 'minutes' when 'rating' is missing comes from the same distribution of 'minutes' when 'rating' is not missing.  Our alternative hypothesis is that the distribution of 'minutes' when 'rating' is missing is different than the distribution of 'minutes' when 'rating' is present.  We do this by finding the observed absolute difference between 'minutes' when 'rating' is missing and 'minutes' when 'rating' is present.  We then continue by running a permutation test on the missing data, essentially shuffling the data around and simulating 1000 test-statistics as if the 'minutes' data came from the same distribution.  The visualization below depicts our results.
<iframe src="assets/missing1.html" width=800 height=600 frameBorder=0></iframe>
As you can see, with 1000 test-statistics we received a p-value of 0.118 which under a significance-level of 0.05, we reject our alternative hypothesis and keep our null-hypothesis that this 'minutes' data comes from the same distribution.  This supports that, in this context, the 'rating' data is MCAR.

> Rating and # of Steps
Here we explore whether the distribution of 'n_steps' differs depending on the missingness of the 'rating' column.  Our null-hypothesis and alternative hypothesis remain the same.  **Null**: the distribution of 'n_steps' remains the same whether or not 'rating' is missing.  **Alternative**: The distribution of 'n_steps' when 'rating' is missing is different from the distribution of 'n_steps' when 'rating' is present.  We use the same test-statistic here and proceed with the same steps to perform our permutation test.
<iframe src="assets/missing2.html" width=800 height=600 frameBorder=0></iframe>
As demonstrated by our visualization above, there are no values in our simulated test-statistics that are as extreme or more extreme than our observed test-statistic; as a result, we end-up with a p-value of 0.0 and we reject our null-hypothesis. The data supports that our 'rating' column is MAR since it depends on the values of the 'n_steps' column.

### Hypothesis Testing
In our final section, we explore the question that we have been alluding to throughout this analysis in our EDA section: *do more experienced food.com contributors receive higher average rating scores than more amateur food.com users?*  For the framing of this question, we define *experienced* food.com users as those who have published >5 unique recipes, while we define amateur food.com users as those with less than 5 recipes. Below, we define our hypotheses: 
**Null Hypothesis**: Contributors who have published more than 5 recipes rate on-average the same as contributors who have published less than 5 recipes.
**Alternative Hypothesis**: Contributors who have published more than 5 recipes recieve higher average ratings than those with less than 5 published recipes.
**Test-Statistic**: The difference in means of average-rating between users with at least 5 recipes published and less than 5 recipes published.
*We will be conducting this test with a significance level of 0.05*
In order to conduct this test, we use the same aggregated DataFrame that we displayed earlier, and we run a permutation test by shuffling the average ratings and finding the difference in means between the group greater than 5 recipes published and the group with less.  The visualization is displayed below: 
<iframe src="assets/ht_plot.html" width=800 height=600 frameBorder=0></iframe>
As seen by the empirical distribution of our simulated test-statistics, there are no simulated test-statistics under our null hypothesis in which there was a result as extreme or more extreme than our observed test-statistic.  This results in a p-value of 0.0.  Under these circumstances, we reject our null-hypothesis in favor of our alternative. The significance of these results lies in the reasons for which our data supported our alternative.  As displayed by our visualizations earlier, there are contributors on food.com with greater than 500 unique recipes published.  These contributors could potentially be large culinary compaines with tons of employees who constantly output these recipes, and our data supports that such contributors still favor quality while outputting quantity.