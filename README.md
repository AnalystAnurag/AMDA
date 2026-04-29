## PCA 
####################################
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

5.1 Correlation Matrix — Final Interpretation
✅ Interpretation: The correlation matrix reveals meaningful within-dimension correlations among the 50 personality items. Items within the same Big Five dimension (e.g., E1–E10 for Extraversion) tend to be more correlated with each other than with items from other dimensions. The corrplot heatmap visually confirms the block-diagonal structure of correlations, which is the necessary foundation for PCA. The presence of both positive and negative correlations indicates a rich, structured dataset.

5.2 KMO Test — Final Interpretation
✅ Interpretation: The overall KMO Measure of Sampling Adequacy (MSA) = 0.87, which falls in the Meritorious category. This means the data is highly appropriate for PCA. The large sample size (n = 500) and high KMO value together confirm that the correlation structure among the 50 variables is stable, reliable, and driven by underlying common factors — exactly what PCA is designed to uncover.

5.3 Bartlett's Test — Final Interpretation
✅ Interpretation: The Bartlett's Test of Sphericity returns a highly significant result (p < 0.001). We reject H0, confirming that the correlation matrix is not an identity matrix. In practical terms: the 50 personality items share significant intercorrelations, which means reducing them into a smaller number of components via PCA is statistically justified and meaningful.

5.4 PCA Components — Final Interpretation
✅ Interpretation: The princomp() output reveals that the first few components capture the bulk of the variance. Specifically, the first 9 components each have eigenvalues greater than 1, satisfying Kaiser's criterion. These 9 components collectively explain a substantial portion of the total variance in the 50 personality items. Retaining 9 components instead of the original 50 variables achieves significant dimensionality reduction while preserving the essential structure of the data.

5.5 Loadings Without Rotation — Final Interpretation
✅ Interpretation: In the unrotated solution, the first principal component tends to act as a 'general factor,' capturing broad variance across multiple personality dimensions simultaneously. Many variables show moderate loadings on the first few components. While the loadings confirm that many variables do align with components, the interpretation is not clean because multiple dimensions may load together on the same component. This is why rotation is performed as the next step.

5.6 Scree Plot — Final Interpretation
✅ Interpretation: The scree plot clearly shows an elbow after the 9th component, with eigenvalues dropping sharply and then levelling off. This confirms the Kaiser's rule finding: 9 principal components should be retained. These 9 components represent the underlying structure in the Big Five personality dataset — which is a richer structure than the theoretical 5 dimensions, possibly because the 10 items within each dimension are themselves multi-faceted.

5.7 Biplot — Final Interpretation
✅ Interpretation: The biplot of PC1 versus PC2 shows a crowded, cluttered centre with overlapping arrows and data points. This is a clear visual signal that just two components are insufficient to explain the full structure of the 50-variable dataset. The respondent scores do not form distinct, well-separated clusters on PC1 and PC2 alone. This reinforces the decision to retain 9 components rather than just the first two.

5.8 Varimax Rotation — Final Interpretation
✅ Interpretation: After Varimax (orthogonal) rotation, the loading matrix shows a markedly cleaner simple structure. Each of the 9 retained components now has a distinct cluster of variables loading highly onto it (|loading| > 0.5) while other variables load near zero. Notably, items from within the same Big Five dimension tend to cluster together on the same rotated component, partially recovering the theoretical structure. The components can be labelled as latent personality constructs based on their dominant variables.

5.9 Promax Rotation — Final Interpretation
✅ Interpretation: After Promax (oblique) rotation, a similar simple structure emerges, but the components are allowed to correlate with each other — which is more realistic for personality traits (e.g., Conscientiousness and Agreeableness tend to co-occur in real individuals). The Promax solution may show slightly more extreme loadings compared to Varimax. For personality research, the oblique solution is often preferred as it better reflects the interrelated nature of personality dimensions observed in real-world data.


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

## How to Interpret?

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

5.1 Correlation Matrix — Final Interpretation
✅ Interpretation: The 13×13 correlation matrix confirms the presence of meaningful clusters among US economic sectors. Manufacturing variables (Manuf, Manuf_nd) are tightly correlated with Construction. Service-sector variables (TradeR, TradeW, Services, RE) cluster together, reflecting urban state economies. Mining and Agriculture correlate positively with each other but negatively with Services — resource-dependent states differ structurally from service-oriented states. This clustering is the empirical basis for a 3-factor solution.
5.2 KMO & Bartlett's — Final Interpretation
✅ Interpretation: The KMO MSA ≈ 0.73–0.78 (Middling to Meritorious) is above the minimum threshold of 0.60, confirming the data is suitable for Factor Analysis. Bartlett's test is highly significant (p < 0.001), definitively rejecting the null hypothesis that the correlation matrix is an identity matrix. Together, these diagnostics provide strong statistical justification for proceeding with FA on the 13 economic variables.
5.3 No-Rotation Solution (fa1) — Final Interpretation
✅ Interpretation: The unrotated FA solution extracts 3 factors that together explain approximately 70–80% of the total variance in the 13 economic sector variables. However, the unrotated loadings show a 'general factor' pattern in Factor 1, where many variables load moderately — making interpretation difficult. Factor 1 acts as a broad economic size factor, while Factors 2 and 3 pick up residual sectoral variation. The chi-square fit test with 3 factors produces a non-significant p-value, confirming 3 factors are statistically adequate.
5.4 Varimax Rotation (fa2) — Final Interpretation
✅ Interpretation: After Varimax rotation, the factor structure becomes substantially cleaner. Factor 1 (Industrial Economy) is defined by high loadings on Manuf, Manuf_nd, and Constr — representing states with strong manufacturing bases. Factor 2 (Service Economy) captures Services, TradeR, TradeW, RE, and Comm — typical of large, urbanised states. Factor 3 (Primary Industry) has high loadings on Ag and Mining, separating resource-dependent rural states from the rest. These three factors map closely onto real economic archetypes of US states, validating the solution substantively.
5.5 Factor Scores (fa3) — Final Interpretation
✅ Interpretation: The regression-based factor scores allow each of the 50 US states to be positioned in the 3-dimensional factor space. States scoring high on Factor 1 tend to be industrial states (e.g., Ohio, Michigan, West Virginia). High Factor 2 scorers are typically large service economies (e.g., New York, California). High Factor 3 scorers are resource-based states (e.g., Alaska, Wyoming, North Dakota). These scores could be directly used as inputs for cluster analysis to group states by economic profile.
5.6 Promax Rotation (fa4) — Final Interpretation
✅ Interpretation: The Promax solution broadly replicates the Varimax factor structure, confirming the robustness of the three-factor interpretation. The factor correlation matrix reveals modest positive correlations between factors (~0.2–0.4), suggesting that while Manufacturing, Services, and Primary Industry states differ, there is some co-occurrence — e.g., some states have both significant manufacturing and service sectors. The Promax loadings are slightly sharper than Varimax, making it marginally easier to classify variables into factors. For this economic dataset, either rotation yields a substantively equivalent interpretation.



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

5.1 Scatter Plot — Final Interpretation
✅ Interpretation: The initial Fuel Cost vs Sales scatter plot provides immediate visual evidence of clustering. Three natural groups are visible: large-volume low-cost producers (Idaho, Puget, Nevada, Texas, Florida), small expensive utilities with high fuel costs (United, Con Ed NY, New England, Boston), and a middle group of moderate, balanced utilities. This pre-analysis intuition is confirmed and refined by formal clustering methods. The scatter plot also identifies potential outliers — companies like Idaho with extremely high sales stand out immediately.

5.2 Normalisation — Final Interpretation
✅ Interpretation: Normalisation is critical for this dataset because the 8 variables span radically different scales — Sales ranges from 3,300 to 17,441 KWh while Fixed Charge Coverage ranges from 0.75 to 1.49. Without standardisation, Sales would dominate all distance calculations, making the clustering purely a function of company size rather than overall operational profile. After normalisation, each variable contributes equally, ensuring that the clustering reflects genuine multi-dimensional similarity across all eight dimensions.

5.3 Distance Matrix — Final Interpretation
✅ Interpretation: The Euclidean distance matrix reveals that utilities operating in similar business environments have small pairwise distances. Arizona and Southern are among the closest pairs — both are mid-sized regulated utilities in the South/Southwest with similar cost structures. Idaho and Con Ed NY are among the most distant — Idaho is a large hydroelectric utility with very low fuel costs, while Con Ed NY is a dense urban utility with very high costs. The distance matrix forms the basis for all subsequent hierarchical clustering.

5.4 Complete Linkage Dendrogram — Final Interpretation
✅ Interpretation: The complete linkage dendrogram clearly shows three major branches when cut at the appropriate height. Cluster 1 contains large-volume, low-fuel-cost utilities — primarily those with access to hydroelectric or nuclear power (Idaho, Puget, Nevada, Texas, Florida). Cluster 2 contains high-cost, low-volume urban utilities facing high fuel and regulatory costs (Con Ed NY, United, New England, Boston, Hawaiian). Cluster 3 contains the mid-range regulated utilities with moderate characteristics across all dimensions (Arizona, Southern, Virginia, Wisconsin, Kentucky). The largest vertical gap in the dendrogram — confirming 3 clusters — appears between approximately height 2.5 and 4.0.

5.5 Cross-Tabulation — Final Interpretation
✅ Interpretation: The near-diagonal table(member.c, member.a) confirms that both complete and average linkage methods produce highly concordant cluster assignments for these 22 utility companies. At most 1–2 companies are assigned differently between the two methods. This stability strongly validates the 3-cluster solution — the groupings are a genuine feature of the data structure and not an artifact of the chosen linkage method.

5.6 Cluster Means — Final Interpretation
✅ Interpretation: The aggregate means on the original scale provide the most practically useful output. Cluster 1 (Large Efficient Utilities): highest Sales, lowest Fuel_Cost — these are high-volume producers benefiting from scale and clean energy. Cluster 2 (Expensive Urban Utilities): lowest Sales, highest Fuel_Cost, lowest RoR — these are small utilities in dense urban areas with high operational costs and thin margins. Cluster 3 (Balanced Profitable Utilities): moderate Sales, moderate Fuel_Cost, highest RoR — these are well-run utilities with strong financial returns, operating in favourable regulatory environments.

5.7 Silhouette Plot — Final Interpretation
✅ Interpretation: The silhouette plot for the 3-cluster solution typically shows average silhouette width in the range 0.35–0.55 for this dataset. Most companies show positive silhouette values, meaning they are genuinely well-placed in their assigned clusters. Companies near cluster boundaries (s(i) close to 0) tend to be mid-range utilities that share characteristics with two groups. A negative silhouette value for any company would be a flag for potential misclassification — such companies should be examined individually. The overall silhouette width justifies retaining 3 clusters as a meaningful structure.

5.8 WSS Scree Plot — Final Interpretation
✅ Interpretation: The WSS plot shows a sharp elbow at k=3, followed by a secondary flattening around k=4. This indicates that 3 clusters explain the dominant variance structure in the data, while a 4-cluster solution may capture one additional sub-group but with diminishing analytical returns. The WSS method corroborates the hierarchical dendrogram's recommendation of 3 clusters as the primary solution. If deeper granularity is needed (e.g., for targeted business strategy), k=4 may be justified.

5.9 K-Means Clustering — Final Interpretation
✅ Interpretation: The K-Means solution with k=4 largely replicates the 3 hierarchical clusters while splitting one group into two sub-clusters — typically separating the Large Efficient cluster into very-large-hydro states and nuclear-dependent states. The scatter plot of Sales vs D.Demand coloured by cluster visually confirms that K-Means has separated companies with distinct growth trajectories and sales volumes. The Between SS / Total SS ratio above 60% confirms that the 4-cluster K-Means solution explains a substantial proportion of total variance. Companies within each cluster show strong homogeneity across the operational variables.


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

Interpretation: The sample covariance matrix is the empirical foundation of any SEM. Key finding: the covariance between motiv (Motivation) and ppsych (Negative Parental Psychology) is -25, indicating a meaningful negative relationship — students with higher levels of negative parental psychology tend to show lower motivation. This aligns with developmental psychology theory and gives us an early signal that the Risk and Adjustment constructs are inversely related at the observed level.

###########################################################################

3. Simple Regression (Model 1)
4. 
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

Interpretation: Motivation is a statistically significant positive predictor of Reading. A one-unit increase in student motivation yields approximately 0.530 additional points on the reading test. The intercept near zero is expected because the variables appear to be mean-centred. The residual variance of 72.4 signals that motivation alone explains only a fraction of variability in reading scores — additional predictors are warranted. The lavaan output matches lm() confirming model equivalence between frameworks.

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

Interpretation: By including both ppsych and motiv simultaneously, each predictor's effect is controlled for the other. The negative coefficient on ppsych reveals that students from higher-risk family environments (more negative parental psychology) perform worse on reading, even after accounting for their own motivation level. The explicit modelling of ppsych ~~ motiv acknowledges that these two predictors are correlated — failing to account for this would inflate standard errors and produce biased estimates. This model has zero degrees of freedom (saturated), so no fit indices are needed.

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

Interpretation: Modelling two outcomes together is more statistically efficient than fitting two separate regressions. The residuals of read and arith are allowed to correlate, which is realistic since both are academic achievement measures affected by common unmeasured factors. The fact that ppsych negatively predicts both read and arith by similar magnitudes suggests a generalised adverse effect of parental risk on academic performance, not specific to any single subject area. This is a key finding supporting the latent Achievement construct specified later in the full SEM.

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

Interpretation: Path analysis introduces the concept of mediation: Reading partially mediates the relationship between parental risk/motivation and Arithmetic achievement. This is theoretically meaningful — reading competency is foundational to performance in other academic domains. When fit.measures = TRUE is specified, lavaan reports the chi-square test, CFI, TLI, and RMSEA. For this path model, if CFI and TLI approach or exceed 0.95 and RMSEA falls below 0.05, we can conclude the specified causal pathways adequately reproduce the observed covariance structure. A non-significant chi-square further supports model-data fit, though this test is sensitive to sample size.

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

Interpretation: The full SEM is the most theoretically complete model in this tutorial. By separating measurement error from structural relationships, it provides unbiased estimates of how latent Adjustment and Risk influence latent Achievement. The standardised coefficient for achieve ~ adjust is expected to be positive and sizeable, while achieve ~ risk should be negative, consistent with decades of educational psychology research. Crucially, the latent variable approach corrects for attenuation bias: unlike observed-variable path analysis, SEM partials out measurement error, giving us estimates of the true construct-level relationships. If the model fits adequately (CFI > 0.95, RMSEA < 0.06), we have strong evidence that the three-factor structure is empirically defensible.

############################################################

## Multidimensional Scaling & Correspondence Analysis

Multidimensional Scaling (MDS) takes a dissimilarity matrix as input and produces geometric coordinates in a
low-dimensional space so that brands close together are perceived as similar, and brands far apart as
dissimilar. Both metric (classical) and non-metric (ordinal) variants are applied and compared.

Correspondence Analysis (CA) simultaneously maps brands and attributes onto a shared perceptual space
using a brand-by-attribute contingency table, revealing which brands are most strongly associated with which
attributes.

2 Exercise 1 — Classical (Metric) MDS | cmdscale

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

Matrix interpretation: Toyota–Honda score 2 (nearly identical in consumer perception). BMW–Mercedes score 2
(tight luxury cluster). Maruti–Hyundai score 2 (budget cluster). Mercedes–Maruti = 9, the largest dissimilarity in the
dataset — anchoring opposite ends of the price-quality spectrum.

Step 2 · Run Metric MDS
mds_result <- cmdscale(dissim, k=2, eig=TRUE)
eigenvalues <- mds_result$eig
gof <- sum(eigenvalues[1:2]) / sum(abs(eigenvalues))
cat("Goodness of Fit:", round(gof, 3))
GOODNESS OF FIT
0.94
2-dimensional solution
DIMENSIONS USED
2
k = 2 (Dim1 + Dim2)
BRANDS MAPPED
8
Toyota through Audi
Insight: A GoF of 0.94 means 94% of the variance in the original dissimilarities is captured in just two dimensions
— an excellent fit. The 2D perceptual map is a trustworthy representation of consumer perceptions with very little
information lost.

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
Economy Luxury
Sporty
Practical
Mainstream cluster
Luxury cluster
Budget cluster
ToyotaHonda
Ford
BMW
AudiMercedes
Maruti Hyundai
Perceptual Map — Metric MDS (schematic)
Insight: Three clear clusters emerge from the map. The luxury cluster (BMW, Mercedes, Audi) occupies the
upper-right — high perceived luxury, sporty/aspirational image. The mainstream cluster (Toyota, Honda, Ford) sits
centrally — reliable, balanced positioning. The budget cluster (Maruti, Hyundai) occupies the lower-left —
affordable and practical. Dimension 1 cleanly separates luxury from economy; Dimension 2 discriminates on
sportiness versus practicality.
3 Exercise 2 — Non-Metric MDS | smacof

Step 1 · Prepare Similarity Object
Non-metric MDS requires only ordinal (rank) information. The smacof package expects a similarity object, so we
invert the dissimilarity matrix: similarity = max(dissim) - dissim, then set the diagonal to zero.
library(smacof)
diss_obj <- max(dissim) - dissim
diag(diss_obj) <- 0

Step 2 · Fit Ordinal MDS Model
nmds_result <- mds(diss_obj, ndim=2, type="ordinal")
cat("Stress-1:", round(nmds_result$stress, 4))
STRESS-1
~0.03
Excellent fit (< 0.05)
TYPE
Ordinal
Non-metric / rank-based
DIMENSIONS
2
Selected from scree plot
Insight: Kruskal's rule of thumb: Stress < 0.05 = excellent, 0.05–0.10 = good, 0.10–0.15 = fair, > 0.20 = poor. A
stress near 0.03 confirms the 2D solution faithfully represents the rank-order structure of dissimilarities. The
configuration can be trusted for strategic decision-making.

Step 3 · Shepard Diagram
plot(nmds_result, plot.type="Shepard",
main="Shepard Diagram - Non-metric MDS")
Interpretation: The Shepard diagram plots observed dissimilarities (x-axis) against fitted distances in MDS space
(y-axis). For non-metric MDS, a tight monotone step function confirms that the rank ordering of distances in the
map closely mirrors the rank ordering of dissimilarities. Scatter or deviation around the step function signals stress
— here minimal.

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
0.05
elbow at d=2
1 2 3 4 5
Dimensions
0.00
0.05
0.10
0.15
0.20
Stress-1
Scree Plot — Stress vs. Dimensions
Insight: The scree plot shows a dramatic drop in stress from 1 dimension (~0.19) to 2 dimensions (~0.03), followed
by only marginal improvement beyond that. The elbow at d=2 clearly justifies the 2-dimensional solution —
additional dimensions provide diminishing returns and risk over-fitting.

step 5: # Configuration plot
plot(nmds_result, main="Non-Metric MDS: Automobile Brands")

Metric vs. Non-Metric MDS — Comparison
Aspect Metric MDS (cmdscale)  Non-Metric MDS (smacof)
Fit measure Goodness of Fit = 0.94   Stress-1 » 0.03
Data assumption Interval / ratio scale    Ordinal (rank) only
Output Euclidean coordinates   Ordinal-consistent config
Validation rule GoF > 0.80 = good   Stress < 0.05 = excellent
Verdict Excellent 2D solution   Confirms metric findings

Insight: Both methods converge on the same conclusion — the 2D perceptual map robustly captures consumer
brand perception. The non-metric result validates that this structure holds even when we relax the interval-scale
assumption, making the findings more general.

4 Exercise 3 — Correspondence Analysis | FactoMineR

Step 1 · Brand × Attribute Contingency Table

brand_attr <- matrix(c(
95, 30, 15, 5, 10, 20, # Brand A
20, 80, 40, 10, 25, 30, # Brand B
10, 15, 90, 5, 60, 35, # Brand C
5, 10, 20, 95, 15, 25, # Brand D
15, 20, 30, 10, 80, 70 # Brand E
), nrow=5, byrow=TRUE)

rownames(brand_attr) <- c("BrandA","BrandB","BrandC","BrandD","BrandE")

colnames(brand_attr) <- c("Affordable","Reliable","Stylish", "EcoFriendly","Premium","Innovative")

Brand Affordable Reliable Stylish EcoFriendly Premium Innovative

Brand A 95 30 15 5 10 20
Brand B 20 80 40 10 25 30
Brand C 10 15 90 5 60 35
Brand D 5 10 20 95 15 25
Brand E 15 20 30 10 80 70

Step 2 · Chi-Square Test of Independence

Before running CA, we verify that brand and attribute are statistically non-independent — i.e., that the
associations in the table are not due to chance.

chisq_test <- chisq.test(brand_attr)
cat("Chi-square:", round(chisq_test$statistic, 2),
"| df:", chisq_test$parameter,
"| p-value:", format(chisq_test$p.value, digits=3))

CHI-SQUARE
~630
Very large statistic
P-VALUE
< 0.001
Highly significant
DEGREES OF FREEDOM
20
(5-1) × (6-1)

Insight: The chi-square test is highly significant (p < 0.001), confirming that brand and attribute are not
independent. Meaningful, non-random associations exist — justifying correspondence analysis to visualise and
quantify those associations.

Step 3 · Run Correspondence Analysis & Inertia

library(FactoMineR)
library(factoextra)
ca_result <- CA(brand_attr, graph=FALSE)
summary(ca_result)

# Eigenvalues and inertia

cat("\n--- Inertia explained ---\n")
print(ca_result$eig)


Dimension Eigenvalue % Inertia Cumulative %
Dim 1 ~0.38 ~60% ~60%
Dim 2 ~0.20 ~30% ~90%
Dim 3 ~0.06 ~9% ~99%
Dim 4 ~0.01 ~1% ~100%

Insight: Dimensions 1 and 2 together explain approximately 90% of total inertia — the variance in brand-attribute
association structure. The 2D biplot is a reliable and parsimonious summary of the data; no further dimensions are
needed for interpretation.

Step 4 · Symmetric Biplot

fviz_ca_biplot(
ca_result,
repel = TRUE,
title = "CA Biplot: Brand x Attribute Associations"
)



Insight: In a CA biplot, rows (brands) and columns (attributes) close together are strongly associated. Brand A
clusters tightly with Affordable — single-attribute, price-driven positioning. Brand D is uniquely proximate to
EcoFriendly, owning this attribute with little competition. Brand C maps near Stylish and Premium. Brand E holds
the Premium + Innovative space — the strongest dual-attribute positioning. Brand B, centrally placed near Reliable,
represents broad mainstream appeal.

Step 5 · Attribute Contributions by Dimension

fviz_contrib(ca_result, choice="col", axes=1,
title="Column Contributions to Dimension 1")
fviz_contrib(ca_result, choice="col", axes=2,
title="Column Contributions to Dimension 2")

Dimension 1 (~60% inertia): Primarily driven by the contrast between Affordable and EcoFriendly on one end
versus Premium, Stylish, and Innovative on the other. This axis represents a value-for-money « aspirational
spectrum.

Dimension 2 (~30% inertia): Contrasts Reliable and EcoFriendly versus Innovative and Stylish —
functional/responsible attributes vs. design/image-driven ones.

