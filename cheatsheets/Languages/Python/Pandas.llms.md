# Pandas 

framework

python

data science

Pandas is a python library for data wrangling and data cleaning.

Basic concept  
Each observation is in its own row (and preferably has an index-value). Each feature/variable is in its own column and has a unique name.

Data Frames  

``` python
my_df = pd.DataFrame(data=numpy_array, index=[0,1,2], columns=["col1", "col2", "col3"])
```

## Data Analysis

| command | description |
|----|----|
| `df.shape` | returns number of rows and columns/shape of the dataframe |
| `len(df)` | returns the number of rows |
| `df.size` | returns the total number of cells, including missing values |
| `df.columns` | returns column names |
| `df.index` | returns row index labels |
| `df.dtypes` | returns the data type of each column |
| `df.info()` | prints a summary of columns, non-null counts, data types, and memory usage |
| `df.head()` | shows the first 5 rows |
| `df.tail()` | shows the last 5 rows |
| `df.describe()` | returns summary statistics for numeric columns by default |
| `df.describe(include='all')` | returns summary statistics for all column types |
| `df.count()` | counts non-missing values in each column |
| `df.isna().sum()` | counts missing values in each column |
| `df.duplicated().sum()` | counts duplicate rows, excluding the first occurrence |
| `df.nunique()` | counts distinct non-missing values in each column |
| `df['col1'].unique()` | returns unique values in a column, including missing values |
| `df['col1'].value_counts()` | counts occurrences of each value, excluding missing values |
| `df['col1'].value_counts(normalize=True)` | returns relative frequencies of non-missing values |
| `df['col1'].value_counts(dropna=False)` | counts occurrences of each value, including missing values |
| `df['col1'].sum()` | returns the sum of a numeric column |
| `df['col1'].mean()` | returns the mean of a numeric column |
| `df['col1'].median()` | returns the median of a numeric column |
| `df['col1'].min()` | returns the minimum value in a column |
| `df['col1'].max()` | returns the maximum value in a column |
| `df['col1'].std()` | returns the sample standard deviation of a numeric column |
| `df['col1'].quantile(0.25)` | returns the 25th percentile of a numeric column |
| `df.corr(numeric_only=True)` | returns pairwise Pearson correlations between numeric columns |
| `df.sort_values('col1', ascending=False)` | returns rows sorted by a column in descending order |
| `df.nlargest(5, 'col1')` | returns rows with the 5 largest values in a numeric column |

Numeric summary methods above skip missing values by default.

## Reshaping data

From multiple columns to two columns  

``` python
pd.melt(my_df)
```

Col1 has former column names, col2 has values

Spread rows into columns  

``` python
my_df.pivot(columns='col1', values=
'col2')
```

Col1’s values are used as column names, col2’s values are used in respective column’s values.

Append one dataframe to another  

``` python
pd.concat([my_df1, my_df2]) # below / vertically
pd.concat([my_df1, my_df2], axis=1) # to the right / horizontally
```

Reset the index to row numbers  

``` python
my_df.reset_index()
```

## Slice DataFrames

First and last rows  

``` python
my_df.head(n=10) 
my_df.tail(n=10)
```

Sample several rows  

``` python
my_df.sample(n=10)
```

Select specific rows  

``` python
my_df.iloc[10:20]
```

Select specific columns  

``` python
my_df["col1"]
my_df[["col1", "col2"]]
```

Select rows based on condition  

``` python
my_df[my_df['col1'] > 42]
```

Select rows based on a list of values in a column  

``` python
my_df[my_df['col1'].isin(['value1', 'value2'])]
```

Remove duplicate rows  

``` python
my_df.drop_duplicates()
```

Select rows excluding certain values in a column  

``` python
my_df[~my_df['col1'].isin(['value1', 'value2'])]
```

## Data Manipulation

Replace values in a column  

``` python
my_df['col1'] = my_df['col1'].replace(['old_value1', 'old_value2'], ['new_value1', 'new_value2'])
```

## Data Aggregation

Group by a column and calculate mean of another column  

``` python
my_df.groupby('col1')['col2'].mean()
```

## Data Visualization

Plot a histogram of a column  

``` python
my_df['col1'].plot.hist()
```

Plot a scatter plot of two columns  

``` python
my_df.plot.scatter(x='col1', y='col2')
```
