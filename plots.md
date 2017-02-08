# Plots

df\[col\].hist\(bins=100\)

df.plot.bar\(stacked=True\)

sns.boxplot\(df\[col1\], df\[col2\]\) \# col1 = xaxis; col2 = yaxis

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
