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




### Assessment of Missingness
### Hypothesis Testing