# Marital_Stat
Based on World Marriage Data 2017 from UN, demonstrate the rich functionalities of Python in Data Science, especially the core libraries, Numpy, Pandas, Matplotlib. 

## Three stages of processing the data
- Convert data source format from xlsx to csv.
- Use Pandas, numpy to manipulate the data. 
- Apply matplotlib to plot different kinds of charts, including line chart, bar chart, pie chart.

First, let us see the results first.
- Using line charts to demonstrate the marital status changes in US and Japan between 1970 and 2010.
- Using bar charts to demonstrate the marital status comparison between genders in US and Japan.
- Using pie charts to demonstrate the comparison among different marital status in US and Japan.
- Using line charts to demonstrate the marital status changes for 7 courtries (US, UK, France, South Africa, China, Japan, South Korea), from 1970 to 2010.

Next, let us see how to break down and achieve the goals.

## Convert data source format from xlsx to csv
The [original data](https://www.un.org/en/development/desa/population/theme/marriage-unions/WMD2017.asp) is in xlsx format, which contains over 250,000 rows and has the file size as 24.7MB. Although [openpyxl](https://openpyxl.readthedocs.io/en/stable/) and pandas' support for excel - [read_excel](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_excel.html) - are pretty handy, the speed of reading/writing big excel file is not very satisfactory. As pandas' support for csv has good performance in handling big file, my practice is to convert the big excel to a csv file. 

```python
csv_src = pd.read_csv(r'./UNPD_WMD_2017_MARITAL_STATUS.csv', parse_dates = ['YearStart', 'YearEnd'], dtype = {'Note on Data':str})
```

To quicken the way to reading/writing the data from csv, I did the two things:
- Explicitly parse the date fields, `parse_dates = ['YearStart', 'YearEnd']`.
- Advocate some fields whose types could be ambigious recognized (like '1' can be a int type, float type or string type), `dtype = {'Note on Data':str}`.

To peek the first 5 rows of the data:
```python
pd_src_excel.iloc[:,0:12].head()
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Country or area</th>
      <th>ISO code</th>
      <th>YearStart</th>
      <th>YearEnd</th>
      <th>Sex</th>
      <th>MaritalStatus</th>
      <th>Non-standard_AgeGroups</th>
      <th>Series_contains_Non-standard_AgeGroups</th>
      <th>AgeGroup</th>
      <th>AgeStart</th>
      <th>AgeEnd</th>
      <th>DataValue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Afghanistan</td>
      <td>4</td>
      <td>1972</td>
      <td>1974</td>
      <td>Men</td>
      <td>Divorced</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>[15-19]</td>
      <td>15</td>
      <td>19</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Afghanistan</td>
      <td>4</td>
      <td>1972</td>
      <td>1974</td>
      <td>Men</td>
      <td>Divorced</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>[20-24]</td>
      <td>20</td>
      <td>24</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Afghanistan</td>
      <td>4</td>
      <td>1972</td>
      <td>1974</td>
      <td>Men</td>
      <td>Divorced</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>[25-29]</td>
      <td>25</td>
      <td>29</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Afghanistan</td>
      <td>4</td>
      <td>1972</td>
      <td>1974</td>
      <td>Men</td>
      <td>Divorced</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>[30-34]</td>
      <td>30</td>
      <td>34</td>
      <td>0.2</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Afghanistan</td>
      <td>4</td>
      <td>1972</td>
      <td>1974</td>
      <td>Men</td>
      <td>Divorced</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>[35-39]</td>
      <td>35</td>
      <td>39</td>
      <td>0.1</td>
    </tr>
  </tbody>
</table>
</div>

Here we only need to notice the columns of *ISO code*, *YearStart*, *Sex*, *MarritalStatus*, *AgeGroup*, *DataValue*.
 
## Use Pandas, numpy to manipulate the data
The body is to manipulate the data as we intend. As Numpy, Pandas are so powerful, so knowing the exact directions before diving in it is a more effective way to learn them.

To achieve the goals:
- Using line charts to demonstrate the marital status changes in US and Japan between 1970 and 2010.
- Using bar charts to demonstrate the marital status comparison between genders in US and Japan.
- Using pie charts to demonstrate the comparison among different marital status in US and Japan.
- Using line charts to demonstrate the marital status changes for 7 courtries (US, UK, France, South Africa, China, Japan, South Korea), from 1970 to 2010.

I need to be have dataframes can be filtered by:
- countries
- gender
- years
- age groups

As I have SQL basics, it would be convenient to adopt the concept of SQL to analyse the data, but Python is a much more powerful and complicated language than SQL. How can we manipulate the tabular data in Python as handily as in SQL? [A good sumary](https://pandas.pydata.org/pandas-docs/stable/getting_started/comparison/comparison_with_sql.html) about comparison between Pandas and SQL inspired me, and I also extended the content with my practice.

Example 1. Want to fetch the marital data in 1980 for American male who are married and aging between 25-29.

```Python
men_usa = csv_src[ (csv_src.AgeGroup.isin(age_group_target))
        & (csv_src['ISO code']==840)    # ISO code of US
        & (csv_src['Sex']=='Men')   # filter by gender
        & (csv_src['AgeGroup']=='[25-29]')  #filtered by age group
        & (csv_src['MaritalStatus']=='Married')]    # filtered by marital status
        .iloc[:,0:12].drop(['Non-standard_AgeGroups','Series_contains_Non-standard_AgeGroups'], axis=1) # trim the shown columns
```
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Country or area</th>
      <th>ISO code</th>
      <th>YearStart</th>
      <th>YearEnd</th>
      <th>Sex</th>
      <th>MaritalStatus</th>
      <th>AgeGroup</th>
      <th>AgeStart</th>
      <th>AgeEnd</th>
      <th>DataValue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>239892</td>
      <td>United States of America</td>
      <td>840</td>
      <td>1970-01-01</td>
      <td>1970-01-01</td>
      <td>Men</td>
      <td>Married</td>
      <td>[25-29]</td>
      <td>25</td>
      <td>29</td>
      <td>75.24</td>
    </tr>
    <tr>
      <td>240029</td>
      <td>United States of America</td>
      <td>840</td>
      <td>1971-01-01</td>
      <td>1971-01-01</td>
      <td>Men</td>
      <td>Married</td>
      <td>[25-29]</td>
      <td>25</td>
      <td>29</td>
      <td>73.32</td>
    </tr>
    <tr>
      <td>240139</td>
      <td>United States of America</td>
      <td>840</td>
      <td>1972-01-01</td>
      <td>1972-01-01</td>
      <td>Men</td>
      <td>Married</td>
      <td>[25-29]</td>
      <td>25</td>
      <td>29</td>
      <td>76.07</td>
    </tr>
    <tr>
      <td>240249</td>
      <td>United States of America</td>
      <td>840</td>
      <td>1973-01-01</td>
      <td>1973-01-01</td>
      <td>Men</td>
      <td>Married</td>
      <td>[25-29]</td>
      <td>25</td>
      <td>29</td>
      <td>73.14</td>
    </tr>
    <tr>
      <td>240359</td>
      <td>United States of America</td>
      <td>840</td>
      <td>1974-01-01</td>
      <td>1974-01-01</td>
      <td>Men</td>
      <td>Married</td>
      <td>[25-29]</td>
      <td>25</td>
      <td>29</td>
      <td>71.32</td>
    </tr>
  </tbody>
</table>
</div>

If using SQL, I will query the database by such a statement:
```SQL
select `Country or area`, `ISO code`, YearStart, YearEnd, Sex, MaritalStatus, AgeGroup, AgeStart, AgeEnd, DataValue
 from Marital_Status_Stat_UN 
    where ISO_CODE='840' AND
        Sex='Men' AND
        AgeGroup='[25-29]' AND
        MaritalStatus='Married'
        LIMIT 5;
```

More examples can be found in the [article](https://pandas.pydata.org/pandas-docs/stable/getting_started/comparison/comparison_with_sql.html)

## Apply matplotlib to plot different kinds of charts

After manipulation in above stage, the data would be ready to be shown in fancy graphs. Matplotlib is really powerful, but here we only make use of a small portion of its features.

To demonstrate the married percentage comparison among different age groups and genders in USA, we can use bar charts.

**One of the core concepts using matplotlib is to recognize what is the x and y in the chart**. Here we know different age groups should be as x-axis indices, and y-axis data should be percetanges. So we will have:


Note that `plt.rcParams["figure.figsize"] = (20,3)` changes the figure size, here I wanted to have better effect to demonstrate the comparison.

To demonstrate the complete marital status comparison between age groups, pie chart is a good choice. As pie charts only need one-dimentional data, here we feed percentages in different status to the charts.
