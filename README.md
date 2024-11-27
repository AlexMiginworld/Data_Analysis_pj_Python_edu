# Deep diving into Data Analysis with Python

### Overview and main goal 


In my search for a comprehensive Python guide to data analysis, I stumbled upon [Luke Barousse's YouTube channel](https://www.youtube.com/@LukeBarousse), and it was a stroke of luck. His easygoing teaching style and clear explanations made this course perfect for beginners like me. The course was also up-to-date, having been released only a few months ago. So, I jumped at the chance to learn data analysis with Python.

Using data from [Luke Barousse's Python course](https://lukebarousse.com/python), I conducted an analysis of job titles, salaries, locations, and essential skills. Through a series of Python scripts, I explored key questions such as job trends, the most in-demand skills in the field of data analytics in Ukraine.


### Core Questions
1. What are the cities & companies with the most job opportunities for data professionals? 
2. What are the distributions of job requirements for a degree, work-from-home options, and job schedule types (part-time, full-time, contract)?
3. What are the most sought-after skills for Data Scientists, Data Engineers, and Data Analysts?
4. How have the in-demand skills for Data Analysts evolved over time in 2023?

### Key Tools

*For my data analysis project, I relied on the following tools:*

- **Python:** The core programming language for data manipulation and analysis. I used the Pandas library for data cleaning and manipulation, Matplotlib and Seaborn for data visualization.
- **Jupyter Notebooks:** An interactive environment that allowed me to combine code, visualizations, and explanatory text in a single document.
- **Visual Studio Code:** A powerful code editor for writing and running Python scripts.
- **Git & GitHub:** Version control tools for tracking changes and sharing my code with collaborators.


# Data Preparation and Processing

*This section details the data preparation steps to ensure accuracy and usability for analysis.*

## Import & Clean Up Data

```python
# Importing Libraries
import ast
import pandas as pd
import seaborn as sns
import numpy as np
from datasets import load_dataset
import matplotlib.pyplot as plt
from matplotlib.ticker import FuncFormatter
from adjustText import adjust_text



# Loading Data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()



# Data Cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date']) 

# Transform job_skills from str to list by using apply method
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)


```


After identifying key research questions, I narrowed down my analysis to the Ukrainian job market. I then selected the top 3 most sought-after Data Analyst roles and applied relevant filters to the dataset

```python
df_DA_UKR = df[(df['job_title_short'].isin(['Data Analyst', 'Data Scientist', 'Data Engineer'])) & (df['job_country'] == 'Ukraine')].copy()
```
The path wasn't easy and I started from this:
```python
df_DA_UKR['job_location'].value_counts().to_frame()

```

<img src="Data_Analysis_Project_Ukr/images/states1.png" alt="Image description" width="300" height="450">

To this:

<img src="Data_Analysis_Project_Ukr/images/cities2.png" alt="Image description" width="125" height="300">



 *These seemingly simple steps were a daunting task for me, like venturing into uncharted territory.*



## 1. What are the cities with the most job opportunities for data professionals? 


View my notebook with detailed steps here: [1_Exploratory_Data_Analysis_Intro](Data_Analysis_Project_Ukr/1_Exploratory_Data_Analysis_Intro.ipynb).

### Visualize Data:


```python

df_DA_plot = df_DA_UKR['job_location'].value_counts().to_frame()

sns.set_theme(style='ticks')
sns.barplot(data=df_DA_plot, x='count', y='job_location', hue='count', palette='dark:#5A9_r', legend= False)
sns.despine()
plt.suptitle('Counts of Job Locations in Ukraine', fontsize=14)
plt.title('For Data Analysts, Data Engineers, Data Scientists', fontsize=10)
plt.xlabel('Number of Jobs', fontsize=10)
plt.ylabel('')
plt.xticks(range(0, 600, 50))
plt.show()
```

### Results:

<img src="Data_Analysis_Project_Ukr/images/Job Locations_in_Ukraine.png" alt="Counts of Job Locations in Ukraine">


<img src="Data_Analysis_Project_Ukr/images/Top_30_Tech_Companies.png" alt="top 30 companies">

*This data is based on whole 2023*

---------

### Insights:

The capital city, Kyiv, clearly stands out as the primary job market hub for data professionals in Ukraine. It has over 500 job openings, which is significantly higher than any other location.
Remote Work Opportunities or "Anywhere" category is indicating a growing trend in flexible work arrangements due to war. 

## 2. What are the distributions of job requirements for a degree, work-from-home options, and job schedule types (part-time, full-time, contract)?



### Visualize Data:


```python

dict_column = {
    'job_work_from_home': 'Work from Home',
    'job_no_degree_mention': 'Degree Required',
    'job_schedule_type': 'Job Schedule Type'
}

fig, ax = plt.subplots(1, 3)
fig.set_size_inches((12, 5))

for i, (column, title) in enumerate(dict_column.items()):
    if i == 2:  # Check if it's the last pie chart
        data = df_DA_UKR[column].value_counts().values
        labels = df_DA_UKR[column].value_counts().index.to_numpy()
    else:
        # Data and labels for the first two pie charts remain the same
        data = df_DA_UKR[column].value_counts()
        labels = ['False', 'True']
        explode = None

    ax[i].pie(data, startangle=90, autopct='%1.1f%%', labels=labels, explode=explode,
             textprops={'fontsize': 12})  
    ax[i].set_title(title)

    if i == 2:
        texts = ax[i].texts
        texts[1].set_position((0.5, -0.1)) 
        adjust_text(texts, force_points=0.2, force_text=0.6, arrowprops=None)

plt.show()
```

### Results:

<img src="Data_Analysis_Project_Ukr/images/piecharts.png" alt="3 pie charts">



### Insights:

In conclusion, the data-related job market in Ukraine during 2023 was characterized by a strong demand for degree-qualified professionals, a preference for full-time roles, and a limited emphasis on remote work.

## 3. What are the most sought-after skills for Data Scientists, Data Engineers, and Data Analysts?

I wanted to know which skills are most in-demand for the top 3 most popular data jobs. I picked the most popular job titles and found the top 10 skills for each one. This helps me see which skills I should focus on to get the job I want.

View my notebook with detailed steps here: [3_Skills_Requirements](Data_Analysis_Project_Ukr/3_Skills_Requirements.ipynb).



### Visualize Data:

```python
fig, ax = plt.subplots(len(job_titles), 1, figsize=(12, 10))

sns.set_theme(style='ticks')

for i, job_titles in enumerate(job_titles):
    df_plot = df_skills_perc[df_skills_perc['job_title_short'] == job_titles].head(10)
    #df_plot.plot(kind='barh', x='job_skills', y='skill_percent', ax=ax[i], title=job_titles)
    sns.barplot(data=df_plot, x='skill_percent', y='job_skills', ax=ax[i], hue='skill_count', palette='dark:b_r')
    ax[i].set_title(job_titles)
    ax[i].set_ylabel('')
    ax[i].set_xlabel('')
    ax[i].get_legend().remove()
    ax[i].set_xlim(0, 70)


    for n, v, in enumerate(df_plot['skill_percent']):
        ax[i].text(v + 1, n, f'{v:.0f}%', va='center')
    
    if i != len(job_titles) - 1:
        ax[i].set_xticks([])


fig.suptitle('Percentage of likelihood Skills in Job Postings in Ukraine 2023', fontsize=15)
fig.tight_layout()
plt.show()
```
### Results:

<img src="Data_Analysis_Project_Ukr/images/Percentage of likelihood Skills.png" alt="Percentage of likelihood Skills in Job Postings in Ukraine 2023">

### Insights:
Overall, this plot provides valuable insights into the skills that are most in-demand in the Ukrainian job market for data-related roles in 2023. Python and SQL are the most sought-after skills across all three roles: Data Analyst, Data Engineer, and Data Scientist. Other important skills include big data technologies (Spark, Airflow, Kafka), cloud platforms (AWS, Azure), and machine learning libraries (TensorFlow, Pandas, PyTorch).


## 4. How have the in-demand skills for Data Analysts evolved over time in 2023?

To analyze skill trends in the Data Analyst field for 2023, I conducted a time-series analysis of job postings. By grouping postings by month and identifying the top 5 skills, I was able to observe the fluctuation of skill demand throughout the year.

View my notebook with detailed steps here: [2_Skills_Trends](Data_Analysis_Project_Ukr/2_Skills_Trends.ipynb)


### Visualize Data:

```python
df_plot = df_DA_Ukr_perc.iloc[:, :5]
sns.lineplot(data=df_plot, dashes=False, legend='full', palette='tab10')
sns.set_theme(style='ticks')
sns.despine() # remove top and right spines

plt.title('Trending Top Skills for Data Analysis in the Ukraine')
plt.ylabel('Likelihood in Job Posting')
plt.xlabel('2023')
plt.legend().remove()
plt.gca().yaxis.set_major_formatter(PercentFormatter(decimals=0))

# annotate the plot with the top 5 skills using plt.text()
for i in range(5):
    plt.text(11.2, df_plot.iloc[-1, i], df_plot.columns[i], color='black')


plt.show()

```

### Results:

<img src="Data_Analysis_Project_Ukr/images/Trending_Top_Skills.png" alt="Trending Top Skills">

*Bar graph visualizing the trending top skills for data analysts in Ukraine in 2023.*



### Insights:

It's interesting to see that Python and SQL are the most in-demand skills, with their likelihood in job postings consistently above 40%. AWS and Tableau also seem to be gaining popularity, with their lines showing an upward trend. Power BI, on the other hand, seems to be less sought-after, with its line staying relatively low. 


# Conclusion

## My victories

This exploration into the data analyst job market has been incredibly informative, highlighting the critical skills and trends that shape this evolving field. The insights I got enhance my understanding and provide actionable guidance for anyone looking to advance their career in data analytics. As the market continues to change, ongoing analysis will be essential to stay ahead in data analytics. This project is a good foundation for future explorations and underscores the importance of continuous learning and adaptation in the data field.

## My misfortunes

While this project provided valuable insights into the Ukrainian data analyst job market, it's important to note that the initial dataset contained inaccuracies regarding salary information for Ukraine. The reported salary range of $60,000 to $90,000 per year for Data Analyst positions is significantly higher than the actual market rates.

Unfortunately, many employers in Ukraine are hesitant to disclose accurate salary information until the interview stage, making it difficult to obtain precise data. Despite these limitations, I attempted to estimate salary ranges based on available information, but the complexity of the task and the lack of reliable data hindered my efforts.


## What I Learned

Reflecting on my learning journey, I gained valuable insights into the data analyst job market and honed my Python skills

Specifically, I:

- Mastered Python tools: I became proficient in using libraries like Pandas for data cleaning and manipulation, Seaborn and Matplotlib for creating informative visualizations, and other tools to tackle complex data analysis challenges.
- Recognized the power of data cleaning: I learned that clean and accurate data is essential for reliable insights. I now understand the importance of thorough data preparation before diving into analysis.
- Developed a strategic approach to skill development: This project highlighted the significance of aligning my skills with market demand. Understanding the connection between skills and job opportunities will help me make informed career decisions in the tech industry.

----

P. S.

Many Thanks for [Mr. Luke Barousse](https://www.linkedin.com/in/luke-b/)!