### Event Recommendations and Tag Suggestions for a Ticketing Platform

**Stephen Reid**

#### Executive summary

[Dandelion](https://dandelion.events) is a web platform that hundreds of organisations use to sell tickets to regenerative events.

*Event recommendations:* After each event, attendees are sent a request for feedback, where they are able to rate the event on a scale of 1-5. We wish to create a new section of the site, 'Recent events you would have enjoyed', that lists recent events that the user would likely have rated highly, if they had attended. To do this, we evaluated a number of collaborative filtering techniques from Python's Surprise library, selected the most promising one (SVD) and performed hyperparameter tuning before producing the required predictions. 

*Event tag suggestions:* Events can have a number of tags. We wish to create a feature that suggests tags for an event based on its name. To do this, we used NLP (natural language processing) techniques to convert event names into numerical features, represented tags (the target variable) as binary arrays, and evaluated a number of classification models before picking the best one to produce tag suggestions.

#### Rationale

*Event recommendations:* Suggesting relevant events to users on the Dandelion platform increases the chance they will attend future events. Dandelion already has a number of suggestion mechanisms, including recommending future events based on attendance by people that attended the same past events, trending events that have sold many tickets in a short space of time, and displaying events that are part of the same family. The new section, 'Recent events you would have enjoyed', will be one more way users can discover relevant events.

*Event tag suggestions:* Quality tagging is vital in assisting users to find events of interest, and can also help to solve the 'cold start' problem with collaborative filtering recommender systems by facilitating content-based filtering.

#### Research Question

*Event recommendations:* What's the best way of predicting user ratings for events that users didn't attend/rate, to populate the 'Recent events you would have enjoyed' section?

*Event tag suggestions:* What's the best way of suggesting tags for an event, given only the event name?

#### Data Sources

The data sources for this research are:

* 5924 user ratings of events (account_id, event_id, rating)
* 3044 events (id, name, tags, date)
* 58,959 tickets (account_id, event_id)

#### Methodology

*Event recommendations:* We evaluated a number of different prediction algorithms (KNNBasic, SVD, NMF, SlopeOne, CoClustering) to get a sense of which would perform well on the data, studying RMSE and fit time. We then performed hyperparameter tuning of the best performing algorithm via a grid search, before using this algorithm to produce the desired predictions.

Why RMSE? RMSE (Root Mean Square Error) is a commonly used metric in predictive modeling, especially in recommendation systems. It measures the average magnitude of the error, i.e., the differences between the predicted and actual values.

In the context of event recommendations, RMSE is used to evaluate the performance of different prediction algorithms. A lower RMSE indicates a better fit of the model to the data, meaning the model's predictions are closer to the actual values.

The reason for using RMSE in this case is because it penalizes large errors more due to the squaring of differences. This makes it a good choice when large errors are particularly undesirable, which is often the case in recommendation systems where a poor recommendation (a large error) could significantly degrade the user experience.

*Event tag suggestions:* We used TfidfVectorizer to convert event names into numerical features, and MultiLabelBinarizer to convert tags into binary arrays. We then evaluated a number of different loss functions of the SGDClassifier (`log_loss`, `hinge` and `squared_hinge` – note using `log_loss` is actually equivalent to LogisticRegression and using `squared_hinge` is equivalent to LinearSVC), scoring by f1_micro.

Why f1_micro? The f1_micro score was used to assess models for event tag suggestions because it is a suitable metric for multilabel classification problems, which is the case for event tag suggestions where an event can have multiple tags.

The f1_micro score calculates metrics globally by counting the total true positives, false negatives, and false positives. This is especially useful when the dataset is imbalanced, as it gives equal weight to each instance or prediction, and is not biased towards any particular class or label.

In the context of event tag suggestions, using f1_micro ensures that all tags are equally important in the evaluation of the model, regardless of how many events are associated with a particular tag. This is crucial because some tags might be less frequent but still very important for accurate recommendations.

#### Results

*Event recommendations:* The SVD algorithm had the lowest RMSE and second lowest fit time, and so was selected as the algorithm for the next stage. Hyperparameter tuning of `n_epochs`, `lr_all` and `reg_all` resulted in the discovery of non-default hyperparameters. We verified the quality of generated predictions for the most active user on the platform (by number of ratings) by manual inspection.

*Event tag suggestions:* The optimal loss function for the SGDClassifier was `hinge`. We manually inspected a number of items from the test set to verify the quality of suggested tags.

#### Next steps

* Allow the Python script to interface directly with the Mongo database where the data came from, to both read relevant data (rather than using CSVs) and write predictions to a table that the Ruby web app can access
* Write the Ruby and HTML code to display predictions to the user in the web app

#### Outline of project

- [Event recommendations notebook](ratings.ipynb)
- [Event tag suggestions](event_tags.ipynb)

#### Contact and Further Information

Stephen Reid  
https://stephenreid.net  
stephen@stephenreid.net