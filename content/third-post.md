title: Homework 3 BIOSTAT823
date: 10-1-2021
author: Yuxuan Chen

# Homework 3 Creating effective visualizations using best practices

I use ipywidgets and plotly package to create interactive plot. I choose that because it is pretty easy to understand and kind of similar to shiny app, which I am more familiar with. Besides, the plotly is good package to create a map.



## Plot 1
The first plot I create is a bar and line plot, where users can choose the entity to see the number of incidence of malaria and the numner of deaths.

```python

import numpy as np
import pandas as pd
import ipywidgets as widgets
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px
import plotly.graph_objects as go

```

```python
inc_df = pd.read_csv('malaria_inc.csv')
inc_df
```

```python
deaths_df = pd.read_csv('malaria_deaths.csv')
deaths_df
```

```python
plot1_df = inc_df.merge(deaths_df,on=["Entity","Year","Code"])
plot1_df
```


```python
entity = widgets.Dropdown(
    options=list(plot1_df['Entity'].unique()),
    value='Afghanistan',
    description='Entity:',
    continuous_update=True
)
container = widgets.HBox([entity])

trace1 = go.Scatter(x= plot1_df[(plot1_df['Entity'] == entity.value)]['Year'], 
                    y = plot1_df[(plot1_df['Entity'] == entity.value)]['Incidence of malaria (per 1,000 population at risk) (per 1,000 population at risk)'], 
                    name = 'Incidence of malaria')
trace2 = go.Bar(x= plot1_df[(plot1_df['Entity'] == entity.value)]['Year'], 
                y = plot1_df[(plot1_df['Entity'] == entity.value)]['Deaths - Malaria - Sex: Both - Age: Age-standardized (Rate) (per 100,000 people)'],
                name='Deaths')

g = go.FigureWidget(data=[trace1,trace2],
                    layout=go.Layout(
                        title=dict(
                            text='Incidence of malaria(per 1,000 population at risk) and Deaths(Rate) (per 100,000 people)'
                        ),
                        template= 'seaborn'
                    ))
g.layout.xaxis.title = 'Year'
g.layout.yaxis.title = 'Number'

def get_data(e):
    with g.batch_update():
        g.data[0].x = plot1_df[(plot1_df['Entity'] == entity.value)]['Year']
        g.data[0].y = plot1_df[(plot1_df['Entity'] == entity.value)]['Incidence of malaria (per 1,000 population at risk) (per 1,000 population at risk)']

        g.data[1].x = plot1_df[(plot1_df['Entity'] == entity.value)]['Year']
        g.data[1].y = plot1_df[(plot1_df['Entity'] == entity.value)]['Deaths - Malaria - Sex: Both - Age: Age-standardized (Rate) (per 100,000 people)']
        
        
        
entity.observe(get_data, names="value")

widgets.VBox([container,g])

```

Run palindrome() and get 906609.

## Plot 2 

The second plot is a pie plot, where users can choose the entity and year to see that proportionof deaths of each age group.

```python
malaria_deaths_age_df = pd.read_csv('malaria_deaths_age.csv')
malaria_deaths_age_df
```

```python
entity1 = widgets.Dropdown(
    options=list(malaria_deaths_age_df['entity'].unique()),
    value='Afghanistan',
    description='Entity:',
    continuous_update=True
)

year1 = widgets.Dropdown(
    options=list(malaria_deaths_age_df['year'].unique()),
    value=1990,
    description='Year:',
    continuous_update=True
)

container1 = widgets.HBox([entity1,year1])

temp_df = malaria_deaths_age_df[(malaria_deaths_age_df['entity'] == entity1.value) & (malaria_deaths_age_df['year'] == year1.value)]

g2 = go.FigureWidget(data=go.Pie(labels=temp_df['age_group'],
                             values=temp_df['deaths']),
                    layout=go.Layout(
                        title=dict(
                            text='Proportion of deaths by age group'
                        ),
                        template= 'seaborn'
                    ))


def get_data2(e):
    with g2.batch_update():
        temp_df = malaria_deaths_age_df[(malaria_deaths_age_df['entity'] == entity1.value) & (malaria_deaths_age_df['year'] == year1.value)]
        g2.data[0].labels = temp_df['age_group']
        g2.data[0].values = temp_df['deaths']
    
entity1.observe(get_data2, names="value")
year1.observe(get_data2, names="value")

widgets.VBox([container1,g2])


```


## Plot 3 

The third plot is a map, where users can zoom out and zoom in. Users can also move the mouse to check the number of deaths and the entity name.


```python
malaria_deaths_age_temp_df = malaria_deaths_age_df[['entity','code','deaths']]
country_group = malaria_deaths_age_temp_df.groupby(['entity','code']).sum()
country_group.reset_index(inplace=True)
country_group 
```

```python
fig_geo = px.scatter_geo(country_group,locations='code',hover_name='entity',color='entity' ,size="deaths",projection="natural earth")
fig_geo.show()

```



