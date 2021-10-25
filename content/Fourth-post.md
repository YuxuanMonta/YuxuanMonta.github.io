title: Homework 4 BIOSTAT823
date: 10-24-2021
author: Yuxuan Chen

# Homework 4 Is there life after graduate school?

In this homework, I basiclly make a dashboard of the data about Science and Engineering PhDs awarded in the US. I use Dash package to make the dashboard.

## Read Data
Here, I use the table 1, table 2 and table 7 to do analysis. 
Table 1 is Doctorate recipients from U.S. colleges and universities from 1958 to 2017.
Table 2 is Doctorate-granting institutions and doctorate recipients per institution from 1973 to 2017.
Table 7 is Doctorate-granting institutions, by state or location and major science and engineering fields of study in 2017.
Note, here I did not modify the original table 1 and table 2, just directly read them. But I modified the table 7 before reading it to make it easier to be used for analysis. In the original table, the state name is in the same level of institution name, which is not what is supposed to be. So I extracted the state name and put them in the first level of row index. Also, for column name,  the All Fields should be 3 levels, but there is only one level in the original data. So I also modified that.
```python
import numpy as np
import pandas as pd
import ipywidgets as widgets
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px
import plotly.graph_objects as go
import dash
from dash.dependencies import Input, Output
from dash import dcc
from dash import html
```

```python
df1 = pd.read_excel("data/sed17-sr-tab001.xlsx",header = 3)
df2 = pd.read_excel("data/sed17-sr-tab002.xlsx",header = 4)

df2.columns = ['Year',"Doctorate-granting_institutions",'Doctorate recipients','Mean (per institution)','Median (per institution)']

df_plot1 = df1.merge(df2,on = ["Year","Doctorate recipients"])
df_plot1
```

```python
df3 = pd.read_excel("data/sed17-sr-tab007-new.xlsx",
                    header=[3,4,5], 
                    index_col=[0,1])
df3
```



## Dashboard
In the dashboard, I basiclly have 2 plots. 
The first plot is a time series for Doctorate recipients, Doctorate-granting instituion number and the mean and median value for the Doctorate recipients per institutions. I merge the table 1 and table 2 for this plot. Users can use the slider to choose the year range and use the drawdown to choose the variable they are interested in.
The second plot is a pie plot created by the new table 7. This plot shows the number and proportion of Doctorate-granting of a institution in each state by  major. Users can use the drawdowns to choose the state, and the fields they are interested in. Note, when choosing the major, the choices of the second drawdown and the third drawdown will change depending on what is chosen in the first drawdown. 

```python
app = dash.Dash()


plot1_year_indicators = df_plot1['Year'].unique()
plot1_col_indicator   = df_plot1.drop(columns=["Year","% change from previous year"]).columns

plot2_index_state     = df3.index.get_level_values(0).unique()
plot2_index_insti     = df3.index.get_level_values(1).unique()
plot2_columns_level1  = df3.columns.get_level_values(0).unique()



app.layout = html.Div([
    html.Div([

        html.Div([
            dcc.Slider(
                id     = 'plot1_year',
                min    = plot1_year_indicators.min(),
                max    = plot1_year_indicators.max(),
                value  = plot1_year_indicators.max(),
                step   = None,
                marks  = {'1973': '1973',
                          '1978': '1978',
                          '1983': '1983',
                          '1988': '1988',
                          '1993': '1993',
                          '1998': '1998',
                          '2003': '2003',
                          '2008': '2008',
                          '2013': '2013',
                          '2017': '2017'}
            ),
            dcc.Dropdown(
                id      = 'plot1_yaxis',
                options = [{'label': i, 'value': i} for i in plot1_col_indicator],
                value   = 'Doctorate recipients'
            )
            
        ],
        style={'width': '49%', 'display': 'inline-block'}),

        html.Div([
            "Select the State",
            dcc.Dropdown(
                id     = 'plot2_state',
                options= [{'label': i, 'value': i} for i in plot2_index_state],
                value  = 'Alabama',
                style  = {'height': '35px'}
            )
        ], style={'width': '24%', 'float': 'right'}),
        
        
        html.Div([
            "Select the Field",
            dcc.Dropdown(
                id='fields_1',
                options=[{'label': i, 'value': i} for i in plot2_columns_level1],
                value='All fields'
            ),
            dcc.Dropdown(
                id='fields_2'
            ),
            dcc.Dropdown(
                id='fields_3'
            )
            
        ], style={'width': '24%', 'float': 'right'})
        
    ], style={
        'borderBottom': 'thin lightgrey solid',
        'backgroundColor': 'rgb(250, 250, 250)',
        'padding': '55px 5px'
    }),

    
    
    
    html.Div([
        dcc.Graph(id='plot1')
    ], style={'width': '49%', 'display': 'inline-block', 'padding': '0 20'}),
    
    
    html.Div([
        dcc.Graph(id='plot2'),
    ], style={ 'width': '49%','float': 'right'}),
])





@app.callback(
    dash.dependencies.Output('fields_2', 'options'),
    [dash.dependencies.Input('fields_1', 'value'),
     dash.dependencies.Input('plot2_state', 'value')])
def set_cities_options(fields_1,plot2_state):
    return [{'label': i, 'value': i} for i in df3.loc[plot2_state,fields_1].columns.get_level_values(0).unique()]


@app.callback(
    dash.dependencies.Output('fields_2', 'value'),
    [dash.dependencies.Input('fields_2', 'options')])
def set_cities_value(available_options):
    return available_options[0]['value']



@app.callback(
    dash.dependencies.Output('fields_3', 'options'),
    [dash.dependencies.Input('fields_1', 'value'),
     dash.dependencies.Input('fields_2', 'value'),
     dash.dependencies.Input('plot2_state', 'value')])
def set_cities_options(fields_1,fields_2,plot2_state):
    return [{'label': i, 'value': i} for i in df3.loc[plot2_state,fields_1].loc[:,fields_2].columns.get_level_values(0).unique()]


@app.callback(
    dash.dependencies.Output('fields_3', 'value'),
    [dash.dependencies.Input('fields_3', 'options')])
def set_cities_value(available_options):
    return available_options[0]['value']


@app.callback(
    dash.dependencies.Output('plot1', 'figure'),
    [dash.dependencies.Input('plot1_year', 'value'),
     dash.dependencies.Input('plot1_yaxis', 'value')])

def plot1(xaxis,yaxis):
    
    plot1_index = df_plot1.Year[ df_plot1.Year == int(xaxis)].index.tolist()[0]
    df_plot1_0  = df_plot1.loc[0:plot1_index]
    
    return {
        'data': [go.Scatter(
            x = df_plot1_0['Year'],
            y = df_plot1_0[yaxis],
            mode='lines+markers'
        )],
            'layout': go.Layout(
                xaxis={
                    'title': "Year"
                },
                yaxis={
                    'title': yaxis
                },
                margin={'l': 40, 'b': 30, 't': 10, 'r': 0},
                height=600,
                hovermode='closest'
        )
    }


@app.callback(
    dash.dependencies.Output('plot2', 'figure'),
    [dash.dependencies.Input('plot2_state', 'value'),
     dash.dependencies.Input('fields_1', 'value'),
     dash.dependencies.Input('fields_2', 'value'),
     dash.dependencies.Input('fields_3', 'value')])
def plot2(plot2_state, fields_1, fields_2, fields_3):
    fig = px.pie(df3.loc[plot2_state,fields_1].loc[:,fields_2], 
                 values = fields_3, 
                 names=df3.loc[plot2_state,fields_1].index,
                 title='Doctorate-granting institutions,<br>by state or location and <br>major science and engineering fields of study<br>2017')
    return fig

if __name__ == '__main__':
    app.run_server()
```




