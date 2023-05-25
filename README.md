## 📽 Movie Recommendation with item features from the Wikidata Knowledge Graph

Lately I have been spending a lot of time working with the Wikidata knowledge graph. Wikidata is an open knowledge base that contains structured data about entities, incluing attributes and relationships between concepts, products, books, places, events, etc.

Movies are great examples of linkable entities—almost all of the titles in the Movielens 100k dataset are linkable to a QID in wikidata. For example, the movie `Fargo (1996)` can be mapped (or linked) to the wikidata ID `Q222720`, and facts about the movie can be found at [this](https://www.wikidata.org/wiki/Q222720) url.

```python
from recommenders.datasets.wikidata import find_wikidata_id
find_wikidata_id('Fargo (1996)') 
# gets us Q222720. find some facts about it at: https://www.wikidata.org/wiki/Q222720
```

My goal for this project was to extract movie item features from Wikidata and see if this knowledge would improve recommendation performance. Specifically, this repo contains an extraction script for movie features and a training script which uses these features to improve ranking for a hybrid recommender system. 


### Contents 
- [extract.ipynb](./extract.ipynb) extracts movie features in two steps. first it searches the movie title for a matching wikidata entry. Next, using the wikidata query service, it extracts the following features:
    - MPA rating
    - Bechdel and Mako Mori tests (passes/fails)
    - rotten tomatoes score ranging from 0.00 to 1.00
    - movie duration in minutes
    - country of origin (there are sometimes multiple) delimited with `|` 
    - count of academy awards
    - count of academy award nominations

- [train.ipynb](./train.ipynb) includes <training and eval for > a hybrid matrix factorization model. I picked [LightFM]() as it is easy to add custom features.

- [data](./data/) contains the csv file that comes out of the extract notebook



### Results
Results are listed in a table below.
| model     | description                                                                               | precision | recall |
|-----------|-------------------------------------------------------------------------------------------|-----------|--------|
| baseline  | LightFM model with no user or item features                                               | 0.148     | 0.045  |
| grouplens | LightFM model with user and item features from grouplens and                              | 0.163     | 0.062  |
| wiki      | LightFM model with grouplens user/item features and item features extracted from wikidata | 0.171     | 0.064  |

### Notes 
- I only had 1682 movies in the 100k dataset and it was infeasible to include actors/directors as features (if I made a column for each actor and director I would have had many more features than movies).
- While writing queries, i focused on extracting features that might be useful for a US audience (e.g. MPA ratings, nominations & award counts from a US-based awards ceremony). This is because most <all?> users in the grouplens dataset have american postal codes. 