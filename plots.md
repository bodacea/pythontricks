# Plots

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

