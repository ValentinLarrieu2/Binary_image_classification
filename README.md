@author: Valentin Larrieu

# Binary image classification challenge Report

The goal of this document is to sum up the principal actions done in order to study the dataset Given. It is composed of 36 column describing characteristics of images (14 for each image A and B and 8 for the combination S). The goal there is to predict if the pair of image correspond to the same person.

1. 1Dataset Exploration

The first things done was to see the repartition of the data and find out that some classes are way more represented than others. Then I plotted the different variables and their potential correlation. I saw that some of may be transformed in order to improve our models later.

After a first exploration of the data, i tried the first model

1. 2Sampling the data to evaluate fastly the model

The example code given was sampling the code by hand. I modified a bit to create train and test set using pandas libraries.

I first try to apply models on the full dataset to evaluate but realise fastly that it was taking to much time at each try. So I only took the full set to create submission when I wanted to see the real score.

1. 3First model

The first model tested was the model given as an example. The simple Linear Discriminant Analysis gave a 0.84 score (on the extract). I tried to improve it by changing the parameters, but it appears quite fast that it was not the best way to classify our data.

That&#39;s why i tried to apply other models.

1. 4Test of different models

I first improved the model i tested by using cross validation, to different parameters on my model in order to find the best result it could give to me. It fast appeared that after some early test that ADABoosting method were giving worse results than Random forest so I decided to go on this way.

1. 5Augment the data

In order to improve the performances, I added some column to the dataset :

- The sum of each A and B column
- The product of each A and B column
- The square of each A and B column
- The square of each S

(I tried a lot of others such as min-max scaling or log transformation)

1. 6Normalise the data

I also used the standard Scaler module of sklearn to get more consistent results and tried feature selection to be more effective (for this one it was not better)

1. 7Improve the best model

After deciding which model i would try to improve more (Random Forest), i found out the best parameters to use to get the best score. To do so I created a function testing a param grid on a model given. It prints the result of each and select the best.

**The best score I could obtain on an extract of 100k lines was 0.97875 which was 0.987 on Kaggle (on full set)**

The main default here was the fact that adding more estimators improved slightly the result but enormaly the calculus time. With only 200 estimators it took 25h to create the submission.

1. 8Try of different boosting model

Trying the XGBoost on my extract seemed pretty promising since it was giving me a better score. I also improved all the parameters of the model and tried it on the full set. It took more than 3 days to get a result on the full set.

That&#39;s why I tried a lighter algorithm to get faster calculus time. The one retained was lightGBM

Boosting method are not very sensitive to feature engineering so I applied the algorithm on the full un-modified set. It gave better result than with the new features. I then tunned the parameters.

[Best Model] : **lightGBM**

- learning\_rate=0.05
- n\_estimators=5000
- num\_leaves=400

Score of **0.994453829886 on Kaggle**

1. 9Model stacking

In order to improve the results, I then implemented model stacking : the idea is there to take the best from different model (with the most diverse structure possible) to get a more accurate prediction.

There different way to do it, one is to take the prediction score of the different models, and take the result of the one with the highest score (you take the model which have the highest probability to be right).

The version I decided to implement is taking the prediction of each model, add it to the feature matrix than retrain a model on it. It did improved a bit the results on the test set on a little sample (100K lines). The main default is here is that you have to optimize each model you will use. And you also train a lot of model on the full dataset so it basically takes an enormous computation time (that&#39;s why I only tried it on samples)

1. 10 Going beyond

A further investigation of the dataset finally gave me a big hints : there were only about 80k different A and B represented in the train AND TEST SET. So it means that knowing A and B match in the train set you can be sure if they match or not with others images in the test set. So the challenge score could be quite broken using it (which is a sort of exploit, since it only works with those data). That&#39;s why I decided not to implement it (it would have easily gave me a 0.999 score).

I finally just did some feature augmentation to improve a bit the algorithm image matching recognition : I doubled the training set by swapping A and B data and adding those to the previous one (basically swapping which one is called A and B).

I wasn&#39;t able to test my algorithm on it but it would probably have improved the results (probably slightly).

