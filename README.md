# Upper-pandas-homework
## For the log data frame from the lesson materials, create a source_type column according to the following rules:

-if the traffic_source is equal to Yandex or Google, then organic is set in source_type;

-for paid and email sources from Russia, set ad;

-for paid and email sources from outside Russia, set other;

-all other options are taken from traffic_source without changes.
```python
import pandas as pd
df = pd.read_csv("visit_log.csv",sep=';')
def det(row):
    row['traffic_source'].lower()    
    if row['traffic_source'] in ['yandex', 'google']:
        return 'organic'
    
    elif row['traffic_source'] in ['paid', 'email'] and row['region'] == 'Russia':
        return 'ad'
    
    elif row['traffic_source'] in ['paid', 'email'] and row['region'] != 'Russia':
        return 'other'
    
    else:
        return row['traffic_source']
df['source_type'] = df.apply(det, axis=1)
```
### The URLs.txt file contains the URLs of news website pages. 
You need to filter it by the addresses of news text pages. 
It is known that the news page template has a URL construction inside: /, then 8 digits, then a hyphen.

Perform the actions:

-Read the file content with a data frame.

-Filter the news text pages using the str.contains method and a regular expression matching the given pattern.
```python
import pandas as pd

df = pd.read_csv('URLs.txt', header=None, names=['url'])
pattern = r'/\d{8}-'
new = df[df['url'].str.contains(pattern, regex=True, na=False)]
```


### Calculate the average lifetime of users who have given more than 100 ratings. 

Use the ml-latest-small/ratings.csv movie ratings file. 

Lifetime refers to the difference between the maximum and minimum values of the timestamp column for a given userId value.

```python

df1 = pd.read_csv("ratings.csv")
user_ratings_count = df1.groupby('userId').size()
active_users = user_ratings_count[user_ratings_count > 100].index
active_ratings = df1[df1['userId'].isin(active_users)]
user_lifetime = (
    active_ratings
    .groupby('userId')['timestamp']
    .agg(lambda x: x.max() - x.min())
)
average_lifetime = user_lifetime.mean()
```
## You need to create two tables:

-a table with three revenue types for each client_id without specifying the client's address;

-a similar table with revenue types and client addresses.
```python
rzd = pd.DataFrame(
    {
        'client_id': [111, 112, 113, 114, 115],
        'rzd_revenue': [1093, 2810, 10283, 5774, 981]
    }
)
auto = pd.DataFrame(
    {
        'client_id': [113, 114, 115, 116, 117],
        'auto_revenue': [57483, 83, 912, 4834, 98]
    }
)
air = pd.DataFrame(
    {
        'client_id': [115, 116, 117, 118],
        'air_revenue': [81, 4, 13, 173]
    }
)
client_base = pd.DataFrame(
    {
        'client_id': [111, 112, 113, 114, 115, 116, 117, 118],
        'address': ['Комсомольская 4', 'Энтузиастов 8а', 'Левобережная 1а', 'Мира 14', 'ЗЖБИиДК 1',
                    'Строителей 18', 'Панфиловская 33', 'Мастеркова 4']
    }
)

tab = rzd.merge(auto, on='client_id', how='outer') \
             .merge(air, on='client_id', how='outer')
tab_adress = tab.merge(client_base, on='client_id', how='left')
