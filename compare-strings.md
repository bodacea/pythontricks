# Compare Strings in columns

value: df\[df\[col\] == value\]

everything in string before "/": df\[col\].str.split\('/'\).str.get\(0\)

rows with one of val1, val2 etc: df\[df\[col\].isin\(\[val1, val2, ''\]\)\]

rows that don't start with val1, val2 etc: df\[df\[col\].str\[:4\].isin\(\[val1, val2, val3\]\) == False\]

non-null values: df\[~df\[col\].isnull\(\)\]



