# Bias-vs-Variance-analysis--Regression
Implement regularized linear regression and use it to study models with different bias-variance properties.
Programming Exercise 5:
Regularized Linear Regression and Bias v.s.
Variance
Machine Learning
Introduction
In this exercise, you will implement regularized linear regression and use it to
study models with dierent bias-variance properties. Before starting on the
programming exercise, we strongly recommend watching the video lectures
and completing the review questions for the associated topics.
To get started with the exercise, you will need to download the starter
code and unzip its contents to the directory where you wish to complete the
exercise. If needed, use the cd command in Octave/MATLAB to change to
this directory before starting this exercise.
You can also nd instructions for installing Octave/MATLAB in the \En-
vironment Setup Instructions" of the course website.
Files included in this exercise
ex5.m - Octave/MATLAB script that steps you through the exercise
ex5data1.mat - Dataset
submit.m - Submission script that sends your solutions to our servers
featureNormalize.m - Feature normalization function
fmincg.m - Function minimization routine (similar to fminunc)
plotFit.m - Plot a polynomial t
trainLinearReg.m - Trains linear regression using your cost function
[?] linearRegCostFunction.m - Regularized linear regression cost func-
tion
[?] learningCurve.m - Generates a learning curve
[?] polyFeatures.m - Maps data into polynomial feature space
[?] validationCurve.m - Generates a cross validation curve
1
? indicates les you will need to complete
Throughout the exercise, you will be using the script ex5.m. These scripts
set up the dataset for the problems and make calls to functions that you will
write. You are only required to modify functions in other les, by following
the instructions in this assignment.
Where to get help
The exercises in this course use Octave1 or MATLAB, a high-level program-
ming language well-suited for numerical computations. If you do not have
Octave or MATLAB installed, please refer to the installation instructions in
the \Environment Setup Instructions" of the course website.
At the Octave/MATLAB command line, typing help followed by a func-
tion name displays documentation for a built-in function. For example, help
plot will bring up help information for plotting. Further documentation for
Octave functions can be found at the Octave documentation pages. MAT-
LAB documentation can be found at the MATLAB documentation pages.
We also strongly encourage using the online Discussions to discuss ex-
ercises with other students. However, do not look at any source code written
by others or share your source code with others.
1 Regularized Linear Regression
In the rst half of the exercise, you will implement regularized linear regres-
sion to predict the amount of water 
owing out of a dam using the change
of water level in a reservoir. In the next half, you will go through some diag-
nostics of debugging learning algorithms and examine the eects of bias v.s.
variance.
The provided script, ex5.m, will help you step through this exercise.
1Octave is a free alternative to MATLAB. For the programming exercises, you are free
to use either Octave or MATLAB.
2
1.1 Visualizing the dataset
We will begin by visualizing the dataset containing historical records on the
change in the water level, x, and the amount of water 
owing out of the dam,
y.
This dataset is divided into three parts:
• A training set that your model will learn on: X, y
• A cross validation set for determining the regularization parameter:
Xval, yval
• A test set for evaluating performance. These are \unseen" examples
which your model did not see during training: Xtest, ytest
The next step of ex5.m will plot the training data (Figure 1). In the
following parts, you will implement linear regression and use that to t a
straight line to the data and plot learning curves. Following that, you will
implement polynomial regression to nd a better t to the data.
−50 −40 −30 −20 −10 0 10 20 30 40
0
5
10
15
20
25
30
35
40
Change in water level (x)
Water flowing out of the dam (y) Figure 1: Data
1.2 Regularized linear regression cost function
Recall that regularized linear regression has the following cost function:
3
J() =
1
2m
 
Xm
i=1
(h(x(i)) 􀀀 y(i))2
!
+

2m
 
Xn
j=1
2
j
!
;
where  is a regularization parameter which controls the degree of regu-
larization (thus, help preventing overtting). The regularization term puts a
penalty on the overal cost J. As the magnitudes of the model parameters j
increase, the penalty increases as well. Note that you should not regularize
the 0 term. (In Octave/MATLAB, the 0 term is represented as theta(1)
since indexing in Octave/MATLAB starts from 1).
You should now complete the code in the le linearRegCostFunction.m.
Your task is to write a function to calculate the regularized linear regression
cost function. If possible, try to vectorize your code and avoid writing loops.
When you are nished, the next part of ex5.m will run your cost function
using theta initialized at [1; 1]. You should expect to see an output of
303.993.
You should now submit your solutions.
1.3 Regularized linear regression gradient
Correspondingly, the partial derivative of regularized linear regression's cost
for j is dened as
@J()
@0
=
1
m
Xm
i=1
(h(x(i)) 􀀀 y(i))x(i)
j for j = 0
@J()
@j
=
 
1
m
Xm
i=1
(h(x(i)) 􀀀 y(i))x(i)
j
!
+

m
j for j  1
In linearRegCostFunction.m, add code to calculate the gradient, re-
turning it in the variable grad. When you are nished, the next part of
ex5.m will run your gradient function using theta initialized at [1; 1].
You should expect to see a gradient of [-15.30; 598.250].
You should now submit your solutions.
1.4 Fitting linear regression
Once your cost function and gradient are working correctly, the next part of
ex5.m will run the code in trainLinearReg.m to compute the optimal values
4
of . This training function uses fmincg to optimize the cost function.
In this part, we set regularization parameter  to zero. Because our
current implementation of linear regression is trying to t a 2-dimensional ,
regularization will not be incredibly helpful for a  of such low dimension. In
the later parts of the exercise, you will be using polynomial regression with
regularization.
Finally, the ex5.m script should also plot the best t line, resulting in
an image similar to Figure 2. The best t line tells us that the model is
not a good t to the data because the data has a non-linear pattern. While
visualizing the best t as shown is one possible way to debug your learning
algorithm, it is not always easy to visualize the data and model. In the next
section, you will implement a function to generate learning curves that can
help you debug your learning algorithm even if it is not easy to visualize the
data.
−50 −40 −30 −20 −10 0 10 20 30 40
−5
0
5
10
15
20
25
30
35
40
Change in water level (x)
Water flowing out of the dam (y)
Figure 2: Linear Fit
2 Bias-variance
An important concept in machine learning is the bias-variance tradeo. Mod-
els with high bias are not complex enough for the data and tend to undert,
while models with high variance overt to the training data.
5
In this part of the exercise, you will plot training and test errors on a
learning curve to diagnose bias-variance problems.
2.1 Learning curves
You will now implement code to generate the learning curves that will be
useful in debugging learning algorithms. Recall that a learning curve plots
training and cross validation error as a function of training set size. Your
job is to ll in learningCurve.m so that it returns a vector of errors for the
training set and cross validation set.
To plot the learning curve, we need a training and cross validation set
error for dierent training set sizes. To obtain dierent training set sizes,
you should use dierent subsets of the original training set X. Specically, for
a training set size of i, you should use the rst i examples (i.e., X(1:i,:)
and y(1:i)).
You can use the trainLinearReg function to nd the  parameters. Note
that the lambda is passed as a parameter to the learningCurve function.
After learning the  parameters, you should compute the error on the train-
ing and cross validation sets. Recall that the training error for a dataset is
dened as
Jtrain() =
1
2m
"
Xm
i=1
(h(x(i)) 􀀀 y(i))2
#
:
In particular, note that the training error does not include the regular-
ization term. One way to compute the training error is to use your existing
cost function and set  to 0 only when using it to compute the training error
and cross validation error. When you are computing the training set error,
make sure you compute it on the training subset (i.e., X(1:n,:) and y(1:n))
(instead of the entire training set). However, for the cross validation error,
you should compute it over the entire cross validation set. You should store
the computed errors in the vectors error train and error val.
When you are nished, ex5.m wil print the learning curves and produce
a plot similar to Figure 3.
You should now submit your solutions.
In Figure 3, you can observe that both the train error and cross validation
error are high when the number of training examples is increased. This
re
ects a high bias problem in the model { the linear regression model is
6
0 2 4 6 8 10 12
0
50
100
150
Learning curve for linear regression
Number of training examples
Error
Train
Cross Validation
Figure 3: Linear regression learning curve
too simple and is unable to t our dataset well. In the next section, you will
implement polynomial regression to t a better model for this dataset.
3 Polynomial regression
The problem with our linear model was that it was too simple for the data
and resulted in undertting (high bias). In this part of the exercise, you will
address this problem by adding more features.
For use polynomial regression, our hypothesis has the form:
h(x) = 0 + 1  (waterLevel) + 2  (waterLevel)2 +    + p  (waterLevel)p
= 0 + 1x1 + 2x2 + ::: + pxp:
Notice that by dening x1 = (waterLevel); x2 = (waterLevel)2; : : : ; xp =
(waterLevel)p, we obtain a linear regression model where the features are the
various powers of the original value (waterLevel).
Now, you will add more features using the higher powers of the existing
feature x in the dataset. Your task in this part is to complete the code in
polyFeatures.m so that the function maps the original training set X of size
m1 into its higher powers. Specically, when a training set X of size m1
is passed into the function, the function should return a mp matrix X poly,
7
where column 1 holds the original values of X, column 2 holds the values of
X.^2, column 3 holds the values of X.^3, and so on. Note that you don't
have to account for the zero-eth power in this function.
Now you have a function that will map features to a higher dimension,
and Part 6 of ex5.m will apply it to the training set, the test set, and the
cross validation set (which you haven't used yet).
You should now submit your solutions.
3.1 Learning Polynomial Regression
After you have completed polyFeatures.m, the ex5.m script will proceed to
train polynomial regression using your linear regression cost function.
Keep in mind that even though we have polynomial terms in our feature
vector, we are still solving a linear regression optimization problem. The
polynomial terms have simply turned into features that we can use for linear
regression. We are using the same cost function and gradient that you wrote
for the earlier part of this exercise.
For this part of the exercise, you will be using a polynomial of degree 8.
It turns out that if we run the training directly on the projected data, will
not work well as the features would be badly scaled (e.g., an example with
x = 40 will now have a feature x8 = 408 = 6:5  1012). Therefore, you will
need to use feature normalization.
Before learning the parameters  for the polynomial regression, ex5.m will
rst call featureNormalize and normalize the features of the training set,
storing the mu, sigma parameters separately. We have already implemented
this function for you and it is the same function from the rst exercise.
After learning the parameters , you should see two plots (Figure 4,5)
generated for polynomial regression with  = 0.
From Figure 4, you should see that the polynomial t is able to follow
the datapoints very well - thus, obtaining a low training error. However, the
polynomial t is very complex and even drops o at the extremes. This is
an indicator that the polynomial regression model is overtting the training
data and will not generalize well.
To better understand the problems with the unregularized ( = 0) model,
you can see that the learning curve (Figure 5) shows the same eect where
the low training error is low, but the cross validation error is high. There
is a gap between the training and cross validation errors, indicating a high
variance problem.
8
−80 −60 −40 −20 0 20 40 60 80
−60
−50
−40
−30
−20
−10
0
10
20
30
40
Change in water level (x)
Water flowing out of the dam (y)
Polynomial Regression Fit (lambda = 0.000000)
Figure 4: Polynomial t,  = 0
0 2 4 6 8 10 12
0
10
20
30
40
50
60
70
80
90
100
Polynomial Regression Learning Curve (lambda = 0.000000)
Number of training examples
Error
Train
Cross Validation
Figure 5: Polynomial learning curve,  = 0
One way to combat the overtting (high-variance) problem is to add
regularization to the model. In the next section, you will get to try dierent
 parameters to see how regularization can lead to a better model.
9
3.2 Optional (ungraded) exercise: Adjusting the reg-
ularization parameter
In this section, you will get to observe how the regularization parameter
aects the bias-variance of regularized polynomial regression. You should
now modify the the lambda parameter in the ex5.m and try  = 1; 100. For
each of these values, the script should generate a polynomial t to the data
and also a learning curve.
For  = 1, you should see a polynomial t that follows the data trend
well (Figure 6) and a learning curve (Figure 7) showing that both the cross
validation and training error converge to a relatively low value. This shows
the  = 1 regularized polynomial regression model does not have the high-
bias or high-variance problems. In eect, it achieves a good trade-o between
bias and variance.
For  = 100, you should see a polynomial t (Figure 8) that does not
follow the data well. In this case, there is too much regularization and the
model is unable to t the training data.
You do not need to submit any solutions for this optional (ungraded)
exercise.
−80 −60 −40 −20 0 20 40 60 80
0
20
40
60
80
100
120
140
160
Change in water level (x) Water flowing out of the dam (
y)
Polynomial Regression Fit (lambda = 1.000000)
Figure 6: Polynomial t,  = 1
10
0 2 4 6 8 10 12
0
10
20
30
40
50
60
70
80
90
100
Polynomial Regression Learning Curve (lambda = 1.000000)
Number of training examples
Error
Train
Cross Validation
Figure 7: Polynomial learning curve,  = 1
−80 −60 −40 −20 0 20 40 60 80
−10
−5
0
5
10
15
20
25
30
35
40
Change in water level (x)
Water flowing out of the dam (y)
Polynomial Regression Fit (lambda = 100.000000)
Figure 8: Polynomial t,  = 100
3.3 Selecting  using a cross validation set
From the previous parts of the exercise, you observed that the value of 
can signicantly aect the results of regularized polynomial regression on
the training and cross validation set. In particular, a model without regular-
ization ( = 0) ts the training set well, but does not generalize. Conversely,
11
a model with too much regularization ( = 100) does not t the training set
and testing set well. A good choice of  (e.g.,  = 1) can provide a good t
to the data.
In this section, you will implement an automated method to select the
 parameter. Concretely, you will use a cross validation set to evaluate
how good each  value is. After selecting the best  value using the cross
validation set, we can then evaluate the model on the test set to estimate
how well the model will perform on actual unseen data.
Your task is to complete the code in validationCurve.m. Specically,
you should should use the trainLinearReg function to train the model using
dierent values of  and compute the training error and cross validation error.
You should try  in the following range: f0; 0:001; 0:003; 0:01; 0:03; 0:1; 0:3; 1; 3; 10g.
0 1 2 3 4 5 6 7 8 9 10
0
2
4
6
8
10
12
14
16
18
20
lambda
Error
Train
Cross Validation
Figure 9: Selecting  using a cross validation set
After you have completed the code, the next part of ex5.m will run your
function can plot a cross validation curve of error v.s.  that allows you select
which  parameter to use. You should see a plot similar to Figure 9. In this
gure, we can see that the best value of  is around 3. Due to randomness
in the training and validation splits of the dataset, the cross validation error
can sometimes be lower than the training error.
You should now submit your solutions.
12
3.4 Optional (ungraded) exercise: Computing test set
error
In the previous part of the exercise, you implemented code to compute the
cross validation error for various values of the regularization parameter .
However, to get a better indication of the model's performance in the real
world, it is important to evaluate the \nal" model on a test set that was
not used in any part of training (that is, it was neither used to select the 
parameters, nor to learn the model parameters ).
For this optional (ungraded) exercise, you should compute the test error
using the best value of  you found. In our cross validation, we obtained a
test error of 3.8599 for  = 3.
You do not need to submit any solutions for this optional (ungraded)
exercise.
3.5 Optional (ungraded) exercise: Plotting learning
curves with randomly selected examples
In practice, especially for small training sets, when you plot learning curves
to debug your algorithms, it is often helpful to average across multiple sets
of randomly selected examples to determine the training error and cross
validation error.
Concretely, to determine the training error and cross validation error for
i examples, you should rst randomly select i examples from the training set
and i examples from the cross validation set. You will then learn the param-
eters  using the randomly chosen training set and evaluate the parameters
 on the randomly chosen training set and cross validation set. The above
steps should then be repeated multiple times (say 50) and the averaged error
should be used to determine the training error and cross validation error for
i examples.
For this optional (ungraded) exercise, you should implement the above
strategy for computing the learning curves. For reference, gure 10 shows the
learning curve we obtained for polynomial regression with  = 0:01. Your
gure may dier slightly due to the random selection of examples.
You do not need to submit any solutions for this optional (ungraded)
exercise.
13
0 2 4 6 8 10 12
0
10
20
30
40
50
60
70
80
90
100
Polynomial Regression Learning Curve (lambda = 0.010000)
Number of training examples
Error
Train
Cross Validation
Figure 10: Optional (ungraded) exercise: Learning curve with randomly
selected examples
Submission and Grading
After completing various parts of the assignment, be sure to use the submit
function system to submit your solutions to our servers. The following is a
breakdown of how each part of this exercise is scored.
Part Submitted File Points
Regularized Linear Regression Cost
Function
linearRegCostFunction.m 25 points
Regularized Linear Regression Gra-
dient
linearRegCostFunction.m 25 points
Learning Curve learningCurve.m 20 points
Polynomial Feature Mapping polyFeatures.m 10 points
Cross Validation Curve validationCurve.m 20 points
Total Points 100 points
You are allowed to submit your solutions multiple times, and we will take
only the highest score into consideration.
14
