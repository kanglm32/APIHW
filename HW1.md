

```python
# Dependencies
import tweepy
import matplotlib.pyplot as plt
import json
import numpy as np
import seaborn
import pandas as pd
# Import and Initialize Sentiment Analyzer
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
analyzer = SentimentIntensityAnalyzer()

```


```python
# Twitter API Keys
# consumer_key = 'RMvCg9G3OTqEOT1mE9PnmcWcH'
# consumer_secret = '9SGFy8XvKyOpQP919xoukP51rV0U7UVOSM95xEkNHoQuT0Gg24'
# access_token = '948782379685969920-SvayeGWXOPn0t07xDWLWH27bwnW6SGR'
# access_token_secret = 'PDKhDh8QX5VLFXGecuZTUVfB62v0hihziHEWE8DG8gaNi'


consumer_key = 'auAZbZcS95FCuAtjbgp7fyQIi'
consumer_secret = 'yEp3WrBwbZ545yGsHdLfd7YInLkgKsT0EmIXFpCxZ9xn2AonRo'
access_token = '2973401010-ldW8df8JFUg6IsXtarrh6QErRyJInunItX9ghMN'
access_token_secret = 'b9dqRDWPaSt89JHrK7wsTmMBQeixDvmm8kuJxQw5lvEbN'


# Setup Tweepy API Authentication
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
# api = tweepy.API(auth, parser=tweepy.parsers.JSONParser())
api = tweepy.API(auth)
```


```python
# Target User Account
target_users = ["@BBC", "@CBS", "@CNN", "@FOX", "@nytimes"]
sentiment_list = []

for user in target_users:

    # Variables for holding sentiments
    compound_list = []
    positive_list = []
    negative_list = []
    neutral_list = []
    word_list = []

    for page in tweepy.Cursor(api.user_timeline, id=user).pages(100):
        # page is a list of statuses
        page = page[0]
        tweet = json.dumps(page._json, indent=3)
        tweet = json.loads(tweet)
        text = tweet['text']
        word_list.append(tweet['text'])
        # print(resp['text'])
        # print("----------")

        # Run Vader Analysis on each tweet
        compound = analyzer.polarity_scores(text)["compound"]
        pos = analyzer.polarity_scores(text)["pos"]
        neu = analyzer.polarity_scores(text)["neu"]
        neg = analyzer.polarity_scores(text)["neg"]

        # Add each value to the appropriate array
        compound_list.append(compound)
        positive_list.append(pos)
        negative_list.append(neg)
        neutral_list.append(neu)  
        
        sentiment = {"Target": user, "Compound":compound_list, "Word": word_list, "Positive": positive_list, "Negative": negative_list, "Neutral": neutral_list}
    sentiment_list.append(sentiment)


# # Print the Averages
# print("")
# print("User: %s" % target_user)
# print("Compound: %s" % np.mean(positive_list))
# print("Positive: %s" % np.mean(positive_list))
# print("Neutral: %s" % np.mean(neutral_list))
# print("Negative: %s" % np.mean(negative_list))
```


```python
df2 = pd.DataFrame()
for i in sentiment_list:
    df = pd.DataFrame.from_dict(i)
    df2 = df2.append(df)
df2.reset_index()
df2.insert(0, 'Tweets_Ago', df2.index)
df2

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Tweets_Ago</th>
      <th>Compound</th>
      <th>Negative</th>
      <th>Neutral</th>
      <th>Positive</th>
      <th>Target</th>
      <th>Word</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0.4215</td>
      <td>0.000</td>
      <td>0.797</td>
      <td>0.203</td>
      <td>@BBC</td>
      <td>What's the true scale of the global trade in h...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>-0.5423</td>
      <td>0.189</td>
      <td>0.763</td>
      <td>0.048</td>
      <td>@BBC</td>
      <td>RT @BBCOne: A mysterious figure reaches out to...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>-0.4939</td>
      <td>0.138</td>
      <td>0.862</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>RT @BBCScotland: Heroin exploded into housing ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>0.7096</td>
      <td>0.000</td>
      <td>0.731</td>
      <td>0.269</td>
      <td>@BBC</td>
      <td>RT @bbcthree: The teen who invented a 3D-print...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>0.4939</td>
      <td>0.000</td>
      <td>0.824</td>
      <td>0.176</td>
      <td>@BBC</td>
      <td>How do you keep your New Year's resolution to ...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>RT @BBCOne: 💀👉🏝 \n\n#DeathInParadise https://t...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>What secrets and stories does the house you li...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>0.4576</td>
      <td>0.000</td>
      <td>0.850</td>
      <td>0.150</td>
      <td>@BBC</td>
      <td>This very special 'paper' made from animal rem...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>0.1280</td>
      <td>0.076</td>
      <td>0.831</td>
      <td>0.093</td>
      <td>@BBC</td>
      <td>RT @BBCNews: Victims of people trafficking are...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>Wildlife rescuers have removed 43 reptile eggs...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>-0.5095</td>
      <td>0.155</td>
      <td>0.845</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>Scientists have decoded the genome of the Suma...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>👯 8 stars you didn't know had a sibling. 👉 htt...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>0.6114</td>
      <td>0.000</td>
      <td>0.501</td>
      <td>0.499</td>
      <td>@BBC</td>
      <td>Happy New Year everyone! 🎉 https://t.co/T2bWii...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>RT @BBCRadio2: 6 memorable moments from 25 yea...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>0.4005</td>
      <td>0.000</td>
      <td>0.856</td>
      <td>0.144</td>
      <td>@BBC</td>
      <td>Nile Rodgers is very excited to bring in the N...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15</td>
      <td>-0.5994</td>
      <td>0.196</td>
      <td>0.804</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>RT @bbcdoctorwho: Mark Gatiss gives us a snaps...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>Meet Tanzania's wheelchair tennis ace. 🎾 https...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>🎶 @BrunoMars: Live in Harlem. 10:25pm on @BBCO...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18</td>
      <td>0.2732</td>
      <td>0.187</td>
      <td>0.549</td>
      <td>0.264</td>
      <td>@BBC</td>
      <td>Meet the women who created a baby-friendly com...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>"The White House or Buckingham Palace?"\n\nThi...</td>
    </tr>
    <tr>
      <th>20</th>
      <td>20</td>
      <td>0.8591</td>
      <td>0.000</td>
      <td>0.537</td>
      <td>0.463</td>
      <td>@BBC</td>
      <td>🍫😋 Can chocolate make you happy, healthy and w...</td>
    </tr>
    <tr>
      <th>21</th>
      <td>21</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>RT @BBCr4today: Prince Harry is all set to gue...</td>
    </tr>
    <tr>
      <th>22</th>
      <td>22</td>
      <td>0.2846</td>
      <td>0.000</td>
      <td>0.918</td>
      <td>0.082</td>
      <td>@BBC</td>
      <td>We all know the lyrics to the beloved Fairytal...</td>
    </tr>
    <tr>
      <th>23</th>
      <td>23</td>
      <td>-0.3182</td>
      <td>0.084</td>
      <td>0.916</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>RT @bbcthree: "Think about the things you did ...</td>
    </tr>
    <tr>
      <th>24</th>
      <td>24</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>RT @BBCSpringwatch: When the kids wake you up ...</td>
    </tr>
    <tr>
      <th>25</th>
      <td>25</td>
      <td>0.4404</td>
      <td>0.000</td>
      <td>0.847</td>
      <td>0.153</td>
      <td>@BBC</td>
      <td>Doodlebugs. Where a clip show meets a sketch s...</td>
    </tr>
    <tr>
      <th>26</th>
      <td>26</td>
      <td>0.8016</td>
      <td>0.000</td>
      <td>0.729</td>
      <td>0.271</td>
      <td>@BBC</td>
      <td>RT @bbcweather: #Sun starting to set on #Chris...</td>
    </tr>
    <tr>
      <th>27</th>
      <td>27</td>
      <td>0.2177</td>
      <td>0.000</td>
      <td>0.911</td>
      <td>0.089</td>
      <td>@BBC</td>
      <td>🤰Expectant mothers are told to change many asp...</td>
    </tr>
    <tr>
      <th>28</th>
      <td>28</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@BBC</td>
      <td>A white-tailed sea eagle waterproofs its feath...</td>
    </tr>
    <tr>
      <th>29</th>
      <td>29</td>
      <td>0.4019</td>
      <td>0.000</td>
      <td>0.870</td>
      <td>0.130</td>
      <td>@BBC</td>
      <td>RT @BBCFood: Cheese straws 3 ways. Which one w...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>70</th>
      <td>70</td>
      <td>-0.2500</td>
      <td>0.100</td>
      <td>0.900</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Reporters checked back in with some of the peo...</td>
    </tr>
    <tr>
      <th>71</th>
      <td>71</td>
      <td>-0.5423</td>
      <td>0.149</td>
      <td>0.851</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Sometimes it seems as if we’re living under a ...</td>
    </tr>
    <tr>
      <th>72</th>
      <td>72</td>
      <td>-0.2732</td>
      <td>0.174</td>
      <td>0.826</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Duterte’s Son Quits Post After Battling Daught...</td>
    </tr>
    <tr>
      <th>73</th>
      <td>73</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>What’s big and shiny and gold and features Don...</td>
    </tr>
    <tr>
      <th>74</th>
      <td>74</td>
      <td>-0.8979</td>
      <td>0.548</td>
      <td>0.452</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Parents Killed After Warning Daughter About Bo...</td>
    </tr>
    <tr>
      <th>75</th>
      <td>75</td>
      <td>-0.4019</td>
      <td>0.184</td>
      <td>0.816</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Watch: A blind Syrian refugee finds his way in...</td>
    </tr>
    <tr>
      <th>76</th>
      <td>76</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>These charts list the full range of outcomes f...</td>
    </tr>
    <tr>
      <th>77</th>
      <td>77</td>
      <td>0.4482</td>
      <td>0.169</td>
      <td>0.570</td>
      <td>0.261</td>
      <td>@nytimes</td>
      <td>This coming year, let’s resolve to set better ...</td>
    </tr>
    <tr>
      <th>78</th>
      <td>78</td>
      <td>-0.6908</td>
      <td>0.388</td>
      <td>0.612</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Why the new blood pressure guidelines could le...</td>
    </tr>
    <tr>
      <th>79</th>
      <td>79</td>
      <td>0.4754</td>
      <td>0.000</td>
      <td>0.872</td>
      <td>0.128</td>
      <td>@nytimes</td>
      <td>John Skipper held one of the most powerful job...</td>
    </tr>
    <tr>
      <th>80</th>
      <td>80</td>
      <td>-0.6249</td>
      <td>0.298</td>
      <td>0.702</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Russia is using extradition requests to block ...</td>
    </tr>
    <tr>
      <th>81</th>
      <td>81</td>
      <td>-0.6908</td>
      <td>0.388</td>
      <td>0.612</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Yemen’s War Enters a Dark Stage as Rebels Sque...</td>
    </tr>
    <tr>
      <th>82</th>
      <td>82</td>
      <td>-0.5106</td>
      <td>0.130</td>
      <td>0.870</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>He was in prison for 26 years, and a chef for ...</td>
    </tr>
    <tr>
      <th>83</th>
      <td>83</td>
      <td>0.8555</td>
      <td>0.000</td>
      <td>0.606</td>
      <td>0.394</td>
      <td>@nytimes</td>
      <td>For some pop stars, a holiday album is a chanc...</td>
    </tr>
    <tr>
      <th>84</th>
      <td>84</td>
      <td>-0.4019</td>
      <td>0.124</td>
      <td>0.876</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Trump signed the most consequential tax legisl...</td>
    </tr>
    <tr>
      <th>85</th>
      <td>85</td>
      <td>-0.3506</td>
      <td>0.114</td>
      <td>0.886</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Ranjuma and her family are Rohingya. They esca...</td>
    </tr>
    <tr>
      <th>86</th>
      <td>86</td>
      <td>-0.7506</td>
      <td>0.314</td>
      <td>0.686</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>A boat. A rickshaw. A funeral. Times journalis...</td>
    </tr>
    <tr>
      <th>87</th>
      <td>87</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Morning Briefing: Here's what you need to know...</td>
    </tr>
    <tr>
      <th>88</th>
      <td>88</td>
      <td>0.0516</td>
      <td>0.093</td>
      <td>0.805</td>
      <td>0.102</td>
      <td>@nytimes</td>
      <td>U.S. calls for the UN Security Council to vote...</td>
    </tr>
    <tr>
      <th>89</th>
      <td>89</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>RT @NYTScience: The Ursids is the last meteor ...</td>
    </tr>
    <tr>
      <th>90</th>
      <td>90</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Introducing the Year in Pictures. To pause and...</td>
    </tr>
    <tr>
      <th>91</th>
      <td>91</td>
      <td>-0.5574</td>
      <td>0.159</td>
      <td>0.841</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>The Times’s Sports editor explains the selecti...</td>
    </tr>
    <tr>
      <th>92</th>
      <td>92</td>
      <td>0.8402</td>
      <td>0.000</td>
      <td>0.529</td>
      <td>0.471</td>
      <td>@nytimes</td>
      <td>12 small but indelible pleasures that brighten...</td>
    </tr>
    <tr>
      <th>93</th>
      <td>93</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>A look back at 2017, in pictures https://t.co/...</td>
    </tr>
    <tr>
      <th>94</th>
      <td>94</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Here's a way to figure out what will happen wi...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>95</td>
      <td>-0.5994</td>
      <td>0.196</td>
      <td>0.804</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Mexico was the deadliest country for journalis...</td>
    </tr>
    <tr>
      <th>96</th>
      <td>96</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>Facebook's job ads are raising concerns about ...</td>
    </tr>
    <tr>
      <th>97</th>
      <td>97</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>@nytimes</td>
      <td>At least 27 people stepped over him on their w...</td>
    </tr>
    <tr>
      <th>98</th>
      <td>98</td>
      <td>0.8176</td>
      <td>0.000</td>
      <td>0.681</td>
      <td>0.319</td>
      <td>@nytimes</td>
      <td>Review: "The Greatest Showman" gives us the re...</td>
    </tr>
    <tr>
      <th>99</th>
      <td>99</td>
      <td>0.3612</td>
      <td>0.000</td>
      <td>0.878</td>
      <td>0.122</td>
      <td>@nytimes</td>
      <td>Breaking News: The $1.5 trillion Republican ta...</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 7 columns</p>
</div>




```python
df2.to_csv('dataframe.csv', sep='\t')
```


```python
seaborn.set(style='ticks')


fg = seaborn.FacetGrid(data=df2, hue='Target', hue_order=target_users, aspect=3)
fg.map(plt.scatter, 'Tweets_Ago', 'Compound').add_legend()
plt.savefig("Sentiment_Plot.png")
plt.show()
```


![png](HW1_files/HW1_5_0.png)



```python
fg2 = seaborn.FacetGrid(data=df2, hue='Target', hue_order=target_users, aspect=3)
fg2.map(plt.bar, 'Tweets_Ago', 'Compound').add_legend()
plt.savefig("Sentiment_bar_100.png")
plt.show()
```


![png](HW1_files/HW1_6_0.png)



```python
avg_comp = df2.groupby("Target")["Compound"].mean()
type(avg_comp)
```




    pandas.core.series.Series




```python
avg_df = pd.DataFrame(avg_comp)

```


```python
avg_df2 = avg_df.reset_index()
avg_df2.insert(0, 'Tweets_Ago', avg_df2.index)

avg_df2
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Tweets_Ago</th>
      <th>Target</th>
      <th>Compound</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>@BBC</td>
      <td>0.153673</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>@CBS</td>
      <td>0.409116</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>@CNN</td>
      <td>-0.059090</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>@FOX</td>
      <td>0.239695</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>@nytimes</td>
      <td>-0.066382</td>
    </tr>
  </tbody>
</table>
</div>




```python
fg3 = seaborn.FacetGrid(data=avg_df2, hue='Target', hue_order=target_users, aspect=3)
fg3.map(plt.bar, 'Tweets_Ago', 'Compound').add_legend()
plt.ylim(-1, 1)
plt.savefig("Sentiment_Bar.png")
plt.show()
```


![png](HW1_files/HW1_10_0.png)

