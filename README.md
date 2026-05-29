# Streaming-Consumption-Intelligence-Audience-Analytics
Analyzed Netflix 2023 content performance data using Python to uncover audience engagement trends across content types, languages, seasons, and release schedules. Identified that shows, Friday releases, and year-end launches generated the highest viewership, providing insights to support content strategy and release planning decisions.

### Project Overview

This project analyzes Netflix's 2023 content performance to understand how content type, language, release timing, and seasonal patterns influence audience engagement. The analysis identifies strategic factors driving viewership and provides data-driven recommendations for content planning.

### Project Objectives
- Analyze viewership trends across content types
- Evaluate language-wise content performance
- Identify seasonal and monthly engagement patterns
- Assess release timing impact on audience viewership
- Discover high-performing content characteristics


### Dataset Description

The dataset contains Netflix content performance data for 2023, including titles, content type, language, release dates, and total hours viewed.

Each record represents a Netflix title and its corresponding audience engagement metrics.

## 📖 Data Dictionary

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| Title | VARCHAR | Name of the Netflix show or movie |
| Content Type | VARCHAR | Type of content (Show or Movie) |
| Language Indicator | VARCHAR | Primary language of the content |
| Release Date | DATE | Date when the content was released on Netflix |
| Hours Viewed | FLOAT | Total number of hours viewed by users |
| Release Day | VARCHAR | Day of the week when content was released |
| Release Month | INT | Month of content release (1-12) |
| Release Season | VARCHAR | Season of release (Winter, Spring, Summer, Fall) |
| Available Globally | BOOLEAN | Indicates whether the content was available globally (Yes/No) |

### Tools Used
- Python
- Pandas
- Plotly
- NumPy
- Power BI

### Analysis Approach
- Cleaned and transformed viewership data
- Compared performance of shows and movies
- Evaluated language-based viewership patterns
- Analyzed monthly and seasonal engagement trends
- Investigated release-day strategies
- Identified top-performing titles
- Assessed holiday release impact

### 1️⃣ Content Type Analysis
```python
netflix_data['Hours Viewed'] = netflix_data['Hours Viewed'].replace(',', '', regex=True).astype(float)

netflix_data[['Title', 'Hours Viewed']].head()
```
**Output:**
<img width="467" height="241" alt="content_type_viewership" src="https://github.com/user-attachments/assets/5de397d8-2fbb-40d3-a412-b88d86b7c959" />

Insight: Shows generated significantly higher watch hours than movies.

### 2️⃣ Language Analysis
```python
# aggregate viewership hours by language
language_viewership = netflix_data.groupby('Language Indicator')['Hours Viewed'].sum().sort_values(ascending=False)

fig = go.Figure(data=[
    go.Bar(
        x=language_viewership.index,
        y=language_viewership.values,
        marker_color='lightcoral'
    )
])

fig.update_layout(
    title='Total Viewership Hours by Language (2023)',
    xaxis_title='Language',
    yaxis_title='Total Hours Viewed (in billions)',
    xaxis_tickangle=45,
    height=600,
    width=1000
)

fig.show()
```
**Output:**
<img width="1000" height="600" alt="Total Viewership Hours by Language (2023)" src="https://github.com/user-attachments/assets/360fef29-8d40-435c-9848-bd232605fb03" />

Insight: English content dominated viewership, while Korean content showed strong global popularity.

### 3️⃣ Monthly Trend Analysis
```python
# convert the "Release Date" to a datetime format and extract the month
netflix_data['Release Date'] = pd.to_datetime(netflix_data['Release Date'])
netflix_data['Release Month'] = netflix_data['Release Date'].dt.month

# aggregate viewership hours by release month
monthly_viewership = netflix_data.groupby('Release Month')['Hours Viewed'].sum()

fig = go.Figure(data=[
    go.Scatter(
        x=monthly_viewership.index,
        y=monthly_viewership.values,
        mode='lines+markers',
        marker=dict(color='blue'),
        line=dict(color='blue')
    )
])

fig.update_layout(
    title='Total Viewership Hours by Release Month (2023)',
    xaxis_title='Month',
    yaxis_title='Total Hours Viewed (in billions)',
    xaxis=dict(
        tickmode='array',
        tickvals=list(range(1, 13)),
        ticktext=['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
    ),
    height=600,
    width=1000
)

fig.show()
```
**Output:**
<img width="1000" height="600" alt="Total Viewership Hours by Release Month (2023)" src="https://github.com/user-attachments/assets/8ca09365-0988-4a33-9b3f-9f6ca43aab04" />
Insight: Viewership peaked during June and December.

### 4️⃣ Seasonal Analysis
```python
# define seasons based on release months
def get_season(month):
    if month in [12, 1, 2]:
        return 'Winter'
    elif month in [3, 4, 5]:
        return 'Spring'
    elif month in [6, 7, 8]:
        return 'Summer'
    else:
        return 'Fall'

# apply the season categorization to the dataset
netflix_data['Release Season'] = netflix_data['Release Month'].apply(get_season)

# aggregate viewership hours by release season
seasonal_viewership = netflix_data.groupby('Release Season')['Hours Viewed'].sum()

# order the seasons as 'Winter', 'Spring', 'Summer', 'Fall'
seasons_order = ['Winter', 'Spring', 'Summer', 'Fall']
seasonal_viewership = seasonal_viewership.reindex(seasons_order)

fig = go.Figure(data=[
    go.Bar(
        x=seasonal_viewership.index,
        y=seasonal_viewership.values,
        marker_color='orange'
    )
])

fig.update_layout(
    title='Total Viewership Hours by Release Season (2023)',
    xaxis_title='Season',
    yaxis_title='Total Hours Viewed (in billions)',
    xaxis_tickangle=0,
    height=500,
    width=800,
    xaxis=dict(
        categoryorder='array',
        categoryarray=seasons_order
    )
)

fig.show()
```
**Output:**
<img width="800" height="500" alt="Total Viewership Hours by Release Season (2023)" src="https://github.com/user-attachments/assets/73ad4c94-bcd1-4c88-b27f-1640d94d223d" />
Insight: Fall recorded the highest audience engagement.

### 5️⃣ Release Day Strategy Analysis
```python
netflix_data['Release Day'] = netflix_data['Release Date'].dt.day_name()

weekday_releases = netflix_data['Release Day'].value_counts().reindex(
    ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
)

# aggregate viewership hours by day of the week
weekday_viewership = netflix_data.groupby('Release Day')['Hours Viewed'].sum().reindex(
    ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
)

fig = go.Figure()

fig.add_trace(
    go.Bar(
        x=weekday_releases.index,
        y=weekday_releases.values,
        name='Number of Releases',
        marker_color='blue',
        opacity=0.6,
        yaxis='y1'
    )
)

fig.add_trace(
    go.Scatter(
        x=weekday_viewership.index,
        y=weekday_viewership.values,
        name='Viewership Hours',
        mode='lines+markers',
        marker=dict(color='red'),
        line=dict(color='red'),
        yaxis='y2'
    )
)

fig.update_layout(
    title='Weekly Release Patterns and Viewership Hours (2023)',
    xaxis=dict(
        title='Day of the Week',
        categoryorder='array',
        categoryarray=['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
    ),
    yaxis=dict(
        title='Number of Releases',
        showgrid=False,
        side='left'
    ),
    yaxis2=dict(
        title='Total Hours Viewed (in billions)',
        overlaying='y',
        side='right',
        showgrid=False
    ),
    legend=dict(
        x=1.05,  
        y=1,
        orientation='v',
        xanchor='left'
    ),
    height=600,
    width=1000
)

fig.show()
```
**Output:**
<img width="1000" height="600" alt="Weekly Release Patterns and Viewership Hours (2023)" src="https://github.com/user-attachments/assets/a08a3f7b-9ae9-4233-8056-aa95a8144093" />
Insight: Friday releases generated the highest audience engagement.


### Key Findings
- Shows generated significantly higher watch hours than movies
- English content dominated viewership, while Korean content showed strong global demand
- Viewership peaked during June and December
- Fall season recorded the highest audience engagement
- Friday releases consistently achieved the highest viewership performance
- Content released around major holidays attracted substantial audience attention

### Business Insights
- Prioritize high-quality series content to maximize watch hours
- Expand investment in successful non-English content categories
- Schedule flagship releases during high-engagement periods
- Continue leveraging Friday release strategy
- Align premium content launches with major holidays and seasonal demand

### Final Note

This project demonstrates how audience behavior, release timing, and content characteristics can be analyzed to optimize streaming platform content strategies and maximize viewer engagement.
