---
id: litvis

narrative-schemas:
  - ../../narrative-schemas/project.yml

elm:
  dependencies:
    gicentre/elm-vegalite: latest
    gicentre/tidy: latest
---

@import "../../css/datavis.less"

```elm {l=hidden}
import Tidy exposing (..)
import VegaLite exposing (..)
```

{(whoami|}
Richard Guaman - richard.guaman-sangucho@city.ac.uk
{|whoami)}

# Insight 1

The images below represent the **average** sentiment(Negative, Positive, Neutral) for each tweet made by Donald Trump (2012-2020).
The chart on the left represent the number of **likes**. The chart on the right represent the number of **retweets**.
At fist saw, this visualization shows that people on average are more likely to "like" and "retweet" negative tweets. (Note - I have used PowerBi to create these charts below)

![Average_DonaldTrump](img/Average_DonaldTrump.PNG)

The image below represents the **total** number of times that a tweet has been retweet/liked.
Now we can see that **positive** tweets are liked/retweeted more than **negatives** tweets.

![Count_DonaldTrump](img/Count_DonaldTrump.PNG)

## Scatter plot 1 - Donald Trump

This scatter plot chart also shows that tweets categorized as positive are more likely to be liked/retweeted.

```elm {l=hidden}
fmColours =
    categoricalDomainMap
        [ ( "Positive", "rgb(8,146,208)" )
        , ( "Negative", "rgb(247,109,47)" )
        , ( "Neutral", "Grey" )
        ]
```

```elm {v }
splom : Spec
splom =
    let
        data =
            dataFromUrl "https://richard-gm.github.io/webServerDataViz/DonaldTrump_Daily_Updated.csv"

        enc =
            encoding
                << position X [ pRepeat arColumn, pQuant, pScale [ scZero True ] ]
                << position Y [ pRepeat arRow, pQuant, pScale [ scZero True ] ]
                << color [ mName "tweet_sentiment", mScale fmColours ]
    in
    toVegaLite
        [ data []
        , repeat
            [ rowFields [ "mean_sentiment", "countOf_tweets_day", "retweet_count", "favorite_count" ]
            , columnFields [ "favorite_count", "retweet_count", "countOf_tweets_day", "mean_sentiment" ]
            ]
        , specification (asSpec [ width 150, height 150, circle [], enc [] ])
        ]
```

## Summary

Averages are misleading when used to compare different groups. In this dataset aggregating data into group using averages should not be used since each sentiment (Negative, Positive, Neutral) is really a calculation (Number of retweets/likes divided by sentiment). Doing so gives each county equal weight even though they each have different population levels.
