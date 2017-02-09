# Dates and Times

df\[col\] = pd.to\_datetime\(df\[col\]\)

df\[col\].dt.month \# 1 to 12

df\[col\].dt.quarter

df\[col\].dt.dayofyear

Number of days between dates:  df\[lag\] = df\[col1\] - df\[col2\]; df\[lag\].dt.days



import datetime

datetime.datetime.strptime\(date\_text, '%Y-%m-%dT%H:%M:%S.%f'\)





