# Voting

# Question 1

```python
import pandas as pd
import math
import copy
```

## a) General Steps

### Step1: gather the voting results
```python
res = {'name' : ["Likud", "Blue and White", "Shas", 
                 "United Torah Judaism", "Hadash-Taal", "The Labor Party", 
                 "Israel is our Home", "Union of right-wing parties", "Meretz", 
                 "All of us", "United Arab List - Balad"], 
       'votes': [1140370, 1125881, 258275, 
                 249049, 193442, 190870, 
                 173004, 159468, 156473, 
                 152756, 143666]}
total_votes = 4340253
valid_votes = 4309270 

df = pd.DataFrame(res)
```

### Step2: calculate the election threshold
```python
df['percentage'] = df.votes/valid_votes
df
```
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>votes</th>
      <th>percentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Likud</td>
      <td>1140370</td>
      <td>0.264632</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Blue and White</td>
      <td>1125881</td>
      <td>0.261270</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shas</td>
      <td>258275</td>
      <td>0.059935</td>
    </tr>
    <tr>
      <th>3</th>
      <td>United Torah Judaism</td>
      <td>249049</td>
      <td>0.057794</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Hadash-Taal</td>
      <td>193442</td>
      <td>0.044890</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Labor Party</td>
      <td>190870</td>
      <td>0.044293</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Israel is our Home</td>
      <td>173004</td>
      <td>0.040147</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Union of right-wing parties</td>
      <td>159468</td>
      <td>0.037006</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Meretz</td>
      <td>156473</td>
      <td>0.036311</td>
    </tr>
    <tr>
      <th>9</th>
      <td>All of us</td>
      <td>152756</td>
      <td>0.035448</td>
    </tr>
    <tr>
      <th>10</th>
      <td>United Arab List - Balad</td>
      <td>143666</td>
      <td>0.033339</td>
    </tr>
  </tbody>
</table>


### Step3: calculate the public price of a seat in the parliament


```python
valid_votes_above_threshold = df.votes.sum()
seat_price = valid_votes_above_threshold/120
seat_price
```


    32860.45



### Step4: allocate promised integer seats.


```python
df['int_seats'] = df.votes.apply(lambda x: math.floor(x/seat_price))
df
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>votes</th>
      <th>percentage</th>
      <th>int_seats</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Likud</td>
      <td>1140370</td>
      <td>0.264632</td>
      <td>34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Blue and White</td>
      <td>1125881</td>
      <td>0.261270</td>
      <td>34</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shas</td>
      <td>258275</td>
      <td>0.059935</td>
      <td>7</td>
    </tr>
    <tr>
      <th>3</th>
      <td>United Torah Judaism</td>
      <td>249049</td>
      <td>0.057794</td>
      <td>7</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Hadash-Taal</td>
      <td>193442</td>
      <td>0.044890</td>
      <td>5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Labor Party</td>
      <td>190870</td>
      <td>0.044293</td>
      <td>5</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Israel is our Home</td>
      <td>173004</td>
      <td>0.040147</td>
      <td>5</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Union of right-wing parties</td>
      <td>159468</td>
      <td>0.037006</td>
      <td>4</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Meretz</td>
      <td>156473</td>
      <td>0.036311</td>
      <td>4</td>
    </tr>
    <tr>
      <th>9</th>
      <td>All of us</td>
      <td>152756</td>
      <td>0.035448</td>
      <td>4</td>
    </tr>
    <tr>
      <th>10</th>
      <td>United Arab List - Balad</td>
      <td>143666</td>
      <td>0.033339</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
But so far, we only allocated 113 seats, the last 7 remaining seats need to be allocated as well. Therefore we compare two common methods, the Hagenbach-Bischoff system and the Hare quota. Both of the methods allow surplus agreements between parties. The following agreements were made for the 2019 election:
### Surplus Agreements
- The Labor Party + Meretz
- The New Right(\*) + Israel is our Home
- United Arab List - Balad + Hadash-Taal
- Union of right-wing parties + Likud
- Shas + United Torah Judaism
- Noah(\*) + Pirates(\*)

(\*) did not pass the required percentage threshold and therefore are not considered in surplus agreements either


```python
name = [df.name[0]+' + '+df.name[7], 
         df.name[1], 
         df.name[2]+' + '+df.name[3], 
         df.name[4]+' + '+df.name[10], 
         df.name[5]+' + '+df.name[8], 
         df.name[6], 
         df.name[9]]

int_seats = [df.int_seats[0]+df.int_seats[7],
         df.int_seats[1],
         df.int_seats[2]+df.int_seats[3],
         df.int_seats[4]+df.int_seats[10],
         df.int_seats[5]+df.int_seats[8],
         df.int_seats[6],
         df.int_seats[9]]

votes_ab = [[df.votes[0], df.votes[7]],
         [df.votes[1]],
         [df.votes[2], df.votes[3]],
         [df.votes[4], df.votes[10]],
         [df.votes[5], df.votes[8]],
         [df.votes[6]],
         [df.votes[9]]]

df_s = pd.DataFrame({'name': name, 
                     'int_seats' : int_seats, 
                     'votes_ab': votes_ab})
df_s['votes'] = df_s.votes_ab.apply(lambda x: sum(x))
df_backup = copy.deepcopy(df_s)
```


```python
df_s[['name', 'votes', 'int_seats']]
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>votes</th>
      <th>int_seats</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Likud + Union of right-wing parties</td>
      <td>1299838</td>
      <td>38</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Blue and White</td>
      <td>1125881</td>
      <td>34</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shas + United Torah Judaism</td>
      <td>507324</td>
      <td>14</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hadash-Taal + United Arab List - Balad</td>
      <td>337108</td>
      <td>9</td>
    </tr>
    <tr>
      <th>4</th>
      <td>The Labor Party + Meretz</td>
      <td>347343</td>
      <td>9</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Israel is our Home</td>
      <td>173004</td>
      <td>5</td>
    </tr>
    <tr>
      <th>6</th>
      <td>All of us</td>
      <td>152756</td>
      <td>4</td>
    </tr>
  </tbody>
</table>



## b) Hagenbach-Bischoff system
### Step5: allocate extra seats

calculate the private price of a seat in the parliament for each party:
$$
    \frac{\text{union's valid votes}}{\text{integer promised seats} + 1}
$$


```python
df_s['private_price'] = df_s.votes/(df_s.int_seats + 1)
df_s[['name', 'votes', 'int_seats', 'private_price']]
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>votes</th>
      <th>int_seats</th>
      <th>private_price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Likud + Union of right-wing parties</td>
      <td>1299838</td>
      <td>38</td>
      <td>33329.179487</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Blue and White</td>
      <td>1125881</td>
      <td>34</td>
      <td>32168.028571</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shas + United Torah Judaism</td>
      <td>507324</td>
      <td>14</td>
      <td>33821.600000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hadash-Taal + United Arab List - Balad</td>
      <td>337108</td>
      <td>9</td>
      <td>33710.800000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>The Labor Party + Meretz</td>
      <td>347343</td>
      <td>9</td>
      <td>34734.300000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Israel is our Home</td>
      <td>173004</td>
      <td>5</td>
      <td>28834.000000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>All of us</td>
      <td>152756</td>
      <td>4</td>
      <td>30551.200000</td>
    </tr>
  </tbody>
</table>

Party that pays the highest private price gets the next seat. 
Then re-calculate private_prices and assign second next seat according to those prices and so on. Until all (120) seats are assigned.

(The function `remaining_seats` is also used for the Hare quota later, where next remaining seat is not given to the party that has the highest private_price, but to the party with the highest reminder of votes. More detailed explaination later.)


```python
def remaining_seats(df, crit):
    if crit == 'Hagenbach-Bischoff': 
        col = 'private_price'
    elif crit == 'Hare': 
        col = 'reminder'
    while sum(df.int_seats) < 120:
        highest_idx = df[col].idxmax()
        df.int_seats[highest_idx] += 1
        if crit == 'Hagenbach-Bischoff':
            df[col] = df.votes/(df.int_seats +1)
        elif crit == 'Hare':
            df[col][highest_idx] = df.votes[highest_idx] - df.int_seats[highest_idx]*seat_price
        return df
```


```python
df_s = remaining_seats(df_s, 'Hagenbach-Bischoff')
df_s
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>int_seats</th>
      <th>name</th>
      <th>votes_ab</th>
      <th>votes</th>
      <th>private_price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Likud + Union of right-wing parties</td>
      <td>[1140370, 159468]</td>
      <td>1299838</td>
      <td>33329.179487</td>
    </tr>
    <tr>
      <th>1</th>
      <td>34</td>
      <td>Blue and White</td>
      <td>[1125881]</td>
      <td>1125881</td>
      <td>32168.028571</td>
    </tr>
    <tr>
      <th>2</th>
      <td>14</td>
      <td>Shas + United Torah Judaism</td>
      <td>[258275, 249049]</td>
      <td>507324</td>
      <td>33821.600000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>9</td>
      <td>Hadash-Taal + United Arab List - Balad</td>
      <td>[193442, 143666]</td>
      <td>337108</td>
      <td>33710.800000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10</td>
      <td>The Labor Party + Meretz</td>
      <td>[190870, 156473]</td>
      <td>347343</td>
      <td>31576.636364</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>Israel is our Home</td>
      <td>[173004]</td>
      <td>173004</td>
      <td>28834.000000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>4</td>
      <td>All of us</td>
      <td>[152756]</td>
      <td>152756</td>
      <td>30551.200000</td>
    </tr>
  </tbody>
</table>

### Step6: splitting up unions, and allocating extra seats between them.

Now it remains to split the seats inside the union. Therefore we calculate the union_price, which indicates how many votes a seat inside the union costs $(union\_votes/union\_seats)$. According to this, we distribute the integer seats inside the union $int(party\_votes/union\_price)$. If it appears that one seat is left-over, similarly to before we calculate how much it would cost the party to get one extra seat, the private price of the party $(party\_votes/(party\_seats+1))$. Then whoever pays the higher price for the extra seat within a union, gets the extra seat. 


```python
def split_unions(df_s):
    df_s['union_price'] = df_s.votes/df_s.int_seats
    df_s['int_seats_ab'] = df_s.apply(lambda x:
                                     [math.floor(i/x.union_price) for i in x.votes_ab], axis=1)
    df_s['private_price'] = df_s.apply(lambda x: 
                                    [x.votes_ab[i] / (x.int_seats_ab[i]+1) for i in range(len(x.votes_ab))], axis=1)
    return df_s

```

```python
df_s = split_unions(df_s)
df_s[['name', 'union_price', 'private_price', 'int_seats_ab']]
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>union_price</th>
      <th>private_price</th>
      <th>int_seats_ab</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Likud + Union of right-wing parties</td>
      <td>32495.950000</td>
      <td>[31676.944444444445, 31893.6]</td>
      <td>[35, 4]</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Blue and White</td>
      <td>32168.028571</td>
      <td>[31274.472222222223]</td>
      <td>[35]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shas + United Torah Judaism</td>
      <td>31707.750000</td>
      <td>[28697.222222222223, 31131.125]</td>
      <td>[8, 7]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hadash-Taal + United Arab List - Balad</td>
      <td>33710.800000</td>
      <td>[32240.333333333332, 28733.2]</td>
      <td>[5, 4]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>The Labor Party + Meretz</td>
      <td>34734.300000</td>
      <td>[31811.666666666668, 31294.6]</td>
      <td>[5, 4]</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Israel is our Home</td>
      <td>34600.800000</td>
      <td>[28834.0]</td>
      <td>[5]</td>
    </tr>
    <tr>
      <th>6</th>
      <td>All of us</td>
      <td>38189.000000</td>
      <td>[30551.2]</td>
      <td>[4]</td>
    </tr>
  </tbody>
</table>


Now we compare the private_prices inside the unions to split up the extra seats. The first price belongs to the first party of the union and the second price to the second party. 

In bold the parties that get the extra seat inside the union:
- Likud + **Union of right-wing parties**
- Shas + **United Torah Judaism**
- **Hadash-Taal** + United Arab List - Balad
- **The Labor Party** + Meretz

Overall we get the result:
- (35) Likud
- (35) Blue and White
- (8) Shas
- (8) United Torah Judaism
- (6) Hadash-Taal
- (6) The Labor Party
- (5) Israel is our Home
- (5) Union of right-wing parties
- (4) Meretz
- (4) All of us
- (4) United Arab List - Balad

## c) Hare quota

Step1 - Step4 are as before and also the unions remain the same. We continue now with another method how to allocate extra seats, the Hare quota. 

### Step5: allocate extra seats


```python
df_hare = copy.deepcopy(df_backup)
```

The Hare quota assigns an extra seat to the party that has the highest reminder of votes. 

$$
\text{reminder} = \text{party's votes} -  \text{integer promised seats} \cdot \text{seat price}
$$

After the party with the highest reminder got an extra seat, the party with the second highest reminder gets a seat and we again continue until all 120 seats are allocated. 


```python
df_hare['reminder'] = df_hare.votes - df_hare.int_seats*seat_price
df_hare
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>int_seats</th>
      <th>name</th>
      <th>votes_ab</th>
      <th>votes</th>
      <th>reminder</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Likud + Union of right-wing parties</td>
      <td>[1140370, 159468]</td>
      <td>1299838</td>
      <td>51140.90</td>
    </tr>
    <tr>
      <th>1</th>
      <td>34</td>
      <td>Blue and White</td>
      <td>[1125881]</td>
      <td>1125881</td>
      <td>8625.70</td>
    </tr>
    <tr>
      <th>2</th>
      <td>14</td>
      <td>Shas + United Torah Judaism</td>
      <td>[258275, 249049]</td>
      <td>507324</td>
      <td>47277.70</td>
    </tr>
    <tr>
      <th>3</th>
      <td>9</td>
      <td>Hadash-Taal + United Arab List - Balad</td>
      <td>[193442, 143666]</td>
      <td>337108</td>
      <td>41363.95</td>
    </tr>
    <tr>
      <th>4</th>
      <td>9</td>
      <td>The Labor Party + Meretz</td>
      <td>[190870, 156473]</td>
      <td>347343</td>
      <td>51598.95</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>Israel is our Home</td>
      <td>[173004]</td>
      <td>173004</td>
      <td>8701.75</td>
    </tr>
    <tr>
      <th>6</th>
      <td>4</td>
      <td>All of us</td>
      <td>[152756]</td>
      <td>152756</td>
      <td>21314.20</td>
    </tr>
  </tbody>
</table>


```python
df_hare = remaining_seats(df_hare, 'Hare')
df_hare
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>int_seats</th>
      <th>name</th>
      <th>votes_ab</th>
      <th>votes</th>
      <th>reminder</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Likud + Union of right-wing parties</td>
      <td>[1140370, 159468]</td>
      <td>1299838</td>
      <td>51140.90</td>
    </tr>
    <tr>
      <th>1</th>
      <td>34</td>
      <td>Blue and White</td>
      <td>[1125881]</td>
      <td>1125881</td>
      <td>8625.70</td>
    </tr>
    <tr>
      <th>2</th>
      <td>14</td>
      <td>Shas + United Torah Judaism</td>
      <td>[258275, 249049]</td>
      <td>507324</td>
      <td>47277.70</td>
    </tr>
    <tr>
      <th>3</th>
      <td>9</td>
      <td>Hadash-Taal + United Arab List - Balad</td>
      <td>[193442, 143666]</td>
      <td>337108</td>
      <td>41363.95</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10</td>
      <td>The Labor Party + Meretz</td>
      <td>[190870, 156473]</td>
      <td>347343</td>
      <td>18738.50</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>Israel is our Home</td>
      <td>[173004]</td>
      <td>173004</td>
      <td>8701.75</td>
    </tr>
    <tr>
      <th>6</th>
      <td>4</td>
      <td>All of us</td>
      <td>[152756]</td>
      <td>152756</td>
      <td>21314.20</td>
    </tr>
  </tbody>
</table>

### Step6: splitting up unions, and allocating extra seats between them.

It is left to split up the unions in the same way as in the previous section (via the private seat price inside the unions). Which leads to the following result:


```python
split_unions(df_hare)[['name', 'private_price', 'int_seats_ab']]
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>private_price</th>
      <th>int_seats_ab</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Likud + Union of right-wing parties</td>
      <td>[31676.944444444445, 31893.6]</td>
      <td>[35, 4]</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Blue and White</td>
      <td>[32168.02857142857]</td>
      <td>[34]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shas + United Torah Judaism</td>
      <td>[32284.375, 31131.125]</td>
      <td>[7, 7]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hadash-Taal + United Arab List - Balad</td>
      <td>[32240.333333333332, 28733.2]</td>
      <td>[5, 4]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>The Labor Party + Meretz</td>
      <td>[27267.14285714286, 31294.6]</td>
      <td>[6, 4]</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Israel is our Home</td>
      <td>[28834.0]</td>
      <td>[5]</td>
    </tr>
    <tr>
      <th>6</th>
      <td>All of us</td>
      <td>[25459.333333333332]</td>
      <td>[5]</td>
    </tr>
  </tbody>
</table>

Now we compare the private_prices inside the unions to split up the extra seats. The first price belongs to the first party of the union and the second price to the second party. 

In bold the parties that get the extra seat inside the union:
- Likud + **Union of right-wing parties**
- **Shas** + United Torah Judaism
- **Hadash-Taal** + United Arab List - Balad
- The Labor Party + **Meretz**

Overall we get the result:
- (35) Likud
- (34) Blue and White
- (8) Shas
- (7) United Torah Judaism (\*)
- (6) Hadash-Taal
- (6) The Labor Party
- (5) Israel is our Home
- (5) Union of right-wing parties
- (5) Meretz (\*)
- (5) All of us (\*)
- (4) United Arab List - Balad

The parties marked with (\*) have one more seat compared to the result from Hagenbach-Bischoff system. They are smaller parties, especially if they were a small party in a surplus agreement with a bigger party. 

This is because the private_price = votes/(int_seats+1) does bigger jumps the smaller int_seats is. So it is more difficult for small parties (fewer votes, fewer seats) to buy more seats. 
But the reminder is fair for all parties, since it is only related to the public seat price.

## d) Shapley-Shubik power index

The parties A-E of the right-wing coalition have the following seats: 

[A: 35, B: 16, C: 5, D: 5, E: 4]

In total 61 votes are necessary to make a decision. Let's find out how often each party is the pivot:

**A**: 	sum(BCD)+A = 26+35 = 61 $\rightarrow$ BCD**A**E and all the other permutations of BCD $\rightarrow$ 3! = 6 combinations

	sum(BCDE)+A = 30+35 = 65 $\rightarrow$ BCDE**A** and all the other permutations of BCDE $\rightarrow$ 4! = 24 combinations

**B**: 	sum(ACD)+B = 45+16 = 61 $\rightarrow$ ACD**B**E $\rightarrow​$ 6 combinations

	sum(ACDE)+B = 49+16 = 65 $\rightarrow$ ACDE**B** $\rightarrow$ 24 combinations

**C**:	sum(ABD)+C = 56+5 = 61 $\rightarrow$ ABD**C**E $\rightarrow$ 6 combinations

	sum(ABDE)+C = 60+5 = 65 $\rightarrow$ ABDE**C** $\rightarrow$ 24 combinations

**D**: 	same as C

**E**: 	sum(ABCD) =  61 already, and if we combine any other 3 parties (ABC, ABD, ACD, BCD), the sum is smaller than 57, which would have been necessary to let E be the pivot. So E is never the pivot. 

A, B, C, D have all a voting power of $\frac{6+24}{5!} = \frac{1}{4} $

Power index: [1/4, 1/4, 1/4, 1/4, 0]

This means that parties A,B,C,D have the same negotiation power, since all of them need to join together to get to 61 votes. What party E does, does not matter, since it does not have any impact on the outcome.

# Question 2

- **Condorcet-loser criteria is not satisfied when using plurality voting**

  Yes. With plurality voting, we might even choose a candidate, that is Condorcet-loser. See the example:

  | 1 vote | 1 vote | 1 vote | 2 vote |
  | :----: | :----: | :----: | :----: |
  |   A    |   B    |   C    |   D    |
  |   B    |   C    |   A    |   A    |
  |   C    |   A    |   B    |   B    |
  |   D    |   D    |   D    |   C    |

  Condorcet-loser is D, since he loses against A, B, C with 2 to 3 votes each time. 

  But in plurality voting D wins, because it got at the first place twice, A, B and C only once each.

- **Sequential pairwise voting, despite its other faults, is Condorcet-consistent.**

  This statement is true. Condorcet-consistent means that if there exists a Condorcet-winner (a candidate that wins in every pairwise election), he will be elected. So it is obviously true that the candidate that is Condorcet-winner will win every pairwise voting in sequential voting, because by definition he beats all other candidate. Hence in Sequential pairwise voting always the candidate that is Condorcet-winner will be elected, so the system is Condorcet-consistent.

- **Plurality with Run-off satisfies the Monotonicity Criterion**

  See the example:

  | 20 votes | 15 votes | 10 votes | 17 votes |
  | -------- | -------- | -------- | -------- |
  | A        | B        | B        | C        |
  | B        | C        | A        | A        |
  | C        | A        | C        | B        |

  For Plurality with Run-off, we first decide which two candidates will run against each other. Since C has the least 1st-place votes, we run A against B. Then:

  20+17=37 voters prefer A over B

  15+10=25 voters prefer B over A

  $\rightarrow$ **A wins**

  *Monotonicity Criterion*: If x is the winner under a voting rule and one or more voters change their preferences in a way favorable to x (without the changing the order in which they prefer any other alternatives), then x should still be the winner”

  What happens now if we change 9 votes from BAC $\rightarrow$ ABC? If this is a counter example of the Monotonicity Criterion, A should not be the winner anymore.

  | 29 votes | 15 votes | 1 vote | 17 votes |
  | -------- | -------- | ------ | -------- |
  | A        | B        | B      | C        |
  | B        | C        | A      | A        |
  | C        | A        | C      | B        |

  Here B has the least 1st-place votes, therefore we run a run-off between A and C. Now:

  29+1=30 voters prefer A over C

  15+17=32 voters prefer C over A

  $\rightarrow$ **C wins**

  This example shows that Plurality with Run-off **does not satisfy** the Monotonicity Criterion.

# Question 3

**Why does Black's voting procedure not satisfy Smith's Generalized Condorcet Criterion?**

See the example:

|Borda weight |1 vote | 1 vote | 1 voter |
|:-------: |:--------:|:-----------:|:-----------:|
| 6 | a |  b|  c|
| 5 | b|  c| a|
|4|x|x| x|
|3|y| y| y|
|2 |z |z |z|
|1 |w |w| w|
|0|c| a| b|

For Smith's Criterion we divide the candidates into two sets A={a,b,c}, B={x,y,z,w} where every member of A beats every member of B in pairwise election. But we do not have a Condorcet-Winner, since a,b,c beat each other cyclically. 

Therefore we choose the second option of Black's voting procedure: If there is no Condorcet-Winner, we choose the winner via Borda count. And we get:

Set A: $ Borda(a)=6+0+5=11, Borda(b)=11, Borda(c)=11​$

Set B: $ Borda(x)=12, Borda(y)=9, Borda(z)=6, Borda(w)=3 $

x has the highest Borda count and therefore is the winner.

In this example we determine a candidate as Border count winner, even though it was beaten in pairwise election. 