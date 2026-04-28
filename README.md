## PCA 
###############################
Step 1 — Import Libraries and Load Data
library(readxl)
library(REdaS)

pca_p <- read_excel("MTE_BA_G2.xlsx", sheet = "PCA")
head(pca_p)    # Preview first 6 rows
attach(pca_p)  # Allow direct column name references
####################################
Step 2 — Data Exploration
str(pca_p)           # Structure: variable types and dimensions
summary(pca_p)       # Descriptive statistics
colSums(is.na(pca_p)) # Check missing values per column
#########################################
Step 3 — Subset Numeric Columns
pca_num <- cbind(E1, E2, E3, E4, E5, E6, E7, E8, E9, E10,
                 N1, N2, N3, N4, N5, N6, N7, N8, N9, N10,
                 A1, A2, A3, A4, A5, A6, A7, A8, A9, A10,
                 C1, C2, C3, C4, C5, C6, C7, C8, C9, C10,
                 O1, O2, O3, O4, O5, O6, O7, O8, O9, O10)
##############################################
Step 4 — Correlation Matrix and Heatmap
library(corrplot)

corr_mat <- cor(pca_num)   # Compute correlation matrix

corrplot(corr_mat,
         method = "color",
         diag = TRUE,
         tl.cex = 0.1,
         addCoef.col = "black",
         number.cex = 0.2)
###################################################
Step 5 — KMO Test
library(REdaS)
KMOS(pca_num)   # Kaiser-Meyer-Olkin Measure of Sampling Adequacy
####################################################
Step 6 — Bartlett's Test of Sphericity
bart_spher(pca_num)   # Bartlett's Sphericity Test
#####################################################
Step 7 — Run PCA
pca1 <- princomp(pca_num, cor = TRUE, scores = TRUE)
pca1   # Display summary of principal components
########################################################
Step 8 — View Loadings (Without Rotation)
loadings(pca1)   # View variable loadings on each component
###########################################################
Step 9 — Scree Plot
plot(pca1)                                   # Variance bar chart
screeplot(pca1, type = "line", main = "Scree Plot")  # Line scree plot
#############################################################
Step 10 — Biplot
biplot(pca1)   # Biplot of scores and loadings on PC1 and PC2
###########################################################
Step 11 — Rotation (Varimax and Promax)
varimax(pca1$loadings)   # Orthogonal rotation
promax(pca1$loadings)    # Oblique rotation
#########################################################
## How to Interpret?
4.1 Interpreting the Correlation Matrix
What to Look For	What It Means
Correlation close to +1	Two variables increase together — positively related
Correlation close to -1	When one increases, the other decreases — negatively related
Correlation close to 0	Variables are largely unrelated
Clusters of high correlations	Suggest an underlying common factor — good for PCA
Uniformly low correlations	PCA may not be meaningful — variables are independent

4.2 Interpreting the KMO Value
The overall KMO value of 0.87 tells us that 87% of the variance in the dataset can be attributed to common factors (shared structure). Individual MSA values below 0.5 for any variable would flag it as unsuitable for inclusion in PCA. In this dataset, all variables are above the acceptable threshold.

4.3 Interpreting Bartlett's Test
Since the p-value is far below 0.05, we reject the null hypothesis that the correlation matrix is an identity matrix. This confirms that the variables are meaningfully correlated with each other, which is a prerequisite for PCA to work correctly. Without shared correlations, PCA has no structure to extract.

4.4 Interpreting princomp() Standard Deviations
The standard deviations represent the square root of eigenvalues. The eigenvalue for each component tells us how much variance it captures. The rule of thumb (Kaiser's criterion) is to retain components with eigenvalues > 1 (i.e., standard deviation > 1). This means the component captures more variance than any single original variable.

Component	Interpretation
Comp.1 (SD > 1)	Largest variance captured — most important dimension
Comp.2 through Comp.9	Each successive component captures less variance
Comp.10 onwards	Eigenvalue < 1 — not retained (explain less than one variable)

4.5 Interpreting Loadings
Loadings are the correlations between original variables and principal components. Guidelines for interpretation:
Loading Magnitude	Strength of Association
|loading| >= 0.7	Very strong — variable is a major contributor to the component
|loading| 0.5 – 0.69	Strong association
|loading| 0.3 – 0.49	Moderate — variable partially defines the component
|loading| < 0.3	Weak — variable does not meaningfully contribute (often suppressed by R)

A component is named/labelled based on which variables load most strongly onto it. For example, if E1–E10 items all load highly onto Component 1, that component is interpreted as 'Extraversion.'

4.6 Interpreting the Scree Plot
The x-axis shows component numbers (1 to 50), and the y-axis shows eigenvalues. The key decision rule: look for the 'elbow' — the point after which eigenvalues drop sharply and level off. Components before the elbow are retained. Components after the elbow contribute trivially. Kaiser's Rule (eigenvalue > 1) overlaps with this: both suggest retaining the first 9 components in this analysis.

4.7 Interpreting the Biplot
In the biplot of PC1 vs PC2:
•	Arrows pointing in the same direction → those variables are positively correlated with each other.
•	Arrows pointing in opposite directions → those variables are negatively correlated.
•	Arrow length → longer arrows indicate the variable is more influential.
•	Points clustered together → those respondents have similar score profiles.
•	Points near an arrow tip → those respondents score high on that variable.

4.8 Interpreting Rotation (Varimax / Promax)
After rotation, the loading matrix is reinterpreted. The goal of rotation is to achieve simple structure:
•	Each variable should load highly on one component and near zero on others.
•	Each component should be defined by a distinct cluster of variables.
This makes it easier to label and interpret each component as a coherent construct. In personality research, we expect 5 components (matching the Big Five dimensions) to emerge after rotation.

##-------------------------------------------------------------------------------------------##
## FA
#########################################################
Step 1 — Load Data & Build Variable Matrix
mydata <- read.csv("pca_gsp.csv")
attach(mydata)
# Bind all 13 numeric economic sector columns into matrix X
X <- cbind(Ag, Mining, Constr, Manuf, Manuf_nd, Transp, Comm,
           Energy, TradeW, TradeR, RE, Services, Govt)
#######################################################
Step 2 — Descriptive Statistics & Correlation
summary(X)   # Min, Q1, Median, Mean, Q3, Max for all 13 variables
cor(X)       # 13 x 13 Pearson correlation matrix
##########################################################
Step 3 — KMO & Bartlett's Tests
library(REdaS)
KMOS(X)         # Kaiser-Meyer-Olkin Measure of Sampling Adequacy
bart_spher(X)   # Bartlett's Test of Sphericity
#################################################
Step 4 — Factor Analysis: No Rotation
# ML Factor Analysis — 3 factors, no rotation
fa1 <- factanal(X, factor = 3, rotation = "none")
fa1   # Print loadings, uniquenesses, and fit test
################################################################
Step 5 — Factor Analysis: Varimax Rotation
# ML Factor Analysis — 3 factors, orthogonal Varimax rotation
fa2 <- factanal(X, factor = 3, rotation = "varimax")
fa2
##################################################################
Step 6 — Factor Analysis with Scores
# FA with Varimax + compute regression-based factor scores
fa3 <- factanal(X, factors = 3, rotation = "varimax", scores = "regression")
fa3
fa3$scores   # Factor score matrix: 50 states x 3 factors
####################################################################
Step 7 — Factor Analysis: Promax Rotation
# ML Factor Analysis — 3 factors, oblique Promax rotation
fa4 <- factanal(X, factor = 3, rotation = "promax")
fa4
########################################################################
Step 8 — PCA for Reference (Scree Plot)
# PCA for scree plot to guide number of factors
pca1 <- princomp(X, scores = TRUE, cor = TRUE)
summary(pca1)                                    # Variance explained
screeplot(pca1, type = "line", main = "Scree Plot")  # Elbow plot
biplot(pca1)                                     # Biplot PC1 vs PC2

# Rotation of PCA loadings for comparison
varimax(pca1$loadings[,1:3])   # Varimax on first 3 PCs
promax(pca1$loadings[,1:3])    # Promax on first 3 PCs
########################################################################
## How to Interpret ?
4.1 Interpreting Factor Loadings
Factor loadings are correlations between original variables and the extracted factors. Use these thresholds:
Loading |value|	Interpretation
|loading| >= 0.70	Very strong — variable is a core definer of the factor
|loading| 0.50–0.69	Strong — variable significantly contributes
|loading| 0.30–0.49	Moderate — partial contribution
|loading| < 0.30	Weak — variable does not define this factor (often suppressed by R)

To name/label a factor: look at the variables with the highest loadings on that factor and find the common conceptual theme. For example, if Manuf, Manuf_nd, and Constr all load highly on Factor 1, it is labelled the 'Industrial/Manufacturing' factor.
4.2 Interpreting Uniqueness (Specific Variance)
Uniqueness = 1 - Communality. It represents the proportion of a variable's variance that is NOT shared with other variables through the common factors.
•	Low uniqueness (< 0.40) → High communality → Variable is well-explained by the factors. Good.
•	High uniqueness (> 0.60) → Low communality → Variable is mostly unique, not captured well by the factors. May indicate a variable that doesn't fit the factor structure.
📌 Note: If many variables have high uniqueness, consider extracting more factors or re-examining the variable set.

4.3 Interpreting Variance Explained
Metric	What It Tells You
SS Loadings per Factor	Variance captured by that factor (like eigenvalue)
Proportion Var	Share of total variance explained by each factor
Cumulative Var	Total variance explained by all retained factors combined
Target	Aim for Cumulative Var > 60–70% for adequate explanation

4.4 Interpreting the Chi-Square Test (Model Fit)
factanal() reports a chi-square test after fitting. This tests whether the chosen number of factors (here: 3) provides a statistically adequate fit to the correlation matrix.
•	p > 0.05 → Fail to reject H0 → 3 factors are sufficient to explain the correlations.
•	p < 0.05 → Reject H0 → 3 factors are not sufficient — try extracting more factors.
📌 Note: With n = 50 states (small sample), this test has limited power and results should be interpreted cautiously alongside substantive criteria.

4.5 Interpreting Factor Scores
Factor scores are the coordinates of each observation (state) in the factor space. They have mean ≈ 0 and SD ≈ 1. Use them to:
•	Profile each state: Which factor(s) are dominant for each state?
•	Compare states: States with similar profiles are nearby in factor space.
•	Input for further analysis: Use scores as predictors in regression or as input for cluster analysis.
4.6 Interpreting Factor Correlation Matrix (Promax only)
The factor correlation matrix shows how much the extracted factors overlap. For a Promax solution:
•	Values close to 0 → Factors are nearly orthogonal (similar to Varimax).
•	Values of 0.3–0.6 → Moderate correlation — factors share some variance but remain distinguishable.
•	Values > 0.7 → Factors overlap heavily — may indicate too many factors were extracted.

##-------------------------------------------------------------------------------------------##
## Cluster Analysis
###############################################################
Step 1 — Load Data & Scatter Plot
mydata <- read.csv("utilities.csv")
attach(mydata)
###############################################################
# Exploratory scatter plot — Fuel Cost vs Sales
plot(Fuel_Cost ~ Sales, data = mydata)
with(mydata, text(Fuel_Cost ~ Sales, labels = Company, pos = 4))
###################################################################################
Step 2 — Normalise the Data
# Remove the Company column (column 1) — keep only numeric variables
z <- mydata[, -c(1, 1)]

# Compute column-wise means and standard deviations
means <- apply(z, 2, mean)
sds   <- apply(z, 2, sd)

# Z-score normalisation
nor <- scale(z, center = means, scale = sds)
nor   # View normalised matrix (22 x 8)
#######################################################################################
Step 3 — Compute Euclidean Distance Matrix
# Pairwise Euclidean distances between all 22 companies
distance <- dist(nor)
print(distance, digits = 3)   # View the 22x22 triangular matrix
##############################################################################
Step 4 — Hierarchical Clustering: Complete Linkage
# Default hclust uses complete linkage
mydata.hclust <- hclust(distance)

# Plot dendrograms
plot(mydata.hclust)                                         # Basic dendrogram
plot(mydata.hclust, labels = mydata$Company,
     main = 'Default from hclust')                          # Labelled dendrogram
plot(mydata.hclust, hang = -1)                              # Hang labels at bottom
######################################################################################
Step 5 — Hierarchical Clustering: Average Linkage
# Hierarchical clustering with average linkage
mydata.hclusta <- hclust(distance, method = "average")
plot(mydata.hclusta, hang = -1)   # Dendrogram with company labels at bottom
#################################################################################
Step 6 — Cut Tree & Cross-Tabulate
# Cut dendrogram into 3 clusters
member.c <- cutree(mydata.hclust,  3)   # Complete linkage: 3 clusters
member.a <- cutree(mydata.hclusta, 3)   # Average linkage:  3 clusters

# Cross-tabulate cluster membership
table(member.c, member.a)   # Agreement matrix between two methods
######################################################
Step 7 — Characterise Clusters
# Cluster means on normalised scale
aggregate(nor, list(member.c), mean)

# Cluster means on original scale (more interpretable)
aggregate(mydata[, -c(1,1)], list(member.c), mean)
##################################################
Step 8 — Silhouette Plot
library(cluster)

# Silhouette plot for 3-cluster complete-linkage solution
plot(silhouette(cutree(mydata.hclust, 3), distance))
####################################################
Step 9 — Scree Plot (WSS)
# Within-cluster sum of squares for 1 to 20 clusters
wss <- (nrow(nor) - 1) * sum(apply(nor, 2, var))
for (i in 2:20) wss[i] <- sum(kmeans(nor, centers = i)$withinss)

plot(1:20, wss, type = "b",
     xlab = "Number of Clusters",
     ylab = "Within groups sum of squares")
#####################################################
Step 10 — K-Means Clustering
set.seed(123)          # For reproducibility
kc <- kmeans(nor, 4)   # K-means with 4 clusters
kc                     # Print cluster details

# Visualise: Sales vs D.Demand coloured by cluster
plot(Sales ~ D.Demand, mydata, col = kc$cluster)
#####################################################
## How to interpret ?
4.1 Interpreting the Dendrogram
Feature	What It Means
Height of merge	How dissimilar the two clusters were when they merged — taller = more different
Companies at same branch	Similar utility profiles — early merges are most alike
Long vertical line before merge	Large gap in similarity — natural cluster boundary
Where to cut	Find the largest vertical gap — cut there to define k clusters

4.2 Interpreting the Cross-Tabulation
The table(member.c, member.a) output should ideally show a near-diagonal structure — most companies assigned to the same cluster regardless of linkage method. Perfect diagonal agreement means both methods produce identical cluster assignments. Off-diagonal entries indicate companies at cluster boundaries that are assigned differently depending on the linkage used.
4.3 Interpreting Cluster Means
Cluster means on the original scale allow business interpretation:
•	Compare each cluster's average across all 8 variables.
•	Identify which variables differ most between clusters — these are the defining characteristics.
•	Label each cluster using a business description: e.g., 'High-volume, low-cost producers' or 'Small, expensive urban utilities.'
4.4 Interpreting Silhouette Width
Average Silhouette Width	Quality Assessment
s > 0.70	Strong cluster structure — well-separated, coherent clusters
0.50 < s < 0.70	Reasonable structure
0.25 < s < 0.50	Weak structure — clusters overlap somewhat
s < 0.25	No substantial cluster structure found

4.5 Interpreting the WSS Scree Plot
The elbow point in the WSS plot marks the optimal cluster count. Before the elbow: adding clusters gives large variance reduction. After the elbow: adding clusters provides diminishing returns. The plot's x-axis is the number of clusters; the y-axis is the total within-group sum of squares. The elbow is typically at k=3 or k=4 for this utilities dataset.
4.6 Interpreting K-Means Output
K-Means Output Element	Interpretation
Cluster sizes	Number of companies per cluster — very unequal sizes may indicate outliers
Cluster means	The centroid of each cluster in variable space — the 'average company' of that cluster
Within-cluster SS	Internal compactness — smaller means tighter, more homogeneous clusters
Between SS / Total SS	Variance explained ratio — above 60% is considered a good clustering result

##-------------------------------------------------------------------------------------------##------------------------------
## MDS & CA
##########################################################
Exercise 1 — Classical (Metric) MDS | cmdscale
###########################################################
Step 1 · Data — Pairwise Dissimilarity Matrix
Eight automobile brands were rated on pairwise dissimilarity using a 1–9 scale (1 = very similar, 9 = very
dissimilar). The matrix is symmetric with zeros on the diagonal.
brands <- c("Toyota","Honda","Ford","BMW","Mercedes","Maruti","Hyundai","Audi")
dissim <- matrix(c(
0, 2, 4, 7, 8, 5, 3, 7,
2, 0, 4, 7, 8, 5, 3, 7,
4, 4, 0, 6, 7, 4, 4, 6,
7, 7, 6, 0, 2, 8, 7, 2,
8, 8, 7, 2, 0, 9, 8, 3,
5, 5, 4, 8, 9, 0, 2, 8,
3, 3, 4, 7, 8, 2, 0, 7,
7, 7, 6, 2, 3, 8, 7, 0
), nrow=8, dimnames=list(brands, brands))
#####################################################################
Step 2 · Run Metric MDS
mds_result <- cmdscale(dissim, k=2, eig=TRUE)
eigenvalues <- mds_result$eig
gof <- sum(eigenvalues[1:2]) / sum(abs(eigenvalues))
cat("Goodness of Fit:", round(gof, 3))
########################################################################
Step 3 · Extract Coordinates & Build Perceptual Map
coords <- as.data.frame(mds_result$points)
colnames(coords) <- c("Dim1", "Dim2")
coords$Brand <- brands
ggplot(coords, aes(x=Dim1, y=Dim2, label=Brand)) +
geom_point(size=4, color="steelblue") +
geom_text_repel(size=4, fontface="bold") +
geom_hline(yintercept=0, linetype="dashed", alpha=0.5) +
geom_vline(xintercept=0, linetype="dashed", alpha=0.5) +
labs(
title="Perceptual Map of Automobile Brands",
x="Dimension 1 (Economy - Luxury)",
y="Dimension 2 (Practical - Sporty)"
) + theme_minimal(base_size=13)
##################################################################################
Exercise 2 — Non-Metric MDS | smacof
################################################
Step 1 · Prepare Similarity Object
Non-metric MDS requires only ordinal (rank) information. The smacof package expects a similarity object, so we
invert the dissimilarity matrix: similarity = max(dissim) - dissim, then set the diagonal to zero.
library(smacof)
diss_obj <- max(dissim) - dissim
diag(diss_obj) <- 0
#############################################################################
Step 2 · Fit Ordinal MDS Model
nmds_result <- mds(diss_obj, ndim=2, type="ordinal")
cat("Stress-1:", round(nmds_result$stress, 4))

##############################################################################
Step 3 · Shepard Diagram
plot(nmds_result, plot.type="Shepard",
main="Shepard Diagram - Non-metric MDS")
#################################################################################
Step 4 · Scree Plot — Stress vs. Dimensions
stress_vals <- numeric(5)
for(d in 1:5){
fit <- mds(diss_obj, ndim=d, type="ordinal")
stress_vals[d] <- fit$stress
}
plot(1:5, stress_vals, type="b", pch=19, col="tomato",
xlab="Number of Dimensions", ylab="Stress",
main="Scree Plot: Stress vs. Dimensions")
abline(h=0.05, lty=2, col="gray50")
################################################################################
Exercise 3 — Correspondence Analysis | FactoMineR
###########################################################
Step 1 · Brand × Attribute Contingency Table
brand_attr <- matrix(c(
95, 30, 15, 5, 10, 20, # Brand A
20, 80, 40, 10, 25, 30, # Brand B
10, 15, 90, 5, 60, 35, # Brand C
5, 10, 20, 95, 15, 25, # Brand D
15, 20, 30, 10, 80, 70 # Brand E
), nrow=5, byrow=TRUE)
rownames(brand_attr) <- c("BrandA","BrandB","BrandC","BrandD","BrandE")
colnames(brand_attr) <- c("Affordable","Reliable","Stylish",
"EcoFriendly","Premium","Innovative")
#################################################################################
Step 2 · Chi-Square Test of Independence
Before running CA, we verify that brand and attribute are statistically non-independent — i.e., that the
associations in the table are not due to chance.
chisq_test <- chisq.test(brand_attr)
cat("Chi-square:", round(chisq_test$statistic, 2),
"| df:", chisq_test$parameter,
"| p-value:", format(chisq_test$p.value, digits=3))
###################################################################################
Step 3 · Run Correspondence Analysis & Inertia
library(FactoMineR)
library(factoextra)
ca_result <- CA(brand_attr, graph=FALSE)
summary(ca_result)
# Eigenvalues and inertia
cat("\n--- Inertia explained ---\n")
print(ca_result$eig)
##################################################
Step 4 · Symmetric Biplot
fviz_ca_biplot(
ca_result,
repel = TRUE,
title = "CA Biplot: Brand x Attribute Associations"
)
###################################################
Step 5 · Attribute Contributions by Dimension
fviz_contrib(ca_result, choice="col", axes=1,
title="Column Contributions to Dimension 1")
fviz_contrib(ca_result, choice="col", axes=2,
title="Column Contributions to Dimension 2")

##--------------------------------------------------------------------------------------------##---------------------------
## SEM
##################################
2.1 R Code
# Install required packages (run once)
install.packages("lavaan", dependencies = TRUE)
library(lavaan)

# Load dataset
dat <- read.csv("https://stats.idre.ucla.edu/wp-content/uploads/2021/02/worland5.csv")
###########################################
2.2 Covariance Matrix
# Sample variance-covariance matrix
cov(dat)
###########################################################################
3. Simple Regression (Model 1)
Simple regression models the relationship of one observed exogenous variable on a single observed endogenous variable. Here, Motivation (motiv) is hypothesised to predict Reading scores (read).

3.1 R Code — Base lm() approach
# Simple regression using lm()
m1a <- lm(read ~ motiv, data = dat)
(fit1a <- summary(m1a))

3.2 R Code — lavaan equivalent
# Simple regression using lavaan
m1b <- '
  # regression
  read ~ 1 + motiv
  # variance (optional)
  motiv ~~ motiv
'
fit1b <- sem(m1b, data = dat)
summary(fit1b)
#######################################################
4. Multiple Regression (Model 2)
Multiple regression extends the simple model by including two exogenous predictors: Negative Parental Psychology (ppsych) and Motivation (motiv) to predict Reading (read). The covariance between the two predictors is explicitly modelled.

4.1 R Code
m2 <- '
  # regression
  read ~ 1 + ppsych + motiv
  # covariance between predictors
  ppsych ~~ motiv
'
fit2 <- sem(m2, data = dat)
summary(fit2)

#################################################################
5. Multivariate Regression (Model 3)
Multivariate regression models multiple outcomes simultaneously. Here, both Reading (read) and Arithmetic (arith) are predicted by ppsych and motiv.

5.1 R Code
m3e <- '
  # regressions
  read  ~ 1 + ppsych + motiv
  arith ~ 1 + ppsych + motiv
'
fit3e <- sem(m3e, data = dat)
summary(fit3e)
####################################################################
6. Path Analysis (Model 4)
Path analysis extends multivariate regression by allowing endogenous variables to act as predictors of other endogenous variables. Here, Reading (read) mediates the effect of ppsych and motiv on Arithmetic (arith), in addition to having its own direct predictors.

6.1 R Code
m4b <- '
  # regressions
  read  ~ 1 + ppsych + motiv
  arith ~ 1 + motiv + read + ppsych
'
fit4b <- sem(m4b, data = dat)
summary(fit4b, fit.measures = TRUE)
###################################################################
7. Full Structural Equation Model — Structural Regression (Model 6A)
The full SEM integrates a measurement model (confirmatory factor analysis) with a structural regression model. Three latent constructs are specified: Adjustment, Risk, and Achievement.

7.1 Model Specification
Measurement Model:
•	Adjustment =~ motiv + harm + stabi
•	Risk       =~ verbal + ppsych + ses
•	Achieve    =~ read + arith + spell

Structural Model:
•	achieve ~ adjust + risk   (Adjustment and Risk predict Achievement)

7.2 R Code
m6a <- '
  # measurement model
  adjust  =~ motiv + harm + stabi
  risk    =~ verbal + ppsych + ses
  achieve =~ read + arith + spell

  # structural regression
  achieve ~ adjust + risk
'
fit6a <- sem(m6a, data = dat)
summary(fit6a, standardized = TRUE, fit.measures = TRUE)
############################################################



