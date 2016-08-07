# Homework Week 12 - SAS File Get Http Assignment
Bill Kerneckel  
August 7, 2016  



****************************

#### Assignment:

The orginal assignment was to pull in datasets from various classmates Github accounts using the SAS proc http method="get" command. Since SMU has locked down the security settings of the university edition of SAS so the proc http method="get" command will not work. The new assignment is now to go to each of the classmates github accounts and download the datasets to your hard drive. Using SAS commands to import the files, merged and clean the data. In the end you wil have a one excel file containing all the merged dataset. The excel file will reside on your Github account.


1. Download data to desktop from various classmates Github accounts.
2. Import and merge each dataset into one excel file. Use the following file "dataset1.sas7bdat" as the dataset to merge the various classmate datasets to. 
3. Upload the excel file to Github.

****************************

#### Modifications to the downloaded files

To make this assignment run smoother I converted all files to .XLS format.


****************************
#### Instructions

- <strong>Step 1:</strong> Download dataset1.zip file from the Files tab on 2ds.
- <strong>Step 2:</strong> Unzip the file and place "dataset1.sas7bdat" file on your desktop.
- <strong>Step 3:</strong> In SAS goto the Explore tab and locate the "dataset1.sas7bdat" that resides on your desktop.
- <strong>Step 4:</strong> When you open the file "dataset1.sas7bdat" you should see the data appear in your SAS session. The data set should contain 51 rows and 14 columns.
- <strong>Step 5:</strong> Create a new .xls file and save it as "testscores.xls" to your desktop.
- <strong>Step 6:</strong> Plug in the following SAS code and run:

```
proc export 
  data=_EXP0_.DATASET1 
  dbms=xls 
  outfile="\\Client\H$\desktop\testscores.xls" 
  replace;
run;
```
<br>
<strong><u>NOTE</u></strong>
<br>
<br>
Make sure you set the data = in the SAS proc export code to whatever the SAS dataset is called. In this example the dataset was called <strong>"_EXPO_.DATASET1_"</strong>.

****************************

#### Download datasets from classmates github accounts to specified folder location on your harddrive.

Use the following SAS command lines to grab the files from your local folder on your hard drive.
<br>
<br>
<strong>Load all datasets:</strong>

```
/* First Dataset*/
filename _inbox "\\Client\H$\desktop\SASHttpGet\data\AAbbottEducation.xls"; 
proc import file=_inbox out=dataset_one dbms=xls; 
getnames=yes;
/*proc print data=dataset_one;*/ /*uncomment this line for debugging*/
run;
data dataset_one_cleaned;
	set dataset_one (keep = State PctMalesNeverMarried PctFemalesNeverMarried);
/*proc print data=dataset_one_cleaned;*/ /*uncomment this line for debugging*/
run;

/* Second Dataset*/
filename _inbox2 "\\Client\H$\desktop\SASHttpGet\data\education_assignment_restaurants_vf.xls"; 
proc import file=_inbox2 out=dataset_two dbms=xls; 
getnames=yes;
/*proc print data=dataset_two;*/ /*uncomment this line for debugging*/
run;

data dataset_two_cleaned;
	set dataset_two (keep = state_abbr Population rest_person rest_100_people );
/*proc print data=dataset_two_cleaned;*/ /*uncomment this line for debugging*/
run;

/* Third Dataset*/
filename _inbox3 "\\Client\H$\desktop\SASHttpGet\data\High_school_graduate_2009.xls"; 
proc import file=_inbox3 out=dataset_three dbms=xls; 
getnames=yes;
/*proc print data=dataset_three;*/ /*uncomment this line for debugging*/
run;

data dataset_three_cleaned;
	set dataset_three (keep = state_abbr Population rest_person rest_100_people );

/*proc print data=dataset_three_cleaned;*/ /*uncomment this line for debugging*/
run;

/* Fourth Dataset*/
filename _inbox4 "\\Client\H$\desktop\SASHttpGet\data\PovertyLevel.xls"; 
proc import file=_inbox4 out=dataset_four dbms=xls; 
getnames=yes;
/*proc print data=dataset_four;*/ /*uncomment this line for debugging*/
run;

/* Fifth Dataset*/
filename _inbox5 "\\Client\H$\desktop\SASHttpGet\data\Shaw_Electoral_Votes.xls"; 
proc import file=_inbox5 out=dataset_five dbms=xls; 
getnames=yes;
/*proc print data=dataset_five;*/ /*uncomment this line for debugging*/
run;

/* Sixth Dataset*/
filename _inbox6 "\\Client\H$\desktop\SASHttpGet\data\UnemploymentRate2016.xls"; 
proc import file=_inbox6 out=dataset_six dbms=xls; 
getnames=yes;
/*proc print data=dataset_six;*/ /*uncomment this line for debugging*/
run;

/* Seventh Dataset - this was my assigned file. The data in this file contains data from _EXPO_.DATASET1_ dataset. This will helpful when I merged the datasets together */
filename _inbox7 "\\Client\H$\desktop\SASHttpGet\data\testscores.xls"; 
proc import file=_inbox7 out=dataset_seven dbms=xls; 
getnames=yes;
/*proc print data=dataset_seven;*/ /*uncomment this line for debugging*/
run;
```

<br>
<strong>Merge Datasets into one dataset</strong>
<br>

```
/* Merge Datasets into one dataset*/
data merged_data;
merge dataset_one_cleaned dataset_two_cleaned dataset_four dataset_five dataset_six dataset_seven;
/*proc print data=merged_data;*/ /*uncomment this line for debugging*/
run;
```

<br>
<strong>Drop unwanted columns from the merged dataset</strong>
<br>

```
/* Drop Columns */
data cleaned_merged_data;
set merged_data (drop=State_Code);
run;
```

<br>
<strong>Rearrange the columns so the dataset is easier to read</strong>
<br>

```
/* Rearrange Columns */
data arranged_data;
Retain
State
state_abbr
PctMalesNeverMarried
PctFemalesNeverMarried
Population
Pop
Prop_Grad
rest_person
rest_100_people
PCT_PovertyLevel
Total_Electoral_Vote_Count
UnemploymentRate
SPENDING_PER_STUDENT
SAT_PCT_TESTED
SATReading
SATMath
SATWriting
SATComb
ACT_PCT_TESTED
ACTEnglish
ACTMath
ACTReading
ACTScience
ACTComb
;
set cleaned_merged_data;
/* proc print data=arranged_data;*/
run;
```

<br>
<strong>Validate the calculations in the Combined total columns in the merged dataset.</strong>
<br>

```
/* Validate Testing Scores */
data valid_dataset;
set arranged_data;
ACTComb = sum(ACTEnglish, ACTMath, ACTReading, ACTScience);
format SPENDING_PER_STUDENT dollar15.2;
run;
```

<br>
<strong>Format columns to meaniful format</strong>
<br>

```
/* Change Column Types to readable formats */
proc print data=valid_dataset;
title 'Merged and Cleaned Dataset for Week 12 Homework Assignment';
run; 
```


****************************

#### Export the data to excel file on a speficed folder location on your hard drive.

```
/* Export dataset to folder */
proc export 
  data=valid_dataset 
  dbms=xlsx 
  outfile="\\Client\H$\desktop\SASHttpGet\data\final_testscores.xlsx" 
  replace;
run;
```

****************************

####  Upload Excel file to github                          

Now that you have the "final_testscores.xlsx" file on your desktop you can now upload it to your gitHub.


****************************

#### View Excel File                                                 

To download and view the excel file click on the icon. [![click Here](images/excel.png)](https://github.com/wkerneck/SASFileGetHttp/blob/master/data/final_testscores.xlsx?raw=true)



