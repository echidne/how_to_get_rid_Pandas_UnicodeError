Hi pythonistas,

I'm used to deal with pandas dataframe with datetime and to translate them in locale values.

Pandas has an argument in pandas.Series.dt.month\_name and pandas.Series.dt.day\_name that help a lot. it s called 'locale'. 'locale' is determining the language in which to return the month or day name (default is English). For example (in jupyter notebook):

#### Import modules
```Python,tabs=4
import pandas as pd
import numpy as np
import locale
```

#### Set the locale language (here: French)
```Python,tabs=4
locale.setlocale(locale.LC_ALL, 'fr_FR.utf-8')
```

#### Create a dataframe
```Python,tabs=4
df= pd.DataFrame(np.array(["2022-01-01","2022-02-01","2022-03-01","2022-04-01","2022-05-01","2022-06-01", "2022-07-01", "2022-08-01","2022-09-01","2022-10-01","2022-11-01","2022-12-01"], dtype='datetime64'), columns= ['Date'])
```

#### Create columns for days in english and french from dates
```
df['Days']=df.Date.dt.day_name()
df['Days_fr']=df.Date.dt.day_name(locale = "french")
print(df.to_markdown())
```
output:

|    | Date                | Days      | Days_fr   |
|---:|:--------------------|:----------|:----------|
|  0 | 2022-01-01 00:00:00 | Saturday  | Samedi    |
|  1 | 2022-02-01 00:00:00 | Tuesday   | Mardi     |
|  2 | 2022-03-01 00:00:00 | Tuesday   | Mardi     |
|  3 | 2022-04-01 00:00:00 | Friday    | Vendredi  |
|  4 | 2022-05-01 00:00:00 | Sunday    | Dimanche  |
|  5 | 2022-06-01 00:00:00 | Wednesday | Mercredi  |
|  6 | 2022-07-01 00:00:00 | Friday    | Vendredi  |
|  7 | 2022-08-01 00:00:00 | Monday    | Lundi     |
|  8 | 2022-09-01 00:00:00 | Thursday  | Jeudi     |
|  9 | 2022-10-01 00:00:00 | Saturday  | Samedi    |
| 10 | 2022-11-01 00:00:00 | Tuesday   | Mardi     |
| 11 | 2022-12-01 00:00:00 | Thursday  | Jeudi     |

So for the moment so far so good.

But If you try to have the month name in French:

#### Create a column for the months in English

```
df['Month']=df.Date.dt.month_name()
print(df.to_markdown())
```
|    | Date                | Days      | Days_fr   | Month     |
|---:|:--------------------|:----------|:----------|:----------|
|  0 | 2022-01-01 00:00:00 | Saturday  | Samedi    | January   |
|  1 | 2022-02-01 00:00:00 | Tuesday   | Mardi     | February  |
|  2 | 2022-03-01 00:00:00 | Tuesday   | Mardi     | March     |
|  3 | 2022-04-01 00:00:00 | Friday    | Vendredi  | April     |
|  4 | 2022-05-01 00:00:00 | Sunday    | Dimanche  | May       |
|  5 | 2022-06-01 00:00:00 | Wednesday | Mercredi  | June      |
|  6 | 2022-07-01 00:00:00 | Friday    | Vendredi  | July      |
|  7 | 2022-08-01 00:00:00 | Monday    | Lundi     | August    |
|  8 | 2022-09-01 00:00:00 | Thursday  | Jeudi     | September |
|  9 | 2022-10-01 00:00:00 | Saturday  | Samedi    | October   |
| 10 | 2022-11-01 00:00:00 | Tuesday   | Mardi     | November  |
| 11 | 2022-12-01 00:00:00 | Thursday  | Jeudi     | December  |

#### Try to translate months in French:

```
df['Month_fr'] =df.Date.dt.month_name(locale="french")
print(df.to_markdown())
```
ouput: 

*Exception in callback BaseAsyncIOLoop._handle_events(1236, 1)
handle: <Handle BaseAsyncIOLoop._handle_events(1236, 1)>
Traceback (most recent call last):
  File "C:....\jupyter_client\session.py", line 104, in json_packer
    ).encode("utf8")
UnicodeEncodeError: 'utf-8' codec can't encode character '\udce9' in position 328: surrogates not allowed*

So visibly pandas has some problems with some french characters :sob:

One solution (that I used for days before finding the correct way) is to use .replace() and to replace each month name in English by the French one:

### Use of .replace()

```
df['Month_fr'] = df.Date.dt.month_name().replace({'January':'Janvier', 'February':'Février', 'March':'Mars', 'April':'Avril', 'May':'Mai', 'June':'Juin' , 'July':'Juillet', 'August':'Août', 'September':'Septembre', 'October':'Octobre', 'November':'Novembre', 'December':'Décembre'})
print(df.to_markdown)
```

output:

|    | Date                | Days      | Days_fr   | Month     | Month_fr   |
|---:|:--------------------|:----------|:----------|:----------|:-----------|
|  0 | 2022-01-01 00:00:00 | Saturday  | Samedi    | January   | Janvier    |
|  1 | 2022-02-01 00:00:00 | Tuesday   | Mardi     | February  | Février    |
|  2 | 2022-03-01 00:00:00 | Tuesday   | Mardi     | March     | Mars       |
|  3 | 2022-04-01 00:00:00 | Friday    | Vendredi  | April     | Avril      |
|  4 | 2022-05-01 00:00:00 | Sunday    | Dimanche  | May       | Mai        |
|  5 | 2022-06-01 00:00:00 | Wednesday | Mercredi  | June      | Juin       |
|  6 | 2022-07-01 00:00:00 | Friday    | Vendredi  | July      | Juillet    |
|  7 | 2022-08-01 00:00:00 | Monday    | Lundi     | August    | Août       |
|  8 | 2022-09-01 00:00:00 | Thursday  | Jeudi     | September | Septembre  |
|  9 | 2022-10-01 00:00:00 | Saturday  | Samedi    | October   | Octobre    |
| 10 | 2022-11-01 00:00:00 | Tuesday   | Mardi     | November  | Novembre   |
| 11 | 2022-12-01 00:00:00 | Thursday  | Jeudi     | December  | Décembre   |

But its not very satisfying to use that trick is not it ? :unamused:

Since the error is an error of encoding special french characters, there is surely a way to change the encoding.
In pandas we can use [Pandas.Series.str.encode()](https://pandas.pydata.org/docs/reference/api/pandas.Series.str.encode.html) to do that. This Pandas method behave like the string.encode() Python builtin: [str.encode](https://docs.python.org/3/library/stdtypes.html#str.encode)
`str.encode` has the 2 arguments we need : `encoding` to choose in what we want to encode our string and `errors` to handle the errors.
So let's see what happend if we ask to escape surrogates:

#### Use of str.encode()
```
df['Month_fr']=df.Date.dt.month_name(locale="french").str.encode('utf-8', 'surrogateescape')
df
```


|    | Date       | Days      | Days_fr  | Month     | Month_fr       |
|----|------------|-----------|----------|-----------|----------------|
| 0  | 01/01/2022 | Saturday  | Samedi   | January   | b'Janvier'     |
| 1  | 01/02/2022 | Tuesday   | Mardi    | February  | b'F\xe9vrier'  |
| 2  | 01/03/2022 | Tuesday   | Mardi    | March     | b'Mars'        |
| 3  | 01/04/2022 | Friday    | Vendredi | April     | b'Avril'       |
| 4  | 01/05/2022 | Sunday    | Dimanche | May       | b'Mai'         |
| 5  | 01/06/2022 | Wednesday | Mercredi | June      | b'Juin'        |
| 6  | 01/07/2022 | Friday    | Vendredi | July      | b'Juillet'     |
| 7  | 01/08/2022 | Monday    | Lundi    | August    | b'Ao\xfbt'     |
| 8  | 01/09/2022 | Thursday  | Jeudi    | September | b'Septembre'   |
| 9  | 01/10/2022 | Saturday  | Samedi   | October   | b'Octobre'     |
| 10 | 01/11/2022 | Tuesday   | Mardi    | November  | b'Novembre'    |
| 11 | 01/12/2022 | Thursday  | Jeudi    | December  | b'D\xe9cembre' |

You probably noticed that i did not use `print(df.to_markdown())` as previously because if you do you ll have that 'nice' message :
*UnicodeDecodeError: 'ascii' codec can't decode byte 0xe9 in position 1: ordinal not in range(128)*

But the good thing that result means we are not far of the solution => to have the the desired result, we have to decode it correctly.
For that we can use the [Pandas.Series.str.decode()](https://pandas.pydata.org/docs/reference/api/pandas.Series.str.decode.html). 

### Use of str.decode()
```
df['Month_fr']=df.Date.dt.month_name(locale="french").str.encode('utf-8', 'surrogateescape').str.decode('iso-8859-15')
print(df.to_markdown())
```

output :

|    | Date                | Days      | Days_fr   | Month     | Month_fr   |
|---:|:--------------------|:----------|:----------|:----------|:-----------|
|  0 | 2022-01-01 00:00:00 | Saturday  | Samedi    | January   | Janvier    |
|  1 | 2022-02-01 00:00:00 | Tuesday   | Mardi     | February  | Février    |
|  2 | 2022-03-01 00:00:00 | Tuesday   | Mardi     | March     | Mars       |
|  3 | 2022-04-01 00:00:00 | Friday    | Vendredi  | April     | Avril      |
|  4 | 2022-05-01 00:00:00 | Sunday    | Dimanche  | May       | Mai        |
|  5 | 2022-06-01 00:00:00 | Wednesday | Mercredi  | June      | Juin       |
|  6 | 2022-07-01 00:00:00 | Friday    | Vendredi  | July      | Juillet    |
|  7 | 2022-08-01 00:00:00 | Monday    | Lundi     | August    | Août       |
|  8 | 2022-09-01 00:00:00 | Thursday  | Jeudi     | September | Septembre  |
|  9 | 2022-10-01 00:00:00 | Saturday  | Samedi    | October   | Octobre    |
| 10 | 2022-11-01 00:00:00 | Tuesday   | Mardi     | November  | Novembre   |
| 11 | 2022-12-01 00:00:00 | Thursday  | Jeudi     | December  | Décembre   |

Et voilà :)

List of the ISO 8859 is on [Wikipedia](https://fr.wikipedia.org/wiki/ISO/CEI_8859)
I hope that'll help some of you

PS1: If you know a better way please share it :wink: