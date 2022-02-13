# Document 2 Feb 12 16:54

## Data with updated data table

![image-20220212165533730](https://asdsadsadsad.oss-cn-beijing.aliyuncs.com/image-20220212165533730.png)

```
from dash import Dash, dcc, html, Input, Output, callback, dash_table
import plotly.express as px
app = Dash(__name__, suppress_callback_exceptions=True)
from Functions import *

df2 = read_example_csv()

# dataframe for total energy frame df3
d = {'Energy consumed': [1], 'Current energy': [3],'Current power': [3],'Current cost': [3]}
d = {'Sensor name': ["robot1","robot2"], 'Type': ["robot","robot"],'State': [3,3],'Power': [4,4],'Total energy used today': [5,5],'Total cost today': [6,6]}
df3=pd.DataFrame(data=d)


# test for period
df_test = pd.read_csv('https://raw.githubusercontent.com/plotly/datasets/master/finance-charts-apple.csv')

fig = px.line(df2, x=df2.index, y='Power', title='Time Series with Range Slider and Selectors')

fig.update_xaxes(
    rangeslider_visible=True,
    rangeselector=dict(
        buttons=list([
            dict(count=1, label="1m", step="month", stepmode="backward"),
            dict(count=6, label="6m", step="month", stepmode="backward"),
            dict(count=1, label="YTD", step="year", stepmode="todate"),
            dict(count=1, label="1y", step="year", stepmode="backward"),
            dict(step="all")
        ])
    )
)


app.layout = html.Div([
    dcc.Location(id='url', refresh=False),
    html.Div(id='page-content')
])


index_page = html.Div([
    dcc.Link('Pie chart', href='/page-1'),
    html.Br(),
    dcc.Link('Line chart', href='/page-2'),
    html.Br(),
    dcc.Link('Table', href='/page-3'),
])

page_1_layout = html.Div([
    html.H1('Page 1'),
    dcc.Graph(

        figure=px.pie(df2, values='Power', names='Machine')
    ),
    html.Div(id='page-1-content'),
    html.Br(),
    dcc.Link('Go to Line chart', href='/page-2'),
    html.Br(),
    dcc.Link('Go back to home', href='/'),
])

@callback(Output('page-1-content', 'children'),
              [Input('page-1-dropdown', 'value')])
def page_1_dropdown(value):
    return f'You have selected {value}'


page_2_layout = html.Div([
    html.H1('Page 2'),

    # dcc.Graph(
    #     figure = px.line(df2, x=df2.index, y='Power', title='Time Series with Range Slider and Selectors')
    # ),
    dcc.Graph(
        figure=dict(
            data=[
                dict(
                    x=df2.index,
                    y=df2['Power'].loc[df2['Machine'] == 'robot1'],  # df.loc[df['column_name'] == some_value]
                    name='Robot 1',
                    marker=dict(
                        color='rgb(55, 83, 109)'
                    )
                ),
                dict(
                    x=df2.index,
                    y=df2['Power'].loc[df2['Machine'] == 'robot2'],  # df.loc[df['column_name'] == some_value]
                    name='Robot 2',
                    marker=dict(
                        color='rgb(26, 118, 255)'
                    )
                )
            ],
            layout=dict(
                title='Robot Power',
                showlegend=True,
                legend=dict(
                    x=0,
                    y=1.0
                ),
                margin=dict(l=40, r=0, t=40, b=30)
            )
        ),

        style={'height': 300},
        id='my-graph'
    ),



    html.Div(id='page-2-content'),
    html.Br(),
    dcc.Link('Go to Pie Chart', href='/page-1'),
    html.Br(),
    dcc.Link('Go back to home', href='/')
])

@callback(Output('page-2-content', 'children'),
              [Input('page-2-radios', 'value')])
def page_2_radios(value):
    return f'You have selected {value}'


page_3_layout = html.Div([
    html.H1('Page 3'),
    dash_table.DataTable(df3.to_dict('records'), [{"name": i, "id": i} for i in df3.columns]),
    html.Div(id='page-3-content'),
    html.Br(),
    dcc.Link('Go to Line chart', href='/page-3'),
    html.Br(),
    dcc.Link('Go back to home', href='/'),
])

@callback(Output('page-3-content', 'children'),
              [Input('page-3-dropdown', 'value')])
def page_3_dropdown(value):
    return f'You have selected {value}'


# Update the index
@callback(Output('page-content', 'children'),
              [Input('url', 'pathname')])
def display_page(pathname):
    if pathname == '/page-1':
        return page_1_layout
    elif pathname == '/page-2':
        return page_2_layout
    elif pathname == '/page-3':
        return page_3_layout
    else:
        return index_page
    # You could also return a 404 "URL not found" page here

if __name__ == '__main__':
    app.run_server(debug=True)
```

## 