{
    "cells": [
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "<center>\n    <img src=\"https://s3-api.us-geo.objectstorage.softlayer.net/cf-courses-data/CognitiveClass/Logos/organization_logo/organization_logo.png\" width=\"300\" alt=\"cognitiveclass.ai logo\"  />\n</center>\n"
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "<h1>Extracting and Visualizing Stock Data</h1>\n<h2>Description</h2>\n"
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Extracting essential data from a dataset and displaying it is a necessary part of data science; therefore individuals can make correct decisions based on the data. In this assignment, you will extract some stock data, you will then display this data in a graph.\n"
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "<h2>Table of Contents</h2>\n<div class=\"alert alert-block alert-info\" style=\"margin-top: 20px\">\n    <ul>\n        <li>Define a Function that Makes a Graph</li>\n        <li>Question 1: Use yfinance to Extract Stock Data</li>\n        <li>Question 2: Use Webscraping to Extract Tesla Revenue Data</li>\n        <li>Question 3: Use yfinance to Extract Stock Data</li>\n        <li>Question 4: Use Webscraping to Extract GME Revenue Data</li>\n        <li>Question 5: Plot Tesla Stock Graph</li>\n        <li>Question 6: Plot GameStop Stock Graph</li>\n    </ul>\n<p>\n    Estimated Time Needed: <strong>30 min</strong></p>\n</div>\n\n<hr>\n"
        },
        {
            "metadata": {
                "scrolled": true
            },
            "cell_type": "code",
            "source": "!pip install yfinance\n#!pip install pandas\n#!pip install requests\n!pip install bs4\n#!pip install plotly",
            "execution_count": 10,
            "outputs": [
                {
                    "output_type": "stream",
                    "text": "Requirement already satisfied: yfinance in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (0.1.55)\nRequirement already satisfied: lxml>=4.5.1 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from yfinance) (4.5.1)\nRequirement already satisfied: requests>=2.20 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from yfinance) (2.24.0)\nRequirement already satisfied: pandas>=0.24 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from yfinance) (1.0.5)\nRequirement already satisfied: multitasking>=0.0.7 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from yfinance) (0.0.9)\nRequirement already satisfied: numpy>=1.15 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from yfinance) (1.18.5)\nRequirement already satisfied: certifi>=2017.4.17 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from requests>=2.20->yfinance) (2020.12.5)\nRequirement already satisfied: idna<3,>=2.5 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from requests>=2.20->yfinance) (2.9)\nRequirement already satisfied: chardet<4,>=3.0.2 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from requests>=2.20->yfinance) (3.0.4)\nRequirement already satisfied: urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from requests>=2.20->yfinance) (1.25.9)\nRequirement already satisfied: pytz>=2017.2 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from pandas>=0.24->yfinance) (2020.1)\nRequirement already satisfied: python-dateutil>=2.6.1 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from pandas>=0.24->yfinance) (2.8.1)\nRequirement already satisfied: six>=1.5 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from python-dateutil>=2.6.1->pandas>=0.24->yfinance) (1.15.0)\nRequirement already satisfied: bs4 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (0.0.1)\nRequirement already satisfied: beautifulsoup4 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from bs4) (4.9.1)\nRequirement already satisfied: soupsieve>1.2 in /opt/conda/envs/Python-3.7-main/lib/python3.7/site-packages (from beautifulsoup4->bs4) (2.0.1)\n",
                    "name": "stdout"
                }
            ]
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "import yfinance as yf\nimport pandas as pd\nimport requests\nfrom bs4 import BeautifulSoup\nimport plotly.graph_objects as go\nfrom plotly.subplots import make_subplots",
            "execution_count": 11,
            "outputs": []
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "## Define Graphing Function\n"
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "In this section, we define the function `make_graph`. You don't have to know how the function works, you should only care about the inputs. It takes a dataframe with stock data (dataframe must contain Date and Close columns), a dataframe with revenue data (dataframe must contain Date and Revenue columns), and the name of the stock.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "def make_graph(stock_data, revenue_data, stock):\n    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=(\"Historical Share Price\", \"Historical Revenue\"), vertical_spacing = .3)\n    fig.add_trace(go.Scatter(x=pd.to_datetime(stock_data.Date, infer_datetime_format=True), y=stock_data.Close.astype(\"float\"), name=\"Share Price\"), row=1, col=1)\n    fig.add_trace(go.Scatter(x=pd.to_datetime(revenue_data.Date, infer_datetime_format=True), y=revenue_data.Revenue.astype(\"float\"), name=\"Revenue\"), row=2, col=1)\n    fig.update_xaxes(title_text=\"Date\", row=1, col=1)\n    fig.update_xaxes(title_text=\"Date\", row=2, col=1)\n    fig.update_yaxes(title_text=\"Price ($US)\", row=1, col=1)\n    fig.update_yaxes(title_text=\"Revenue ($US Millions)\", row=2, col=1)\n    fig.update_layout(showlegend=False,\n    height=900,\n    title=stock,\n    xaxis_rangeslider_visible=True)\n    fig.show()",
            "execution_count": 12,
            "outputs": []
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "## Question 1: Use yfinance to Extract Stock Data\n"
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Using the `Ticker` function enter the ticker symbol of the stock we want to extract data on to create a ticker object. The stock is Tesla and its ticker symbol is `TSLA`.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "tesla = yf.Ticker('TSLA')",
            "execution_count": 13,
            "outputs": []
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Using the ticker object and the function `history` extract stock information and save it in a dataframe named `tesla_data`. Set the `period` parameter to `max` so we get information for the maximum amount of time.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "tesla_data = tesla.history(period=\"max\")",
            "execution_count": 14,
            "outputs": []
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "**Reset the index** using the `reset_index(inplace=True)` function on the tesla_data DataFrame and display the first five rows of the `tesla_data` dataframe using the `head` function. Take a screenshot of the results and code from the beginning of Question 1 to the results below.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "tesla_data.reset_index(inplace=True)\ntesla_data.head()",
            "execution_count": 15,
            "outputs": [
                {
                    "output_type": "execute_result",
                    "execution_count": 15,
                    "data": {
                        "text/plain": "        Date   Open   High    Low  Close    Volume  Dividends  Stock Splits\n0 2010-06-29  3.800  5.000  3.508  4.778  93831500          0           0.0\n1 2010-06-30  5.158  6.084  4.660  4.766  85935500          0           0.0\n2 2010-07-01  5.000  5.184  4.054  4.392  41094000          0           0.0\n3 2010-07-02  4.600  4.620  3.742  3.840  25699000          0           0.0\n4 2010-07-06  4.000  4.000  3.166  3.222  34334500          0           0.0",
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Date</th>\n      <th>Open</th>\n      <th>High</th>\n      <th>Low</th>\n      <th>Close</th>\n      <th>Volume</th>\n      <th>Dividends</th>\n      <th>Stock Splits</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>2010-06-29</td>\n      <td>3.800</td>\n      <td>5.000</td>\n      <td>3.508</td>\n      <td>4.778</td>\n      <td>93831500</td>\n      <td>0</td>\n      <td>0.0</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>2010-06-30</td>\n      <td>5.158</td>\n      <td>6.084</td>\n      <td>4.660</td>\n      <td>4.766</td>\n      <td>85935500</td>\n      <td>0</td>\n      <td>0.0</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>2010-07-01</td>\n      <td>5.000</td>\n      <td>5.184</td>\n      <td>4.054</td>\n      <td>4.392</td>\n      <td>41094000</td>\n      <td>0</td>\n      <td>0.0</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>2010-07-02</td>\n      <td>4.600</td>\n      <td>4.620</td>\n      <td>3.742</td>\n      <td>3.840</td>\n      <td>25699000</td>\n      <td>0</td>\n      <td>0.0</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>2010-07-06</td>\n      <td>4.000</td>\n      <td>4.000</td>\n      <td>3.166</td>\n      <td>3.222</td>\n      <td>34334500</td>\n      <td>0</td>\n      <td>0.0</td>\n    </tr>\n  </tbody>\n</table>\n</div>"
                    },
                    "metadata": {}
                }
            ]
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "## Question 2: Use Webscraping to Extract Tesla Revenue Data\n"
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Use the `requests` library to download the webpage [https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue](https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue?cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork-23455606&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork-23455606&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork-23455606&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork-23455606&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ). Save the text of the response as a variable named `html_data`.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "url = 'https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue'\nhtml_data = requests.get(url).text",
            "execution_count": 55,
            "outputs": []
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Parse the html data using `beautiful_soup`.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "soup = BeautifulSoup(html_data,\"html5lib\")",
            "execution_count": 56,
            "outputs": []
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Using beautiful soup extract the table with `Tesla Quarterly Revenue` and store it into a dataframe named `tesla_revenue`. The dataframe should have columns `Date` and `Revenue`. Make sure the comma and dollar sign is removed from the `Revenue` column. \n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "tesla_revenue = pd.DataFrame(columns=['Date', 'Revenue'])\n\nfor table in soup.find_all('table'):\n\n    if ('Tesla Quarterly Revenue' in table.find('th').text):\n        rows = table.find_all('tr')\n        \n        for row in rows:\n            col = row.find_all('td')\n            \n            if col != []:\n                date = col[0].text\n                revenue = col[1].text.replace(',','').replace('$','')\n\n                tesla_revenue = tesla_revenue.append({\"Date\":date, \"Revenue\":revenue}, ignore_index=True)\n",
            "execution_count": 57,
            "outputs": []
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "<details><summary>Click here if you need help removing the dollar sign and comma</summary>\n\n```\n    \nIf you parsed the HTML table by row and column you can use the replace function on the string\n    \n    revenue = col[1].text.replace(\"$\", \"\").replace(\",\", \"\")\n    \nIf you use the read_html function you can use the replace function on the string representation of the column\n\n    tesla_revenue[\"Revenue\"] = tesla_revenue[\"Revenue\"].str.replace(\"$\", \"\").str.replace(\",\", \"\")\n\n```\n\n</details>\n"
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Remove the rows in the dataframe that are empty strings or are NaN in the Revenue column. Print the entire `tesla_revenue` DataFrame to see if you have any.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "tesla_revenue",
            "execution_count": 58,
            "outputs": [
                {
                    "output_type": "execute_result",
                    "execution_count": 58,
                    "data": {
                        "text/plain": "          Date Revenue\n0   2020-12-31   10744\n1   2020-09-30    8771\n2   2020-06-30    6036\n3   2020-03-31    5985\n4   2019-12-31    7384\n5   2019-09-30    6303\n6   2019-06-30    6350\n7   2019-03-31    4541\n8   2018-12-31    7226\n9   2018-09-30    6824\n10  2018-06-30    4002\n11  2018-03-31    3409\n12  2017-12-31    3288\n13  2017-09-30    2985\n14  2017-06-30    2790\n15  2017-03-31    2696\n16  2016-12-31    2285\n17  2016-09-30    2298\n18  2016-06-30    1270\n19  2016-03-31    1147\n20  2015-12-31    1214\n21  2015-09-30     937\n22  2015-06-30     955\n23  2015-03-31     940\n24  2014-12-31     957\n25  2014-09-30     852\n26  2014-06-30     769\n27  2014-03-31     621\n28  2013-12-31     615\n29  2013-09-30     431\n30  2013-06-30     405\n31  2013-03-31     562\n32  2012-12-31     306\n33  2012-09-30      50\n34  2012-06-30      27\n35  2012-03-31      30\n36  2011-12-31      39\n37  2011-09-30      58\n38  2011-06-30      58\n39  2011-03-31      49\n40  2010-12-31      36\n41  2010-09-30      31\n42  2010-06-30      28\n43  2010-03-31      21\n44  2009-12-31        \n45  2009-09-30      46\n46  2009-06-30      27\n47  2008-12-31        ",
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Date</th>\n      <th>Revenue</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>2020-12-31</td>\n      <td>10744</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>2020-09-30</td>\n      <td>8771</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>2020-06-30</td>\n      <td>6036</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>2020-03-31</td>\n      <td>5985</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>2019-12-31</td>\n      <td>7384</td>\n    </tr>\n    <tr>\n      <th>5</th>\n      <td>2019-09-30</td>\n      <td>6303</td>\n    </tr>\n    <tr>\n      <th>6</th>\n      <td>2019-06-30</td>\n      <td>6350</td>\n    </tr>\n    <tr>\n      <th>7</th>\n      <td>2019-03-31</td>\n      <td>4541</td>\n    </tr>\n    <tr>\n      <th>8</th>\n      <td>2018-12-31</td>\n      <td>7226</td>\n    </tr>\n    <tr>\n      <th>9</th>\n      <td>2018-09-30</td>\n      <td>6824</td>\n    </tr>\n    <tr>\n      <th>10</th>\n      <td>2018-06-30</td>\n      <td>4002</td>\n    </tr>\n    <tr>\n      <th>11</th>\n      <td>2018-03-31</td>\n      <td>3409</td>\n    </tr>\n    <tr>\n      <th>12</th>\n      <td>2017-12-31</td>\n      <td>3288</td>\n    </tr>\n    <tr>\n      <th>13</th>\n      <td>2017-09-30</td>\n      <td>2985</td>\n    </tr>\n    <tr>\n      <th>14</th>\n      <td>2017-06-30</td>\n      <td>2790</td>\n    </tr>\n    <tr>\n      <th>15</th>\n      <td>2017-03-31</td>\n      <td>2696</td>\n    </tr>\n    <tr>\n      <th>16</th>\n      <td>2016-12-31</td>\n      <td>2285</td>\n    </tr>\n    <tr>\n      <th>17</th>\n      <td>2016-09-30</td>\n      <td>2298</td>\n    </tr>\n    <tr>\n      <th>18</th>\n      <td>2016-06-30</td>\n      <td>1270</td>\n    </tr>\n    <tr>\n      <th>19</th>\n      <td>2016-03-31</td>\n      <td>1147</td>\n    </tr>\n    <tr>\n      <th>20</th>\n      <td>2015-12-31</td>\n      <td>1214</td>\n    </tr>\n    <tr>\n      <th>21</th>\n      <td>2015-09-30</td>\n      <td>937</td>\n    </tr>\n    <tr>\n      <th>22</th>\n      <td>2015-06-30</td>\n      <td>955</td>\n    </tr>\n    <tr>\n      <th>23</th>\n      <td>2015-03-31</td>\n      <td>940</td>\n    </tr>\n    <tr>\n      <th>24</th>\n      <td>2014-12-31</td>\n      <td>957</td>\n    </tr>\n    <tr>\n      <th>25</th>\n      <td>2014-09-30</td>\n      <td>852</td>\n    </tr>\n    <tr>\n      <th>26</th>\n      <td>2014-06-30</td>\n      <td>769</td>\n    </tr>\n    <tr>\n      <th>27</th>\n      <td>2014-03-31</td>\n      <td>621</td>\n    </tr>\n    <tr>\n      <th>28</th>\n      <td>2013-12-31</td>\n      <td>615</td>\n    </tr>\n    <tr>\n      <th>29</th>\n      <td>2013-09-30</td>\n      <td>431</td>\n    </tr>\n    <tr>\n      <th>30</th>\n      <td>2013-06-30</td>\n      <td>405</td>\n    </tr>\n    <tr>\n      <th>31</th>\n      <td>2013-03-31</td>\n      <td>562</td>\n    </tr>\n    <tr>\n      <th>32</th>\n      <td>2012-12-31</td>\n      <td>306</td>\n    </tr>\n    <tr>\n      <th>33</th>\n      <td>2012-09-30</td>\n      <td>50</td>\n    </tr>\n    <tr>\n      <th>34</th>\n      <td>2012-06-30</td>\n      <td>27</td>\n    </tr>\n    <tr>\n      <th>35</th>\n      <td>2012-03-31</td>\n      <td>30</td>\n    </tr>\n    <tr>\n      <th>36</th>\n      <td>2011-12-31</td>\n      <td>39</td>\n    </tr>\n    <tr>\n      <th>37</th>\n      <td>2011-09-30</td>\n      <td>58</td>\n    </tr>\n    <tr>\n      <th>38</th>\n      <td>2011-06-30</td>\n      <td>58</td>\n    </tr>\n    <tr>\n      <th>39</th>\n      <td>2011-03-31</td>\n      <td>49</td>\n    </tr>\n    <tr>\n      <th>40</th>\n      <td>2010-12-31</td>\n      <td>36</td>\n    </tr>\n    <tr>\n      <th>41</th>\n      <td>2010-09-30</td>\n      <td>31</td>\n    </tr>\n    <tr>\n      <th>42</th>\n      <td>2010-06-30</td>\n      <td>28</td>\n    </tr>\n    <tr>\n      <th>43</th>\n      <td>2010-03-31</td>\n      <td>21</td>\n    </tr>\n    <tr>\n      <th>44</th>\n      <td>2009-12-31</td>\n      <td></td>\n    </tr>\n    <tr>\n      <th>45</th>\n      <td>2009-09-30</td>\n      <td>46</td>\n    </tr>\n    <tr>\n      <th>46</th>\n      <td>2009-06-30</td>\n      <td>27</td>\n    </tr>\n    <tr>\n      <th>47</th>\n      <td>2008-12-31</td>\n      <td></td>\n    </tr>\n  </tbody>\n</table>\n</div>"
                    },
                    "metadata": {}
                }
            ]
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "tesla_revenue = tesla_revenue[tesla_revenue['Revenue'].astype(bool)]",
            "execution_count": 59,
            "outputs": []
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "<details><summary>Click here if you need help removing the Nan or empty strings</summary>\n\n```\n    \nIf you have NaN in the Revenue column\n    \n    tesla_revenue.dropna(inplace=True)\n    \nIf you have emtpty string in the Revenue column\n\n    tesla_revenue = tesla_revenue[tesla_revenue['Revenue'] != \"\"]\n\n```\n\n</details>\n"
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Display the last 5 row of the `tesla_revenue` dataframe using the `tail` function. Take a screenshot of the results.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "tesla_revenue.tail()",
            "execution_count": 60,
            "outputs": [
                {
                    "output_type": "execute_result",
                    "execution_count": 60,
                    "data": {
                        "text/plain": "          Date Revenue\n41  2010-09-30      31\n42  2010-06-30      28\n43  2010-03-31      21\n45  2009-09-30      46\n46  2009-06-30      27",
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Date</th>\n      <th>Revenue</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>41</th>\n      <td>2010-09-30</td>\n      <td>31</td>\n    </tr>\n    <tr>\n      <th>42</th>\n      <td>2010-06-30</td>\n      <td>28</td>\n    </tr>\n    <tr>\n      <th>43</th>\n      <td>2010-03-31</td>\n      <td>21</td>\n    </tr>\n    <tr>\n      <th>45</th>\n      <td>2009-09-30</td>\n      <td>46</td>\n    </tr>\n    <tr>\n      <th>46</th>\n      <td>2009-06-30</td>\n      <td>27</td>\n    </tr>\n  </tbody>\n</table>\n</div>"
                    },
                    "metadata": {}
                }
            ]
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "## Question 3: Use yfinance to Extract Stock Data\n"
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Using the `Ticker` function enter the ticker symbol of the stock we want to extract data on to create a ticker object. The stock is GameStop and its ticker symbol is `GME`.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "gme = yf.Ticker('GME')",
            "execution_count": 37,
            "outputs": []
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Using the ticker object and the function `history` extract stock information and save it in a dataframe named `gme_data`. Set the `period` parameter to `max` so we get information for the maximum amount of time.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "gme_data = gme.history(period='max')",
            "execution_count": 43,
            "outputs": []
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "**Reset the index** using the `reset_index(inplace=True)` function on the gme_data DataFrame and display the first five rows of the `gme_data` dataframe using the `head` function. Take a screenshot of the results and code from the beginning of Question 3 to the results below.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "gme_data.reset_index(inplace=True)\ngme_data.head()",
            "execution_count": 44,
            "outputs": [
                {
                    "output_type": "execute_result",
                    "execution_count": 44,
                    "data": {
                        "text/plain": "        Date      Open      High       Low     Close    Volume  Dividends  \\\n0 2002-02-13  6.480513  6.773399  6.413183  6.766666  19054000        0.0   \n1 2002-02-14  6.850831  6.864296  6.682506  6.733003   2755400        0.0   \n2 2002-02-15  6.733001  6.749833  6.632006  6.699336   2097400        0.0   \n3 2002-02-19  6.665671  6.665671  6.312189  6.430017   1852600        0.0   \n4 2002-02-20  6.463681  6.648838  6.413183  6.648838   1723200        0.0   \n\n   Stock Splits  \n0           0.0  \n1           0.0  \n2           0.0  \n3           0.0  \n4           0.0  ",
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Date</th>\n      <th>Open</th>\n      <th>High</th>\n      <th>Low</th>\n      <th>Close</th>\n      <th>Volume</th>\n      <th>Dividends</th>\n      <th>Stock Splits</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>2002-02-13</td>\n      <td>6.480513</td>\n      <td>6.773399</td>\n      <td>6.413183</td>\n      <td>6.766666</td>\n      <td>19054000</td>\n      <td>0.0</td>\n      <td>0.0</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>2002-02-14</td>\n      <td>6.850831</td>\n      <td>6.864296</td>\n      <td>6.682506</td>\n      <td>6.733003</td>\n      <td>2755400</td>\n      <td>0.0</td>\n      <td>0.0</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>2002-02-15</td>\n      <td>6.733001</td>\n      <td>6.749833</td>\n      <td>6.632006</td>\n      <td>6.699336</td>\n      <td>2097400</td>\n      <td>0.0</td>\n      <td>0.0</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>2002-02-19</td>\n      <td>6.665671</td>\n      <td>6.665671</td>\n      <td>6.312189</td>\n      <td>6.430017</td>\n      <td>1852600</td>\n      <td>0.0</td>\n      <td>0.0</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>2002-02-20</td>\n      <td>6.463681</td>\n      <td>6.648838</td>\n      <td>6.413183</td>\n      <td>6.648838</td>\n      <td>1723200</td>\n      <td>0.0</td>\n      <td>0.0</td>\n    </tr>\n  </tbody>\n</table>\n</div>"
                    },
                    "metadata": {}
                }
            ]
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "## Question 4: Use Webscraping to Extract GME Revenue Data\n"
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Use the `requests` library to download the webpage [https://www.macrotrends.net/stocks/charts/GME/gamestop/revenue](https://www.macrotrends.net/stocks/charts/GME/gamestop/revenue?cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork-23455606&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork-23455606&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork-23455606&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork-23455606&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ). Save the text of the response as a variable named `html_data`.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "url = 'https://www.macrotrends.net/stocks/charts/GME/gamestop/revenue'\nhtml_data = requests.get(url).text",
            "execution_count": 45,
            "outputs": []
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Parse the html data using `beautiful_soup`.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "soup = BeautifulSoup(html_data,\"html5lib\")",
            "execution_count": 46,
            "outputs": []
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Using beautiful soup extract the table with `GameStop Quarterly Revenue` and store it into a dataframe named `gme_revenue`. The dataframe should have columns `Date` and `Revenue`. Make sure the comma and dollar sign is removed from the `Revenue` column using a method similar to what you did in Question 2.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "gme_revenue = pd.DataFrame(columns=['Date', 'Revenue'])\n\nfor table in soup.find_all('table'):\n\n    if ('GameStop Quarterly Revenue' in table.find('th').text):\n        rows = table.find_all('tr')\n        \n        for row in rows:\n            col = row.find_all('td')\n            \n            if col != []:\n                date = col[0].text\n                revenue = col[1].text.replace(',','').replace('$','')\n\n                gme_revenue = gme_revenue.append({\"Date\":date, \"Revenue\":revenue}, ignore_index=True)\n",
            "execution_count": 49,
            "outputs": []
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Display the last five rows of the `gme_revenue` dataframe using the `tail` function. Take a screenshot of the results.\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "gme_revenue.tail()",
            "execution_count": 50,
            "outputs": [
                {
                    "output_type": "execute_result",
                    "execution_count": 50,
                    "data": {
                        "text/plain": "          Date Revenue\n59  2006-01-31    1667\n60  2005-10-31     534\n61  2005-07-31     416\n62  2005-04-30     475\n63  2005-01-31     709",
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Date</th>\n      <th>Revenue</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>59</th>\n      <td>2006-01-31</td>\n      <td>1667</td>\n    </tr>\n    <tr>\n      <th>60</th>\n      <td>2005-10-31</td>\n      <td>534</td>\n    </tr>\n    <tr>\n      <th>61</th>\n      <td>2005-07-31</td>\n      <td>416</td>\n    </tr>\n    <tr>\n      <th>62</th>\n      <td>2005-04-30</td>\n      <td>475</td>\n    </tr>\n    <tr>\n      <th>63</th>\n      <td>2005-01-31</td>\n      <td>709</td>\n    </tr>\n  </tbody>\n</table>\n</div>"
                    },
                    "metadata": {}
                }
            ]
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "## Question 5: Plot Tesla Stock Graph\n"
        },
        {
            "metadata": {},
            "cell_type": "markdown",
            "source": "Use the `make_graph` function to graph the Tesla Stock Data, also provide a title for the graph. The structure to call the `make_graph` function is `make_graph(tesla_data, tesla_revenue, 'Tesla')`\n"
        },
        {
            "metadata": {},
            "cell_type": "code",
            "source": "make_graph(tesla_data[['Date','Close']], tesla_revenue, 'Tesla')",
            "execution_count": 61,
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
