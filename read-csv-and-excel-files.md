# Reading into pandas dataframes

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



