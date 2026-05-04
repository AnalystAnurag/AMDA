Visualizations:

After data splitting, box plot to show categorical dependent variable against multiple predictors

import matplotlib.pyplot as plt

# Convert target
train['default_label'] = train['default'].map({0: 'Non-Defaulter', 1: 'Defaulter'})

num_vars = ["age", "employ", "income", "debtinc", "creddebt", "othdebt"]
colors = ['#4CAF50', '#F44336']  # Green, Red

fig, axes = plt.subplots(2, 3, figsize=(15, 8))
axes = axes.flatten()

for ax, var in zip(axes, num_vars):
    bp = train.boxplot(column=var, by='default_label', ax=ax, patch_artist=True)
    
    # Apply colors
    for patch, color in zip(ax.artists, colors):
        patch.set_facecolor(color)
    
    ax.set_title(var)
    ax.set_xlabel("")
    ax.set_ylabel("")

plt.suptitle("")
plt.tight_layout()
plt.show()
#......
import matplotlib.pyplot as plt
import seaborn as sns

sns.set_style("whitegrid")

# -------------------------------
# Variables (edit as needed)
# -------------------------------
target_cat = 'default'      # categorical target
target_cont = 'target'      # continuous target
time_var = 'date'           # time variable

num_vars = ["age", "employ", "income", "debtinc", "creddebt", "othdebt"]

# -------------------------------
# 1. BOX PLOTS (Categorical Target vs Predictors)
# -------------------------------
fig, axes = plt.subplots(2, 3, figsize=(15, 8))

for ax, var in zip(axes.flatten(), num_vars):
    sns.boxplot(x=target_cat, y=var, data=train, ax=ax)
    ax.set_title(f"{var} vs {target_cat}")

plt.tight_layout()
plt.show()


# -------------------------------
# 2. SCATTER PLOTS (Continuous Target vs Predictors)
# -------------------------------
fig, axes = plt.subplots(2, 3, figsize=(15, 8))

for ax, var in zip(axes.flatten(), num_vars):
    sns.scatterplot(x=var, y=target_cont, data=train, ax=ax)
    ax.set_title(f"{target_cont} vs {var}")

plt.tight_layout()
plt.show()


# -------------------------------
# 3. LINE PLOTS (Time Series)
# -------------------------------
if time_var in train.columns:
    fig, axes = plt.subplots(2, 3, figsize=(15, 8))

    for ax, var in zip(axes.flatten(), num_vars):
        sns.lineplot(x=time_var, y=var, data=train, ax=ax)
        ax.set_title(f"{var} over time")

    plt.tight_layout()
    plt.show()


# -------------------------------
# 4. PAIRPLOT (Predictor Relationships)
# -------------------------------
sns.pairplot(train[num_vars])
plt.show()


# -------------------------------
# 5. HISTOGRAM (Target Distribution)
# -------------------------------
plt.figure(figsize=(6,4))
sns.histplot(train[target_cont], kde=True)
plt.title("Target Distribution")
plt.show()

#............................
Correlation Matrix:
## Let's create correlation matrix 
cols = ['Y','X1','X2','X3','X4','X5','X6']
corr_matrix = emp[cols].corr(method= 'pearson')
corr_matrix

correlation plot
# Let's view the matrix 
plt.figure(figsize=(8,6))
sns.heatmap(corr_matrix,
            annot=True,
            fmt=".2f",
            cmap='coolwarm',
            center=0)
plt.title("Correlation Matrix of Variables")
plt.show()

#-----------------------------------------------------------------------------------------------------------------------#
Linear Regression 
PRF:
$$
\begin{aligned}
Y = \beta_0 +\beta_1 X1 +\beta_2 X2+\beta_3 X3+\\
+\beta_4 X4+\beta_5X5 + u
\end{aligned}
$$
 Interpretation:
	R2 value , F-statistic, p-value (<0.05) 
	Individual variable coefficient (t-value and p-value)
	1 unit increase in X1 increases or decreases the beta_1 unit in Y. 

Log-linear 

PRF: 

$$
\begin{aligned}
Log(Y) = \beta_0 +\beta_1 X1 +\beta_2 X2+\beta_3 X3+\\
+\beta_4 X4+\beta_5X5 + u
\end{aligned}
$$
Interpretation:
	R2 value , F-statistic, p-value (<0.05) 
	Individual variable coefficient (t-value and p-value)
	A 1 unit increase in X1 leads to a 100×β_1% change in Y 

Log-log Model
PRF:
$$
\begin{aligned}
Log(Y) = \beta_0 +\beta_1 log(X1) +\beta_2 log(X2)+\beta_3 X3+\\
+\beta_4 X4+\beta_5X5 + u
\end{aligned}
$$
Interpretation: 
	R2 value , F-statistic, p-value (<0.05) 
	Individual variable coefficient (t-value and p-value)
	A 1% increase in Xleads to a β_1% change in Y.
#--------------------------------------------------------------------------------------------------------------------------------#
Dummy Variables: 
# Convert to category
bike['season'] = bike['season'].astype('category')
bike['weathersit'] = bike['weathersit'].astype('category')

# Create dummies
dummies = pd.get_dummies(
    bike[['season', 'weathersit']],
    drop_first=True
).astype(int)
bike = pd.concat([bike, dummies], axis= 1)
print(bike.head())
SRF: 
$$
\begin{aligned}
log(cnt) = beta_0 + \beta_1 temp + \beta_2 hum +\beta_3 windspeed+\beta_4 season_2 +\beta_5 season_3+\beta_6 season_4 \\
+\beta_7 weathersit_1 +\beta_8 weathersit_2 +\beta_9 weathersit_3+\beta_10 workingday + u 
\end{aligned} 
$$
#----------------------------------------------------------------------------------------------------------------------------#
Interaction Term
Female as interaction term
$$
\begin{aligned}
lwage_i &= \beta_0 
+ \beta_1 belavg_i 
+ \beta_2 abvavg_i 
+ \beta_3 female_i 
+ \beta_4 educ_i \\
&\quad + \beta_5 exper_i 
+ \beta_6 expersq_i 
+ \beta_7 (female_i \times belavg_i) \\
&\quad + \beta_8 (female_i \times abvavg_i)
+ \beta_9 (female_i \times educ_i) \\
&\quad + \beta_{10} (female_i \times exper_i)
+ \beta_{11} (female_i \times expersq_i)
+ u_i
\end{aligned}
$$
	Joint Hypothesis test: 
hypothesis = """
female:belavg = 0,
female:abvavg = 0,
female:educ = 0,
female:exper = 0,
female:expersq = 0
"""
print("F-Test for Joint Significance of Interaction Terms:\n")
model2.f_test(hypothesis)
#----------------------------------------------------------------------------------------------------------------------------#
## For single predictor hypothesis check, we perform t-test
 
model8.t_test("LNP= 0")

#............................................................................................................................................#
Diagnostics:
(1)Residual diagnostics
	Residual vs Fitted values : If curve line or non random pattern appears, non linearity is there.
	Q-Q plot: If at the tails some outliers are there and on the line data points are not aligned then non-normal distribution is there. 
	Scale-Location plot: If residuals are  not randomly scattered, meaning some pattern is there like spread more towards the end or beginning or any funnel shape is there, it indicates heteroscedasticity.
	Residual vs Leverage plot: It shows influential points capturing overpowering residuals.
Code:
Import lmdiag 
lmdiag.plot(model)
#--------------------------------------------------------------------------------------------------------------------------------#
(2) Multicollinearity:
	Correlation plot and model high condition number shows the presence of potential multicollinearity.
VIF calculation:
# VIF
from statsmodels.stats.outliers_influence import variance_inflation_factor  
X = model1.model.exog 
# Get feature names - corrected to use the model's exog_names attribute
feature_names = model1.model.exog_names  

# Compute VIF 
vif_data = pd.DataFrame({
    "Variable": feature_names, 
    "VIF": [variance_inflation_factor(X, i) for i in range(X.shape[1])] 
})

print(vif_data)

Interpretation:
If VIF >10 : High Multicollinearity
Vif (5-10): Moderate Multicollinearity
Vif(1-5): Very negligible multicollinearity 
Vif=1 : No Multicollinearity

#.....................................................................................................................................#
(3) Hetroscedasticity:
$$
\begin{aligned}
H_0&: Homoscedasticity
H_1&: Heteroscedasticity
\end{aligned}
$$

import statsmodels.stats.api as sms
residuals = model1.resid 
exog = model1.model.exog 
bp_test = sms.het_breuschpagan(residuals, exog)

labels = ['LM Statistic', 'LM p-value', 'F-Statistic', 'F p-value']
print(dict(zip(labels, bp_test)))

Interpretation:
	If p< 0.05, reject null hypothesis 

#--------------------------------------------------------------------------------------------------------------------------------#
(4) Autocorrelation
## Calculating residuals
blaisdell['uhat_1']= model1.resid 
blaisdell['uhat_2']= model1.resid.shift(1)
#### ACF plot
uhat1 = blaisdell['uhat_1'].values 
import statsmodels.api as sm
## Let's compute Lag.Max Equivalent 
lag_max = int(np.floor(len(uhat1)/4)) 

plt.clf() 
sm.graphics.tsa.plot_acf(uhat1, lags = lag_max) 
plt.title("ACF Plot of residuals")
plt.tight_layout() 
plt.show()
### PACF plot 
uhat1 = blaisdell['uhat_1'].values 
#import statsmodels.api as sm
## Let's compute Lag.Max Equivalent 
lag_max = int(np.floor(len(uhat1)/4)) 

plt.clf() 
sm.graphics.tsa.plot_pacf(uhat1, lags = lag_max) 
plt.title("PACF Plot of residuals")
plt.tight_layout() 
plt.show()

### Durbin-Watson Test (1st order Autocorrelation) 
from statsmodels.stats.stattools import durbin_watson
dw = durbin_watson(model1.resid)
print("Durbin-Watson Statistic:", dw)

interpretation:
	DW ≈ 2 → Good model (no autocorrelation) 
	DW < 2 → Residuals are positively correlated 
	DW > 2 → Residuals are negatively correlated 
### Breusch-Godfrey Test 
from statsmodels.stats.diagnostic import acorr_breusch_godfrey

bg_test = acorr_breusch_godfrey(model1, nlags=4)

print("LM Statistic:", bg_test[0])
print("LM p-value:", bg_test[1])
print("F Statistic:", bg_test[2])
print("F p-value:", bg_test[3])

Interpretation: 
If p-value > 0.05 → Fail to reject H₀
 No autocorrelation ✅ 
 If p-value < 0.05 → Reject H₀
Autocorrelation present ⚠️
#.................................................................................................................................................#
(5)Model Misspecification Test: 
#RESET test 
from statsmodels.stats.diagnostic import linear_reset
reset_test = linear_reset(model_improved, power=2, use_f=True) 
print("\nRESET Test F-stat:", reset_test.fvalue)
print("RESET Test p-value:", reset_test.pvalue)

#--------------------------------------------------------------------------------------------------------------------------------#
Logistic Regression
## PRF:
$$
\log\left(\frac{P(\text{Cancer}=1 \mid X)}{1 - P(\text{Cancer}=1 \mid X)}\right)
= \beta_0 + \beta_1 \text{Age} + \beta_2 \text{HIGD} + \beta_3 \text{CHK} + \beta_4 \text{AGPI} + \beta_5 \text{Miscarriages} + \beta_6 \text{Weight}
$$
Code:
import statsmodels.api as sm
log_model = smf.glm('Cancer~Age+HIGD+CHK+AGPI+Miscarriages+Weight', family = sm.families.Binomial(), data = benign).fit() 
print(log_model.summary())

## Interpretation:
*pseudo rsquared :
0.02 – 0.10	Weak fit
0.10 – 0.20	Moderate fit
0.20 – 0.40	Good fit 
> 0.40	Very strong fit (rare)

## Log of odds, Odds ratio and Percentage
coefficients = logit_fit1.params 

#calculate the odds ratios 
odds_ratios = np.exp(coefficients) 
#calculate the percentage change in odds 
percent_change = (odds_ratios - 1)* 100 

#create a pandas dataframe to combine the results 

results = pd.DataFrame({
    'Estimate': coefficients.round(4), 
    'OR': odds_ratios.round(4), 
    '% Change in Odds': percent_change.round(2) }) 
results
######Why R2 is meaning less in case of logistic regression ?
#Why R2 value is very low, present graphically
# plot debtinc vs default
plt.figure(figsize=(8,6))
plt.plot(train['debtinc'], train['default'],'*', color = "red") 
plt.title('Debt to income vs. Default') 
plt.xlabel("Debtinc")
plt.ylabel("Default")
# Fit a regression model 
model = smf.ols(formula='default~debtinc',data=train).fit() 

# Plot the regression line
plt.plot(train['debtinc'],model.fittedvalues, color ="blue") 
plt.grid(True)
plt.show()
#-----------------------------------------------------------------------------------------------------------------------------#
Pseudo R2:
# Mc Fadden R2
mc_fadden = log_model.pseudo_rsquared(kind="mcf").round(2)
# Cox-Snell R2
cs = log_model.pseudo_rsquared(kind="cs").round(2)
print("Mc Fadden R square:", mc_fadden) 
print("Cox-Snell R square:", cs)
#-----------------------------------------------------------------------------------------------------------------------------#
Confusion Matrix
from sklearn.metrics import confusion_matrix

y_true = benign['Cancer']
y_pred = (log_model.predict(benign)>0.5).astype(int) 

cm= confusion_matrix(y_true,y_pred)

tn,fp,fn,tp = cm.ravel()

## metrics
Accuracy = (tp+tn)/cm.sum()
sensitivity = tp / (tp + fn)   # recall
specificity = tn / (tn + fp)

print("Confusion Matrix:\n", cm)
print("Accuracy:", accuracy)
print("Sensitivity:", sensitivity)
print("Specificity:", specificity)

Interpretation:
Accuracy: Proportion of total correct predictions
Sensitivity/Recall: Ability to correctly identify actual positives
Specificity: Ability to correctly identify actual negatives
Precision: Out of predicted positives, how many are actually positive

### ROC Curve
# Predict probabilities for the positive class (response)
prob = logit_fit1.predict()
# Get the actual outcome values
actual = logit_fit1.model.endog
# Call the libraries
from sklearn.metrics import roc_curve, auc
# Calculate ROC curve
fpr, tpr, thresholds = roc_curve(actual, prob)
# Calculate AUC
roc_auc = auc(fpr, tpr)
# Plot ROC curve
plt.figure(figsize=(8, 6))
plt.plot(fpr, tpr, color='blue', lw=2, label=f'ROC curve (AUC = {roc_auc:.2f})')
plt.plot([0, 1], [0, 1], color='gray', linestyle='--')  # Diagonal line for random guessing
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate (FPR)')
plt.ylabel('True Positive Rate (TPR)')
plt.title('ROC Curve for logit_fit1')
plt.legend(loc='lower right')
plt.show()

#.. To combinely show coefficients

import statsmodels.formula.api as smf 
from statsmodels.iolib.summary2 import summary_col
model1 = smf.ols('lwage~belavg+abvavg', data = beauty[beauty['female']==0]).fit() 
model2 = smf.ols('lwage~belavg+abvavg', data = beauty[beauty['female']==1]).fit()

table = summary_col([model1,model2],model_names=['men', 'women'], stars = True)
print(table)
