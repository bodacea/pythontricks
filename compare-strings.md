# Compare Strings in columns

* value: df\[df\[col\] == value\]
* everything in string before "/": df\[col\].str.split\('/'\).str.get\(0\)

* rows with one of val1, val2 etc: df\[df\[col\].isin\(\[val1, val2, ''\]\)\]

* rows that don't start with val1, val2 etc: df\[df\[col\].str\[:4\].isin\(\[val1, val2, val3\]\) == False\]

* non-null values: df\[~df\[col\].isnull\(\)\]

* rows starting with value; ignoring NaNs: df\[df\[col\].str.startswith\(value, na=False\)\]
* df\[df\[col\].str.contains\(value\)\]
* df\[col\].astype\('str'\).str.replace\('\[^0-9.\]', ''\)
* df\[col\].replace\('', np.NaN\)
* 


