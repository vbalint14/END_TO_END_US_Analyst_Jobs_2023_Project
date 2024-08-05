# END TO END US Analyst Jobs 2023 Project Documentation #
The project includes an [original dataset](https://huggingface.co/datasets/lukebarousse/data_jobs) that was cleaned before making further analysis and 5 additional csv files in which the data for visualizing the results of the analysis can be found. The project focuses on only 5 cities in the United States.
<br>
## 1. Count of Job postings per City
The first analysis contains data about the five most popular US cities: New York, Atlanta, Austin, Dallas and Chicago. <br>
**Content of [1_data_jobs_location_counts.csv](https://github.com/vbalint14/END_TO_END_US_Analyst_Jobs_2023_Project/blob/main/csv_files/1_data_jobs_location_counts.csv):**
| Job Location | Count |
|--------------|-------|
| New York     | 2715  |
| Atlanta      | 2417  |
| Chicago      | 1985  |
| Dallas       | 1587  |
| Austin       | 1584  |

<br>**Python script: ([1_job_locations.ipynb](https://github.com/vbalint14/END_TO_END_US_Analyst_Jobs_2023_Project/blob/main/jupyter_notebooks/1_job_locations.ipynb))**
```python
import pandas as pd
# Load the cleaned jobs data
df = pd.read_csv('data_jobs_cleaned.csv')
# Filter out rows where the job location is 'Anywhere' or 'United States'
df = df[(df['job_location'] != 'Anywhere') & (df['job_location'] != 'United States')]
# Clean the 'job_location' column to keep only the first part before the comma and strip any whitespace
df['job_location'] = df['job_location'].str.split(',', expand=True)[0].str.strip()
# Display the cleaned dataframe
df
# Summarize the frequency of job locations for 'Data Analyst' job postings in the US
df_us_job_counts = (df[(df['job_title_short'] == 'Data Analyst') & (df['job_country'] == 'United States')]
                    .groupby('job_location')
                    .size()
                    .sort_values(ascending=False)
                    .head(5)
                   )
# Reset the index of the job counts dataframe and rename the size column to 'count'
df_us_job_counts = df_us_job_counts.reset_index(name='count')
# Display the job counts dataframe
df_us_job_counts
# Save the filtered dataframe to a CSV file
df_us_job_counts.to_csv('1_data_jobs_location_counts.csv', index=False)



```
## 2. Annual analyst salaries
This analysis focuses on annual salaries comparing three major analyst roles: BI analyst, Data Analyst and Senior Data Analyst. <br>
**Content of [2_analyst_salaries.csv](https://github.com/vbalint14/END_TO_END_US_Analyst_Jobs_2023_Project/blob/main/csv_files/2_analyst_salaries.csv):**
| Job Title       | Job Location | Mean   | Min   | Max    |
|-----------------|--------------|--------|-------|--------|
| Business Analyst| Atlanta      | 113731 | 60000 | 151950 |
| Business Analyst| Austin       | 113774 | 57500 | 138500 |
| Business Analyst| Chicago      | 114129 | 55000 | 118000 |
| Business Analyst| Dallas       | 113530 | 50000 | 131085 |
| Business Analyst| New York     | 114081 | 80000 | 142500 |
| Data Analyst    | Atlanta      | 114086 | 35000 | 245000 |
| Data Analyst    | Austin       | 114397 | 37500 | 375000 |
| Data Analyst    | Chicago      | 113733 | 36000 | 225000 |
| Data Analyst    | Dallas       | 113907 | 32500 | 185000 |
| Data Analyst    | New York     | 113188 | 42500 | 240000 |
| Senior Data Analyst| Atlanta  | 115048 | 64000 | 200000 |
| Senior Data Analyst| Austin   | 114620 | 90000 | 175000 |
| Senior Data Analyst| Chicago  | 114952 | 82000 | 162500 |
| Senior Data Analyst| Dallas   | 114844 | 71850 | 175000 |
| Senior Data Analyst| New York | 114934 | 60000 | 340000 |

<br>**Python script: ([2_da_median_salaries.ipynb](https://github.com/vbalint14/END_TO_END_US_Analyst_Jobs_2023_Project/blob/main/jupyter_notebooks/2_da_median_salaries.ipynb))**
```python
import pandas as pd
# Load the cleaned jobs data
df = pd.read_csv('data_jobs_cleaned.csv')
# Load the job counts data by location
df_us_job_counts = pd.read_csv('1_data_jobs_location_counts.csv')
# Clean the 'job_location' column to keep only the first part before the comma and strip any whitespace
df['job_location'] = df['job_location'].str.split(',', expand=True)[0].str.strip()
# Create a list of job locations from the job counts data
states_list = df_us_job_counts['job_location'].tolist()
# Filter and count the top 5 job locations in the US for 'Analyst' job titles
df_us_job_counts = (df[(df['job_title_short'].str.contains('Analyst')) & (df['job_country'] == 'United States')]
                    .groupby('job_location')
                    .size()
                    .sort_values(ascending=False)
                    .head(5)
                   )
# Reset the index of the job counts dataframe and rename the size column to 'count'
df_us_job_counts = df_us_job_counts.reset_index(name='count')
# Display the job counts dataframe
df_us_job_counts
# Filter the original dataframe for 'Analyst' jobs in the top states
df_us_da = df[(df['job_location'].isin(states_list)) & (df['job_title_short'].str.contains('Analyst'))]
# Display the counts of job locations
df_us_da['job_location'].value_counts()
# Display the counts of job titles
df_us_da['job_title_short'].value_counts()

import numpy as np
# Group the data by job title and location, and aggregate the average, minimum, and maximum salaries
da_jobs_grouped = df_us_da.groupby(['job_title_short', 'job_location'])['salary_year_avg'].agg(['mean', 'min', 'max'])
# Round up the mean salary values and convert all salary columns to integers
da_jobs_grouped['mean'] = np.ceil(da_jobs_grouped['mean']).astype(int)
da_jobs_grouped['min'] = da_jobs_grouped['min'].astype(int)
da_jobs_grouped['max'] = da_jobs_grouped['max'].astype(int)
# Display the grouped data
da_jobs_grouped
# Save the grouped data to a CSV file
da_jobs_grouped.to_csv('2_analyst_salaries.csv')
```
## 3. Job postings per Analyst roles
The third analysis highlights the division between the mentioned three main analyst roles on the job market: <br>
**Content of [3_da_job_postings.csv](https://github.com/vbalint14/END_TO_END_US_Analyst_Jobs_2023_Project/blob/main/csv_files/3_da_job_postings.csv):**
| Job Title          | Job Location | Count |
|--------------------|--------------|-------|
| Business Analyst   | Atlanta      | 298   |
| Business Analyst   | Austin       | 196   |
| Business Analyst   | Chicago      | 221   |
| Business Analyst   | Dallas       | 204   |
| Business Analyst   | New York     | 158   |
| Data Analyst       | Atlanta      | 1711  |
| Data Analyst       | Austin       | 1235  |
| Data Analyst       | Chicago      | 1442  |
| Data Analyst       | Dallas       | 1170  |
| Data Analyst       | New York     | 2170  |
| Senior Data Analyst| Atlanta      | 410   |
| Senior Data Analyst| Austin       | 167   |
| Senior Data Analyst| Chicago      | 324   |
| Senior Data Analyst| Dallas       | 235   |
| Senior Data Analyst| New York     | 389   |
**Python script**: []():
```python

```
## 4. Most required analyst skills
The goal of this analysis is to define the top 5 skills regarding all analyst roles per city. <br>
**Content of [4_da_top_skills.csv](https://github.com/vbalint14/END_TO_END_US_Analyst_Jobs_2023_Project/blob/main/csv_files/4_da_top_skills.csv):**
| Job Location | Job Skills | Count | Count Total | Percentage |
|--------------|------------|-------|-------------|------------|
| New York     | SQL        | 851   | 2717        | 31         |
| New York     | Excel      | 664   | 2717        | 24         |
| New York     | Tableau    | 585   | 2717        | 21         |
| New York     | Python     | 542   | 2717        | 19         |
| New York     | SAS        | 294   | 2717        | 10         |
| Atlanta      | SQL        | 1295  | 2419        | 53         |
| Atlanta      | Excel      | 930   | 2419        | 38         |
| Atlanta      | Tableau    | 750   | 2419        | 31         |
| Atlanta      | Python     | 653   | 2419        | 26         |
| Atlanta      | SAS        | 552   | 2419        | 22         |
| Chicago      | SQL        | 1552  | 1987        | 78         |
| Chicago      | Excel      | 1147  | 1987        | 57         |
| Chicago      | Python     | 916   | 1987        | 46         |
| Chicago      | Tableau    | 907   | 1987        | 45         |
| Chicago      | R          | 518   | 1987        | 26         |
| Dallas       | SQL        | 1101  | 1609        | 68         |
| Dallas       | Excel      | 873   | 1609        | 54         |
| Dallas       | Python     | 612   | 1609        | 38         |
| Dallas       | Tableau    | 610   | 1609        | 37         |
| Dallas       | SAS        | 420   | 1609        | 26         |
| Austin       | SQL        | 891   | 1598        | 55         |
| Austin       | Excel      | 638   | 1598        | 39         |
| Austin       | Tableau    | 487   | 1598        | 30         |
| Austin       | Python     | 427   | 1598        | 26         |
| Austin       | Power BI   | 326   | 1598        | 20         |
**Python script**: []():
```python

```
## 5. Count of analyst job postings throughout 2023
The final analysis of the project summarizes analyst job postings in the 5 cities and breaks them down to months. <br>
**The first 20 lines of [5_postings_months.csv](https://github.com/vbalint14/END_TO_END_US_Analyst_Jobs_2023_Project/blob/main/csv_files/5_postings_months.csv):**
| Index | Job Location | Job Posted Month | Count |
|-------|--------------|------------------|-------|
| 0     | Atlanta      | 1                | 495   |
| 1     | Atlanta      | 2                | 379   |
| 2     | Atlanta      | 3                | 413   |
| 3     | Atlanta      | 4                | 377   |
| 4     | Atlanta      | 5                | 363   |
| 5     | Atlanta      | 6                | 356   |
| 6     | Atlanta      | 7                | 368   |
| 7     | Atlanta      | 8                | 465   |
| 8     | Atlanta      | 9                | 386   |
| 9     | Atlanta      | 10               | 333   |
| 10    | Atlanta      | 11               | 255   |
| 11    | Atlanta      | 12               | 244   |
| 12    | Austin       | 1                | 320   |
| 13    | Austin       | 2                | 247   |
| 14    | Austin       | 3                | 276   |
| 15    | Austin       | 4                | 286   |
| 16    | Austin       | 5                | 226   |
| 17    | Austin       | 6                | 228   |
| 18    | Austin       | 7                | 189   |
| 19    | Austin       | 8                | 319   |

## The final dashboard
![image](https://github.com/user-attachments/assets/ba7756ea-71f8-4614-90c8-2196119fd67b)

