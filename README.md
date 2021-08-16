## Problem Statement

I set out to create a playlist generator that would create playlist based on different moods. I wanted a way to create more customizable playlist without relying solely on Spotify's premade ones.

### Table of Contents

1. [Executive Summary](#executive-summary)
2. [Data Acquisition](#data-acquisition)
3. [Exploratory Data Analysis](#exploratory-data-analysis)
4. [Modeling](#modeling)
5. [Results and Conclusions](#results-and-conclusions)
6. [Playlist Generator](#playlist-generator)
7. [Data Dictionary](#data-dictionary)
8. [Next Steps](#next-steps)

## Executive Summary

Spotify uses various algorithms to predict what a given user will enjoy based on what they already listen to. There are also copious premade playlists available based on occasion, mood, etc. My goal was to generate playlists based on a mood that I specify using each song's features (energy, tempo, etc), using songs of any genre I want. The primary motivation behind this was my own frustration about my Discover Weekly playlists not being to my taste, as well as a small sample of peers who felt similarly.

##  Data Acquisition

- Set up Spotify developer credentials (you will need a client ID and a client secret in order to use the Spotipy library)

    1. Go to https://developer.spotify.com/dashboard/applications and create a new app
    2. You'll be able to access the id and secret in the app settings
    3. Spotipy client credentials flow can be found in the documentation [here](https://spotipy.readthedocs.io/en/2.18.0/#client-credentials-flow)
    
- Wrote webscraper to pull spotify album, song, and song feature information using Spotipy library
- Used [this](https://gist.github.com/mbejda/9912f7a366c62c1f296c) list of 10,000 artists from MTV to filter artists by genre


**Genres used:**

- Alternative
- Indie Rock
- Alt rock
- Folk/rock

- obtained features per song (see data dictionary below for features obtained)
- **Client keys are not written in notebook so these cells will not run**

## Exploratory Data Analysis

### Features

- examine feature distributions using histograms
- model on all features initially, then using features with normal distributions
- fit models both including and excluding outliers
- write function to remove outliers and compare results to model including outliers


## Modeling

- Researched KMeans, DBSCAN, and Mean Shift clustering algorithms
- Decided to use KMeans for its simpler implementation and ability to use fixed clusters
- used kmeans++ to see if it positively impacted the model performance, there was a slight improvement

### Model Interpretation

- Clusters were labelled 0, 1, 2, and 3
- Analyze five number summary of songs in each cluster to determine mood label (calm, energetic, happy, sad)
- Used median values of energy, tempo, danceability, and valence to determine label

**Risks/Limitations**

- The accuracy of these feature ratings is in the hands of Spotify
- Interpretation of the clustering algorithm is somewhat subjective

## Results and Conclusions

- Best silhouette score with 4 clusters: 24.3% (outliers removed)
- Removing outliers only slightly improved the score of the model, but I removed them for the sake of a more cohesive listening experience
- Using normally distributed features resulted in better model (valence, energy, tempo, danceability)

*An important note on genre:*

Originally I used a multitude of genres, including Rap, Hip/Hip, R&B, and others. It's important to note that treating all genres in the same manner does not do justice to the genre in question. Additionally, the feature designations are very different across genres (ie a calm indie rock song is not equivalent to a calm rap song). For future iterations, I will be categorizing songs by subgenres (Rock, Pop, Hip/Hop, etc.) and running them through their own neural network in order to create more diverse playlists and maintain a cohesive listening experience. It was only when I narrowed my focus as it relates to genre that I produced good quality playlists.


## Playlist Generator

- Split dataframe with labels into four separate dataframes (one per mood)
- Write function for user to input their name, choice of mood, and playlist title
- Function generates a random sample of 15 ong IDs
- Use Spotipy API to generate a playlist in user's account
- Another function to fill said playlist with the songs generated in user input function

*Currently only able to generate playlists on my own account due to app permissions**

## Data Dictionary


|Feature | Type | Description|
|-----|------|------|
|acousticness | *float* | A confidence measure from 0.0 to 1.0 of whether the track is acoustic. 1.0 represents high confidence the track is acoustic. |
|danceability | *float* |  describes how suitable a track is for dancing based on a combination of musical elements including tempo, rhythm stability, beat strength, and overall regularity. A value of 0.0 is least danceable and 1.0 is most danceable. |
|energy | *float* | a measure from 0.0 to 1.0 and represents a perceptual measure of intensity and activity. |
instrumentalness | *float* | Predicts whether a track contains no vocals. “Ooh” and “aah” sounds are treated as instrumental in this context. |
|liveness | *float* | Detects the presence of an audience in the recording. | 
|loudness | *float* | The overall loudness of a track in decibels (range between -60 and 0 db) |
|speechiness | *float* | detects the presence of spoken words in a track. |
|tempo | *float* | The overall estimated tempo of a track in beats per minute (BPM) |
|valence | *float* | A measure from 0.0 to 1.0 describing the musical positiveness conveyed by a track. Tracks with high valence sound more positive (e.g. happy, cheerful, euphoric), while tracks with low valence sound more negative (e.g. sad, depressed, angry) | 
|uri| *object*| unique song identifier needed to create playlists in Spotify |
|kmeans | *int* | centroid label determined in kmeans++ algorithm; used to improve the quality of the clustering |
|labels | *int* | kmeans label determining mood|

## Next Steps

- Further optimize model
- Add additional clusters to provide more accurate and granular mood determinations AND
- Use separate model for each genre category (Rock, rap, pop, etc)
- Use additional genres
- Flask app for playlist maker
- Figure out how to create playlists for other users

**Sources used:**

- [MTV artist dataset](https://gist.github.com/mbejda/9912f7a366c62c1f296c)
- [Spotify documentation](https://developer.spotify.com/documentation/web-api/reference/#category-tracks)
- [Spotipy documentation](https://spotipy.readthedocs.io/en/2.18.0/)
- [Clustering algorithm research](https://machinelearningmastery.com/clustering-algorithms-with-python/)
- [Silhouette score analysis for num_clusters](https://scikit-learn.org/stable/auto_examples/cluster/plot_kmeans_silhouette_analysis.html#sphx-glr-auto-examples-cluster-plot-kmeans-silhouette-analysis-py)
- [3D Scatterplot](https://www.geeksforgeeks.org/3d-scatter-plotting-in-python-using-matplotlib/)
- [Additional reading on KMeans](https://towardsdatascience.com/unsupervised-learning-techniques-using-python-k-means-and-silhouette-score-for-clustering-d6dd1f30b660)