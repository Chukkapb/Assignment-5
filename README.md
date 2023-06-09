# Assignment-5
# Bhargavi Chukkapalli - 700743393
# video link : https://drive.google.com/file/d/1T1wu0ivRyW4bB-V_4jflc8-RNvUXW_WB/view?usp=share_link
import the libraries 
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
import seaborn as sns
from sklearn import preprocessing, metrics
from sklearn.preprocessing import StandardScaler, LabelEncoder
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score, classification_report, confusion_matrix
from sklearn.decomposition import PCA
from sklearn.cluster import KMeans
sns.set(style="white", color_codes=True)
import warnings
warnings.filterwarnings("ignore")
# Question-1: Principal Component Analysis
df = pd.read_csv(r"C:\Users\dhruv\OneDrive\Desktop\Bhargavi Chukkapalli\datasets\CC GENERAL.csv")
df.head()

df.isnull().any()

df.fillna(df.mean(), inplace=True)
df.isnull().any()

x = df.iloc[:,1:-1]
y = df.iloc[:,-1]
print(x.shape,y.shape)

# a. Apply PCA on CC dataset.
# Datasets can be analyzed with PCA so that redundant features can be removed without losing too much information.
pca = PCA(3)   #Instantiate PCA
x_pca = pca.fit_transform(x)
principalDf = pd.DataFrame(data = x_pca, columns = ['principal component 1', 'principal component 2', 'principal component 3'])
finalDf = pd.concat([principalDf, df.iloc[:,-1]], axis = 1)
finalDf.head()

# PCA(3)- performs principal component analysis (PCA) on dataset x, reducing the dimensionality of the data from the original number of features to 3 principal components.
# fit_transform()- method of the PCA object is called on the data x to obtain a transformed version of the data, where each observation is represented by its three principal components.
# principalDf- represents the transformed data x_pca and three principal components
# finalDf- concatenating principalDf with the last column of the original DataFrame df using pd.concat(). This is likely done to include the target variable (the variable being predicted) with the transformed data.

# b. Apply k-means algorithm on the PCA result and report your observation if the silhouette score has improved or not?
X = finalDf.iloc[:,0:-1]
y = finalDf.iloc[:,-1]
print(X.shape,y.shape)

# X- predictor variable- contains all rows of finalDf except for the last column, representing the principal components generated by PCA
# y- target variable- contains only the last column of finalDf, representing the target variable.

nclusters = 3 # this is the k in kmeans
km = KMeans(n_clusters=nclusters)
km.fit(X)

# predict the cluster for each data point
y_cluster_kmeans = km.predict(X)

# Summary of the predictions made by the classifier
print(classification_report(y, y_cluster_kmeans, zero_division=1))
print(confusion_matrix(y, y_cluster_kmeans))

# finding the accuracy
train_accuracy = accuracy_score(y, y_cluster_kmeans)
print("\nAccuracy for our Training dataset with PCA:", train_accuracy)

# Calculating sihouette Score
score = metrics.silhouette_score(X, y_cluster_kmeans)
print("Sihouette Score: ",score)   #ranges from -1 to +1, high value shows that it is matched more

# c. Perform Scaling+PCA+K-Means and report performance.

x = df.iloc[:,1:-1]
y = df.iloc[:,-1]
print(x.shape,y.shape)


## Scale the dataset; This is very important before you apply PCA
scaler = StandardScaler()
scaler.fit(x)
X_scaled_array = scaler.transform(x)

# Instantiate PCA
pca = PCA(3)

# Determine transformed features
x_pca = pca.fit_transform(X_scaled_array)
principalDf = pd.DataFrame(data = x_pca, columns = ['principal component 1', 'principal component 2','principal component 3'])
finalDf = pd.concat([principalDf, df.iloc[:,-1]], axis = 1)
finalDf.head()

x = finalDf.iloc[:,0:-1]
y = finalDf["TENURE"]
print(X.shape,y.shape)

X_train, X_test, y_train, y_test = train_test_split(x, y, test_size=0.34,random_state=0)
nclusters = 3 
# this is the k in kmeans
km = KMeans(n_clusters=nclusters)
km.fit(X_train,y_train)


# predict the cluster for each training data point
y_clus_train = km.predict(X_train)

# Summary of the predictions made by the classifier
print(classification_report(y_train, y_clus_train, zero_division=1))
print(confusion_matrix(y_train, y_clus_train))

train_accuracy = accuracy_score(y_train, y_clus_train)
print("Accuracy for our Training dataset with PCA:", train_accuracy)

# Calculating sihouette Score
score = metrics.silhouette_score(X_train, y_clus_train)
print("Sihouette Score: ",score)   # ranges from -1 to +1, high value shows that it is matched more

# predict the cluster for each testing data point
y_clus_test = km.predict(X_test)

# Summary of the predictions made by the classifier
print(classification_report(y_test, y_clus_test, zero_division=1))
print(confusion_matrix(y_test, y_clus_test))

train_accuracy = accuracy_score(y_test, y_clus_test)
print("\nAccuracy for our Testing dataset with PCA:", train_accuracy)

# Calculating sihouette Score
score = metrics.silhouette_score(X_test, y_clus_test)
print("Sihouette Score: ",score)   #ranges from -1 to +1, high value shows that it is matched more


# First scale the data Applies the fit_transform() method of the StandardScaler instance to the feature matrix X to perform feature scaling. 
# This method first computes the mean and standard deviation of each feature in X, and then scales the features such that they have zero mean and unit variance
# Then apply PCA to reduce the dimensionality to 3 components. 
# Then split the data into training and testing sets using the train_test_split() function. 
# Perform K-means clustering on the training set and test set and predict the cluster for each training data point. 
# Finally, evaluate the performance of the clustering on the training & training set using classification_report(), confusion_matrix(), accuracy_score(), and silhouette_score() functions from sklearn.metrics.

 # Question-2: Use pd_speech_features.csv
 
 df_pd = pd.read_csv("/content/pd_speech_features.csv")
df_pd.head()

df_pd.isnull().any()

X = df_pd.drop('class',axis=1).values
Y = df_pd['class'].values

# this codes represents dropping the target variable class from main data frame and creates a new data fram X
# Y returns the class column from the main data frame 

# a. Perform Scaling

# Scaling Data
scaler = StandardScaler()
X_Scale = scaler.fit_transform(X)

# StandardScaler to scale the input X, this is important as it ensures that all the features are on the same scale and prevents features with larger magnitude from dominating the distance calculations
# Applies the fit_transform() method of the StandardScaler instance to the feature matrix X to perform feature scaling

# b. Apply PCA (k=3)

# Apply PCA with k =3
pca3 = PCA(n_components=3)
principalComponents = pca3.fit_transform(X_Scale)

principalDf = pd.DataFrame(data = principalComponents, columns = ['principal component 1', 'principal component 2','Principal Component 3'])

finalDf = pd.concat([principalDf, df_pd[['class']]], axis = 1)
finalDf.head()

X = finalDf.drop('class',axis=1).values
Y = finalDf['class'].values
X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.3,random_state=0)

# c. Use SVM to report performance

from sklearn.svm import SVC

svmClassifier = SVC()
svmClassifier.fit(X_train, Y_train)

y_pred = svmClassifier.predict(X_test)

# Summary of the predictions made by the classifier
print(classification_report(Y_test, y_pred, zero_division=1))
print(confusion_matrix(Y_test, y_pred))

# Accuracy score
glass_acc_svc = accuracy_score(y_pred,Y_test)
print('accuracy is',glass_acc_svc)

# Calculate sihouette Score
score = metrics.silhouette_score(X_test, y_pred)
print("Sihouette Score: ",score) 

# It then trains an SVM classifier on the training set, predicts the classes for the test set using the trained classifier, and evaluates the performance using a classification report, confusion matrix, accuracy score, and silhouette score.

# Question-3: Apply Linear Discriminant Analysis (LDA) on Iris.csv dataset to reduce dimensionality of data to k=2.

# A classifier with a linear decision boundary, generated by fitting class conditional densities to the data and using Bayes’ rule.

from sklearn.discriminant_analysis import LinearDiscriminantAnalysis
df_iris = pd.read_csv("/content/Iris.csv")
df_iris.head()

df_iris.isnull().any()

x = df_iris.iloc[:,1:-1]
y = df_iris.iloc[:,-1]
print(x.shape,y.shape)


X_train, X_test, y_train, y_test = train_test_split(x, y, test_size=0.3, random_state=0)

sc = StandardScaler()
X_train = sc.fit_transform(X_train)
X_test = sc.transform(X_test)
le = LabelEncoder()
y = le.fit_transform(y)

from sklearn.discriminant_analysis import LinearDiscriminantAnalysis as LDA
lda = LDA(n_components=2)
X_train = lda.fit_transform(X_train, y_train)
X_test = lda.transform(X_test)
print(X_train.shape,X_test.shape)

# fit and transform the scaler object on our training data and only transform our test data.
# LabelEncoder to encode our target variable y into numerical values.
# (LDA) to perform dimensionality reduction on our input features x. Here, we are reducing the number of input features to 2 using n_components=2
# we transform our training and test data using the fit_transform and transform methods of the LDA object respectively





