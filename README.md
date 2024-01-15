# Carlos T Portfolio
This will showcase my public files. 
Hi and welcome to my portfolio page. Here you will find examples of some of the projects I have managed and implemented. 


{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "DtNBZFHO3M7n"
   },
   "source": [
    "# **Waze Project**\n",
    "**Course 6 - The nuts and bolts of machine learning**"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "XfCZ5KuI_2lT"
   },
   "source": [
    "Your team is close to completing their user churn project. Previously, you completed a project proposal, and used Python to explore and analyze Waze’s user data, create data visualizations, and conduct a hypothesis test. Most recently, you built a binomial logistic regression model based on multiple variables.\n",
    "\n",
    "Leadership appreciates all your hard work. Now, they want your team to build a machine learning model to predict user churn. To get the best results, your team decides to build and test two tree-based models: random forest and XGBoost.\n",
    "\n",
    "Your work will help leadership make informed business decisions to prevent user churn, improve user retention, and grow Waze’s business.\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "rgSbVJvomcVa"
   },
   "source": [
    "# **Course 6 End-of-Course Project: Build a machine learning model**\n",
    "\n",
    "In this activity, you will practice using tree-based modeling techniques to predict on a binary target class.\n",
    "<br/>\n",
    "\n",
    "**The purpose** of this model is to find factors that drive user churn.\n",
    "\n",
    "**The goal** of this model is to predict whether or not a Waze user is retained or churned.\n",
    "<br/>\n",
    "\n",
    "*This activity has three parts:*\n",
    "\n",
    "**Part 1:** Ethical considerations\n",
    "* Consider the ethical implications of the request\n",
    "\n",
    "* Should the objective of the model be adjusted?\n",
    "\n",
    "**Part 2:** Feature engineering\n",
    "\n",
    "* Perform feature selection, extraction, and transformation to prepare the data for modeling\n",
    "\n",
    "**Part 3:** Modeling\n",
    "\n",
    "* Build the models, evaluate them, and advise on next steps\n",
    "\n",
    "Follow the instructions and answer the questions below to complete the activity. Then, you will complete an Executive Summary using the questions listed on the PACE Strategy Document.\n",
    "\n",
    "Be sure to complete this activity before moving on. The next course item will provide you with a completed exemplar to compare to your own work.\n",
    "\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "SsrI9g32nrAs"
   },
   "source": [
    "# **Build a machine learning model**\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "NzDjfCSLf6Jq"
   },
   "source": [
    "<img src=\"images/Pace.png\" width=\"100\" height=\"100\" align=left>\n",
    "\n",
    "# **PACE stages**\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "R8kJRDEKn4A-"
   },
   "source": [
    "Throughout these project notebooks, you'll see references to the problem-solving framework PACE. The following notebook components are labeled with the respective PACE stage: Plan, Analyze, Construct, and Execute."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "E5g1A74r0ow_"
   },
   "source": [
    "<img src=\"images/Plan.png\" width=\"100\" height=\"100\" align=left>\n",
    "\n",
    "\n",
    "## **PACE: Plan**\n",
    "\n",
    "Consider the questions in your PACE Strategy Document to reflect on the Plan stage.\n",
    "\n",
    "In this stage, consider the following questions:\n",
    "\n",
    "1.   What are you being asked to do?\n",
    "\n",
    "\n",
    "2.   What are the ethical implications of the model? What are the consequences of your model making errors?\n",
    "  *   What is the likely effect of the model when it predicts a false negative (i.e., when the model says a Waze user won't churn, but they actually will)?\n",
    "  *   What is the likely effect of the model when it predicts a false positive (i.e., when the model says a Waze user will churn, but they actually won't)?\n",
    "\n",
    "3.  Do the benefits of such a model outweigh the potential problems?\n",
    "4.  Would you proceed with the request to build this model? Why or why not?\n",
    "\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "y755T4Q18iwC"
   },
   "source": [
    "1) Will the customer churn\n",
    "2) Will the model be accurate? Will there be any identifuable data? will you exclude any discrimanatory information? Will it cost more money to retain customers of the model is not accurate? \n",
    "3) You won't know until you test the model. \n",
    "4) Yes, as long as you keep in mind any ethical rules, you should proceed. Just becuase you build a model, does not mean you have to use it"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "e8Vm3QEfGELS"
   },
   "source": [
    "### **Task 1. Imports and data loading**\n",
    "\n",
    "Import packages and libraries needed to build and evaluate random forest and XGBoost classification models."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {
    "id": "fKhnX2Puf4Bt"
   },
   "outputs": [],
   "source": [
    "# Import packages for data manipulation\n",
    "import numpy as np\n",
    "import pandas as pd\n",
    "\n",
    "# Import packages for data visualization\n",
    "import matplotlib.pyplot as plt\n",
    "\n",
    "# This lets us see all of the columns, preventing Juptyer from redacting them.\n",
    "pd.set_option('display.max_columns', None)\n",
    "\n",
    "# Import packages for data modeling\n",
    "from sklearn.model_selection import GridSearchCV, train_test_split\n",
    "from sklearn.metrics import roc_auc_score, roc_curve, auc\n",
    "from sklearn.metrics import accuracy_score, precision_score, recall_score,\\\n",
    "f1_score, confusion_matrix, ConfusionMatrixDisplay, RocCurveDisplay, PrecisionRecallDisplay\n",
    "\n",
    "from sklearn.ensemble import RandomForestClassifier\n",
    "from xgboost import XGBClassifier\n",
    "\n",
    "# This is the function that helps plot feature importance\n",
    "from xgboost import plot_importance\n",
    "\n",
    "# This module lets us save our models once we fit them.\n",
    "import pickle"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "IeXTZ2tdbALL"
   },
   "source": [
    "Now read in the dataset as `df0` and inspect the first five rows.\n",
    "\n",
    "**Note:** As shown in this cell, the dataset has been automatically loaded in for you. You do not need to download the .csv file, or provide more code, in order to access the dataset and proceed with this lab. Please continue with this activity by completing the following instructions."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {
    "id": "5weTXGKqa_iG"
   },
   "outputs": [],
   "source": [
    "# Import dataset\n",
    "df0 = pd.read_csv('waze_dataset.csv')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {
    "id": "1HyORSaQo_LU"
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>ID</th>\n",
       "      <th>label</th>\n",
       "      <th>sessions</th>\n",
       "      <th>drives</th>\n",
       "      <th>total_sessions</th>\n",
       "      <th>n_days_after_onboarding</th>\n",
       "      <th>total_navigations_fav1</th>\n",
       "      <th>total_navigations_fav2</th>\n",
       "      <th>driven_km_drives</th>\n",
       "      <th>duration_minutes_drives</th>\n",
       "      <th>activity_days</th>\n",
       "      <th>driving_days</th>\n",
       "      <th>device</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0</td>\n",
       "      <td>retained</td>\n",
       "      <td>283</td>\n",
       "      <td>226</td>\n",
       "      <td>296.748273</td>\n",
       "      <td>2276</td>\n",
       "      <td>208</td>\n",
       "      <td>0</td>\n",
       "      <td>2628.845068</td>\n",
       "      <td>1985.775061</td>\n",
       "      <td>28</td>\n",
       "      <td>19</td>\n",
       "      <td>Android</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>retained</td>\n",
       "      <td>133</td>\n",
       "      <td>107</td>\n",
       "      <td>326.896596</td>\n",
       "      <td>1225</td>\n",
       "      <td>19</td>\n",
       "      <td>64</td>\n",
       "      <td>13715.920550</td>\n",
       "      <td>3160.472914</td>\n",
       "      <td>13</td>\n",
       "      <td>11</td>\n",
       "      <td>iPhone</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>2</td>\n",
       "      <td>retained</td>\n",
       "      <td>114</td>\n",
       "      <td>95</td>\n",
       "      <td>135.522926</td>\n",
       "      <td>2651</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>3059.148818</td>\n",
       "      <td>1610.735904</td>\n",
       "      <td>14</td>\n",
       "      <td>8</td>\n",
       "      <td>Android</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>3</td>\n",
       "      <td>retained</td>\n",
       "      <td>49</td>\n",
       "      <td>40</td>\n",
       "      <td>67.589221</td>\n",
       "      <td>15</td>\n",
       "      <td>322</td>\n",
       "      <td>7</td>\n",
       "      <td>913.591123</td>\n",
       "      <td>587.196542</td>\n",
       "      <td>7</td>\n",
       "      <td>3</td>\n",
       "      <td>iPhone</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>4</td>\n",
       "      <td>retained</td>\n",
       "      <td>84</td>\n",
       "      <td>68</td>\n",
       "      <td>168.247020</td>\n",
       "      <td>1562</td>\n",
       "      <td>166</td>\n",
       "      <td>5</td>\n",
       "      <td>3950.202008</td>\n",
       "      <td>1219.555924</td>\n",
       "      <td>27</td>\n",
       "      <td>18</td>\n",
       "      <td>Android</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>5</th>\n",
       "      <td>5</td>\n",
       "      <td>retained</td>\n",
       "      <td>113</td>\n",
       "      <td>103</td>\n",
       "      <td>279.544437</td>\n",
       "      <td>2637</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>901.238699</td>\n",
       "      <td>439.101397</td>\n",
       "      <td>15</td>\n",
       "      <td>11</td>\n",
       "      <td>iPhone</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>6</th>\n",
       "      <td>6</td>\n",
       "      <td>retained</td>\n",
       "      <td>3</td>\n",
       "      <td>2</td>\n",
       "      <td>236.725314</td>\n",
       "      <td>360</td>\n",
       "      <td>185</td>\n",
       "      <td>18</td>\n",
       "      <td>5249.172828</td>\n",
       "      <td>726.577205</td>\n",
       "      <td>28</td>\n",
       "      <td>23</td>\n",
       "      <td>iPhone</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7</th>\n",
       "      <td>7</td>\n",
       "      <td>retained</td>\n",
       "      <td>39</td>\n",
       "      <td>35</td>\n",
       "      <td>176.072845</td>\n",
       "      <td>2999</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>7892.052468</td>\n",
       "      <td>2466.981741</td>\n",
       "      <td>22</td>\n",
       "      <td>20</td>\n",
       "      <td>iPhone</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>8</th>\n",
       "      <td>8</td>\n",
       "      <td>retained</td>\n",
       "      <td>57</td>\n",
       "      <td>46</td>\n",
       "      <td>183.532018</td>\n",
       "      <td>424</td>\n",
       "      <td>0</td>\n",
       "      <td>26</td>\n",
       "      <td>2651.709764</td>\n",
       "      <td>1594.342984</td>\n",
       "      <td>25</td>\n",
       "      <td>20</td>\n",
       "      <td>Android</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9</th>\n",
       "      <td>9</td>\n",
       "      <td>churned</td>\n",
       "      <td>84</td>\n",
       "      <td>68</td>\n",
       "      <td>244.802115</td>\n",
       "      <td>2997</td>\n",
       "      <td>72</td>\n",
       "      <td>0</td>\n",
       "      <td>6043.460295</td>\n",
       "      <td>2341.838528</td>\n",
       "      <td>7</td>\n",
       "      <td>3</td>\n",
       "      <td>iPhone</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   ID     label  sessions  drives  total_sessions  n_days_after_onboarding  \\\n",
       "0   0  retained       283     226      296.748273                     2276   \n",
       "1   1  retained       133     107      326.896596                     1225   \n",
       "2   2  retained       114      95      135.522926                     2651   \n",
       "3   3  retained        49      40       67.589221                       15   \n",
       "4   4  retained        84      68      168.247020                     1562   \n",
       "5   5  retained       113     103      279.544437                     2637   \n",
       "6   6  retained         3       2      236.725314                      360   \n",
       "7   7  retained        39      35      176.072845                     2999   \n",
       "8   8  retained        57      46      183.532018                      424   \n",
       "9   9   churned        84      68      244.802115                     2997   \n",
       "\n",
       "   total_navigations_fav1  total_navigations_fav2  driven_km_drives  \\\n",
       "0                     208                       0       2628.845068   \n",
       "1                      19                      64      13715.920550   \n",
       "2                       0                       0       3059.148818   \n",
       "3                     322                       7        913.591123   \n",
       "4                     166                       5       3950.202008   \n",
       "5                       0                       0        901.238699   \n",
       "6                     185                      18       5249.172828   \n",
       "7                       0                       0       7892.052468   \n",
       "8                       0                      26       2651.709764   \n",
       "9                      72                       0       6043.460295   \n",
       "\n",
       "   duration_minutes_drives  activity_days  driving_days   device  \n",
       "0              1985.775061             28            19  Android  \n",
       "1              3160.472914             13            11   iPhone  \n",
       "2              1610.735904             14             8  Android  \n",
       "3               587.196542              7             3   iPhone  \n",
       "4              1219.555924             27            18  Android  \n",
       "5               439.101397             15            11   iPhone  \n",
       "6               726.577205             28            23   iPhone  \n",
       "7              2466.981741             22            20   iPhone  \n",
       "8              1594.342984             25            20  Android  \n",
       "9              2341.838528              7             3   iPhone  "
      ]
     },
     "execution_count": 3,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Inspect the first five rows\n",
    "\n",
    "df0.head(10)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "EgPRBjizg1oo"
   },
   "source": [
    "<img src=\"images/Analyze.png\" width=\"100\" height=\"100\" align=left>\n",
    "\n",
    "## **PACE: Analyze**\n",
    "Consider the questions in your PACE Strategy Document to reflect on the Analyze stage."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "5VZowX9rhU1o"
   },
   "source": [
    "### **Task 2. Feature engineering**\n",
    "\n",
    "You have already prepared much of this data and performed exploratory data analysis (EDA) in previous courses. You know that some features had stronger correlations with churn than others, and you also created some features that may be useful.\n",
    "\n",
    "In this part of the project, you'll engineer these features and some new features to use for modeling.\n",
    "\n",
    "To begin, create a copy of `df0` to preserve the original dataframe. Call the copy `df`."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {
    "id": "mBOSW8IDbO_d"
   },
   "outputs": [],
   "source": [
    "# Copy the df0 dataframe\n",
    "\n",
    "df = df0.copy()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "fTgC9H_tt-G2"
   },
   "source": [
    "Call `info()` on the new dataframe so the existing columns can be easily referenced."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {
    "id": "teUeCF-yf_6o"
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "<class 'pandas.core.frame.DataFrame'>\n",
      "RangeIndex: 14999 entries, 0 to 14998\n",
      "Data columns (total 13 columns):\n",
      " #   Column                   Non-Null Count  Dtype  \n",
      "---  ------                   --------------  -----  \n",
      " 0   ID                       14999 non-null  int64  \n",
      " 1   label                    14299 non-null  object \n",
      " 2   sessions                 14999 non-null  int64  \n",
      " 3   drives                   14999 non-null  int64  \n",
      " 4   total_sessions           14999 non-null  float64\n",
      " 5   n_days_after_onboarding  14999 non-null  int64  \n",
      " 6   total_navigations_fav1   14999 non-null  int64  \n",
      " 7   total_navigations_fav2   14999 non-null  int64  \n",
      " 8   driven_km_drives         14999 non-null  float64\n",
      " 9   duration_minutes_drives  14999 non-null  float64\n",
      " 10  activity_days            14999 non-null  int64  \n",
      " 11  driving_days             14999 non-null  int64  \n",
      " 12  device                   14999 non-null  object \n",
      "dtypes: float64(3), int64(8), object(2)\n",
      "memory usage: 1.5+ MB\n"
     ]
    }
   ],
   "source": [
    "df.info()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "BPtJEHjcuepR"
   },
   "source": [
    "#### **`km_per_driving_day`**\n",
    "\n",
    "1. Create a feature representing the mean number of kilometers driven on each driving day in the last month for each user. Add this feature as a column to `df`.\n",
    "\n",
    "2. Get descriptive statistics for this new feature\n",
    "\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {
    "id": "vAB6cv6xfvZn"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "count    1.499900e+04\n",
       "mean              inf\n",
       "std               NaN\n",
       "min      3.022063e+00\n",
       "25%      1.672804e+02\n",
       "50%      3.231459e+02\n",
       "75%      7.579257e+02\n",
       "max               inf\n",
       "Name: km_per_driving_day, dtype: float64"
      ]
     },
     "execution_count": 6,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# 1. Create `km_per_driving_day` feature\n",
    "df['km_per_driving_day'] = df['driven_km_drives'] / df['driving_days']\n",
    "\n",
    "# 2. Get descriptive stats\n",
    "df['km_per_driving_day'].describe()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "MSqM4oiyuuzw"
   },
   "source": [
    "Notice that some values are infinite. This is the result of there being values of zero in the `driving_days` column. Pandas imputes a value of infinity in the corresponding rows of the new column because division by zero is undefined.\n",
    "\n",
    "1. Convert these values from infinity to zero. You can use `np.inf` to refer to a value of infinity.\n",
    "\n",
    "2. Call `describe()` on the `km_per_driving_day` column to verify that it worked."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "metadata": {
    "id": "vv3owriWuuDQ"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "count    14999.000000\n",
       "mean       578.963113\n",
       "std       1030.094384\n",
       "min          0.000000\n",
       "25%        136.238895\n",
       "50%        272.889272\n",
       "75%        558.686918\n",
       "max      15420.234110\n",
       "Name: km_per_driving_day, dtype: float64"
      ]
     },
     "execution_count": 7,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# 1. Convert infinite values to zero\n",
    "df.loc[df['km_per_driving_day']==np.inf, 'km_per_driving_day'] = 0\n",
    "\n",
    "# 2. Confirm that it worked\n",
    "df['km_per_driving_day'].describe()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "nZfNE37b-LlJ"
   },
   "source": [
    "#### **`percent_sessions_in_last_month`**\n",
    "\n",
    "1. Create a new column `percent_sessions_in_last_month` that represents the percentage of each user's total sessions that were logged in their last month of use.\n",
    "\n",
    "2. Get descriptive statistics for this new feature"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 8,
   "metadata": {
    "id": "4mRefXCF-K_c"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "count    14999.000000\n",
       "mean         0.449255\n",
       "std          0.286919\n",
       "min          0.000000\n",
       "25%          0.196221\n",
       "50%          0.423097\n",
       "75%          0.687216\n",
       "max          1.530637\n",
       "Name: percent_sessions_in_last_month, dtype: float64"
      ]
     },
     "execution_count": 8,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# 1. Create `percent_sessions_in_last_month` feature\n",
    "df['percent_sessions_in_last_month'] = df['sessions'] / df['total_sessions']\n",
    "\n",
    "# 2. Get descriptive stats\n",
    "df['percent_sessions_in_last_month'].describe()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "TjgkLrOf_OrE"
   },
   "source": [
    "#### **`professional_driver`**\n",
    "\n",
    "Create a new, binary feature called `professional_driver` that is a 1 for users who had 60 or more drives <u>**and**</u> drove on 15+ days in the last month.\n",
    "\n",
    "**Note:** The objective is to create a new feature that separates professional drivers from other drivers. In this scenario, domain knowledge and intuition are used to determine these deciding thresholds, but ultimately they are arbitrary."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "L5UK5jR6U9t1"
   },
   "source": [
    "To create this column, use the [`np.where()`](https://numpy.org/doc/stable/reference/generated/numpy.where.html) function. This function accepts as arguments:\n",
    "1. A condition\n",
    "2. What to return when the condition is true\n",
    "3. What to return when the condition is false\n",
    "\n",
    "```\n",
    "Example:\n",
    "x = [1, 2, 3]\n",
    "x = np.where(x > 2, 100, 0)\n",
    "x\n",
    "array([  0,   0, 100])\n",
    "```"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 9,
   "metadata": {
    "id": "dQdMgikKU-5T"
   },
   "outputs": [],
   "source": [
    "# Create `professional_driver` feature\n",
    "df['professional_driver'] = np.where((df['drives'] >= 60) & (df['driving_days'] >= 15), 1, 0)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "J3bWzofHVIuC"
   },
   "source": [
    "#### **`total_sessions_per_day`**\n",
    "\n",
    "Now, create a new column that represents the mean number of sessions per day _since onboarding_."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "metadata": {
    "id": "bWXPMPHSVJQd"
   },
   "outputs": [],
   "source": [
    "# Create `total_sessions_per_day` feature\n",
    "df['total_sessions_per_day'] = df['total_sessions'] / df['n_days_after_onboarding']"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "8HLX7SwJVJlO"
   },
   "source": [
    "As with other features, get descriptive statistics for this new feature."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "metadata": {
    "id": "h1DFSMNSVKEg"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "count    14999.000000\n",
       "mean         0.338698\n",
       "std          1.314333\n",
       "min          0.000298\n",
       "25%          0.051037\n",
       "50%          0.100775\n",
       "75%          0.216269\n",
       "max         39.763874\n",
       "Name: total_sessions_per_day, dtype: float64"
      ]
     },
     "execution_count": 11,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Get descriptive stats\n",
    "df['total_sessions_per_day'].describe()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "c6sCAgOoVZM7"
   },
   "source": [
    "#### **`km_per_hour`**\n",
    "\n",
    "Create a column representing the mean kilometers per hour driven in the last month."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "metadata": {
    "id": "Zu142H3aVc3o"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "count    14999.000000\n",
       "mean       190.394608\n",
       "std        334.674026\n",
       "min         72.013095\n",
       "25%         90.706222\n",
       "50%        122.382022\n",
       "75%        193.130119\n",
       "max      23642.920871\n",
       "Name: km_per_hour, dtype: float64"
      ]
     },
     "execution_count": 12,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Create `km_per_hour` feature\n",
    "df['km_per_hour'] = df['driven_km_drives'] / (df['duration_minutes_drives'] / 60)\n",
    "df['km_per_hour'].describe()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "8d6N9jf8ViW-"
   },
   "source": [
    "#### **`km_per_drive`**\n",
    "\n",
    "Create a column representing the mean number of kilometers per drive made in the last month for each user. Then, print descriptive statistics for the feature."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "metadata": {
    "id": "v5R5-MteVlMB"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "count    1.499900e+04\n",
       "mean              inf\n",
       "std               NaN\n",
       "min      1.008775e+00\n",
       "25%      3.323065e+01\n",
       "50%      7.488006e+01\n",
       "75%      1.854667e+02\n",
       "max               inf\n",
       "Name: km_per_drive, dtype: float64"
      ]
     },
     "execution_count": 13,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Create `km_per_drive` feature\n",
    "df['km_per_drive'] = df['driven_km_drives'] / df['drives']\n",
    "df['km_per_drive'].describe()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "txY8qR1LVlq1"
   },
   "source": [
    "This feature has infinite values too. Convert the infinite values to zero, then confirm that it worked."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "metadata": {
    "id": "PZrHMuPuVmIt"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "count    14999.000000\n",
       "mean       232.817946\n",
       "std        620.622351\n",
       "min          0.000000\n",
       "25%         32.424301\n",
       "50%         72.854343\n",
       "75%        179.347527\n",
       "max      15777.426560\n",
       "Name: km_per_drive, dtype: float64"
      ]
     },
     "execution_count": 14,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# 1. Convert infinite values to zero\n",
    "df.loc[df['km_per_drive']==np.inf, 'km_per_drive'] = 0\n",
    "\n",
    "# 2. Confirm that it worked\n",
    "df['km_per_drive'].describe()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "O5Sxs6agVunA"
   },
   "source": [
    "#### **`percent_of_sessions_to_favorite`**\n",
    "\n",
    "Finally, create a new column that represents the percentage of total sessions that were used to navigate to one of the users' favorite places. Then, print descriptive statistics for the new column.\n",
    "\n",
    "This is a proxy representation for the percent of overall drives that are to a favorite place. Since total drives since onboarding are not contained in this dataset, total sessions must serve as a reasonable approximation.\n",
    "\n",
    "People whose drives to non-favorite places make up a higher percentage of their total drives might be less likely to churn, since they're making more drives to less familiar places."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "metadata": {
    "id": "vh22o46AVxd_"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "count    14999.000000\n",
       "mean         1.665439\n",
       "std          8.865666\n",
       "min          0.000000\n",
       "25%          0.203471\n",
       "50%          0.649818\n",
       "75%          1.638526\n",
       "max        777.563629\n",
       "Name: percent_of_drives_to_favorite, dtype: float64"
      ]
     },
     "execution_count": 15,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Create `percent_of_sessions_to_favorite` feature\n",
    "df['percent_of_drives_to_favorite'] = (\n",
    "    df['total_navigations_fav1'] + df['total_navigations_fav2']) / df['total_sessions']\n",
    "\n",
    "# Get descriptive stats\n",
    "df['percent_of_drives_to_favorite'].describe()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "IZO0mvHRWGmF"
   },
   "source": [
    "### **Task 3. Drop missing values**\n",
    "\n",
    "Because you know from previous EDA that there is no evidence of a non-random cause of the 700 missing values in the `label` column, and because these observations comprise less than 5% of the data, use the `dropna()` method to drop the rows that are missing this data."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "metadata": {
    "id": "2TdA6SnGWJY-"
   },
   "outputs": [],
   "source": [
    "# Drop rows with missing values\n",
    "df = df.dropna(subset=['label'])"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "Du5kGt5CWJ4J"
   },
   "source": [
    "### **Task 4. Outliers**\n",
    "\n",
    "You know from previous EDA that many of these columns have outliers. However, tree-based models are resilient to outliers, so there is no need to make any imputations."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "kxBYyXDSWPkw"
   },
   "source": [
    "### **Task 5. Variable encoding**"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "57psLWIeaTk2"
   },
   "source": [
    "#### **Dummying features**\n",
    "\n",
    "In order to use `device` as an X variable, you will need to convert it to binary, since this variable is categorical.\n",
    "\n",
    "In cases where the data contains many categorical variables, you can use pandas built-in [`pd.get_dummies()`](https://pandas.pydata.org/docs/reference/api/pandas.get_dummies.html), or you can use scikit-learn's [`OneHotEncoder()`](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html) function.\n",
    "\n",
    "**Note:** Each possible category of each feature will result in a feature for your model, which could lead to an inadequate ratio of features to observations and/or difficulty understanding your model's predictions.\n",
    "\n",
    "Because this dataset only has one remaining categorical feature (`device`), it's not necessary to use one of these special functions. You can just implement the transformation directly.\n",
    "\n",
    "Create a new, binary column called `device2` that encodes user devices as follows:\n",
    "\n",
    "* `Android` -> `0`\n",
    "* `iPhone` -> `1`"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 17,
   "metadata": {
    "id": "fntUcR4-aUfH"
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>device</th>\n",
       "      <th>device2</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>14994</th>\n",
       "      <td>iPhone</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>14995</th>\n",
       "      <td>Android</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>14996</th>\n",
       "      <td>iPhone</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>14997</th>\n",
       "      <td>iPhone</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>14998</th>\n",
       "      <td>iPhone</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "        device  device2\n",
       "14994   iPhone        1\n",
       "14995  Android        0\n",
       "14996   iPhone        1\n",
       "14997   iPhone        1\n",
       "14998   iPhone        1"
      ]
     },
     "execution_count": 17,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Create new `device2` variable\n",
    "df['device2'] = np.where(df['device']=='Android', 0, 1)\n",
    "df[['device', 'device2']].tail()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "LgbEm7cOb6t8"
   },
   "source": [
    "#### **Target encoding**\n",
    "\n",
    "The target variable is also categorical, since a user is labeled as either \"churned\" or \"retained.\" Change the data type of the `label` column to be binary. This change is needed to train the models.\n",
    "\n",
    "Assign a `0` for all `retained` users.\n",
    "\n",
    "Assign a `1` for all `churned` users.\n",
    "\n",
    "Save this variable as `label2` so as not to overwrite the original `label` variable.\n",
    "\n",
    "**Note:** There are many ways to do this. Consider using `np.where()` as you did earlier in this notebook."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 18,
   "metadata": {
    "id": "0jiVjplLb8W-"
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>label</th>\n",
       "      <th>label2</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>14994</th>\n",
       "      <td>retained</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>14995</th>\n",
       "      <td>retained</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>14996</th>\n",
       "      <td>retained</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>14997</th>\n",
       "      <td>churned</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>14998</th>\n",
       "      <td>retained</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "          label  label2\n",
       "14994  retained       0\n",
       "14995  retained       0\n",
       "14996  retained       0\n",
       "14997   churned       1\n",
       "14998  retained       0"
      ]
     },
     "execution_count": 18,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Create binary `label2` column\n",
    "df['label2'] = np.where(df['label']=='churned', 1, 0)\n",
    "df[['label', 'label2']].tail()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "fD_zG59eaV2c"
   },
   "source": [
    "### **Task 6. Feature selection**\n",
    "\n",
    "Tree-based models can handle multicollinearity, so the only feature that can be cut is `ID`, since it doesn't contain any information relevant to churn.\n",
    "\n",
    "Note, however, that `device` won't be used simply because it's a copy of `device2`.\n",
    "\n",
    "Drop `ID` from the `df` dataframe."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 19,
   "metadata": {
    "id": "kf3uGtUQaWSL"
   },
   "outputs": [],
   "source": [
    "# Drop `ID` column\n",
    "df = df.drop(['ID'], axis=1)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "ajj50RCCaXrF"
   },
   "source": [
    "### **Task 7. Evaluation metric**\n",
    "\n",
    "Before modeling, you must decide on an evaluation metric. This will depend on the class balance of the target variable and the use case of the model.\n",
    "\n",
    "First, examine the class balance of your target variable."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 20,
   "metadata": {
    "id": "3JkjEYByaYbr"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "label\n",
       "retained    0.822645\n",
       "churned     0.177355\n",
       "Name: proportion, dtype: float64"
      ]
     },
     "execution_count": 20,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Get class balance of 'label' col\n",
    "df['label'].value_counts(normalize=True)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "w9vnV1wtaZWJ"
   },
   "source": [
    "Approximately 18% of the users in this dataset churned. This is an unbalanced dataset, but not extremely so. It can be modeled without any class rebalancing.\n",
    "\n",
    "Now, consider which evaluation metric is best. Remember, accuracy might not be the best gauge of performance because a model can have high accuracy on an imbalanced dataset and still fail to predict the minority class.\n",
    "\n",
    "It was already determined that the risks involved in making a false positive prediction are minimal. No one stands to get hurt, lose money, or suffer any other significant consequence if they are predicted to churn. Therefore, select the model based on the recall score."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "3n1eikFh8akS"
   },
   "source": [
    "<img src=\"images/Construct.png\" width=\"100\" height=\"100\" align=left>\n",
    "\n",
    "## **PACE: Construct**\n",
    "Consider the questions in your PACE Strategy Document to reflect on the Construct stage."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "G5jzGjOS8iiv"
   },
   "source": [
    "### **Task 8. Modeling workflow and model selection process**\n",
    "\n",
    "The final modeling dataset contains 14,299 samples. This is towards the lower end of what might be considered sufficient to conduct a robust model selection process, but still doable.\n",
    "\n",
    "1. Split the data into train/validation/test sets (60/20/20)\n",
    "\n",
    "Note that, when deciding the split ratio and whether or not to use a validation set to select a champion model, consider both how many samples will be in each data partition, and how many examples of the minority class each would therefore contain. In this case, a 60/20/20 split would result in \\~2,860 samples in the validation set and the same number in the test set, of which \\~18%&mdash;or 515 samples&mdash;would represent users who churn.\n",
    "2. Fit models and tune hyperparameters on the training set\n",
    "3. Perform final model selection on the validation set\n",
    "4. Assess the champion model's performance on the test set\n",
    "\n",
    "![](https://raw.githubusercontent.com/adacert/tiktok/main/optimal_model_flow_numbered.svg)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "Nx41bVxX89Fe"
   },
   "source": [
    "### **Task 9. Split the data**\n",
    "\n",
    "Now you're ready to model. The only remaining step is to split the data into features/target variable and training/validation/test sets.\n",
    "\n",
    "1. Define a variable `X` that isolates the features. Remember not to use `device`.\n",
    "\n",
    "2. Define a variable `y` that isolates the target variable (`label2`).\n",
    "\n",
    "3. Split the data 80/20 into an interim training set and a test set. Don't forget to stratify the splits, and set the random state to 42.\n",
    "\n",
    "4. Split the interim training set 75/25 into a training set and a validation set, yielding a final ratio of 60/20/20 for training/validation/test sets. Again, don't forget to stratify the splits and set the random state."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 21,
   "metadata": {
    "id": "qLbapbSWDUL-"
   },
   "outputs": [],
   "source": [
    "# 1. Isolate X variables\n",
    "X = df.drop(columns=['label', 'label2', 'device'])\n",
    "\n",
    "# 2. Isolate y variable\n",
    "y = df['label2']\n",
    "\n",
    "# 3. Split into train and test sets\n",
    "X_tr, X_test, y_tr, y_test = train_test_split(X, y, stratify=y,\n",
    "                                              test_size=0.2, random_state=42)\n",
    "\n",
    "# 4. Split into train and validate sets\n",
    "X_train, X_val, y_train, y_val = train_test_split(X_tr, y_tr, stratify=y_tr,\n",
    "                                                  test_size=0.25, random_state=42)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "moLls6Lech47"
   },
   "source": [
    "Verify the number of samples in the partitioned data."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 22,
   "metadata": {
    "id": "qWIog8v_ckIg"
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "8579\n",
      "2860\n",
      "2860\n"
     ]
    }
   ],
   "source": [
    "for x in [X_train, X_val, X_test]:\n",
    "    print(len(x))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "0x-4vGANcki4"
   },
   "source": [
    "This aligns with expectations."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "4vSaa0-xcu4Q"
   },
   "source": [
    "### **Task 10. Modeling**"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "vynZs5het1b_"
   },
   "source": [
    "#### **Random forest**\n",
    "\n",
    "Begin with using `GridSearchCV` to tune a random forest model.\n",
    "\n",
    "1. Instantiate the random forest classifier `rf` and set the random state.\n",
    "\n",
    "2. Create a dictionary `cv_params` of any of the following hyperparameters and their corresponding values to tune. The more you tune, the better your model will fit the data, but the longer it will take.\n",
    " - `max_depth`\n",
    " - `max_features`\n",
    " - `max_samples`\n",
    " - `min_samples_leaf`\n",
    " - `min_samples_split`\n",
    " - `n_estimators`\n",
    "\n",
    "3. Define a dictionary `scoring` of scoring metrics for GridSearch to capture (precision, recall, F1 score, and accuracy).\n",
    "\n",
    "4. Instantiate the `GridSearchCV` object `rf_cv`. Pass to it as arguments:\n",
    " - estimator=`rf`\n",
    " - param_grid=`cv_params`\n",
    " - scoring=`scoring`\n",
    " - cv: define the number of cross-validation folds you want (`cv=_`)\n",
    " - refit: indicate which evaluation metric you want to use to select the model (`refit=_`)\n",
    "\n",
    " `refit` should be set to `'recall'`.<font/>\n",
    "\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "vvpX9RDFc2MD"
   },
   "source": [
    "**Note:** To save time, this exemplar doesn't use multiple values for each parameter in the grid search, but you should include a range of values in your search to home in on the best set of parameters."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 23,
   "metadata": {
    "id": "Vj5rJWOv5O3d"
   },
   "outputs": [],
   "source": [
    "# 1. Instantiate the random forest classifier\n",
    "rf = RandomForestClassifier(random_state=42)\n",
    "\n",
    "# 2. Create a dictionary of hyperparameters to tune\n",
    "cv_params = {'max_depth': [None],\n",
    "             'max_features': [1.0],\n",
    "             'max_samples': [1.0],\n",
    "             'min_samples_leaf': [2],\n",
    "             'min_samples_split': [2],\n",
    "             'n_estimators': [300],\n",
    "             }\n",
    "\n",
    "# 3. Define a dictionary of scoring metrics to capture\n",
    "scoring = {'accuracy', 'precision', 'recall', 'f1'}\n",
    "\n",
    "# 4. Instantiate the GridSearchCV object\n",
    "rf_cv = GridSearchCV(rf, cv_params, scoring=scoring, cv=4, refit='recall')"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "Wv_WvRA1RqTl"
   },
   "source": [
    "Now fit the model to the training data."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 24,
   "metadata": {
    "id": "OXuBiTGi5ZHn"
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "CPU times: user 1min 56s, sys: 24.7 ms, total: 1min 56s\n",
      "Wall time: 1min 56s\n"
     ]
    },
    {
     "data": {
      "text/html": [
       "<style>#sk-container-id-1 {color: black;background-color: white;}#sk-container-id-1 pre{padding: 0;}#sk-container-id-1 div.sk-toggleable {background-color: white;}#sk-container-id-1 label.sk-toggleable__label {cursor: pointer;display: block;width: 100%;margin-bottom: 0;padding: 0.3em;box-sizing: border-box;text-align: center;}#sk-container-id-1 label.sk-toggleable__label-arrow:before {content: \"▸\";float: left;margin-right: 0.25em;color: #696969;}#sk-container-id-1 label.sk-toggleable__label-arrow:hover:before {color: black;}#sk-container-id-1 div.sk-estimator:hover label.sk-toggleable__label-arrow:before {color: black;}#sk-container-id-1 div.sk-toggleable__content {max-height: 0;max-width: 0;overflow: hidden;text-align: left;background-color: #f0f8ff;}#sk-container-id-1 div.sk-toggleable__content pre {margin: 0.2em;color: black;border-radius: 0.25em;background-color: #f0f8ff;}#sk-container-id-1 input.sk-toggleable__control:checked~div.sk-toggleable__content {max-height: 200px;max-width: 100%;overflow: auto;}#sk-container-id-1 input.sk-toggleable__control:checked~label.sk-toggleable__label-arrow:before {content: \"▾\";}#sk-container-id-1 div.sk-estimator input.sk-toggleable__control:checked~label.sk-toggleable__label {background-color: #d4ebff;}#sk-container-id-1 div.sk-label input.sk-toggleable__control:checked~label.sk-toggleable__label {background-color: #d4ebff;}#sk-container-id-1 input.sk-hidden--visually {border: 0;clip: rect(1px 1px 1px 1px);clip: rect(1px, 1px, 1px, 1px);height: 1px;margin: -1px;overflow: hidden;padding: 0;position: absolute;width: 1px;}#sk-container-id-1 div.sk-estimator {font-family: monospace;background-color: #f0f8ff;border: 1px dotted black;border-radius: 0.25em;box-sizing: border-box;margin-bottom: 0.5em;}#sk-container-id-1 div.sk-estimator:hover {background-color: #d4ebff;}#sk-container-id-1 div.sk-parallel-item::after {content: \"\";width: 100%;border-bottom: 1px solid gray;flex-grow: 1;}#sk-container-id-1 div.sk-label:hover label.sk-toggleable__label {background-color: #d4ebff;}#sk-container-id-1 div.sk-serial::before {content: \"\";position: absolute;border-left: 1px solid gray;box-sizing: border-box;top: 0;bottom: 0;left: 50%;z-index: 0;}#sk-container-id-1 div.sk-serial {display: flex;flex-direction: column;align-items: center;background-color: white;padding-right: 0.2em;padding-left: 0.2em;position: relative;}#sk-container-id-1 div.sk-item {position: relative;z-index: 1;}#sk-container-id-1 div.sk-parallel {display: flex;align-items: stretch;justify-content: center;background-color: white;position: relative;}#sk-container-id-1 div.sk-item::before, #sk-container-id-1 div.sk-parallel-item::before {content: \"\";position: absolute;border-left: 1px solid gray;box-sizing: border-box;top: 0;bottom: 0;left: 50%;z-index: -1;}#sk-container-id-1 div.sk-parallel-item {display: flex;flex-direction: column;z-index: 1;position: relative;background-color: white;}#sk-container-id-1 div.sk-parallel-item:first-child::after {align-self: flex-end;width: 50%;}#sk-container-id-1 div.sk-parallel-item:last-child::after {align-self: flex-start;width: 50%;}#sk-container-id-1 div.sk-parallel-item:only-child::after {width: 0;}#sk-container-id-1 div.sk-dashed-wrapped {border: 1px dashed gray;margin: 0 0.4em 0.5em 0.4em;box-sizing: border-box;padding-bottom: 0.4em;background-color: white;}#sk-container-id-1 div.sk-label label {font-family: monospace;font-weight: bold;display: inline-block;line-height: 1.2em;}#sk-container-id-1 div.sk-label-container {text-align: center;}#sk-container-id-1 div.sk-container {/* jupyter's `normalize.less` sets `[hidden] { display: none; }` but bootstrap.min.css set `[hidden] { display: none !important; }` so we also need the `!important` here to be able to override the default hidden behavior on the sphinx rendered scikit-learn.org. See: https://github.com/scikit-learn/scikit-learn/issues/21755 */display: inline-block !important;position: relative;}#sk-container-id-1 div.sk-text-repr-fallback {display: none;}</style><div id=\"sk-container-id-1\" class=\"sk-top-container\"><div class=\"sk-text-repr-fallback\"><pre>GridSearchCV(cv=4, estimator=RandomForestClassifier(random_state=42),\n",
       "             param_grid={&#x27;max_depth&#x27;: [None], &#x27;max_features&#x27;: [1.0],\n",
       "                         &#x27;max_samples&#x27;: [1.0], &#x27;min_samples_leaf&#x27;: [2],\n",
       "                         &#x27;min_samples_split&#x27;: [2], &#x27;n_estimators&#x27;: [300]},\n",
       "             refit=&#x27;recall&#x27;, scoring={&#x27;precision&#x27;, &#x27;recall&#x27;, &#x27;accuracy&#x27;, &#x27;f1&#x27;})</pre><b>In a Jupyter environment, please rerun this cell to show the HTML representation or trust the notebook. <br />On GitHub, the HTML representation is unable to render, please try loading this page with nbviewer.org.</b></div><div class=\"sk-container\" hidden><div class=\"sk-item sk-dashed-wrapped\"><div class=\"sk-label-container\"><div class=\"sk-label sk-toggleable\"><input class=\"sk-toggleable__control sk-hidden--visually\" id=\"sk-estimator-id-1\" type=\"checkbox\" ><label for=\"sk-estimator-id-1\" class=\"sk-toggleable__label sk-toggleable__label-arrow\">GridSearchCV</label><div class=\"sk-toggleable__content\"><pre>GridSearchCV(cv=4, estimator=RandomForestClassifier(random_state=42),\n",
       "             param_grid={&#x27;max_depth&#x27;: [None], &#x27;max_features&#x27;: [1.0],\n",
       "                         &#x27;max_samples&#x27;: [1.0], &#x27;min_samples_leaf&#x27;: [2],\n",
       "                         &#x27;min_samples_split&#x27;: [2], &#x27;n_estimators&#x27;: [300]},\n",
       "             refit=&#x27;recall&#x27;, scoring={&#x27;precision&#x27;, &#x27;recall&#x27;, &#x27;accuracy&#x27;, &#x27;f1&#x27;})</pre></div></div></div><div class=\"sk-parallel\"><div class=\"sk-parallel-item\"><div class=\"sk-item\"><div class=\"sk-label-container\"><div class=\"sk-label sk-toggleable\"><input class=\"sk-toggleable__control sk-hidden--visually\" id=\"sk-estimator-id-2\" type=\"checkbox\" ><label for=\"sk-estimator-id-2\" class=\"sk-toggleable__label sk-toggleable__label-arrow\">estimator: RandomForestClassifier</label><div class=\"sk-toggleable__content\"><pre>RandomForestClassifier(random_state=42)</pre></div></div></div><div class=\"sk-serial\"><div class=\"sk-item\"><div class=\"sk-estimator sk-toggleable\"><input class=\"sk-toggleable__control sk-hidden--visually\" id=\"sk-estimator-id-3\" type=\"checkbox\" ><label for=\"sk-estimator-id-3\" class=\"sk-toggleable__label sk-toggleable__label-arrow\">RandomForestClassifier</label><div class=\"sk-toggleable__content\"><pre>RandomForestClassifier(random_state=42)</pre></div></div></div></div></div></div></div></div></div></div>"
      ],
      "text/plain": [
       "GridSearchCV(cv=4, estimator=RandomForestClassifier(random_state=42),\n",
       "             param_grid={'max_depth': [None], 'max_features': [1.0],\n",
       "                         'max_samples': [1.0], 'min_samples_leaf': [2],\n",
       "                         'min_samples_split': [2], 'n_estimators': [300]},\n",
       "             refit='recall', scoring={'precision', 'recall', 'accuracy', 'f1'})"
      ]
     },
     "execution_count": 24,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "%%time\n",
    "rf_cv.fit(X_train, y_train)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "5wHi_YJduQOH"
   },
   "source": [
    "Examine the best average score across all the validation folds."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 25,
   "metadata": {
    "id": "YtAgrH0zy4CE"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.12678201409034398"
      ]
     },
     "execution_count": 25,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Examine best score\n",
    "rf_cv.best_score_"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "heGb51fHh3E5"
   },
   "source": [
    "Examine the best combination of hyperparameters."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 26,
   "metadata": {
    "id": "kazNtYG4fQOI"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "{'max_depth': None,\n",
       " 'max_features': 1.0,\n",
       " 'max_samples': 1.0,\n",
       " 'min_samples_leaf': 2,\n",
       " 'min_samples_split': 2,\n",
       " 'n_estimators': 300}"
      ]
     },
     "execution_count": 26,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Examine best hyperparameter combo\n",
    "rf_cv.best_params_"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "qZZnem5yiAau"
   },
   "source": [
    "Use the `make_results()` function to output all of the scores of your model. Note that the function accepts three arguments."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "GeW48TS742jN"
   },
   "source": [
    "<details>\n",
    "  <summary><h5>HINT</h5></summary>\n",
    "\n",
    "To learn more about how this function accesses the cross-validation results, refer to the [`GridSearchCV` scikit-learn documentation](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html?highlight=gridsearchcv#sklearn.model_selection.GridSearchCV) for the `cv_results_` attribute.\n",
    "\n",
    "</details>"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 27,
   "metadata": {
    "id": "u-UodWEOedxz"
   },
   "outputs": [],
   "source": [
    "def make_results(model_name:str, model_object, metric:str):\n",
    "    '''\n",
    "    Arguments:\n",
    "        model_name (string): what you want the model to be called in the output table\n",
    "        model_object: a fit GridSearchCV object\n",
    "        metric (string): precision, recall, f1, or accuracy\n",
    "\n",
    "    Returns a pandas df with the F1, recall, precision, and accuracy scores\n",
    "    for the model with the best mean 'metric' score across all validation folds.\n",
    "    '''\n",
    "\n",
    "    # Create dictionary that maps input metric to actual metric name in GridSearchCV\n",
    "    metric_dict = {'precision': 'mean_test_precision',\n",
    "                   'recall': 'mean_test_recall',\n",
    "                   'f1': 'mean_test_f1',\n",
    "                   'accuracy': 'mean_test_accuracy',\n",
    "                   }\n",
    "\n",
    "    # Get all the results from the CV and put them in a df\n",
    "    cv_results = pd.DataFrame(model_object.cv_results_)\n",
    "\n",
    "    # Isolate the row of the df with the max(metric) score\n",
    "    best_estimator_results = cv_results.iloc[cv_results[metric_dict[metric]].idxmax(), :]\n",
    "\n",
    "    # Extract accuracy, precision, recall, and f1 score from that row\n",
    "    f1 = best_estimator_results.mean_test_f1\n",
    "    recall = best_estimator_results.mean_test_recall\n",
    "    precision = best_estimator_results.mean_test_precision\n",
    "    accuracy = best_estimator_results.mean_test_accuracy\n",
    "\n",
    "    # Create table of results\n",
    "    table = pd.DataFrame({'model': [model_name],\n",
    "                          'precision': [precision],\n",
    "                          'recall': [recall],\n",
    "                          'F1': [f1],\n",
    "                          'accuracy': [accuracy],\n",
    "                          },\n",
    "                         )\n",
    "\n",
    "    return table"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "diQezudIfzHn"
   },
   "source": [
    "Pass the `GridSearch` object to the `make_results()` function."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 28,
   "metadata": {
    "id": "qAYb2QigiT_h"
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>model</th>\n",
       "      <th>precision</th>\n",
       "      <th>recall</th>\n",
       "      <th>F1</th>\n",
       "      <th>accuracy</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>RF cv</td>\n",
       "      <td>0.457163</td>\n",
       "      <td>0.126782</td>\n",
       "      <td>0.198445</td>\n",
       "      <td>0.81851</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   model  precision    recall        F1  accuracy\n",
       "0  RF cv   0.457163  0.126782  0.198445   0.81851"
      ]
     },
     "execution_count": 28,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "results = make_results('RF cv', rf_cv, 'recall')\n",
    "results"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "SB-yhW9uu7dO"
   },
   "source": [
    "Asside from the accuracy, the scores aren't that good. However, recall that when you built the logistic regression model in the last course the recall was \\~0.09, which means that this model has 33% better recall and about the same accuracy, and it was trained on less data.\n",
    "\n",
    "If you want, feel free to try retuning your hyperparameters to try to get a better score. You might be able to marginally improve the model."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "DOlktJ6l4Tgt"
   },
   "source": [
    "#### **XGBoost**\n",
    "\n",
    " Try to improve your scores using an XGBoost model.\n",
    "\n",
    "1. Instantiate the XGBoost classifier `xgb` and set `objective='binary:logistic'`. Also set the random state.\n",
    "\n",
    "2. Create a dictionary `cv_params` of the following hyperparameters and their corresponding values to tune:\n",
    " - `max_depth`\n",
    " - `min_child_weight`\n",
    " - `learning_rate`\n",
    " - `n_estimators`\n",
    "\n",
    "3. Define a dictionary `scoring` of scoring metrics for grid search to capture (precision, recall, F1 score, and accuracy).\n",
    "\n",
    "4. Instantiate the `GridSearchCV` object `xgb_cv`. Pass to it as arguments:\n",
    " - estimator=`xgb`\n",
    " - param_grid=`cv_params`\n",
    " - scoring=`scoring`\n",
    " - cv: define the number of cross-validation folds you want (`cv=_`)\n",
    " - refit: indicate which evaluation metric you want to use to select the model (`refit='recall'`)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 29,
   "metadata": {
    "id": "0ciO48nhiTqO"
   },
   "outputs": [],
   "source": [
    "# 1. Instantiate the XGBoost classifier\n",
    "xgb = XGBClassifier(objective='binary:logistic', random_state=42)\n",
    "\n",
    "# 2. Create a dictionary of hyperparameters to tune\n",
    "cv_params = {'max_depth': [6, 12],\n",
    "             'min_child_weight': [3, 5],\n",
    "             'learning_rate': [0.01, 0.1],\n",
    "             'n_estimators': [300]\n",
    "             }\n",
    "\n",
    "# 3. Define a dictionary of scoring metrics to capture\n",
    "scoring = {'accuracy', 'precision', 'recall', 'f1'}\n",
    "\n",
    "# 4. Instantiate the GridSearchCV object\n",
    "xgb_cv = GridSearchCV(xgb, cv_params, scoring=scoring, cv=4, refit='recall')"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "Y78-hQF9680x"
   },
   "source": [
    "Now fit the model to the `X_train` and `y_train` data.\n",
    "\n",
    "Note this cell might take several minutes to run."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 31,
   "metadata": {
    "id": "dYCWs_HX6804"
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "CPU times: user 4min 21s, sys: 1.77 s, total: 4min 23s\n",
      "Wall time: 2min 13s\n"
     ]
    },
    {
     "data": {
      "text/html": [
       "<style>#sk-container-id-2 {color: black;background-color: white;}#sk-container-id-2 pre{padding: 0;}#sk-container-id-2 div.sk-toggleable {background-color: white;}#sk-container-id-2 label.sk-toggleable__label {cursor: pointer;display: block;width: 100%;margin-bottom: 0;padding: 0.3em;box-sizing: border-box;text-align: center;}#sk-container-id-2 label.sk-toggleable__label-arrow:before {content: \"▸\";float: left;margin-right: 0.25em;color: #696969;}#sk-container-id-2 label.sk-toggleable__label-arrow:hover:before {color: black;}#sk-container-id-2 div.sk-estimator:hover label.sk-toggleable__label-arrow:before {color: black;}#sk-container-id-2 div.sk-toggleable__content {max-height: 0;max-width: 0;overflow: hidden;text-align: left;background-color: #f0f8ff;}#sk-container-id-2 div.sk-toggleable__content pre {margin: 0.2em;color: black;border-radius: 0.25em;background-color: #f0f8ff;}#sk-container-id-2 input.sk-toggleable__control:checked~div.sk-toggleable__content {max-height: 200px;max-width: 100%;overflow: auto;}#sk-container-id-2 input.sk-toggleable__control:checked~label.sk-toggleable__label-arrow:before {content: \"▾\";}#sk-container-id-2 div.sk-estimator input.sk-toggleable__control:checked~label.sk-toggleable__label {background-color: #d4ebff;}#sk-container-id-2 div.sk-label input.sk-toggleable__control:checked~label.sk-toggleable__label {background-color: #d4ebff;}#sk-container-id-2 input.sk-hidden--visually {border: 0;clip: rect(1px 1px 1px 1px);clip: rect(1px, 1px, 1px, 1px);height: 1px;margin: -1px;overflow: hidden;padding: 0;position: absolute;width: 1px;}#sk-container-id-2 div.sk-estimator {font-family: monospace;background-color: #f0f8ff;border: 1px dotted black;border-radius: 0.25em;box-sizing: border-box;margin-bottom: 0.5em;}#sk-container-id-2 div.sk-estimator:hover {background-color: #d4ebff;}#sk-container-id-2 div.sk-parallel-item::after {content: \"\";width: 100%;border-bottom: 1px solid gray;flex-grow: 1;}#sk-container-id-2 div.sk-label:hover label.sk-toggleable__label {background-color: #d4ebff;}#sk-container-id-2 div.sk-serial::before {content: \"\";position: absolute;border-left: 1px solid gray;box-sizing: border-box;top: 0;bottom: 0;left: 50%;z-index: 0;}#sk-container-id-2 div.sk-serial {display: flex;flex-direction: column;align-items: center;background-color: white;padding-right: 0.2em;padding-left: 0.2em;position: relative;}#sk-container-id-2 div.sk-item {position: relative;z-index: 1;}#sk-container-id-2 div.sk-parallel {display: flex;align-items: stretch;justify-content: center;background-color: white;position: relative;}#sk-container-id-2 div.sk-item::before, #sk-container-id-2 div.sk-parallel-item::before {content: \"\";position: absolute;border-left: 1px solid gray;box-sizing: border-box;top: 0;bottom: 0;left: 50%;z-index: -1;}#sk-container-id-2 div.sk-parallel-item {display: flex;flex-direction: column;z-index: 1;position: relative;background-color: white;}#sk-container-id-2 div.sk-parallel-item:first-child::after {align-self: flex-end;width: 50%;}#sk-container-id-2 div.sk-parallel-item:last-child::after {align-self: flex-start;width: 50%;}#sk-container-id-2 div.sk-parallel-item:only-child::after {width: 0;}#sk-container-id-2 div.sk-dashed-wrapped {border: 1px dashed gray;margin: 0 0.4em 0.5em 0.4em;box-sizing: border-box;padding-bottom: 0.4em;background-color: white;}#sk-container-id-2 div.sk-label label {font-family: monospace;font-weight: bold;display: inline-block;line-height: 1.2em;}#sk-container-id-2 div.sk-label-container {text-align: center;}#sk-container-id-2 div.sk-container {/* jupyter's `normalize.less` sets `[hidden] { display: none; }` but bootstrap.min.css set `[hidden] { display: none !important; }` so we also need the `!important` here to be able to override the default hidden behavior on the sphinx rendered scikit-learn.org. See: https://github.com/scikit-learn/scikit-learn/issues/21755 */display: inline-block !important;position: relative;}#sk-container-id-2 div.sk-text-repr-fallback {display: none;}</style><div id=\"sk-container-id-2\" class=\"sk-top-container\"><div class=\"sk-text-repr-fallback\"><pre>GridSearchCV(cv=4,\n",
       "             estimator=XGBClassifier(base_score=None, booster=None,\n",
       "                                     callbacks=None, colsample_bylevel=None,\n",
       "                                     colsample_bynode=None,\n",
       "                                     colsample_bytree=None,\n",
       "                                     early_stopping_rounds=None,\n",
       "                                     enable_categorical=False, eval_metric=None,\n",
       "                                     feature_types=None, gamma=None,\n",
       "                                     gpu_id=None, grow_policy=None,\n",
       "                                     importance_type=None,\n",
       "                                     interaction_constraints=None,\n",
       "                                     learning_rate=None,...\n",
       "                                     max_delta_step=None, max_depth=None,\n",
       "                                     max_leaves=None, min_child_weight=None,\n",
       "                                     missing=nan, monotone_constraints=None,\n",
       "                                     n_estimators=100, n_jobs=None,\n",
       "                                     num_parallel_tree=None, predictor=None,\n",
       "                                     random_state=42, ...),\n",
       "             param_grid={&#x27;learning_rate&#x27;: [0.01, 0.1], &#x27;max_depth&#x27;: [6, 12],\n",
       "                         &#x27;min_child_weight&#x27;: [3, 5], &#x27;n_estimators&#x27;: [300]},\n",
       "             refit=&#x27;recall&#x27;, scoring={&#x27;precision&#x27;, &#x27;recall&#x27;, &#x27;accuracy&#x27;, &#x27;f1&#x27;})</pre><b>In a Jupyter environment, please rerun this cell to show the HTML representation or trust the notebook. <br />On GitHub, the HTML representation is unable to render, please try loading this page with nbviewer.org.</b></div><div class=\"sk-container\" hidden><div class=\"sk-item sk-dashed-wrapped\"><div class=\"sk-label-container\"><div class=\"sk-label sk-toggleable\"><input class=\"sk-toggleable__control sk-hidden--visually\" id=\"sk-estimator-id-4\" type=\"checkbox\" ><label for=\"sk-estimator-id-4\" class=\"sk-toggleable__label sk-toggleable__label-arrow\">GridSearchCV</label><div class=\"sk-toggleable__content\"><pre>GridSearchCV(cv=4,\n",
       "             estimator=XGBClassifier(base_score=None, booster=None,\n",
       "                                     callbacks=None, colsample_bylevel=None,\n",
       "                                     colsample_bynode=None,\n",
       "                                     colsample_bytree=None,\n",
       "                                     early_stopping_rounds=None,\n",
       "                                     enable_categorical=False, eval_metric=None,\n",
       "                                     feature_types=None, gamma=None,\n",
       "                                     gpu_id=None, grow_policy=None,\n",
       "                                     importance_type=None,\n",
       "                                     interaction_constraints=None,\n",
       "                                     learning_rate=None,...\n",
       "                                     max_delta_step=None, max_depth=None,\n",
       "                                     max_leaves=None, min_child_weight=None,\n",
       "                                     missing=nan, monotone_constraints=None,\n",
       "                                     n_estimators=100, n_jobs=None,\n",
       "                                     num_parallel_tree=None, predictor=None,\n",
       "                                     random_state=42, ...),\n",
       "             param_grid={&#x27;learning_rate&#x27;: [0.01, 0.1], &#x27;max_depth&#x27;: [6, 12],\n",
       "                         &#x27;min_child_weight&#x27;: [3, 5], &#x27;n_estimators&#x27;: [300]},\n",
       "             refit=&#x27;recall&#x27;, scoring={&#x27;precision&#x27;, &#x27;recall&#x27;, &#x27;accuracy&#x27;, &#x27;f1&#x27;})</pre></div></div></div><div class=\"sk-parallel\"><div class=\"sk-parallel-item\"><div class=\"sk-item\"><div class=\"sk-label-container\"><div class=\"sk-label sk-toggleable\"><input class=\"sk-toggleable__control sk-hidden--visually\" id=\"sk-estimator-id-5\" type=\"checkbox\" ><label for=\"sk-estimator-id-5\" class=\"sk-toggleable__label sk-toggleable__label-arrow\">estimator: XGBClassifier</label><div class=\"sk-toggleable__content\"><pre>XGBClassifier(base_score=None, booster=None, callbacks=None,\n",
       "              colsample_bylevel=None, colsample_bynode=None,\n",
       "              colsample_bytree=None, early_stopping_rounds=None,\n",
       "              enable_categorical=False, eval_metric=None, feature_types=None,\n",
       "              gamma=None, gpu_id=None, grow_policy=None, importance_type=None,\n",
       "              interaction_constraints=None, learning_rate=None, max_bin=None,\n",
       "              max_cat_threshold=None, max_cat_to_onehot=None,\n",
       "              max_delta_step=None, max_depth=None, max_leaves=None,\n",
       "              min_child_weight=None, missing=nan, monotone_constraints=None,\n",
       "              n_estimators=100, n_jobs=None, num_parallel_tree=None,\n",
       "              predictor=None, random_state=42, ...)</pre></div></div></div><div class=\"sk-serial\"><div class=\"sk-item\"><div class=\"sk-estimator sk-toggleable\"><input class=\"sk-toggleable__control sk-hidden--visually\" id=\"sk-estimator-id-6\" type=\"checkbox\" ><label for=\"sk-estimator-id-6\" class=\"sk-toggleable__label sk-toggleable__label-arrow\">XGBClassifier</label><div class=\"sk-toggleable__content\"><pre>XGBClassifier(base_score=None, booster=None, callbacks=None,\n",
       "              colsample_bylevel=None, colsample_bynode=None,\n",
       "              colsample_bytree=None, early_stopping_rounds=None,\n",
       "              enable_categorical=False, eval_metric=None, feature_types=None,\n",
       "              gamma=None, gpu_id=None, grow_policy=None, importance_type=None,\n",
       "              interaction_constraints=None, learning_rate=None, max_bin=None,\n",
       "              max_cat_threshold=None, max_cat_to_onehot=None,\n",
       "              max_delta_step=None, max_depth=None, max_leaves=None,\n",
       "              min_child_weight=None, missing=nan, monotone_constraints=None,\n",
       "              n_estimators=100, n_jobs=None, num_parallel_tree=None,\n",
       "              predictor=None, random_state=42, ...)</pre></div></div></div></div></div></div></div></div></div></div>"
      ],
      "text/plain": [
       "GridSearchCV(cv=4,\n",
       "             estimator=XGBClassifier(base_score=None, booster=None,\n",
       "                                     callbacks=None, colsample_bylevel=None,\n",
       "                                     colsample_bynode=None,\n",
       "                                     colsample_bytree=None,\n",
       "                                     early_stopping_rounds=None,\n",
       "                                     enable_categorical=False, eval_metric=None,\n",
       "                                     feature_types=None, gamma=None,\n",
       "                                     gpu_id=None, grow_policy=None,\n",
       "                                     importance_type=None,\n",
       "                                     interaction_constraints=None,\n",
       "                                     learning_rate=None,...\n",
       "                                     max_delta_step=None, max_depth=None,\n",
       "                                     max_leaves=None, min_child_weight=None,\n",
       "                                     missing=nan, monotone_constraints=None,\n",
       "                                     n_estimators=100, n_jobs=None,\n",
       "                                     num_parallel_tree=None, predictor=None,\n",
       "                                     random_state=42, ...),\n",
       "             param_grid={'learning_rate': [0.01, 0.1], 'max_depth': [6, 12],\n",
       "                         'min_child_weight': [3, 5], 'n_estimators': [300]},\n",
       "             refit='recall', scoring={'precision', 'recall', 'accuracy', 'f1'})"
      ]
     },
     "execution_count": 31,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "%%time\n",
    "xgb_cv.fit(X_train, y_train)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "ruQISDB76805"
   },
   "source": [
    "Get the best score from this model."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 32,
   "metadata": {
    "id": "UFLTmIDm6805"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.1734683657963807"
      ]
     },
     "execution_count": 32,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Examine best score\n",
    "xgb_cv.best_score_"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "fwmWDuXZ6805"
   },
   "source": [
    "And the best parameters."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 33,
   "metadata": {
    "id": "cdPUCuND6805"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "{'learning_rate': 0.1,\n",
       " 'max_depth': 12,\n",
       " 'min_child_weight': 3,\n",
       " 'n_estimators': 300}"
      ]
     },
     "execution_count": 33,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Examine best parameters\n",
    "xgb_cv.best_params_"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "X8v8HTmQ7KdC"
   },
   "source": [
    "Use the `make_results()` function to output all of the scores of your model. Note that the function accepts three arguments."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 34,
   "metadata": {
    "id": "QL19dH2h7KdD"
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>model</th>\n",
       "      <th>precision</th>\n",
       "      <th>recall</th>\n",
       "      <th>F1</th>\n",
       "      <th>accuracy</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>RF cv</td>\n",
       "      <td>0.457163</td>\n",
       "      <td>0.126782</td>\n",
       "      <td>0.198445</td>\n",
       "      <td>0.81851</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>XGB cv</td>\n",
       "      <td>0.442586</td>\n",
       "      <td>0.173468</td>\n",
       "      <td>0.248972</td>\n",
       "      <td>0.81478</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "    model  precision    recall        F1  accuracy\n",
       "0   RF cv   0.457163  0.126782  0.198445   0.81851\n",
       "0  XGB cv   0.442586  0.173468  0.248972   0.81478"
      ]
     },
     "execution_count": 34,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Call 'make_results()' on the GridSearch object\n",
    "xgb_cv_results = make_results('XGB cv', xgb_cv, 'recall')\n",
    "results = pd.concat([results, xgb_cv_results], axis=0)\n",
    "results"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "c5IRnMO27KdD"
   },
   "source": [
    "This model fit the data even better than the random forest model. The recall score is nearly double the recall score from the logistic regression model from the previous course, and it's almost 50% better than the random forest model's recall score, while maintaining a similar accuracy and precision score."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "NfX0SjJffkh1"
   },
   "source": [
    "### **Task 11. Model selection**\n",
    "\n",
    "Now, use the best random forest model and the best XGBoost model to predict on the validation data. Whichever performs better will be selected as the champion model."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "chgR3Tx8fn1s"
   },
   "source": [
    "#### **Random forest**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 35,
   "metadata": {
    "id": "DUswawM2fyAf"
   },
   "outputs": [],
   "source": [
    "# Use random forest model to predict on validation data\n",
    "rf_val_preds = rf_cv.best_estimator_.predict(X_val)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "uz1eb4rqf11Z"
   },
   "source": [
    "Use the `get_test_scores()` function to generate a table of scores from the predictions on the validation data."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 36,
   "metadata": {
    "id": "AJ9mCl0Uf4P4"
   },
   "outputs": [],
   "source": [
    "def get_test_scores(model_name:str, preds, y_test_data):\n",
    "    '''\n",
    "    Generate a table of test scores.\n",
    "\n",
    "    In:\n",
    "        model_name (string): Your choice: how the model will be named in the output table\n",
    "        preds: numpy array of test predictions\n",
    "        y_test_data: numpy array of y_test data\n",
    "\n",
    "    Out:\n",
    "        table: a pandas df of precision, recall, f1, and accuracy scores for your model\n",
    "    '''\n",
    "    accuracy = accuracy_score(y_test_data, preds)\n",
    "    precision = precision_score(y_test_data, preds)\n",
    "    recall = recall_score(y_test_data, preds)\n",
    "    f1 = f1_score(y_test_data, preds)\n",
    "\n",
    "    table = pd.DataFrame({'model': [model_name],\n",
    "                          'precision': [precision],\n",
    "                          'recall': [recall],\n",
    "                          'F1': [f1],\n",
    "                          'accuracy': [accuracy]\n",
    "                          })\n",
    "\n",
    "    return table"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 37,
   "metadata": {
    "id": "22ANR4ZHf5NK"
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>model</th>\n",
       "      <th>precision</th>\n",
       "      <th>recall</th>\n",
       "      <th>F1</th>\n",
       "      <th>accuracy</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>RF cv</td>\n",
       "      <td>0.457163</td>\n",
       "      <td>0.126782</td>\n",
       "      <td>0.198445</td>\n",
       "      <td>0.818510</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>XGB cv</td>\n",
       "      <td>0.442586</td>\n",
       "      <td>0.173468</td>\n",
       "      <td>0.248972</td>\n",
       "      <td>0.814780</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>RF val</td>\n",
       "      <td>0.445255</td>\n",
       "      <td>0.120316</td>\n",
       "      <td>0.189441</td>\n",
       "      <td>0.817483</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "    model  precision    recall        F1  accuracy\n",
       "0   RF cv   0.457163  0.126782  0.198445  0.818510\n",
       "0  XGB cv   0.442586  0.173468  0.248972  0.814780\n",
       "0  RF val   0.445255  0.120316  0.189441  0.817483"
      ]
     },
     "execution_count": 37,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Get validation scores for RF model\n",
    "rf_val_scores = get_test_scores('RF val', rf_val_preds, y_val)\n",
    "\n",
    "# Append to the results table\n",
    "results = pd.concat([results, rf_val_scores], axis=0)\n",
    "results"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "yDeuk16igBD0"
   },
   "source": [
    "Notice that the scores went down from the training scores across all metrics, but only by very little. This means that the model did not overfit the training data."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "D8h2s5RpgEER"
   },
   "source": [
    "#### **XGBoost**\n",
    "\n",
    "Now, do the same thing to get the performance scores of the XGBoost model on the validation data."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 38,
   "metadata": {
    "id": "mQoTuRkngHjp"
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>model</th>\n",
       "      <th>precision</th>\n",
       "      <th>recall</th>\n",
       "      <th>F1</th>\n",
       "      <th>accuracy</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>RF cv</td>\n",
       "      <td>0.457163</td>\n",
       "      <td>0.126782</td>\n",
       "      <td>0.198445</td>\n",
       "      <td>0.818510</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>XGB cv</td>\n",
       "      <td>0.442586</td>\n",
       "      <td>0.173468</td>\n",
       "      <td>0.248972</td>\n",
       "      <td>0.814780</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>RF val</td>\n",
       "      <td>0.445255</td>\n",
       "      <td>0.120316</td>\n",
       "      <td>0.189441</td>\n",
       "      <td>0.817483</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>XGB val</td>\n",
       "      <td>0.430769</td>\n",
       "      <td>0.165680</td>\n",
       "      <td>0.239316</td>\n",
       "      <td>0.813287</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "     model  precision    recall        F1  accuracy\n",
       "0    RF cv   0.457163  0.126782  0.198445  0.818510\n",
       "0   XGB cv   0.442586  0.173468  0.248972  0.814780\n",
       "0   RF val   0.445255  0.120316  0.189441  0.817483\n",
       "0  XGB val   0.430769  0.165680  0.239316  0.813287"
      ]
     },
     "execution_count": 38,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Use XGBoost model to predict on validation data\n",
    "xgb_val_preds = xgb_cv.best_estimator_.predict(X_val)\n",
    "\n",
    "# Get validation scores for XGBoost model\n",
    "xgb_val_scores = get_test_scores('XGB val', xgb_val_preds, y_val)\n",
    "\n",
    "# Append to the results table\n",
    "results = pd.concat([results, xgb_val_scores], axis=0)\n",
    "results"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "GspkQqUNgIm3"
   },
   "source": [
    "Just like with the random forest model, the XGBoost model's validation scores were lower, but only very slightly. It is still the clear champion."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "_HGsWfEOeWPm"
   },
   "source": [
    "<img src=\"images/Execute.png\" width=\"100\" height=\"100\" align=left>\n",
    "\n",
    "## **PACE: Execute**\n",
    "Consider the questions in your PACE Strategy Document to reflect on the Execute stage."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "iOm4n_1OgUND"
   },
   "source": [
    "### **Task 12. Use champion model to predict on test data**\n",
    "\n",
    "Now, use the champion model to predict on the test dataset. This is to give a final indication of how you should expect the model to perform on new future data, should you decide to use the model."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 39,
   "metadata": {
    "id": "7BkheTIsgU2b"
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>model</th>\n",
       "      <th>precision</th>\n",
       "      <th>recall</th>\n",
       "      <th>F1</th>\n",
       "      <th>accuracy</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>RF cv</td>\n",
       "      <td>0.457163</td>\n",
       "      <td>0.126782</td>\n",
       "      <td>0.198445</td>\n",
       "      <td>0.818510</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>XGB cv</td>\n",
       "      <td>0.442586</td>\n",
       "      <td>0.173468</td>\n",
       "      <td>0.248972</td>\n",
       "      <td>0.814780</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>RF val</td>\n",
       "      <td>0.445255</td>\n",
       "      <td>0.120316</td>\n",
       "      <td>0.189441</td>\n",
       "      <td>0.817483</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>XGB val</td>\n",
       "      <td>0.430769</td>\n",
       "      <td>0.165680</td>\n",
       "      <td>0.239316</td>\n",
       "      <td>0.813287</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>XGB test</td>\n",
       "      <td>0.388889</td>\n",
       "      <td>0.165680</td>\n",
       "      <td>0.232365</td>\n",
       "      <td>0.805944</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "      model  precision    recall        F1  accuracy\n",
       "0     RF cv   0.457163  0.126782  0.198445  0.818510\n",
       "0    XGB cv   0.442586  0.173468  0.248972  0.814780\n",
       "0    RF val   0.445255  0.120316  0.189441  0.817483\n",
       "0   XGB val   0.430769  0.165680  0.239316  0.813287\n",
       "0  XGB test   0.388889  0.165680  0.232365  0.805944"
      ]
     },
     "execution_count": 39,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Use XGBoost model to predict on test data\n",
    "xgb_test_preds = xgb_cv.best_estimator_.predict(X_test)\n",
    "\n",
    "# Get test scores for XGBoost model\n",
    "xgb_test_scores = get_test_scores('XGB test', xgb_test_preds, y_test)\n",
    "\n",
    "# Append to the results table\n",
    "results = pd.concat([results, xgb_test_scores], axis=0)\n",
    "results"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "F8L_LyIbgV1I"
   },
   "source": [
    "The recall was exactly the same as it was on the validation data, but the precision declined notably, which caused all of the other scores to drop slightly. Nonetheless, this is stil within the acceptable range for performance discrepancy between validation and test scores."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "S5GNoz_QgWug"
   },
   "source": [
    "### **Task 13. Confusion matrix**\n",
    "\n",
    "Plot a confusion matrix of the champion model's predictions on the test data."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 40,
   "metadata": {
    "id": "WF3KErX8gXPc"
   },
   "outputs": [
    {
     "data": {
      "image/png": "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",
      "text/plain": [
       "<Figure size 640x480 with 2 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "# Generate array of values for confusion matrix\n",
    "cm = confusion_matrix(y_test, xgb_test_preds, labels=xgb_cv.classes_)\n",
    "\n",
    "# Plot confusion matrix\n",
    "disp = ConfusionMatrixDisplay(confusion_matrix=cm,\n",
    "                             display_labels=['retained', 'churned'])\n",
    "disp.plot();"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "6xL4OujkgYC3"
   },
   "source": [
    "The model predicted three times as many false negatives than it did false positives, and it correctly identified only 16.6% of the users who actually churned."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "P33INGPmgY1o"
   },
   "source": [
    "### **Task 14. Feature importance**\n",
    "\n",
    "Use the `plot_importance` function to inspect the most important features of your final model."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 41,
   "metadata": {
    "id": "N4fc2i8XgZoE"
   },
   "outputs": [
    {
     "data": {
      "image/png": "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ccUhhBBCCPEsGQwIAVSvXp1du3blu79///70798/z32DBw/WuLn4Wd26daNbt2751vv8lUI7Ozu+++67fPOHhIQQEhKikfbsewpeFocQQgghxLPkDcRCCCGEEEK8o2QwIIQQQgghxDtKBgNCCCGEEEK8o2QwIIQQQgghxDtKBgNCCCGEEEK8o2QwIIQQQgghxDtKBgNCCCGEEEK8o2QwILRWzku8CsrBwSHft5C+LgcOHEClUvHw4cN/tV0h3lUZGRlMmTIFR0dHDA0NcXJyYubMmWRlZSl51Go1ISEh2NjYYGhoiIeHBxcvXlT2x8bGolKp8vz69ttvX9j+smXLcHR0pHTp0ri5uXH48OHXFqsQQhSEDAZEiRcSEkK9evVypcfHxxfqzbunT59myJAhynZhBxNCiLffvHnzWLFiBUuXLuXy5cvMnz+fsLAwjTd9z58/n/DwcJYuXcrp06exsrKiXbt2/PPPPwDY2toSHx+v8TVjxgyMjY1f+JmzceNGAgMDmTx5MufOnaNly5Z07NiRuLi41x63EELkRwYDQmtZWVlhYGBQ4Pzly5fHyMjoNfZICPGmHT9+nK5du9KpUyccHBzo1asX7du358yZM0D2rEBERASTJ0+mR48e1KpVizVr1pCSkkJUVBQAurq6WFlZaXxt27YNb29vTExM8m07PDwcf39/Bg0aRPXq1YmIiMDW1pbly5f/K7ELIUReZDAg3gq7du2iRYsWlC1bFgsLCz744AOuX7+u7L916xZ9+/bF3NwcY2NjGjRowMmTJ1m9ejUzZszgl19+UabpV69eDWhe2W/atCkTJkzQaPPvv/9GX1+f/fv3A5rLhBwcHADo3r07KpUKBwcHYmNj0dHRUU4acixZsgR7e3vUavVL44yOjqZq1aoYGhrSunVrYmNjNfbfv3+ffv36UalSJYyMjKhduzbffPONsn/t2rVYWFiQmpqqUa5nz54MGDAAgF9++YXWrVtjamqKmZkZbm5uufosxLuqRYsW7N27l99//x3I/n05cuQIXl5eANy4cYOEhATat2+vlDEwMMDd3Z1jx47lWWdMTAznz5/H398/33bT0tKIiYnRqBegffv2+dYrhBD/Br033QEhAB4/fkxQUBC1a9fm8ePHTJs2je7du3P+/HlSUlJwd3enYsWK7NixAysrK86ePUtWVhbe3t78+uuv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",
      "text/plain": [
       "<Figure size 640x480 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "plot_importance(xgb_cv.best_estimator_);"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "EU3GIZNrga5z"
   },
   "source": [
    "The XGBoost model made more use of many of the features than did the logistic regression model from the previous course, which weighted a single feature (`activity_days`) very heavily in its final prediction.\n",
    "\n",
    "If anything, this underscores the importance of feature engineering. Notice that engineered features accounted for six of the top 10 features (and three of the top five). Feature engineering is often one of the best and easiest ways to boost model performance.\n",
    "\n",
    "Also, note that the important features in one model might not be the same as the important features in another model. That's why you shouldn't discount features as unimportant without thoroughly examining them and understanding their relationship with the dependent variable, if possible. These discrepancies between features selected by models are typically caused by complex feature interactions.\n",
    "\n",
    "Remember, sometimes your data simply will not be predictive of your chosen target. This is common. Machine learning is a powerful tool, but it is not magic. If your data does not contain predictive signal, even the most complex algorithm will not be able to deliver consistent and accurate predictions. Do not be afraid to draw this conclusion.\n",
    "\n",
    "Even if you cannot use the model to make strong predictions, was the work done in vain? What insights can you report back to stakeholders?"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "ill21hQ4ej9-"
   },
   "source": [
    "### **Task 15. Conclusion**\n",
    "\n",
    "Now that you've built and tested your machine learning models, the next step is to share your findings with the Waze leadership team. Consider the following questions as you prepare to write your executive summary. Think about key points you may want to share with the team, and what information is most relevant to the user churn project.\n",
    "\n",
    "**Questions:**\n",
    "\n",
    "1. Would you recommend using this model for churn prediction? Why or why not?\n",
    "\n",
    "2. What tradeoff was made by splitting the data into training, validation, and test sets as opposed to just training and test sets?\n",
    "\n",
    "3. What is the benefit of using a logistic regression model over an ensemble of tree-based models (like random forest or XGBoost) for classification tasks?\n",
    "\n",
    "4. What is the benefit of using an ensemble of tree-based models like random forest or XGBoost over a logistic regression model for classification tasks?\n",
    "\n",
    "5. What could you do to improve this model?\n",
    "\n",
    "6. What additional features would you like to have to help improve the model?"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "_NrXTUydBady"
   },
   "source": [
    "1) ALthough the acuracy score is pretty hight, all other scores are pretty low. Although it might seem like a waste, there is an opportunity to pull more data and create a new/updated model. \n",
    "2) The trade off is that you have a smaller data set and less information on each transaction. Maybe this is an opportunity to add more records. \n",
    "3) Those models take less time to generate and are easier to explain. \n",
    "4) You model improves and you can compare models to see which ones can improve by making small adjustments. \n",
    "5) I would add more data and see if there are other variables available. I would also look for other predictors so see if different combunations will work.\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "ij_DDQ2xSwyD"
   },
   "source": [
    "### **BONUS**\n",
    "\n",
    "The following content is not required, but demonstrates further steps that you might take to tailor your model to your use case."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "cw4qgviwSwXK"
   },
   "source": [
    "#### **Identify an optimal decision threshold**\n",
    "\n",
    "The default decision threshold for most implementations of classification algorithms&mdash;including scikit-learn's&mdash;is 0.5. This means that, in the case of the Waze models, if they predicted that a given user had a 50% probability or greater of churning, then that user was assigned a predicted value of `1`&mdash;the user was predicted to churn.\n",
    "\n",
    "With imbalanced datasets where the response class is a minority, this threshold might not be ideal. You learned that a precision-recall curve can help to visualize the trade-off between your model's precision and recall.\n",
    "\n",
    "Here's the precision-recall curve for the XGBoost champion model on the test data."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 42,
   "metadata": {
    "id": "kVgiwBWtSxq4"
   },
   "outputs": [
    {
     "data": {
      "image/png": "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TG6IiIjIoDC5ISIiIoPC5IaIiIgMSokmNydPnkS3bt3g7OwMiUSCvXv3FrjPiRMn0KRJE5iZmaFKlSpYt25d0QdKREREZUaJJjcpKSlo0KABvv/+e43qh4eHIyAgAC1btkRoaChmzJiBcePGYffu3UUcKREREZUVJbpwZufOndG5c2eN669btw6VK1fGihUrAABeXl64cOECvvvuO7z//vtFFKVm5AqB2MQ0tdtszE1QzsykmCMiIiJ6O5WpVcHPnj2Ljh07qpT5+/tj48aNyMrKgolJ7gQiIyMDGRkZyudJSUlFEtuzlAy8u/i42m2mxkY4ML4lqla0KpJzExER0f+UqQHFcXFxqFSpkkpZpUqVkJ2djfj4eLX7LFy4EDY2NsqHm5tbkcVnamyU6yGRABnZCtyJSy6y8xIREdH/lKmWGwCQSCQqz4UQastzTJ8+HRMnTlQ+T0pKKpIEx6GcGW5/nbuL7YN1Z/FfxHO9n4+IiIjUK1PJjaOjI+Li4lTKnjx5AmNjY9jb26vdx9TUFKampsURHhEREZUCZapbysfHB0eOHFEpO3z4MLy9vdWOtyEiIqK3T4kmNy9fvsTly5dx+fJlAK+mel++fBmRkZEAXnUpDR48WFl/9OjRePjwISZOnIibN2/ip59+wsaNGzF58uSSCJ+IiIhKoRLtlrpw4QLatGmjfJ4zNmbIkCHYvHkzYmNjlYkOAHh6euLAgQP4/PPPsWbNGjg7O2PVqlUlPg2ciIiISo8STW5at26tHBCszubNm3OV+fn54dKlS0UYFREREZVlZWrMDREREVFBmNwQERGRQWFyQ0RERAaFyQ0REREZFCY3REREZFCY3BAREZFBYXJDREREBoXJDRERERkUJjdERERkUJjcEBERkUFhckNEREQGhckNERERGRQmN0RERGRQmNwQERGRQWFyQ0RERAaFyQ0REREZFCY3REREZFCY3BAREZFBYXJDREREBoXJDRERERkUJjdERERkUJjcEBERkUFhckNEREQGhckNERERGRQmN0RERGRQmNwQERGRQWFyQ0RERAaFyQ0REREZFCY3REREZFCY3BAREZFBYXJDREREBoXJDRERERkUJjdERERkUJjcEBERkUFhckNEREQGhckNERERGRQmN0RERGRQmNwUkzFbL2HJoVslHQYREZHBY3JTjAKD7uNpckZJ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",
      "text/plain": [
       "<Figure size 640x480 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "# Plot precision-recall curve\n",
    "display = PrecisionRecallDisplay.from_estimator(\n",
    "    xgb_cv.best_estimator_, X_test, y_test, name='XGBoost'\n",
    "    )\n",
    "plt.title('Precision-recall curve, XGBoost model');"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "wxyauTCNSyND"
   },
   "source": [
    "As recall increases, precision decreases. But what if you determined that false positives aren't much of a problem? For example, in the case of this Waze project, a false positive could just mean that a user who will not actually churn gets an email and a banner notification on their phone. It's very low risk.\n",
    "\n",
    "So, what if instead of using the default 0.5 decision threshold of the model, you used a lower threshold?\n",
    "\n",
    "Here's an example where the threshold is set to 0.4:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 43,
   "metadata": {
    "id": "iiKAYfRwS1bW"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([[0.9765248 , 0.0234752 ],\n",
       "       [0.5623678 , 0.43763223],\n",
       "       [0.9964199 , 0.00358006],\n",
       "       ...,\n",
       "       [0.80931014, 0.19068986],\n",
       "       [0.9623124 , 0.03768761],\n",
       "       [0.64760244, 0.35239756]], dtype=float32)"
      ]
     },
     "execution_count": 43,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Get predicted probabilities on the test data\n",
    "predicted_probabilities = xgb_cv.best_estimator_.predict_proba(X_test)\n",
    "predicted_probabilities"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "P4sLUzxFTcP9"
   },
   "source": [
    "The `predict_proba()` method returns a 2-D array of probabilities where each row represents a user. The first number in the row is the probability of belonging to the negative class, the second number in the row is the probability of belonging to the positive class. (Notice that the two numbers in each row are complimentary to each other and sum to one.)\n",
    "\n",
    "You can generate new predictions based on this array of probabilities by changing the decision threshold for what is considered a positive response. For example, the following code converts the predicted probabilities to {0, 1} predictions with a threshold of 0.4. In other words, any users who have a value ≥ 0.4 in the second column will get assigned a prediction of `1`, indicating that they churned."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 44,
   "metadata": {
    "id": "ynzlIds4S1xi"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([0, 1, 0, ..., 0, 0, 0])"
      ]
     },
     "execution_count": 44,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Create a list of just the second column values (probability of target)\n",
    "probs = [x[1] for x in predicted_probabilities]\n",
    "\n",
    "# Create an array of new predictions that assigns a 1 to any value >= 0.4\n",
    "new_preds = np.array([1 if x >= 0.4 else 0 for x in probs])\n",
    "new_preds"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 45,
   "metadata": {
    "id": "pYAKZbeqS5Vr"
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>model</th>\n",
       "      <th>precision</th>\n",
       "      <th>recall</th>\n",
       "      <th>F1</th>\n",
       "      <th>accuracy</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>XGB, threshold = 0.4</td>\n",
       "      <td>0.383333</td>\n",
       "      <td>0.226824</td>\n",
       "      <td>0.285006</td>\n",
       "      <td>0.798252</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "                  model  precision    recall        F1  accuracy\n",
       "0  XGB, threshold = 0.4   0.383333  0.226824  0.285006  0.798252"
      ]
     },
     "execution_count": 45,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Get evaluation metrics for when the threshold is 0.4\n",
    "get_test_scores('XGB, threshold = 0.4', new_preds, y_test)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "Z91aOpXQS51a"
   },
   "source": [
    "Compare these numbers with the results from earlier."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 46,
   "metadata": {
    "id": "i-tsYPiJTzdn"
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>model</th>\n",
       "      <th>precision</th>\n",
       "      <th>recall</th>\n",
       "      <th>F1</th>\n",
       "      <th>accuracy</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>RF cv</td>\n",
       "      <td>0.457163</td>\n",
       "      <td>0.126782</td>\n",
       "      <td>0.198445</td>\n",
       "      <td>0.818510</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>XGB cv</td>\n",
       "      <td>0.442586</td>\n",
       "      <td>0.173468</td>\n",
       "      <td>0.248972</td>\n",
       "      <td>0.814780</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>RF val</td>\n",
       "      <td>0.445255</td>\n",
       "      <td>0.120316</td>\n",
       "      <td>0.189441</td>\n",
       "      <td>0.817483</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>XGB val</td>\n",
       "      <td>0.430769</td>\n",
       "      <td>0.165680</td>\n",
       "      <td>0.239316</td>\n",
       "      <td>0.813287</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>XGB test</td>\n",
       "      <td>0.388889</td>\n",
       "      <td>0.165680</td>\n",
       "      <td>0.232365</td>\n",
       "      <td>0.805944</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "      model  precision    recall        F1  accuracy\n",
       "0     RF cv   0.457163  0.126782  0.198445  0.818510\n",
       "0    XGB cv   0.442586  0.173468  0.248972  0.814780\n",
       "0    RF val   0.445255  0.120316  0.189441  0.817483\n",
       "0   XGB val   0.430769  0.165680  0.239316  0.813287\n",
       "0  XGB test   0.388889  0.165680  0.232365  0.805944"
      ]
     },
     "execution_count": 46,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "results"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "F_Hl_g6rTz5o"
   },
   "source": [
    "Recall and F1 score increased significantly, while precision and accuracy decreased.\n",
    "\n",
    "So, using the precision-recall curve as a guide, suppose you knew that you'd be satisfied if the model had a recall score of 0.5 and you were willing to accept the \\~30% precision score that comes with it. In other words, you'd be happy if the model successfully identified half of the people who will actually churn, even if it means that when the model says someone will churn, it's only correct about 30% of the time.\n",
    "\n",
    "What threshold will yield this result? There are a number of ways to determine this. Here's one way that uses a function to accomplish this."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 47,
   "metadata": {
    "id": "ahSvceazUsnP"
   },
   "outputs": [],
   "source": [
    "def threshold_finder(y_test_data, probabilities, desired_recall):\n",
    "    '''\n",
    "    Find the threshold that most closely yields a desired recall score.\n",
    "\n",
    "    Inputs:\n",
    "        y_test_data: Array of true y values\n",
    "        probabilities: The results of the `predict_proba()` model method\n",
    "        desired_recall: The recall that you want the model to have\n",
    "\n",
    "    Outputs:\n",
    "        threshold: The threshold that most closely yields the desired recall\n",
    "        recall: The exact recall score associated with `threshold`\n",
    "    '''\n",
    "    probs = [x[1] for x in probabilities]  # Isolate second column of `probabilities`\n",
    "    thresholds = np.arange(0, 1, 0.001)    # Set a grid of 1,000 thresholds to test\n",
    "\n",
    "    scores = []\n",
    "    for threshold in thresholds:\n",
    "        # Create a new array of {0, 1} predictions based on new threshold\n",
    "        preds = np.array([1 if x >= threshold else 0 for x in probs])\n",
    "        # Calculate recall score for that threshold\n",
    "        recall = recall_score(y_test_data, preds)\n",
    "        # Append the threshold and its corresponding recall score as a tuple to `scores`\n",
    "        scores.append((threshold, recall))\n",
    "\n",
    "    distances = []\n",
    "    for idx, score in enumerate(scores):\n",
    "        # Calculate how close each actual score is to the desired score\n",
    "        distance = abs(score[1] - desired_recall)\n",
    "        # Append the (index#, distance) tuple to `distances`\n",
    "        distances.append((idx, distance))\n",
    "\n",
    "    # Sort `distances` by the second value in each of its tuples (least to greatest)\n",
    "    sorted_distances = sorted(distances, key=lambda x: x[1], reverse=False)\n",
    "    # Identify the tuple with the actual recall closest to desired recall\n",
    "    best = sorted_distances[0]\n",
    "    # Isolate the index of the threshold with the closest recall score\n",
    "    best_idx = best[0]\n",
    "    # Retrieve the threshold and actual recall score closest to desired recall\n",
    "    threshold, recall = scores[best_idx]\n",
    "\n",
    "    return threshold, recall\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "13WcPXrEUgHx"
   },
   "source": [
    "Now, test the function to find the threshold that results in a recall score closest to 0.5."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 48,
   "metadata": {
    "id": "rVlW592vT_kT"
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(0.124, 0.5029585798816568)"
      ]
     },
     "execution_count": 48,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Get the predicted probabilities from the champion model\n",
    "probabilities = xgb_cv.best_estimator_.predict_proba(X_test)\n",
    "\n",
    "# Call the function\n",
    "threshold_finder(y_test, probabilities, 0.5)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "gZkp1FY3Uw22"
   },
   "source": [
    "Setting a threshold of 0.124 will result in a recall of 0.503.\n",
    "\n",
    "To verify, you can repeat the steps performed earlier to get the other evaluation metrics for when the model has a threshold of 0.124. Based on the precision-recall curve, a 0.5 recall score should have a precision of \\~0.3."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 49,
   "metadata": {
    "id": "2PuIr8byUx8y"
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>model</th>\n",
       "      <th>precision</th>\n",
       "      <th>recall</th>\n",
       "      <th>F1</th>\n",
       "      <th>accuracy</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>XGB, threshold = 0.124</td>\n",
       "      <td>0.304296</td>\n",
       "      <td>0.502959</td>\n",
       "      <td>0.379182</td>\n",
       "      <td>0.708042</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "                    model  precision    recall        F1  accuracy\n",
       "0  XGB, threshold = 0.124   0.304296  0.502959  0.379182  0.708042"
      ]
     },
     "execution_count": 49,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Create an array of new predictions that assigns a 1 to any value >= 0.124\n",
    "probs = [x[1] for x in probabilities]\n",
    "new_preds = np.array([1 if x >= 0.124 else 0 for x in probs])\n",
    "\n",
    "# Get evaluation metrics for when the threshold is 0.124\n",
    "get_test_scores('XGB, threshold = 0.124', new_preds, y_test)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "LUvjKSSJVDMH"
   },
   "source": [
    "It worked! Hopefully now you understand that changing the decision threshold is another tool that can help you achieve useful results from your model."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**Congratulations!** You've completed this lab. However, you may not notice a green check mark next to this item on Coursera's platform. Please continue your progress regardless of the check mark. Just click on the \"save\" icon at the top of this notebook to ensure your work has been logged."
   ]
  }
 ],
 "metadata": {
  "colab": {
   "provenance": [
    {
     "file_id": "1DHsmIEwaXUmfVT4tFzyOwyyfXAX0v6IF",
     "timestamp": 1675262571681
    },
    {
     "file_id": "1oNheYh5WbljxkvoK_BMkQTey2DWnFXMs",
     "timestamp": 1674856595373
    }
   ]
  },
  "kernelspec": {
   "display_name": "Python 3 (ipykernel)",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.11.3"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 1
}
