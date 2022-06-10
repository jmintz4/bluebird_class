# Predicting the Presence or Absence of the Eastern Bluebird

## Overview and Problem Statement
* Eastern Bluebird populations in the U.S. are known to be affected by factors such as habitat loss, changes in weather, and predation.
* Given data on the above factors, how well can the presence or absence of a bluebird be predicted within an area? 
* What are the most critical factors affecting bluebird presence or absence? 
* How does the presence of invasive birds rank relative to the other factors?

## Data
* The data comes from Project FeederWatch – a project orchestrated by the Cornell Lab of Ornithology.
https://feederwatch.org/explore/raw-dataset-requests/
* Cornell collects data from thousands of participant sites across North America.
* The FeederWatch season spans November through April.
* Participants count the number of observations of birds of each species they see.
* Participants are instructed to record, on a checklist (entered in an app), the maximum number of each species observed simultaneously within a 2-day period so that the same bird is not counted twice over the two days.
![image](https://user-images.githubusercontent.com/91767180/167979650-3d00aaf6-6e38-4591-aab2-56bb74b9e83d.png)

## Aggregation and Mapping to Other Data
![image](https://user-images.githubusercontent.com/91767180/167979828-e727ea6f-ec01-439b-bc35-dcf9f673b80c.png)
* Each Project FeederWatch (PFW) site (latitude / longitude) was mapped to the nearest county centroid, in order to aggregate with the Census and USDA data.  The Census data was also mapped to the PFW years.
* The gridded ACIS daily weather data was mapped / aggregated with the sites by latitude, longitude, month, day, and year.

## Data Preparation and Exploration
![image](https://user-images.githubusercontent.com/91767180/167980596-7cf930a2-c179-45cf-a39b-4530b4ba1970.png) <br/><br/>
* To protect against bias, outlier checklists with very high counts of bird observations were removed. <br/>
![image](https://user-images.githubusercontent.com/91767180/167980949-3e73d4e1-337d-4e06-8e45-c35ce21feb4b.png) <br/><br/>
* In the following histograms and box plots, the low end represents the high number of species of rarely seen birds.
* And the high end represents the low number of species of birds that are seen numerous times.  The high end is the focus of the study.  Therefore, the rarely seen birds were removed. <br/>
![image](https://user-images.githubusercontent.com/91767180/167981473-473ce2da-5c4f-48ad-9f73-d5e6cc3e89df.png) <br/><br/>
* Site reduction has been performed to:
  * Improve the uniformity of the spatial distribution
  * Improve the balance of urban vs. rural sites
  * Improve the balance of the target, eastern bluebird presence vs. absence
* Site reduction is also a side effect of protecting against other bias. <br/>
![image](https://user-images.githubusercontent.com/91767180/167983149-164cd3a6-5000-40db-a286-57f2be6b491e.png)

## Modeling
* The final data shape modeled:
  * 34 features, 21 of which are bird species
  * 1 target:  eastern bluebird presence / absence
  * 182881 records (i.e. checklists)
* The following three classification models were evaluated:
  * Random forest
  * Gradient boosting
  * Neural network
* Grid search was used to tune the hyperparameters for the random forest and gradient boosting.

## Results
![image](https://user-images.githubusercontent.com/91767180/167983672-2c34b1f0-b90c-4d21-814f-cc6c3f7c1e79.png)
* Both the random forest and the gradient boosting classifiers are very generalizable as indicated by their low standard deviation of accuracy scores over the cross-validation folds.
* The gradient boosting classifier has:
  * The highest test accuracy, F1, and ROCAUC scores.
  * The highest recall, while its precision is comparable to that of the random forest.
  * The longest run time, but it is not impractical.
* Therefore, the gradient boosting classifier is chosen as the best model to move forward. <br/><br/>
* The recall is significantly improved with the gradient boosting classifier, i.e., it has a higher proportion of correctly predicted instances in the positive class (bluebird presence). <br/>
![image](https://user-images.githubusercontent.com/91767180/167984173-30005ae1-2255-4af2-84cd-37bfa49a1d83.png) <br/><br/>
* The ROC curve also shows a clear advantage with the gradient boosting classifier since the ROCAUC indicates that the gradient boosting classifier has a greater probability of ranking a positive instance (bluebird presence) higher than a negative one (bluebird absence). <br/>
![image](https://user-images.githubusercontent.com/91767180/167984318-5a63f831-2286-48f1-af9c-1cc6eb7e0ade.png) <br/><br/>
![image](https://user-images.githubusercontent.com/91767180/167983887-fbddbc2b-0cb5-4825-ad0a-8a82e057aacd.png)

## Conclusions
* Among the classification models evaluated for predicting eastern bluebird presence/absence, the gradient boosting model is the best performer.  Moreover, it proves to be generalizable, as the cross-validation resulted in consistent scores.
* With the gradient boosting classifier, interactions between variables could be identified, such as the negative interaction between the eastern bluebird and the house sparrow.
* On the other hand, some bird species, such as the American goldfinch, have high feature importance, positively influencing the presence of eastern bluebirds.
* Aside from latitude, longitude, and time variables with high feature importance, population density and water area proved to be important even though those variables are at the county level.

## Next Steps
* Integrate gridded land cover data from the National Land Cover Database (NLCD).  This would provide an additional dimension to more clearly explore local habitat influences.
* Aggregate with additional bird observation sources, such as the Audubon Christmas Bird Count.
* Look into other ways to improve the spatial and temporal distribution of the data, and to improve the data balance of bluebird presence/absence without doing it artificially.
  * Selecting continuous concentrations of sites could be a use case for unsupervised learning, as opposed to relying on state boundaries to equalize site concentrations. **--Currently developing a method using DBSCAN to improve spatial distribution**   https://github.com/jmintz4/bluebird_class/tree/main/site_DBSCAN
![DBSCAN_thin](https://user-images.githubusercontent.com/91767180/172981490-3274954c-f66a-440b-b4c3-925247d7ef94.PNG)



