# App-Revenue-Analysis
This repository contains the STATA and econometrics project report focused on analyzing app revenue data from the Google Play Store. The project employs various statistical and econometric techniques to investigate the factors influencing app performance and revenue generation.
# Overview
This repository contains the project report and accompanying analysis code for a study on app revenue from the Google Play Store. The project leverages various econometric and statistical techniques to explore factors influencing app performance and revenue.
# Introduction
This project report analyzes data from the Google Play Store, focusing on paid apps and various characteristics that may impact app performance. The dataset includes 3173 observations and 9 variables collected in June 2023. The main objective is to explore factors influencing app success, measured by revenue
# Descriptive Analysis
Descriptive Statistics: Summary statistics for continuous variables across different app categories.
Revenue Disparities : Examination of potential revenue disparities among app categories using ANOVA and t-tests.
Correlation Matrix: Analysis of the relationships between various numerical variables.
# Exploratory Analysis
Visual Exploration: Graphical representation of data patterns, including summary statistics and distributions.
Independent vs. Dependent Variables: Scatter plots and correlations to explore the relationship between independent variables (e.g., rating, price) and dependent variables (revenue).
# Main Regression Analysis
Base Model: Regression analysis to determine the impact of various factors on app revenue.
Monetization Strategies: Evaluation of different monetization strategies and their effectiveness in revenue generation.
App Ratings Influence: Analysis of how app ratings impact revenue under different monetization schemes.
# Diagnostics and Robustness Analysis
Heteroskedasticity: Detection and remedy for heteroskedasticity in the regression model.
Quadratic Impact: Examination of the quadratic relationship between app price and revenue.
Endogeneity: Addressing endogeneity issues in the baseline model with instrumental variable regression.
# STATA Codes
gen application_category = 1 if main_category == "Games"
replace application_category = 2 if main_category != "Games"

tabstat rating log_of_price monetization_strategies_dum age_target_dum size num_langs log_of_revenue, statistics(count mean median min max sd) by(application_category) columns(statistics) longstub

pwcorr rating log_of_price monetization_strategies_dum age_target_dum num_langs application_category log_of_revenue, star(0.05)

graph box log_of_revenue,by(application_category)

graph bar (mean) log_of_price  (sd) log_of_price, over(application_category) ytitle(size) name(g14)
graph bar (mean) log_of_price  (sd) log_of_price, over(monetization_strategies_dum) ytitle(size) name(g15)
graph bar (mean) log_of_price  (sd) log_of_price, over(age_target_dum) ytitle(size) name(g16) 
graph combine g14 g15 g16 


graph bar (mean)  log_of_revenue (sd) log_of_revenue, over(application_category) ytitle(log_of_revenue) name(g18)
graph bar (mean)  log_of_revenue (sd) log_of_revenue, over(monetization_strategies_dum) ytitle(log_of_revenue) name(g19)
graph bar (mean)  log_of_revenue (sd) log_of_revenue, over(age_target_dum) ytitle(log_of_revenue) name(g20) 
graph combine g18 g19 g20 



graph bar (mean)  age_num  (sd) age_num , over(application_category) ytitle(size) name(g10)
graph bar (mean)  age_num (sd) age_num , over(devices) ytitle(size) name(g11)
graph bar (mean)  age_num  (sd) age_num , over(monetization_strategies_dum) ytitle(size) name(g12)
graph combine g10 g11 g12 


graph bar (mean) version_dum    (sd) version_dum  , over(application_category) ytitle(size) name(g1)
graph bar (mean)  version_dum   (sd) version_dum  , over(monetization_strategies_dum) ytitle(size) name(g2)
graph bar (mean) version_dum   (sd) version_dum  , over(age_target_dum) ytitle(size) name(g3) 
graph combine g1 g2 g3 




histogram main_category_num, normal name(a) 
histogram age_target_dum, normal name(b) 
histogram log_of_revenue, normal name(c) 
histogram age_target_dum, normal name(d) 
histogram log_of_revenue, normal name(e)
histogram log_of_price, normal name(f) 
histogram rating, normal name(g) 
histogram monetization_strategies_dum, normal name(h) 

graph combine a b c d e f g h

graph box log_of_revenue log_of_price monetization_strategies_dum age_target_dum
graph box rating size developer_dum main_category_num monetization_strategies_dum


graph box rating size log_of_price  main_category_num age_target_dum
graph box log_of_revenue developer_dum monetization_strategies_dum 

regress log_of_revenue rating log_of_price i.monetization_strategies_dum i.age_target_dum num_langs i.main_category_num


margins monetization_strategies_dum
marginsplot
marginsplot, recastci(rarea) 
graph bar (mean) log_of_revenue, over(monetization_strategies_dum) ytitle(Revenue) name(p)
graph bar (mean) log_of_revenue, over(age_target) ytitle(Revenue) name(s)
graph combine s n


gen rating_level="High" if rating >4
replace rating_level = "low" if rating < 2
replace rating_level= "avg" if rating_level ==""
encode rating_level, generate(rating_level1)
tabulate rating_level1 
tabulate rating_level1, nolabel

reg log_of_revenue i.rating_level1 i.monetization_strategies_dum
margins monetization_strategies_dum , at (rating_level1=(2(1)3))
marginsplot



asdoc tabstat rating log_of_price monetization_strategies_dum age_target_dum size num_langs log_of_revenue, statistics(count mean median min max sd) by(application_category) columns(statistics) longstub

asdoc pwcorr rating log_of_price monetization_strategies_dum age_target_dum num_langs application_category log_of_revenue, star(0.05)

asdoc oneway log_of_revenue main_category


asdoc ttest revenue, by(application_category)

asdoc regress log_of_revenue rating log_of_price i.monetization_strategies_dum i.age_target_dum num_langs main_category_num


asdoc margins monetization_strategies_dum
asdoc reg log_of_revenue i.rating_level1 i.monetization_strategies_dum


asdoc margins monetization_strategies_dum , at (rating_level1=(2(1)3))

asdoc reg log_of_revenue i.rating_level1 i.monetization_strategies_dum

ttest log_of_revenue, by(application_category)
asdoc ttest log_of_revenue, by(application_category)


asdoc ivregress 2sls log_of_revenue rating (size  developer_num = price  revenue monetization_strategies_dum age_target_dum n
> um_langs_chang)


regress log_of_revenue rating log_of_price i.monetization_strategies_dum i.age_target_dum num_langs main_category_num
rvfplot,	yline(0)



regress log_of_revenue log_of_price c.log_of_price#c.log_of_price i.monetization_strategies_dum i.age_target_dum num_langs main_category_num

predict predicted_log_of_revenue, xb
twoway line quadratic_fit log_of_price, color(red) legend(label(1 "Quadratic Fit"))  name(line_plot)
scatter log_of_price predicted_log_of_revenue, title("Scatter Plot of Predicted Log Revenue vs. Logged App Price") xtitle("Logged App Price") ytitle("Predicted Log Revenue")  xlabel(0.5(0.5)5) ylabel(5(2)25) name(scatter_plot)

graph combine scatter_plot line_plot, rows(2) name(combined_graph)

 
twoway (scatter log_of_revenue rating , mcolor(khaki) msize(vsmall) msymbol(circle)) (lfit log_of_revenue rating, lcolor(black) lwidth(medium)), ytitle(log_of_revenue) xtitle(rating) name(r1)

twoway (scatter log_of_revenue log_of_price , mcolor(khaki) msize(vsmall) msymbol(circle)) (lfit log_of_revenue log_of_price, lcolor(black) lwidth(medium)), ytitle(log_of_revenue) xtitle(log_of_price) name(r2)

twoway (scatter log_of_revenue monetization_strategies_dum , mcolor(khaki) msize(vsmall) msymbol(circle)) (lfit log_of_revenue monetization_strategies_dum, lcolor(black) lwidth(medium)), ytitle(log_of_revenue) xtitle(monetization_strategies_dum) name(r3) 

twoway (scatter log_of_revenue age_target_dum , mcolor(khaki) msize(khaki) msymbol(circle)) (lfit log_of_revenue age_target_dum, lcolor(black) lwidth(medium)), ytitle(log_of_revenue) xtitle(age_target_dum) name(r4)

twoway (scatter log_of_revenue size , mcolor(khaki) msize(vsmall) msymbol(circle)) (lfit log_of_revenue size, lcolor(black) lwidth(medium)), ytitle(log_of_revenue) xtitle(size) name(r5)
graph combine r1 r2 r3 r4 r5

graph drop r1 r2 r3 r4 r5

graph bar (mean) log_of_revenue , over age_target_dum ytitle(age revenue generation)


graph bar (mean) log_of_revenue, over(age_target_dum) ytitle("Age Revenue Generation") ///
  title("Mean Log Revenue by Age Target") name(bar_graph)
  
generate #price = log_of_price*log_of_price


twoway(scatter log_of_revenue log_of_price)(lowess log_of_revenue log_of_price)


cor log_of_revenue developer_dum
cor log_of_revenue version_dum
ivregress 2sls log_of_revenue log_of_price (version_dum = age_target_dum rating monetization_strategies_dum)
estat endog
estat firststage

# Key Findings
Game apps generate higher revenue compared to non-game apps.
Paid in-app purchases are identified as the most effective monetization strategy.
Target age groups such as "Everyone 10+" significantly contribute to revenue generation.
Addressing heteroskedasticity and endogeneity improves the model's robustness.


