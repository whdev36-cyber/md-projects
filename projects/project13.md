# Scikit-Learn in One Project: Comprehensive Machine Learning Example

This project demonstrates a complete machine learning workflow using Scikit-Learn, covering data loading, preprocessing, model training, evaluation, and deployment.

## Table of Contents
1. [Data Loading and Exploration](#data-loading)
2. [Data Preprocessing](#preprocessing)
3. [Model Training](#training)
4. [Model Evaluation](#evaluation)
5. [Hyperparameter Tuning](#tuning)
6. [Model Deployment](#deployment)

## 1. Data Loading and Exploration <a name="data-loading"></a>

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.datasets import load_iris, fetch_california_housing
from sklearn.model_selection import train_test_split

# Classification dataset
iris = load_iris()
X_class = iris.data
y_class = iris.target

# Regression dataset
housing = fetch_california_housing()
X_reg = housing.data
y_reg = housing.target

# Create DataFrames for exploration
iris_df = pd.DataFrame(X_class, columns=iris.feature_names)
iris_df['target'] = y_class

housing_df = pd.DataFrame(X_reg, columns=housing.feature_names)
housing_df['MedHouseVal'] = y_reg

# Explore classification data
print("Iris Dataset:")
print(iris_df.head())
print("\nClass distribution:")
print(iris_df['target'].value_counts())

# Explore regression data
print("\nHousing Dataset:")
print(housing_df.head())
print("\nStatistics:")
print(housing_df.describe())

# Visualizations
plt.figure(figsize=(12, 5))
plt.subplot(1, 2, 1)
sns.boxplot(data=iris_df.iloc[:, :4])
plt.title('Iris Features')

plt.subplot(1, 2, 2)
sns.scatterplot(data=housing_df, x='MedInc', y='MedHouseVal')
plt.title('Income vs House Value')
plt.tight_layout()
plt.show()
```

## 2. Data Preprocessing <a name="preprocessing"></a>

```python
from sklearn.preprocessing import StandardScaler, MinMaxScaler, OneHotEncoder
from sklearn.impute import SimpleImputer
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline
from sklearn.feature_selection import SelectKBest, f_classif

# Classification preprocessing
X_class_train, X_class_test, y_class_train, y_class_test = train_test_split(
    X_class, y_class, test_size=0.2, random_state=42, stratify=y_class)

class_preprocessor = Pipeline([
    ('imputer', SimpleImputer(strategy='mean')),  # Handle missing values
    ('scaler', StandardScaler()),  # Standardize features
    ('selector', SelectKBest(f_classif, k=2))  # Feature selection
])

# Regression preprocessing (with more complex pipeline)
numeric_features = housing.feature_names
numeric_transformer = Pipeline([
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', MinMaxScaler())
])

# Example if we had categorical features:
# categorical_transformer = Pipeline([
#     ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
#     ('onehot', OneHotEncoder(handle_unknown='ignore'))
# ])

reg_preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        # ('cat', categorical_transformer, categorical_features)
    ])

X_reg_train, X_reg_test, y_reg_train, y_reg_test = train_test_split(
    X_reg, y_reg, test_size=0.2, random_state=42)

# Apply preprocessing
X_class_train_prep = class_preprocessor.fit_transform(X_class_train, y_class_train)
X_class_test_prep = class_preprocessor.transform(X_class_test)

X_reg_train_prep = reg_preprocessor.fit_transform(X_reg_train)
X_reg_test_prep = reg_preprocessor.transform(X_reg_test)
```

## 3. Model Training <a name="training"></a>

```python
from sklearn.linear_model import LogisticRegression, LinearRegression
from sklearn.tree import DecisionTreeClassifier, DecisionTreeRegressor
from sklearn.ensemble import RandomForestClassifier, RandomForestRegressor, GradientBoostingRegressor
from sklearn.svm import SVC, SVR
from sklearn.neighbors import KNeighborsClassifier, KNeighborsRegressor
from sklearn.naive_bayes import GaussianNB

# Classification models
log_reg = LogisticRegression(max_iter=1000, random_state=42)
dt_class = DecisionTreeClassifier(random_state=42)
rf_class = RandomForestClassifier(random_state=42)
svm_class = SVC(probability=True, random_state=42)
knn_class = KNeighborsClassifier()
nb_class = GaussianNB()

# Regression models
lin_reg = LinearRegression()
dt_reg = DecisionTreeRegressor(random_state=42)
rf_reg = RandomForestRegressor(random_state=42)
svm_reg = SVR()
knn_reg = KNeighborsRegressor()
gb_reg = GradientBoostingRegressor(random_state=42)

# Train classification models
classification_models = {
    "Logistic Regression": log_reg,
    "Decision Tree": dt_class,
    "Random Forest": rf_class,
    "SVM": svm_class,
    "KNN": knn_class,
    "Naive Bayes": nb_class
}

for name, model in classification_models.items():
    model.fit(X_class_train_prep, y_class_train)
    print(f"{name} trained")

# Train regression models
regression_models = {
    "Linear Regression": lin_reg,
    "Decision Tree": dt_reg,
    "Random Forest": rf_reg,
    "SVM": svm_reg,
    "KNN": knn_reg,
    "Gradient Boosting": gb_reg
}

for name, model in regression_models.items():
    model.fit(X_reg_train_prep, y_reg_train)
    print(f"{name} trained")
```

## 4. Model Evaluation <a name="evaluation"></a>

```python
from sklearn.metrics import (
    accuracy_score, precision_score, recall_score, f1_score, roc_auc_score,
    confusion_matrix, classification_report, mean_squared_error, r2_score
)

# Classification evaluation
print("Classification Results:")
for name, model in classification_models.items():
    y_pred = model.predict(X_class_test_prep)
    y_proba = model.predict_proba(X_class_test_prep)[:, 1] if hasattr(model, "predict_proba") else None
    
    print(f"\n{name}:")
    print(f"Accuracy: {accuracy_score(y_class_test, y_pred):.4f}")
    print(f"Precision (macro): {precision_score(y_class_test, y_pred, average='macro'):.4f}")
    print(f"Recall (macro): {recall_score(y_class_test, y_pred, average='macro'):.4f}")
    print(f"F1 (macro): {f1_score(y_class_test, y_pred, average='macro'):.4f}")
    
    if y_proba is not None:
        print(f"ROC AUC (OvR): {roc_auc_score(y_class_test, y_proba, multi_class='ovr'):.4f}")
    
    print("\nClassification Report:")
    print(classification_report(y_class_test, y_pred))

# Regression evaluation
print("\nRegression Results:")
for name, model in regression_models.items():
    y_pred = model.predict(X_reg_test_prep)
    
    print(f"\n{name}:")
    print(f"MSE: {mean_squared_error(y_reg_test, y_pred):.4f}")
    print(f"RMSE: {np.sqrt(mean_squared_error(y_reg_test, y_pred)):.4f}")
    print(f"R2: {r2_score(y_reg_test, y_pred):.4f}")

# Plot feature importance for Random Forest
plt.figure(figsize=(12, 5))
plt.subplot(1, 2, 1)
importances = rf_class.feature_importances_
indices = np.argsort(importances)[::-1]
plt.title("Classification Feature Importance")
plt.bar(range(X_class_train_prep.shape[1]), importances[indices])
plt.xticks(range(X_class_train_prep.shape[1]), 
           [iris.feature_names[i] for i in indices], rotation=90)

plt.subplot(1, 2, 2)
importances = rf_reg.feature_importances_
indices = np.argsort(importances)[::-1]
plt.title("Regression Feature Importance")
plt.bar(range(X_reg_train_prep.shape[1]), importances[indices])
plt.xticks(range(X_reg_train_prep.shape[1]), 
           housing.feature_names[indices], rotation=90)
plt.tight_layout()
plt.show()
```

## 5. Hyperparameter Tuning <a name="tuning"></a>

```python
from sklearn.model_selection import GridSearchCV, RandomizedSearchCV
from scipy.stats import randint, uniform

# Tune Random Forest Classifier
param_grid = {
    'n_estimators': [50, 100, 200],
    'max_depth': [None, 10, 20, 30],
    'min_samples_split': [2, 5, 10],
    'min_samples_leaf': [1, 2, 4]
}

rf_grid = GridSearchCV(
    RandomForestClassifier(random_state=42),
    param_grid=param_grid,
    cv=5,
    scoring='accuracy',
    n_jobs=-1
)

rf_grid.fit(X_class_train_prep, y_class_train)

print("Best parameters for Random Forest Classifier:")
print(rf_grid.best_params_)
print(f"Best accuracy: {rf_grid.best_score_:.4f}")

# Tune Gradient Boosting Regressor with Randomized Search
param_dist = {
    'n_estimators': randint(50, 500),
    'learning_rate': uniform(0.01, 0.3),
    'max_depth': randint(1, 10),
    'min_samples_split': randint(2, 20),
    'min_samples_leaf': randint(1, 10)
}

gb_random = RandomizedSearchCV(
    GradientBoostingRegressor(random_state=42),
    param_distributions=param_dist,
    n_iter=50,
    cv=5,
    scoring='neg_mean_squared_error',
    random_state=42,
    n_jobs=-1
)

gb_random.fit(X_reg_train_prep, y_reg_train)

print("\nBest parameters for Gradient Boosting Regressor:")
print(gb_random.best_params_)
print(f"Best RMSE: {np.sqrt(-gb_random.best_score_):.4f}")
```

## 6. Model Deployment <a name="deployment"></a>

```python
import joblib
from sklearn.pipeline import make_pipeline

# Create complete pipelines with preprocessing and best models
best_class_pipeline = make_pipeline(
    class_preprocessor,
    rf_grid.best_estimator_
)

best_reg_pipeline = make_pipeline(
    reg_preprocessor,
    gb_random.best_estimator_
)

# Save the models
joblib.dump(best_class_pipeline, 'iris_classifier.pkl')
joblib.dump(best_reg_pipeline, 'housing_regressor.pkl')

# Example of loading and using the model
loaded_classifier = joblib.load('iris_classifier.pkl')
new_iris_data = [[5.1, 3.5, 1.4, 0.2]]  # Example new data
prediction = loaded_classifier.predict(new_iris_data)
print(f"\nPredicted class for new iris data: {iris.target_names[prediction[0]]}")

# Create a simple Flask API (example - would need Flask installed)
"""
from flask import Flask, request, jsonify
app = Flask(__name__)

@app.route('/predict_iris', methods=['POST'])
def predict_iris():
    data = request.json
    features = [data['sepal_length'], data['sepal_width'], 
                data['petal_length'], data['petal_width']]
    prediction = loaded_classifier.predict([features])[0]
    return jsonify({'class': iris.target_names[prediction]})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
"""
```

## Summary

This comprehensive project demonstrates:
1. Loading and exploring datasets
2. Data preprocessing with pipelines
3. Training multiple machine learning models
4. Evaluating models with appropriate metrics
5. Hyperparameter tuning with GridSearch and RandomizedSearch
6. Saving models and preparing for deployment

The project covers both classification and regression tasks, providing a template that can be adapted for various machine learning problems.