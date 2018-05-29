# pythontricks

All the python tricks I keep forgetting how to do (Gitbook is being migrated into this readme file)

If you want to contribute to this list (please do), send me a pull request or contact me [@bodacea](https://twitter.com/bodacea)

## Table of Contents

<!-- MarkdownTOC depth=4 -->

- [Pandas](#pandas)
    - [Read CSV and Excel files](#readfiles)
    - [Compare Strings](#comparestrings)
    - [Summarise one or more columns](#summarisesinglecolumns)
    - [Summarise pairs (or more) of columns](#summarisemultiplecolumns)
    - [Joins and merges](#joinsandmerges)
    - [Dates and times](#datesandtimes)
    - [Plots](#plots)
    - [Jupyter notebook settings](#jupytersettings)

- [Common APIs](#commonapis)

- [Other things](#otherthings)
    - [Lists](#lists)
    = [Json and Xml](#jsonandxml)

<!-- /MarkdownTOC -->

# Pandas

## Read files

Read excel without the top blank row:  df = pd.read\_excel\(filename, skiprows=1\)

### Type conversions

df\[col\].astype\(int\)

df\[col\].astype\(float\).replace\({ 0 : np.nan }\) \# String replace: nan instead of 0

### Creating a blank dataframe

headers = \[head1, head2, etc\]

df = pd.DataFrame\(\[\], columns=headers\)

df = df.append\(pd.DataFrame\(\[val1, val2, etc\], columns=headers\)\)

### Check a bunch of files

filelist = glob.glob\('../\*.csv'\)

df = pd.DataFrame\(\[\]\)

df = df.append\(newdata\)

### Read/Write raw CSV

import csv

```
fin = open\(infilename, 'r'\)

fout = open\(outfilename, 'w', newline=''\)

cin = csv.reader\(fin\)

cout = csv.writer\(fout, quoting=csv.QUOTE\_NONNUMERIC\)
```



## Compare strings

* value: df\[df\[col\] == value\]
* everything in string before "/": df\[col\].str.split\('/'\).str.get\(0\)

* rows with one of val1, val2 etc: df\[df\[col\].isin\(\[val1, val2, ''\]\)\]

* rows that don't start with val1, val2 etc: df\[df\[col\].str\[:4\].isin\(\[val1, val2, val3\]\) == False\]

* non-null values: df\[~df\[col\].isnull\(\)\]

* rows starting with value; ignoring NaNs: df\[df\[col\].str.startswith\(value, na=False\)\]

* df\[df\[col\].str.contains\(value\)\]
* df\[col\].astype\('str'\).str.replace\('\[^0-9.\]', ''\)
* df\[col\].replace\('', np.NaN\)
* df\['state'\].replace\(\['Illinois', 'Md.', 'Georgia'\], \['IL', 'MD', 'GA'\], inplace=True\)



Count commas: df\[\[col\]\].applymap\(lambda x: str.count\(x, ','\)\)\[col\].value\_counts\(\)



## Summarise single columns


List of all values: df\[col\].unique\(\)

Count of all values: df\[col\].value\_counts\(\)

Sort by columns: df.sort\_values\(\[col1, col2\]\)

Stats of numerical column: df\[col\].describe\(\)



All the unique values in a column, in alphabetical order:

vals = df\[col\].value\_counts\(dropna=True\).index.tolist\(\)

vals.sort\(\) \# inplace sort

print\('{}'.format\(vals\)\)


## Summarise multiple columns

### Create summary of all values in a column, indexed by column2

`df = dfin[['col2', 'col', 'col3']].pivot_table(`

```
index='col2', columns='col', aggfunc='count', fill_value=0)
```

`df.reset_index().head()`

`df['total'] = df.sum(axis=1)`

`for col in df.columns:`

```
df[col] = df[col].astype(str)
```

`df['col2'] = df.index.values`

`df['text'] = df['col2'] + '<br>Total: ' + df['total']`

`for status in df['col3'].columns:`

```
df['text'] += '<br>' + status + ': ' + df['col3'][status]
```


## Joins and merges

pd.merge\(df1, df2, how='left',  left\_on=\[col1, col2\], right\_on=\[col1, col2\]\)

Compare two columns of values: pd.crosstab\(df\[col1\], df\[col2\]\)


## Dates and times

df\[col\] = pd.to\_datetime\(df\[col\]\)

df\[col\].dt.month \# 1 to 12

df\[col\].dt.quarter

df\[col\].dt.dayofyear

Number of days between dates:  df\[lag\] = df\[col1\] - df\[col2\]; df\[lag\].dt.days

```

import datetime

datetime.datetime.strptime\(date\_text, '%Y-%m-%dT%H:%M:%S.%f'\)
```


## Plots


df\[col\].hist\(bins=100\)

df.plot.bar\(stacked=True\)

sns.boxplot\(df\[col1\], df\[col2\]\) \# col1 = xaxis; col2 = yaxis

plt.figure\(figsize=\(10,10\)\)

### Subplots

ax = plt.subplot\(3, 1, 1\)

df\[col1\].hist\(bins=50\)

ax = plt.subplot\(3, 1, 2\)

df\[col2\].hist\(bins=50\)

ax = plt.subplot\(3, 1, 3\)

plt.scatter\(df\[col1\], df\[col2\]\)

### Axis labels

fig = plt.figure\(\)

ax = fig.add\_subplot\(1,1,1\)

ax.scatter\(df\[col1\], df\[col2\], c=df\[col3\]\)

ax.set\_xlabel\(label1\)

ax.set\_ylabel\(label2\)

\#ax.legend\(\)

### Using Plotly

import plotly

plotly.tools.set\_config\_file\(world\_readable=False, sharing='private'\)

### Save to file/web

df.to\_html\(\)

### Choropleth \(in new page\)

import matplotlib.pyplot as plt

%matplotlib inline

import plotly

plotly.tools.set\_config\_file\(world\_readable=False, sharing='private'\)

scl = \[\[0.0, 'rgb\(242,240,247\)'\],\[0.2, 'rgb\(218,218,235\)'\],\[0.4, 'rgb\(188,189,220\)'\],\

            \[0.6, 'rgb\(158,154,200\)'\],\[0.8, 'rgb\(117,107,177\)'\],\[1.0, 'rgb\(84,39,143\)'\]\]

data = \[ dict\(

        type='choropleth',

        colorscale = scl,

        autocolorscale = False,

        locations = dfbystate\['state'\],

        z = dfbystate\['borrowers'\].astype\(float\),

        locationmode = 'USA-states',

        text = dfbystate\['text'\],

        marker = dict\(

            line = dict \(

                color = 'rgb\(255,255,255\)',

                width = 2

            \) \),

        colorbar = dict\(

            title = 'Number of borrowers'\)

        \) \]



layout = dict\(

        title = 'Borrowers&lt;br&gt;\(Hover for breakdown\)',

        geo = dict\(

            scope='usa',

            projection=dict\( type='albers usa' \),

            showlakes = True,

            lakecolor = 'rgb\(255, 255, 255\)'\),

             \)

    

fig = dict\( data=data, layout=layout \)

plotly.offline.plot\( fig, filename='../../data/borrowers\_map.html' \)



## Jupyter settings

See \*all\* the columns in a dataframe:

See plots on the page \(not as popups\): %matplotlib inline

See the whole string in each dataframe: pd.set\_option\('display.max\_colwidth', -1\)



# Common APIs

## Box

> `from boxsdk import Client, OAuth2`
>
> `oauth = OAuth2(client_id=client_id, client_secret=client_secret, access_token=developer_token)`
>
> `client = Client(oauth)`
>
> `estream = client.events()`
>
> `events = estream.get_events()`



# Other things

## Lists

','.join\[val1, val2, etc\]

string.split\(','\)

Remove key-value from list: value = list.pop\(key\)

### DefaultDicts

Fills in the 'default' value every time you access a dict key that doesn't exist yet. Genius.

from collections import defaultdict



## Json and xml

### Write json file \(1\)

import json

fout = open\(filename, 'w'\)

json.dump\(dataset, fout\)

### Write json file \(2\)

If the format's a bit hinky \(e.g. array of json objects\), try pickle...

import pickle 

fout = open\(filename, 'wb'\)

pickle.dump\(dataset, fout\) \# get back with pickle.load\(\)

fout.close\(\)

### Prettyprint json

def printkids\(heading, parent\):

```
for kid in parent.keys\(\): 

    if type\(parent\[kid\]\) == dict:

        print\('{} {}'.format\(heading, kid\)\)

        printkids\(heading + '--', parent\[kid\]\)

    else:

        print\('{} {}: {}'.format\(heading, kid, parent\[kid\]\)\)

return
```


# Notes

Note for Sara: \(checking through old code: have got to but not started on file_ingestions_analysis in work codebase\). 
