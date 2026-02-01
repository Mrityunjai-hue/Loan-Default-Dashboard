# Loan Default-Dashboard

### Dashboard Link : https://app.powerbi.com/links/MP6Xvt_ogG?ctid=9ef5d60f-35f4-45f1-ba96-6e6f3d549eff&pbi_source=linkShare


## Problem Statement

This dashboard helps financial institutions analyze **loan performance and default risk** across customer demographics, employment types, credit score categories, and time. It provides a consolidated view of loan distribution, default rates, and financial risk metrics, enabling stakeholders to identify high-risk segments and monitor portfolio health more effectively.

From the analysis, the **overall default rate remains around 11–12% across multiple years (2013–2018)**, indicating persistent credit risk. The dashboard also shows significant loan exposure, with the **total loan amount exceeding 32.5 billion**, including **over 21.7 billion from high-income customers**, while unemployed customers exhibit the **highest default rate (~3.39%)**. These insights highlight the need for proactive, data-driven credit risk management to reduce defaults and optimize lending strategies.


### Steps followed 

- Step 1 : Load data into Power BI Desktop, dataset is a csv file.
- Step 2 : Open power query editor & in view tab under Data preview section, check "column distribution", "column quality" & "column profile" options.
- Step 3 : Also since by default, profile will be opened only for 1000 rows so you need to select "column profiling based on entire dataset".
- Step 4 : It was observed that in none of the columns errors & empty values were present.
- Step 5 : Since Our data has date column but not year column , thus we added new column Year, for creating new column following DAX expression was written;
       
      Year = YEAR('loan_default'[Loan_Date_DD_MM_YYYY].[Date])
   
- Step 6 : In the report view, under the view tab, theme was selected and to simply the look a seperate table named "Measures_Table1", "Measures_Table2", "Measures_Table3" were created in order to store the every measure in respective table page wise.
- Step 7 : Since the data contains various purpose for which loan was issued, thus in order to represent loan amount by purpose, a new visual was added using the line chart in the visualizations pane in report. 
- Step 8 : we created a new measure "**Loan Amount by Purpose**" used SUMX with a filter to exclude blank loan amounts and ensure accurate, purpose-wise loan aggregation that remains robust under all dashboard filters, Following DAX Expression was used :.
           
           Loan Amount by Purpose = SUMX(FILTER('Loan_default',NOT(ISBLANK('Loan_default'[LoanAmount]))),'Loan_default'[LoanAmount])
- Step 9 : To help assess income stability and credt risk as there are various Eployment type with their different individual incomes a new measure was created **Average Income by Employment Type** using following DAX Expression : 
           
           Average Income by Employment Type =CALCULATE(AVERAGE('Loan_default'[Income]),ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType]))
 This meaure used with line chart to show average income trend of various employment types.             
- Step 10 : Since assessing of defualters in loan is a must, for which we used Line chart to visualise which Employment type is inclined to more defaulters in loan as a perecntage contribution, for that purpose we created new measure **Default Rate by EmploymentType** , following DAX expression was used:
       
        Default Rate by EmploymentType = 
        
        var totalrecords = COUNTROWS(all('Loan_default'))
        
        var defaultcases = COUNTROWS(filter('Loan_default','Loan_default'[Default] = True()))
        
        return
        CALCULATE(DIVIDE(defaultcases, totalrecords), ALLEXCEPT('Loan_default','Loan_default'[EmploymentType])) * 100
 
- Step 11 : As our data consist of only ***Age*** Column and not age groups, therfore for creating the **Average Loan by Age Group** line chart visual to visualise which age group tends to borrow loan more than the others; we created a new measure **Age Groups** with following DAX Expression:
       
        Age Group = 
        
        IF('Loan_default'[Age]<=19, "Teen",
        
           IF('Loan_default'[Age] <= 39, "Adults",
        
              IF('Loan_default'[Age] <= 59, "Middle Age Adults", "Senior Citizens")))
  

- Step 12 : Now that we got Defualters by the EmploymentType, Now that since we have created the Year Column; we could look through years in which larger number of default cases were happened and would be able to take preventive measures against it.
The most Suitable Visual to represent it could be Line Chart which will show us Default Rate by Year as a Percentage, For the cause we created measure **Default Rate by Year** which will show out of total loans how many were default, which further multiplied by 100 to represent the perecntage contribution with DAX Expression followed :
       
        Default Rate by Year = 
        
        var totalloans = CALCULATE(COUNTROWS('Loan_default'),ALLEXCEPT('Loan_default',Loan_default[Year]))
        
        var default = CALCULATE(COUNTROWS(FILTER('Loan_default',Loan_default[Default] = TRUE())),ALLEXCEPT('Loan_default',Loan_default[Year]))
        
        RETURN 
        DIVIDE(default, totalloans) * 100 

- Step 13 :Now the Report for **Loan Defult & Overview** is completed, we create a new report in next page for **Applicant Demographics & financial Profile** . 
- Step 14 : Calculated column was created in which, customers were grouped into various Credit Score bins for their respective Credit Score.

for creating new column following DAX expression was written;
       
        Credit Score Bins = 
        
        IF(Loan_default[CreditScore] <= 400, "Very Low",
        
           IF(Loan_default[CreditScore] <= 450, "Low",
        
              IF(Loan_default[CreditScore] <= 650, "Medium", "High")))
        
Snap of new calculated column ,
![Snap_Count](https://github.com/user-attachments/assets/f4e275f4-6329-40b1-ac0a-a53aa140381f)


        
- Step 15 : New measure was created to find Median of Loan Amount by credit store.

Following DAX expression was written for the same,
        
        Median By Credit Score = Medianx('Loan_default',Loan_default[LoanAmount])
        
A Line Chart visual was used to represent Median **Loan Amount by Credit Score Category**.

 - Step 16 : New measure was created to find  **Average Loan Amount for High Credit Score Borrowers**,
 
 Following DAX expression was written to find Average,
 
         Average Loan Amount for High Credit = AVERAGEX(FILTER('Loan_default',Loan_default[Credit Score Bins] = "High"),Loan_default[LoanAmount])
 
 A Donut Chart visual was used to represent this perecntage share of Average Loan Amount by High Credit Score Borrowers along with thier Marital Status.

 Snap of % of customers who preferred business class
 
 ![snap](https://github.com/user-attachments/assets/7ba77605-f0b8-4ae5-8fbe-0edf9d997639)

 
 - Step 17 : New measure was created to calculate **Total Loan Amount by Adult Age Category** & a Line Chart visual was used to represent which credit category in Adults borrowed most loan amount as a total.
 
 Following DAX expression was written to find **Total Loan Amount by Adult Age Category**,
 
         Total Loan Amount by Adult Age Category = CALCULATE(sum('Loan_default'[LoanAmount]),Loan_default[Age Groups] = "Adults",ALLEXCEPT('Loan_default',Loan_default[Age],Loan_default[Age Groups],Loan_default[Credit Score Bins],Loan_default[CreditScore]))
    
 A Line Chart visual was used to represent this Loan (Adults) by Credit Categories.
 
 
![snap1](https://github.com/user-attachments/assets/2596b476-b471-40b5-93fc-830a5ee6625d)
 
 - Step 18 : New measure was created to calculate **Total Loan for Middle Age Adults** & a Clustured Column Chart visual was used to represent total amount by the **has Mortgages** and **has Dependents** along with them.
         Total Loan for Middle Age Adults = SUMX(FILTER('Loan_default',Loan_default[Age Groups] = "Middle Age Adults"),Loan_default[LoanAmount])
 
 
![snap4](https://github.com/user-attachments/assets/68c35f88-7da2-4f11-bf2d-7485b78459f0)

- Step 19 : New measure was created to calculate **Loan by Education Type** & a Line Chart visual was used to represent which Education Category Borrowing the most.

         Loan by Education Type = COUNTROWS(FILTER('Loan_default',NOT(ISBLANK(Loan_default[LoanID]))))
 
![snap5](https://github.com/user-attachments/assets/3fd8bf28-404f-42a4-bc5a-59a7c9ffaa12)

- Step 20 : Now the Report for **Applicant Demographics & Financial Role** is completed, we create a new report in next page for **Financial Risk Metrics** .
- Step 21 : New measure was created to calculate **YOY Loan Amount Change** & a Line Chart visual was used to represent Year on Year Loan Amount Change.

         YOY Loan Amount Change = DIVIDE(CALCULATE( SUM('Loan_default'[LoanAmount]),
        'Loan_default'[Year] =
            YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])))-CALCULATE(
        SUM('Loan_default'[LoanAmount,
        'Loan_default'[Year] =
            YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1), CALCULATE(
        SUM('Loan_default'[LoanAmount]),
        'Loan_default'[Year] =
            YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1  ),0)

 
![snap6](https://github.com/user-attachments/assets/36bc66e6-b987-44f6-959a-6e9926b8db88)

- Step 22 : New measure was created to calculate **YOY Default Loans Change** & a Line Chart visual was used to represent Year on Year Default Loan Cases Change.

         YOY Default Loan Change = DIVIDE(CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default] = TRUE())), 'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])))-
         CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default] = TRUE())), 'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1), CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default] = TRUE())), 'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1),0)

![snap7](https://github.com/user-attachments/assets/cce14f80-4df9-4205-84f5-9de42642848e)

- Step 23 : New measure was created to calculate **YTD Loan Amount** & a Ribbon Chart visual was used to represent Year Till Date Loan Amount by Credit Score Bins and MaritalStatus.

         YTD Loan Amount = CALCULATE(SUM('Loan_default'[LoanAmount]), DATESYTD('Loan_default'[Loan_Date_DD_MM_YYYY].[Date]), ALLEXCEPT('Loan_default', Loan_default[Credit Score Bins],Loan_default[MaritalStatus]))

![snap8](https://github.com/user-attachments/assets/10c590d0-9d33-48b2-96b1-d32d741a29ba)

- Step 24 : A new Decomposition Tree visual was added to analyze sum of **Loan Amount** which explained by **Income Bracket** and **Employment Type**, The **Income Bracket** is the new column to categorise the income , following DAX Expression was used:e Bins and MaritalStatus.

         Income Bracket = SWITCH(
       TRUE(),
      'Loan_default'[Income] < 30000,   "Low Income",
        'Loan_default'[Income] >= 30000 && 'Loan_default'[Income] < 60000, "Medium Income",
      'Loan_default'[Income] >= 60000, "High") 

![snap9](https://github.com/user-attachments/assets/02ac11e5-97db-417c-8f24-eb8b95e192ce)

# Snapshot of Semantic Model (Power BI Service)

![semantic1](https://github.com/user-attachments/assets/25d393d3-9951-4e02-a0c0-1b561039fc8e)

 
 # Report Snapshot (Power BI DESKTOP)

 
![dashboard1](https://github.com/user-attachments/assets/42813d7f-ae16-4f94-a200-2339136d1071)

![dashboard2](https://github.com/user-attachments/assets/9f3811a1-c4a8-4b8f-8036-d4145562c130)

![dashboard3](https://github.com/user-attachments/assets/b7fcd175-77ff-433b-8756-b6fbc575d55f)

# Insights

Three page report was created on Power BI Desktop & it was then published to Power BI Service.

Following inferences can be drawn from the dashboard;

### [1] Total Number of Borrowers = 2,55,347

   Number of Default Loan Cases = 29653 (11.62 %)

   Number of Non-Default Cases = 225694 (88.38 %)



           thus, higher number of customers are Non-Default Cases.
           
### [2] Total Loan Amount (Round Off) For Diffrent Purpose

    a) Home - 6545 Millions
    b) Business - 6522 Millions
    c) Education - 6511 Millions
    d) Auto - 6501 Millions
    e) Other - 6498 Millions
  
  
  while calculating Total Loan Amount Purpose vise, Comes that For the purpose of Home majority of the loan cases are filed. 
  
  These Values will change if different visual filters will be applied.  
  
  ### [3] Average Income respect to each Employment Type 
  
      a) Average Income For Full Time - 82,890.30
      b) Average Income For Self-Employed - 82,446.71
      c) Average Income For Part-Time - 82,389.36
      d) Average Income for unemployed - 82,272.37


 ### [4] Some other insights
 
 ### Class
 
 1.1) 3.39 % deffault cases  by Un-Employed Applicants .
 
 1.2) 3.01 % deffault cases  by Part-Time Applicants.
 
 1.3) 2.86 % deffault cases  by Self-Employed Applicants.

 1.4) 2.36 % deffault cases  by Full-time Applicants
 
         thus, maximum default cases perecntage wise are caused by Un-Employed Applicants .
 
 ### Age Group
 
 2.1)  3.857 % Applicants belong to '<=19' Teen age group.
 
 2.2)  38.483 % Applicants belong to '20-39' Adults age group.
 
 2.3)  38.480 % Applicants belong to '40-59' Middle Age Adults age group.
 
 2.4)  19.18 % customers belong to '>=60' age group.
 
         thus, maximum customers belong to '20-39' Adults age group.
         
### Average Loan by  Age Groups

3.1) Average Loan Amount by **Adults** age group is 1,27,901.01.

3.2) Average Loan Amount by **Middle Age Adults** age group is 1,27,458.87.

3.3) Average Loan Amount by **Senior Citizens** age group is 1,27,355.19.

3.4) Average Loan Amount by **Teen** age group is 1,26,673.94.
       
       thus, **Adults** have maximum Average Loan Amount.

### Default Rate % by Year 

4.1) In year 2013, Default Rate was 11.62 % .

4.2) In year 2014, Default Rate was 11.50 % .

4.3) In year 2015, Default Rate was 11.70 % .

4.4) In year 2016, Default Rate was 11.75 % .

4.5) In year 2017, Default Rate was 11.50 % .

4.6) In year 2018, Default Rate was 11.60 % .

        thus, minimum default rate was observed in year 2014 & 2017,
        and maximum default rate was observed in year 2016

## Loan Applicants by Education 

5.1) Bachelors Applicants were found to be 64366 in numbers.

5.2) High School Applicants were found to be 63903 in numbers.

5.3) Masters Applicants were found to be 63541 in numbers.

5.4) PhD Applicants were found to be 63537 in numbers.
