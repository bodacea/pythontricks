# Summarise pairs \(or more\) of columns

### Create summary of all values in a column, indexed by column2

df = dfin\[\['col2', 'col', 'col3'\]\].pivot\_table\(

    index='col2', columns='col', aggfunc='count', fill\_value=0\)

df.reset\_index\(\).head\(\)

df\['total'\] = df.sum\(axis=1\)

for col in df.columns:

    df\[col\] = df\[col\].astype\(str\)

df\['col2'\] = df.index.values

df\['text'\] = df\['col2'\] + '&lt;br&gt;Total: ' + df\['total'\]

for status in df\['col3'\].columns:

    df\['text'\] += '&lt;br&gt;' + status + ': ' + df\['col3'\]\[status\]



