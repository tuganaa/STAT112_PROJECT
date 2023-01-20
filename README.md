# STAT112_PROJECT
############### Birth Weight & Other Variables #######################
###############  Emre Geniş & Tuğana Akın & Gürdal Safel & Yağmur Çınar ##########################
 
# Lets import the modules to use it for cleaning the data
import numpy as np 
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# We import the excel file
table=pd.read_excel("child_birth.xlsx")

# to see the dirty data visually we use this codes.
table.info()

# To see the null values
table.isnull().sum()

# To see how many object(str) in the dataset
for i in table.select_dtypes(include="object").columns:
  print(table[i].value_counts())

# Fixing the elements of the data with title format
table.columns=table.columns.str.title()

### This code helps to arrange specific column names.

table=table.rename({"Arsiv.No":"Archive Number"},axis="columns")
table =table.rename({"age":"Age"},axis="columns")
table =table.rename({"Birth.Weight":"Birth Weight"},axis="columns")
table =table.rename({"Birth.Week":"Birth Week"},axis="columns")
table =table.rename({"Bmi":"BMI"},axis="columns")
table =table.rename({"Wgdp":"WGDP"},axis="columns")
table =table.rename({"Fbs":"FBS"},axis="columns")
table =table.rename({"Pp1":"PP1"},axis="columns")
table =table.rename({"Pp2":"PP2"},axis="columns")

# Inside the data the value 10000 was extraorinary value so we decided to get rid of them

print(table['Birth Weight'].isnull().sum())
table['Birth Weight'].loc[table['Birth Weight']==10000]=table['Birth Weight'].loc[table['Birth Weight']==10000]=np.nan
print(table['Birth Weight'].isnull().sum())

# We observed that data below 0.5 should be in kg unit and above that should be in gr unit so corrected it

table["Unit"].loc[table["Birth Weight"]<0.5]=table["Unit"].loc[table["Birth Weight"]<0.5]="kg"
table["Unit"].loc[table["Birth Weight"]>0.5]=table["Unit"].loc[table["Birth Weight"]>0.5]="gr"

# Unit and Birth Weight column was not matching, so we changed the birth weight with the corresponding value for kg and gr. 
table['Birth Weight'].loc[table['Unit'] =="kg"] = table['Birth Weight'].loc[table['Unit'] =="kg"]*10000
table['Birth Weight'].loc[table['Unit'] =="gr"] = table['Birth Weight'].loc[table['Unit'] =="gr"]*100

# In the units column, all columns should be gr; therefore, we change kg to gr.
table["Unit"]=table["Unit"].loc[table["Unit"]=="kg"]="gr"


# The archive Number was not so significant; therefore, we dropped it using the drop method.
table = table.drop('Archive Number',axis = 1)


# This code helps to find the mode, mean, and median in that column. 
mode_age = table['Age'].mode()
mean_BMI = table['BMI'].mean()
mean_WGDP = table['WGDP'].mean()
mean_birthweight = table['Birth Weight'].mean()
mean_birthweek = table['Birth Week'].mean()
mean_FBS = table['FBS'].mean()
mean_PP1 = table['PP1'].mean()
mean_PP2 = table['PP2'].mean()


# Above this, we find the mean, the mode, and the median.Use that specifics value to fill the na inside the data.
table["WGDP"]=table['WGDP'].fillna(mean_WGDP)
table["Birth Weight"]=table['Birth Weight'].fillna(mean_birthweight)
table["Birth Week"]=table['Birth Week'].fillna(mean_birthweek)
table["BMI"]=table['BMI'].fillna(mean_BMI)
table["FBS"]=table['FBS'].fillna(mean_FBS)
table["PP1"]=table['PP1'].fillna(mean_PP1)
table["PP2"]=table['PP2'].fillna(mean_PP2)
table["Age"]=table['Age'].fillna(mode_age)
table["Gender"]=table["Gender"].fillna(table["Gender"].mode())

# Glucose presence in urine depends on PP2 and FBS levels. According to our researchs,individuals with FBS rates below 95 or PP2 rates below 140 are not including glucose in their urines which shows that individuals with these rates don't have diabetes. However, we observed that patients with high rates of FBS and PP2 are given 'No' for glucose presence. So that we changed the unvalid values to 'Yes'. While doing that we changed null values too. Additionally, the remaining values are filled with 'No'.

table['Glucose'].loc[table["PP2"]>=140]=table['Glucose'].loc[table["PP2"]>=140]="Yes"
table['Glucose'].loc[table["FBS"]>=95]=table['Glucose'].loc[table["FBS"]>=95]="Yes"
table["Glucose"]=table["Glucose"].fillna("No")

# All remaining null values were located in 'Birth Weight' element. Since 'Birth Weight' data is essential for our research, we decided to drop null values.

table = table.dropna(axis=0)

# To observe the accuracy of the code given above, we inserted the codes given below.

print(table.describe())
print(table.isnull().sum())
table.head(55)



# Inside The Glucose and The Gender columns, the spelling of the values was not the same; therefore, we made the same with these codes.
table["Glucose"]=table["Glucose"].str.title()
table["Glucose"]=table["Glucose"].str.strip()
table["Gender"]=table["Gender"].str.title()
table["Gender"]=table["Gender"].str.strip()

# To observe the accuracy codes given above, we inserted the codes given below.

for i in table.select_dtypes(include="object").columns:
  print(table[i].value_counts())


#  To observe the descriptive statistics of the values we own, we inserted the code given below.

table.describe().transpose()

# To observe the outliers on 'Age' rates, we calculated the boundaries.

median = np.median(table['Age'])
upper_quartile = np.percentile(table['Age'], 75)
lower_quartile = np.percentile(table['Age'], 25)

iqr = upper_quartile - lower_quartile
upper_whisker = upper_quartile + 1.5iqr
lower_whisker = lower_quartile - 1.5iqr
 
print(upper_whisker)
print(lower_whisker)

#We eliminate the outliers wşth using this codes.
table["Birth Weight"].loc[table["Birth Weight"]>7720.240]=table["Birth Weight"].loc[table["Birth Weight"]>7720.240]=table["Birth Weight"].mean()
table["WGDP"].loc[table["WGDP"]>18]=table["WGDP"].loc[table["WGDP"]>18]=table["WGDP"].mean()

# After we calculated the boundaries as 49 and 17 for 'Age' values, we deleted the values above 49 and below 17 since they are considered as outliers. The only outlier value in our 'Age' data was 99 which could be considered unrealistic.

table_filtered_age=table.loc[table["Age"]<49]

# To work faster on our cleaned dataset, we created a new excel file which includes cleaned datas. Hence, we wrote our table_filtered_age dataset to the new file.

table_filtered_age.to_excel("stat112excel.xlsx",sheet_name="sheet1")


# Lets make the visualization by using seaborn and matplot.


# This code visualize 'WGDP' and 'Birth Week' with lineplot.With hue method we add the categorical variable.Fontsize change the size of the title and with fontweight we make bold.With xlabel and ylabel we subtitle below the axisis.
plt.figure(figsize = (14,8))
sns.lineplot(data=proje112, x="Birth Week", y="WGDP",hue="Glucose")
plt.title('Relationship Between WGDP and Birth Week',fontsize = 15, fontweight = "bold")
plt.xlabel('Birth Week')
plt.ylabel('WGDP (kg)')


# This code visualize 'Birth Week' and 'WGDP' with scatter plot and we add the categorical variable of glucose too.
sns.lmplot(data=proje112, x="Birth Weight", y="WGDP",hue="Glucose",height=8)
plt.title('Relationship between WGDP and Birth Weight',fontsize = 15, fontweight = "bold")
plt.xlabel('Birth Weight (gr)')
plt.ylabel('WGDP (kg)')

# This code visualize 'Birth Week' and 'Gender' with barplot.With figsize we arrange the size of the bars.
plt.figure(figsize = (12,8))
sns.barplot(data=proje112, y="Birth Weight", x="Gender",hue="Glucose")
plt.title('Relationship between Birth Weight and Gender',fontsize = 15, fontweight = "bold")
plt.ylabel('Birth Weight (g)')

# This code visualize 'Birth Week' and 'Birth Weight' with lineplot.With hue we add categorical variable.
plt.figure(figsize=(14,8))
sns.lineplot(data=proje112, x="Birth Week", y="Birth Weight",hue="Glucose")
plt.title('Relationship between Birth Weight and Birth Week',fontsize = 15, fontweight = "bold")
plt.xlabel('Birth Week')
plt.ylabel('Birth Weight (gr)')

# This code gives us the distribution of 'WGDP' with boxplot.
plt.figure(figsize=(12,8))
sns.boxplot(x=proje112["WGDP"])
plt.title('Distribution of WGDP',fontsize = 15, fontweight = "bold")
plt.xlabel('WGDP (kg)')

# This code gives us the histogram of the 'Birth Weight'.
sns.displot(proje112, x = "Birth Weight",height = 8)
plt.title('Distribution of Birth Weight',fontsize = 15, fontweight = "bold")
plt.xlabel('Birth Weight (gr)')


