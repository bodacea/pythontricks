# Reading into pandas dataframes

Read excel without the top blank row:  df = pd.read\_excel\(filename, skiprows=1\)

### Type conversions

df\[col\].astype\(int\)

df\[col\].astype\(float\).replace\({ 0 : np.nan }\) \# String replace: nan instead of 0



### Creating a blank dataframe

headers = \[head1, head2, etc\]

df = pd.DataFrame\(\[\], columns=headers\)

df = df.append\(pd.DataFrame\(\[val1, val2, etc\], columns=headers\)\)



