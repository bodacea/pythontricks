# Reading CSV and Excel files

Read excel without the top blank row:  df = pd.read\_excel\(filename, skiprows=1\)

### Type conversions

df\[col\].astype\(int\)

df\[col\].astype\(float\).replace\({ 0 : np.nan }\) \# String replace: nan instead of 0





