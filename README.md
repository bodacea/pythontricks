# pythontricks

All the python tricks I keep forgetting how to do (Gitbook is being migrated into this readme file)

If you want to contribute to this list (please do), send me a pull request or contact me [@bodacea](https://twitter.com/bodacea)

## Table of Contents

<!-- MarkdownTOC depth=4 -->

- [Pandas](#pandas)
    - [Read CSV Excel and databases](#readfiles)
    - [Handle strings](#handlestrings)
    - [Convert datatypes](#convertdatatypes)
    - [Summarise one or more columns](#summarisesinglecolumns)
    - [Summarise pairs (or more) of columns](#summarisemultiplecolumns)
    - [Reduce dimensions](#reducedimensions)
    - [Joins and merges](#joinsandmerges)
    - [Dates and times](#datesandtimes)
    - [Visualisations](#visualisations)
    - [Jupyter notebook settings](#jupytersettings)

- [Common APIs](#commonapis)

- [Other things](#otherthings)
    - [Lists](#lists)
    - [Json and Xml](#jsonandxml)
    - [Dicts, sets, arrays](#dictssetsarrays)

<!-- /MarkdownTOC -->

# Pandas

Create blank data frame
```
pd.DataFrame([], columns=[col1, col2])
```

Add row to data frame,. data row is a dict, with the same keys as the data frame column names
```
df = df.append(datarow, ignore_index=True)
```

```
df.transpose() # very useful for viewing big sets of columns
```

Apply a function to every column value
```
df[col2] = df.apply(lambda row: my_function(row[col1]), axis=1)
```

this one counts the commas in each cell
```
df[[col1]].applymap(lambda x: str.count(x, ','))[col1].value_counts()
```

Get statistics for numerical columns
```
df.describe()
```

List column values
```
df[col1].unique() #list all unique values in a column
df[col1].value_counts() # Get column value counts
pd.DataFrame({col1: df[col1].unique()}) # as dataframe
```

Filter data frame by list of possible column values
```
df[df[col].isin([val1, val2, val3, val4])]
df[df[col1].isnull()] #NaNs
df[(df[col1].isnull) & df[col2].isin([val1, val2])] #need brackets for ‘&’, ‘|' etc
df[~(df[col1] == ‘xxx’)] #how to do negatives
```

Sort by 1 or more columns
```
df.sort_values([col1, col2], ascending=[True, False])
```

Combine two dataframes by joining on one or more column values
```
df3 = pd.merge(df1, df2, left_on=[col1], right_on=[col2])
```

Combine two data frames by putting their columns side-by-side. NB need to have the same number of rows in each dataframe
```
df3 = pd.concat([df1, df2], axis=1)
df3 = pd.concat([df1, df2], axis=1, join_axes=[df1.index]) # force index to df1 values
```

divide one column by another
```
df[col1].divide(df[col2])
```

Create column from function of other columns (in this case, make struct)
```
df[col3] = list(zip(df[col1], df[col2]))
```

Process data frame row by row
```
for index, row in df.iterrows():
  print(‘{}’.format(row[col1]))
```


## Read and write files

Get list of files in a given directory
```
glob.glob(‘mydir1/mydir2/*.txt’)
```

### read from CSV
```
pd.read_csv(filename) # your basic workhorse
pd.read_csv(filename, header=None, names=[col1, col2, col3]) # set column names
pd.read_csv(filename, dtype={‘col1’:int, ‘col2’:float, ‘col3’:str}) #force types
```
NB forcing date time types is tricky; you might want to use astype() on a column after read_csv instead

Large integers can cause problems because they get read in as inexact. This can be especially annoying when you’re tracking money. To deal with this, convert columns to Decimal when you read them in:
```
from decimal import Decimal
df = pd.read_csv(filename, converters={‘col1':Decimal})
```

### Read from Excel
```
pd.read_excel(filename) # handles most excel types.  
pd.read_excel(filename, skiprows=1) #ignores first row of file
```

Read from more complex Excel
```
xl = pd.ExcelFile(filename)
xl.sheet_names
df = xl.parse(xl.sheet_names[1])
```
### Write to CSV
```
df.to_csv(filename, index=False)  #write columns only (ignore index column)
df.to_csv(filename, index=False, encoding='utf-8’) #you might need this...
```

### Read and write to databases

write a data frame to SQL
```
df.to_sql(tablename, engine, if_exists=‘replace’, index=False)
```

Get stuff from an RDS store
```
engine = rdsutils.start_rds_alc() # we write this: uses sqlalchemy to link to RDS
datatable = engine.execute('SELECT * FROM ' ‘“my table name"')
```

### Type conversions
```
df[col].astype(int)
df[col].astype(float).replace({ 0 : np.nan }) # String replace: nan instead of 0
```

### Creating a blank dataframe
```
headers = [head1, head2, etc]
df = pd.DataFrame([], columns=headers)
df = df.append(pd.DataFrame([val1, val2, etc], columns=headers))
```

### Load a bunch of files into one dataframe

```
filelist = glob.glob('../*.csv')
df = pd.DataFrame([])
df = df.append(newdata)
```


## Handle strings

Create a string with array values joined by ‘, ‘
```
', '.join(['a', 'b', 'c’]) 
```

Create array from string that has with commas in it
```
'a,b,c'.split(',’)
```


dataframe string handling
```
df[col1].str.lower() #lowercase this column
df[col1].str.startswith(‘xx’) #look for values starting with ‘xx’
df[col1].str.lower().str.startswith(‘xx’) #use str if you’re chaining string commands
df[col1].str.startswith(‘xx', na=False) # Output 'False’ if you encounter NaN. 
df[col1].str.contains(‘abc’) #Get all strings containing this substring
df[col1].str.contains(‘abc|def|ghi|abd’) #Get all strings containing any of these
df[col1].str.strip().str.titlecase() #clean ends, capitalise
df[col1].str.split(‘,').str[1] #get first thing after ‘,‘
df[col1].str.split(‘,').str[:2] #get first two things separated by ‘,'
```

Regular expressions: https://docs.python.org/2/library/re.html
```
df[col].str.replace('\W+', ‘') #clean out anything that isn’t alphanumeric
```

Print almost anything without hassle
```
print('{}'.format(mydatathing))
```

### Compare strings

* value: df[df[col] == value]
* everything in string before "/": df[col].str.split('/').str.get(0)

* rows with one of val1, val2 etc: df[df[col].isin([val1, val2, ''])]

* rows that don't start with val1, val2 etc: df[df[col].str[:4].isin([val1, val2, val3]) == False]

* non-null values: df[~df[col].isnull()]

* rows starting with value; ignoring NaNs: df[df[col].str.startswith(value, na=False)]

* df[df[col].str.contains(value)]
* df[col].astype('str').str.replace('[^0-9.]', '')
* df[col].replace('', np.NaN)
* df['state'].replace(['Illinois', 'Md.', 'Georgia'], ['IL', 'MD', 'GA'], inplace=True)


Count commas: df[[col]].applymap(lambda x: str.count(x, ','))[col].value_counts()

## Convert datatypes

Convert all or part of a data frame to a dictionary
```
df.to_dict() #all of the dataframe
df[col1].to_dict() # just one column
```

convert dataframe column to a list
```
df[col1].tolist()
```

convert array to dataframe
```
df = pd.DataFrame(myarray, columns=[‘col1’, ‘col2’])
```

convert dictionary to dataframe
```
df = pd.DataFrame.from_dict(dict1, orient='index')
```

Use a dataframe column as its index
```
df.set_index(col1, inplace=True)
df.index.name=None # do this, or the index will be annoyingly hierarchical
```

Convert dataframe index into a column
```
df[col1] = df.index.values
```

Rename dataframe columns
```
df.rename(columns={col1:newname1, col2:newname2}, inplace=True)
```

convert column type to string
```
df[‘col’] = df[‘col’].astype(str)
``` 
Other types include int, float, bool and 'datetime64[ns]’ (for conversion to date time). Bool converts from 0/1 values; you might have to clean your data column before trying it.  Converting to int is tricky if you have NaNs (Pandas can’t convert these to int). You might have to set a default value, or use float for this instead, e.g. 
```
df[col] = df[col].replace('', np.NaN)
df[col] = df[col].astype('float')
df[col] = df[col].replace(np.NaN, ‘')
```

Round a floating-point value to nearest integer
```
df[col].round(0)
```

Convert string to datetime
```
df['dtcol'] = pd.to_datetime(df[col1]) 
df['dtcol'] = pd.to_datetime(df[col1], errors=‘coerce’) # don’t crash on wrong format 
```

convert datetime column to EST timezone
```
df['dtcol'] = df['dtcol'].dt.tz_localize('UTC').dt.tz_convert('US/Eastern')
```

Get different pieces of datetime
```
df['dtcol'].apply(pd.datetools.normalize_date) #day-month-year
df['dtcol'].dt.year #can also use month, day, hour, dayofyear etc
```

Get different parts of a date range
```
df[‘age’] = df[date1] - df[date2] # get date range
df[‘age’] = df[‘age’].dt.days # get range in number of days
```

Clean up a phone number or dollar amount
```
df[col1].str.replace('[^0-9.]', ‘’) #keep digits and ‘.'
df[col1].str.replace('\D', ‘') # just keep digits
```

Convert column values to something different
```
df.loc[df[col] == 1, col] = ‘True’ # one value
df[col1].replace([‘a’,’b’,’c’], [‘x’,’y’,’z’]), inplace=True) # list of values
df[col1].replace(list(dict1.values()), list(dict1.keys()), inplace=True) #dict vals to keys
```

Clean up NaNs
```
df = df.fillna(‘') # Replaces NaNs
df[col].replace(np.NaN, ‘') # Same, but col and could use any values here
```

If you do get a hierarchical index, here’s how to access its values
```
df.index.get_level_values(0)
```


## Summarise single columns

List of all values: df[col].unique()

Count of all values: df[col].value_counts()

Sort by columns: df.sort_values([col1, col2])

Stats of numerical column: df[col].describe()


All the unique values in a column, in alphabetical order:

```
vals = df[col].value_counts(dropna=True).index.tolist()
vals.sort() # inplace sort
print('{}'.format(vals))
```

## Summarise multiple columns

### Create summary of all values in a column, indexed by column2

```
df = dfin[['col2', 'col', 'col3']].pivot_table(index='col2', columns='col', 
                                               aggfunc='count', fill_value=0)
df.reset_index().head()
```

```
df['total'] = df.sum(axis=1)
```

```
for col in df.columns:
    df[col] = df[col].astype(str)
```

```
df['col2'] = df.index.values
```

```
df['text'] = df['col2'] + '<br>Total: ' + df['total']
```

```
for status in df['col3'].columns:
    df['text'] += '<br>' + status + ': ' + df['col3'][status]
```

## Reduce dimensions

Drop *all* the duplicate rows (as in all copies of duplicates, including the originals) from a data frame: 
```
df[~df.duplicated(keep=False)]
```

Only keep the rows with duplicate values of (col1, col2)
```
df[df.duplicated(keep=False, subset=[col1, col2])]
```

Get all the combinations of a named set of columns 
```
df.drop_duplicates(subset=[col1, col2])
```
Do it, and reset the index to 0,1,2…n
```
df.drop_duplicates(subset=[col1, col2]).reset_index(drop=True)
```

Keep just the last row of each set of duplicate values
```
df.sort_values(col3).drop_duplicates(subset=[col1, col2], keep='last')
```

For a given combination of columns, sum numerical values in other columns
```
df.groupby([col1, col2], as_index=False).sum() # .count(), .mean() etc also work
```

Get array of value counts for a column
```
pd.DataFrame(df[col1].value_counts()).rename(columns={col1:'rows’}) #index; col for count
df[col1].value_counts().reset_index().sort_values('index’) #columns for value and count
```

Remove dataframe columns
```
df.drop(col1, axis=1, inplace=True) # one column
df.drop([col1, col2], axis=1, inplace=True) #multiple columns
```

show pairs of values 
```
pd.crosstab(df[col1], df[col2]) #as row-column table
df.drop_duplicates(subset=[col1, col2]).reset_index(drop=True) # as list
df[[col1, col2]].groupby([col1, col2]).count() # as hierarchical table
```

Pivot: show counts of value combinations as a table
NB if you’re showing 2 columns, you need a 3rd column to count values in; you might want to add a dummy column for this, e.g. df[‘dummy’] = 1 
```
df[[col1, col2, col3]].pivot_table(index=col1, columns=col2, aggfunc='count', fill_value=0) # can also use aggfunc=‘sum’, aggfunc=np.mean
```

Pivot: show counts of value combinations, as a row-column table
```
pd.pivot_table(df, values=col1, index=[col2], columns=[col3]).reset_index()
```

Pivot with a non-standard aggregation function
```
pd.pivot_table(df, index=[col1, col2], values=[col3], aggfunc=lambda x: len(x.unique()))
```

Aggregation function that produces an array - how to get at those values
```
pd.pivot_table(df, index=[col1, col2], values=[col3], aggfunc=lambda x: x[col3].describe().values[1])
```

sum row values
```
df.sum(axis=1)
```


## Joins and merges
```
pd.merge(df1, df2, how='left',  left_on=[col1, col2], right_on=[col1, col2])
```

Compare two columns of values: 
```
pd.crosstab(df[col1], df[col2])
```

## Dates and times

```
df[col] = pd.to_datetime(df[col])
df[col].dt.month # 1 to 12
df[col].dt.quarter
df[col].dt.dayofyear
```

Number of days between dates:  
```
df[lag] = df[col1] - df[col2]; df[lag].dt.days
```

Convert string to time:
```
import datetime
datetime.datetime.strptime(date_text, '%Y-%m-%dT%H:%M:%S.%f')
```

Get timezone
```
import pytz
timezone = pytz.timezone("America/New_York”)
```

Get filename date time
```
import time
import os
from datetime import datetime
tstring = time.ctime(os.path.getmtime(filename))
timezone.localize(datetime.strptime(tstring, '%a %b %d %H:%M:%S %Y'))
```

Get minimum time value
```
timezone.localize(datetime.datetime(1970, 1, 1, 0, 0))
```

Get today’s date
```
import datetime
datetoday = datetime.datetime.now().strftime('%Y-%m-%d')
```

Convert month number 0…12 to month name
```
monthname = calendar.month_name[monthnum]
```

Use a time offset
```
import datetime
df[col1] - datetime.timedelta(days=28)
```

Get month-end for each date (remove ‘M’ to get month-start
```
df[‘month end’] = df[‘date’].dt.to_period('M').dt.to_timestamp('M’)
df[‘month end’] = df[‘date’].dt.to_period('M’) # just get the month
```

## Visualisations

Save a plot from pylab
```
from pylab import savefig
savefig('aaa.jpg’) #NB the file extension you use sets the output format
```

Plot a histogram
```
df[col1].hist(bins=50)
plt.suptitle(‘my title')
ax.set_xlabel(‘my xlabel')
ax.set_ylabel(‘my ylabel')
```

Plot a histogram with data weighted by a second column value
```
hist, bins = np.histogram(df[col1], weights=df[col2])
width = 0.7 * (bins[1] - bins[0])
center = (bins[:-1] + bins[1:]) / 2
plt.bar(center, hist, align='center', width=width)
```

just get the histogram (not plotting)
```
heights, values = np.histogram(df[col1].dropna())
```

boxplots: use seaborn
```
import seaborn as sns
sns.set_style('white’) # make it pretty
sns.set(style="whitegrid", palette='pastel', color_codes=True) # prettier
snsplot = sns.boxplot(df[col1], df[col2])
```

Simple lineplot
```
df[col1].plot(title=‘my title’)
```

Lineplot: the pretty Seaborn version
```
g = sns.FacetGrid(df, hue=col3, size=15, aspect=0.7)
snsplot = g.map(plt.plot, col1, col2)
_x = g.ax.set(xlabel=‘my label', ylabel=‘my ylabel’, title=‘my title’)
```

save Seaborn plot to a file
```
snsplot.savefig(‘my filename.png’) 
```

Add text anywhere in a Seaborn plot
```
plt.text(col1val, col2val, 'my text')
```

Simple barchart
```
df[col].plot.bar(color=‘b’, title=‘my title') # color = r, g, b, y etc all work
plt.bar(df[col1], df[col2]) # using matplotlib; col1 is x axis, col2 is y axis
```

Get stacked barchart; columns = colors, index=axis labels
```
df.plot.bar(stacked=True)
```

change plot labels etc on standard plots
```
ax = df[col1].plot.bar()
ax = ax.set_yticklabels(df[col2])
```

change plot labels etc on seaborn plots
```
import seaborn as sns
import matplotlib.pyplot as plt
plt.figure(0)
ax = plt.axes()
sns.boxplot(df[col1], df[col2])
ax.set_title(‘my title’)
```

Get colored scatterplot
```
ax.scatter(df[col1], df[col2], c=df[col3])
```

Use seaborn for regression plots
```
sns.regplot(data=df, x=col1, y=col2)
```

Using Plotly?  Make sure you set your visualizations to local-only… 
```
import plotly
plotly.tools.set_config_file(world_readable=False, sharing='private')
```

Create multiple Matplotlib figures together
```
plt.figure()
ax = plt.subplot(3, 1, 1) #3 rows, 1 column, first plot
df[col1].hist(bins=50)
ax = plt.subplot(3, 1, 2)
df[col2].hist(bins=50)
ax = plt.subplot(3, 1, 3)
df[col3].hist(bins=50)
```

Create multiple Matplotlib figures without counting…
```
for index in range(0, numplots):
  plt.figure(index)
  df[col].plot.bar()
  ```

Gui widgets: use ipywidgets
```
from ipywidgets import widgets
from IPython.display import display
```

Rotate your matplotlib axis labels
```
df.plot()
plt.xticks(rotation=90)
```

Violin plot? Use Seaborn 
```
sns.violinplot(data=df, x=col1, y=col2, hue=col3, split=True, 
                      palette={col3val1:'g', col3val2:'r'}, size=10, aspect=0.5)
```

Put a matplotlib legend wherever you want it
```
ax = plt.subplot(111)
box = ax.get_position()
ax.set_position([box.x0, box.y0, box.width * 0.8, box.height])
ax.legend(loc='center left', bbox_to_anchor=(1, 0.5))
```


```
df[col].hist(bins=100)
df.plot.bar(stacked=True)
sns.boxplot(df[col1], df[col2]) # col1 = xaxis; col2 = yaxis
plt.figure(figsize=(10,10))
```

### Subplots
```
ax = plt.subplot(3, 1, 1)
df[col1].hist(bins=50)
ax = plt.subplot(3, 1, 2)
df[col2].hist(bins=50)
ax = plt.subplot(3, 1, 3)
plt.scatter(df[col1], df[col2])
```

### Axis labels

```
fig = plt.figure()
ax = fig.add_subplot(1,1,1)
ax.scatter(df[col1], df[col2], c=df[col3])
ax.set_xlabel(label1)
ax.set_ylabel(label2)
#ax.legend()
```

### Using Plotly

```
import plotly
plotly.tools.set_config_file(world_readable=False, sharing='private')
```

### Save to file/web

```
df.to_html()
```

### Choropleth (in new page)

```
import matplotlib.pyplot as plt
%matplotlib inline
import plotly

plotly.tools.set_config_file(world_readable=False, sharing='private')

scl = [[0.0, 'rgb(242,240,247)'],[0.2, 'rgb(218,218,235)'],[0.4, 'rgb(188,189,220)'],\
            [0.6, 'rgb(158,154,200)'],[0.8, 'rgb(117,107,177)'],[1.0, 'rgb(84,39,143)']]

data = [ dict(
        type='choropleth',
        colorscale = scl,
        autocolorscale = False,
        locations = dfbystate['state'],
        z = dfbystate['borrowers'].astype(float),
        locationmode = 'USA-states',
        text = dfbystate['text'],
        marker = dict(
            line = dict (
                color = 'rgb(255,255,255)',
                width = 2
            ) ),
        colorbar = dict(
            title = 'Number of borrowers')
        ) ]

layout = dict(
        title = 'Borrowers&lt;br&gt;(Hover for breakdown)',
        geo = dict(
            scope='usa',
            projection=dict( type='albers usa' ),
            showlakes = True,
            lakecolor = 'rgb(255, 255, 255)'),
             )

fig = dict( data=data, layout=layout )
plotly.offline.plot( fig, filename='../../data/borrowers_map.html' )
```


## Jupyter settings

Change numbers of rows and columns displayed
```
pd.set_option('display.max_columns', 1000)
pd.set_option('display.max_rows', 1000)
```

Set pandas output to show whole strings, not just starts of strings
```
pd.set_option('display.max_colwidth', None)
```

Reload a library in Python3.4 onwards:
```
import importlib
solveutils = importlib.reload(solveutils)
```

import from a different directory
```
import sys
sys.path.insert(0, "../libraries")
import solveutils
```

Embed plots in the notebook, instead of having to call show() to get a popup
```
%matplotlib inline
```

Run both Python2 and Python3:
* https://medium.com/towards-data-science/environment-management-with-conda-python-2-3-b9961a8a5097
    * conda install nb_conda_kernels
    * conda create -n py27 python=2.7 ipykernel
    * source activate py27
    * source deactivate


# Common APIs

## Box

```
from boxsdk import Client, OAuth2
oauth = OAuth2(client_id=client_id, client_secret=client_secret, access_token=developer_token)
client = Client(oauth)
estream = client.events()
events = estream.get_events()
```


# Other things

One-line if statement
```
myarray = [myjson[mykey]] if mykey in myjson.keys() else [‘']
```

loop through json keys
```
for mykey, mydata in jdata.items():
```

Key not in list
```
if (mykey not in myjson):
```

User input
```
myvar = input(“my friendly text> “)
```

## Read/Write raw CSV

```
import csv

fin = open(infilename, 'r')
fout = open(outfilename, 'w', newline='')
cin = csv.reader(fin)
cout = csv.writer(fout, quoting=csv.QUOTE_NONNUMERIC)
```

## Lists

Converting lists to and from strings (useful for e.g. SQL calls):
```
','.join[val1, val2, etc]
string.split(',')
```

Remove key-value from list: 
```
value = list.pop(key)
```

### DefaultDicts

Fills in the 'default' value every time you access a dict key that doesn't exist yet. Genius.

```
from collections import defaultdict
```


## Json and xml

Write json file:

```
import json
fout = open(filename, 'w')
json.dump(dataset, fout)
```

Get json back from string containing json
```
import json
jdata = json.loads(jsonstring)
```

If the format's a bit hinky (e.g. array of json objects), try pickle...

```
import pickle 
fout = open(filename, 'wb')
pickle.dump(dataset, fout) # get back with pickle.load()
fout.close()
```

Get json file back from pickle file
```
fin = open(jfile, ‘rb')
jdata = pickle.load(fin)
```

### Prettyprint json

SJT note: ungodly hack. There's also a single command for this. Will find in notes. 

```
def printkids(heading, parent):
    for kid in parent.keys(): 
        if type(parent[kid]) == dict:
            print('{} {}'.format(heading, kid))
            printkids(heading + '--', parent[kid])
        else:
            print('{} {}: {}'.format(heading, kid, parent[kid]))
    return
```

## Dicts sets arrays

mydict.keys() is *not* a list. If you want a list, do this
```
list(mydict.keys())
```

combine two dicts together
```
newdict = dict1.update(dict2)
```

do something to every item in an array
```
[x*2 for x in myarray]
```

sets
```
set(['a', 'b', 'c', 'a']) # create set
set(['a', 'b', 'c', 'a'])  - set(['b','d']) #everything in first but not second
```
other set functions include & (intersection, or), | (union, and), ^ (nor, not or)

combine two arrays
```
arr3 = arr1 + arr2
```

Remove key-value pair from dict
```
value1 = mydict.pop(key1)
```

loop over array index and values
```
for index, value in enumerate(myarray):
```

Loop over dict key-value pairs
```
for key, val in mydict.items():
```


# Notes

Note for Sara: (checking through old code: have got to but not started on file_ingestions_analysis in work codebase). 
