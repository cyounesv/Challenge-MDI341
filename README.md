# Challenge-MDI341
Anomaly Detection Challenge from class MDI341 

This notebook contains work on a data challenge aiming to detect anomalies in time series. The time series correspond to accelerations from Airbus Aircrafts. 

1. **Data exploration** 

We will first take a look at the different accelerations from both our train and test data, plotting random observations then taking a look a statistics on these. This first step will allow us to notice that:

    * train and test observations seem to differ 
    * observation can seem to show located anomalies.


2. **Clustering** 

To look more into the train and test data difference we will then use a K means clustering algorithm. With 5 clusters, it will classify all training data in one, about 96% of test data and that same one and the rest of test data pretty uniformly in the four others. This is interesting in two ways : 
    * it looks like our first intuition was correct and that train data doesn't contain abnormal samples this will help us in calibrating our anomaly detection algorithms with a low contamination rate, 
    * the rest of the data is in four classes making us think that abnormalities can manifest in different ways.
We tried using the clusterisation as a feature but this did not improve our models. (No impact).


3. **Functional Isolation Forest**

Before starting feature engineering, we tried the Functional Isolation Forest algorithm with the code provided on Guillaume Staermann's github (See bibliographie for all reference and adapted code). This took a few hours, up to 40Go of RAM and gave a score under 0.75. By lack of time this algorithm being very time consumming, we did not come back to this article and algorithm, but it would have been interesting to take another look at it cutting down the signals into smaller ones like we did latter on or even constructing new multivariate time series with different statistics taken on small windows.


4. **Feature engineering**

Leveraging on observations made in the exploratory part, we calculated different statistics on the signals. We then ran 3 classic anomaly detection algorithms. More feature engineering was done later on on transformed data (Fourier filter, Wavelet decompositions).

5. **Anomaly Detection**

Isolation Forest, Local Outlier Factor and One Class Support Vector Machine, this last one gave higher scoring on the leader board and we decided to focus on it.


6. **OCSVM tuning and score evaluation**

We spent a lot of time reading work from Albert Thomas, Nicolas Goix and members of TSI research team from Telecom. On an unsupervised problem like this finding a way of evaluating our models seemed key. We used code from Albert Thomas's github to try and tune the algorithm, compare different sets of features (comparing sets with the same number of features) and evaluate the accuracy of a model. However, this method did not give us very different minimum volume sets and therefore was not conclusive.
We therefore used the histograms of score anomalies to choose which scores to save and submit : when score did not allow a seperation of the data or when too few points were isolated we stoped submitting those scores.


7. **Filtering training samples**

Having noticed that our train data was normal, we filtered its scores to eliminate the 10% most abnormal observation and retraining our model, considering that these observation were introducing a biais. This had a minor improvement effect and we did not reproduce this technique on following models, although it could have been interesting to test.


8. **Wavelet Transform**

We then used the wavelet coefficients of our signals to build a new set of features keeping 5th and 95th percentiles of coefficient on each wavelet scale plus the median for most dilated wavelets. We then ran a OCSVM and combined scores with scores obtained before averaging ranked scores. This gave us .84 on the leader board.

9. **Signal denoising** 

From different lectures on anomaly detection on similar data, denoising the signal in order to recover the 'real' normal density is often looked at. We therefore tried denoising the observation taking discrete Fourier
Transforms on wich we apply a filter before reconstructing the signal thanks to the inverse discrete fourier transform function.
We then took the same approach constructing features from basic statistics but on our denoised observations, this improved score to about .85. This approach also led us to adding the highest frequencies of our signal as features on our raw data approach.

10. **Window break down**

Finally we tried to exploit the difference between different windows in our signal observed in the exploraotry phasis by cutting our signal in several periods. Having a one minute signal we tried dividing it in 3 seconds than one second windows. This gave us our final score improvement with a submission score of **~.86**. We used PyOd combine_average method to ensemble scores from all windows.

11. **PyOd PCA, ensembling methods**

We also tried exploiring PyOd library,using its Principal Component analysis for Anomaly detection and ensembling methods. This last algorithm equalled our best score (~.86).

We also tried training a GAN with no success yet. With more time taking a look at PyOd's Variational Auto Encodeur as well as other anomaly detection libraries listed in repo below could have been interesting.
https://github.com/rob-med/awesome-TS-anomaly-detection
