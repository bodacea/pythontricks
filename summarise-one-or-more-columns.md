# Summarise one or more columns

List of all values: df\[col\].unique\(\)

Count of all values: df\[col\].value\_counts\(\)

Sort by columns: df.sort\_values\(\[col1, col2\]\)

Stats of numerical column: df\[col\].describe\(\)



All the unique values in a column, in alphabetical order: 

vals = df\[col\].value\_counts\(dropna=True\).index.tolist\(\)

vals.sort\(\) \# inplace sort

print\('{}'.format\(vals\)\)





