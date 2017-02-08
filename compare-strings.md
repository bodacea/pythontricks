# Compare Strings in columns

Find value: df\[df\[col\] == value\]

Get everything in string before "/": df\[col\].str.split\('/'\).str.get\(0\)

Get all rows with one of val1, val2 etc: df\[df\[col\].isin\(\[val1, val2, ''\]\)\]

Get all rows that don't start with val1, val2 etc: df\[df\[col\].str\[:4\].isin\(\[val1, val2, val3\]\) == False\]



