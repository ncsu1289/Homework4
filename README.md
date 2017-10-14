

```python
#Within the following code we break down the city's school district, and include a District Summary
#A school Summary with both the top 5 and bottom 5 schools w/in the District based upon 
#overall pass rate, and average math and reading scores.
# Within this summary there are the following observed trends.

# Trend 1: Charter Schools appear to out perform District schools.  Each of the top 5 performing
#   schools within the District are Charter Schools.
# Trend 2: All Charter Schools tend to have a 100% passing rate.  
# Trend 3: Compared to Charter Schools, the expense of District schools tend to be higher on average. 
#   All but one District school wound up having a larger school budget per student for lower average passing rate.
# Trend 4: Charter Schools tend to have a smaller size student population than District schools.
```


```python
#dependencies
import pandas as pd

```


```python
#read data files
one_path=("raw_data/schools_complete.csv")
school_data=pd.read_csv(one_path)
#school_data.head()
```


```python
two_path=("raw_data/students_complete.csv")
student_data=pd.read_csv(two_path)
#student_data.head()
```


```python
rename_school_data=school_data.rename(columns={"name":"High School Name"})
#rename_school_data.head()
```


```python
rename_student_data=student_data.rename(columns={"school":"High School Name"})
#rename_student_data.head()
```


```python
merge_school=pd.merge(rename_school_data,rename_student_data, on="High School Name")
#merge_school.head()
```


```python
#merge_school.describe()
```


```python
tot_schools=rename_school_data["High School Name"].count()
#tot_schools
```


```python
tot_student=rename_student_data["name"].count()
#Atot_student
```


```python
tot_budget=rename_school_data["budget"].sum()
#tot_budget
```


```python
#avg math score compares with describe statistics
avg_math_score=rename_student_data["math_score"].sum()/tot_student
#avg_math_score
```


```python
#avg read score compares with describe statistics
avg_read_score=rename_student_data["reading_score"].sum()/tot_student
#avg_read_score
```


```python
pass_math=merge_school.loc[merge_school["math_score"]>=60,:]
tot_pass_math=pass_math["math_score"].count()
#tot_pass_math
```


```python
per_pass_math=(tot_pass_math/tot_student)*100
#per_pass_math
```


```python
pass_read=merge_school.loc[merge_school["reading_score"]>=60,:]
tot_pass_read=pass_read["reading_score"].count()
#tot_pass_read
```


```python
#compares with describe statistics where the lowest reading score was 63.  So, everyone is above 60 or "D"
per_pass_read=(tot_pass_read/tot_student)*100
#per_pass_read
```


```python
#overall passing rate as the average of % passing math and % passing reading
overall_pass=(per_pass_math + per_pass_read)/2.0
#overall_pass
```


```python
# This is for the Summary Data Frame
print("**District Summary**")

```

    **District Summary**



```python
dist_sum=[{"Total Schools":tot_schools,"Total Students":tot_student,"Total Budget":tot_budget,"Average Math Score":avg_math_score,"Average Reading Score":avg_read_score,"% Passing Math":per_pass_math,"% Passing Reading":per_pass_read,"Overall Passing Rate":overall_pass}]
District_Summary=pd.DataFrame(dist_sum)
District_Summary.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>Overall Passing Rate</th>
      <th>Total Budget</th>
      <th>Total Schools</th>
      <th>Total Students</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>92.445749</td>
      <td>100.0</td>
      <td>78.985371</td>
      <td>81.87784</td>
      <td>96.222875</td>
      <td>24649428</td>
      <td>15</td>
      <td>39170</td>
    </tr>
  </tbody>
</table>
</div>




```python
# This section begins the School Summary
```


```python
group_school_avg=merge_school.groupby(["High School Name"])
group_school_avg=pd.DataFrame(group_school_avg.mean())
gp_school_avg=group_school_avg.reset_index()
#gp_school_avg.head()
```


```python
parse_avg_school=gp_school_avg[["High School Name","reading_score","math_score"]]
#parse_avg_school.head()
```


```python
merged_school_data=pd.merge(rename_school_data,parse_avg_school, on="High School Name")
#merged_school_data.head()
```


```python
rename_merge_school_data=merged_school_data.rename(columns={"type":"School Type","size":"Total Students","budget":"Total School Budget","reading_score":"Average Reading Score","math_score":"Average Math Score"})
#rename_merge_school_data.head()
```


```python
rename_merge_school_data["Per Student Budget"]=rename_merge_school_data["Total School Budget"]/rename_merge_school_data["Total Students"]
#rename_merge_school_data.head()
```


```python
passing_read=merge_school.loc[merge_school["reading_score"]>=60,:]
pss_read=passing_read[["High School Name","reading_score"]]
#pss_read.head()
```


```python
read_pass=pss_read["High School Name"].value_counts()
#read_pass.head()

```


```python
passing_math=merge_school.loc[merge_school["math_score"]>=60,:]
pss_math=passing_math[["High School Name","math_score"]]
#pss_math.head()
```


```python
math_pass=pss_math["High School Name"].value_counts()
#math_pass.head()
```


```python
r_m_passing=pd.DataFrame({"Passing Reading":read_pass,"Passing Math":math_pass})
#r_m_passing.head()
```


```python
passing=r_m_passing.reset_index()
#passing.head()
```


```python
per_pass=passing.rename(columns={"index":"High School Name"})
#per_pass.head()
```


```python
new_merge=pd.merge(rename_merge_school_data,per_pass,on="High School Name")
#new_merge.head(20)
```


```python
new_merge["% Passing Math"]=(new_merge["Passing Math"]/new_merge["Total Students"])*100
new_merge["% Passing Reading"]=(new_merge["Passing Reading"]/new_merge["Total Students"])*100
new_merge["% Overall Passing"]=(new_merge["% Passing Math"]+new_merge["% Passing Reading"])/2
#new_merge.head()
```


```python
print("**School Summary**")
```

    **School Summary**



```python

```


```python
school_sum=new_merge[["High School Name","School Type","Total Students","Total School Budget","Per Student Budget","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading", "% Overall Passing"]]
school_sum.round(2)

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>High School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.63</td>
      <td>81.18</td>
      <td>88.86</td>
      <td>100.0</td>
      <td>94.43</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.71</td>
      <td>81.16</td>
      <td>88.44</td>
      <td>100.0</td>
      <td>94.22</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.36</td>
      <td>83.73</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>77.29</td>
      <td>80.93</td>
      <td>89.08</td>
      <td>100.0</td>
      <td>94.54</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.35</td>
      <td>83.82</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.27</td>
      <td>83.99</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.06</td>
      <td>83.98</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Bailey High School</td>
      <td>District</td>
      <td>4976</td>
      <td>3124928</td>
      <td>628.0</td>
      <td>77.05</td>
      <td>81.03</td>
      <td>89.53</td>
      <td>100.0</td>
      <td>94.76</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.80</td>
      <td>83.81</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.84</td>
      <td>84.04</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.68</td>
      <td>83.96</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.84</td>
      <td>80.74</td>
      <td>88.55</td>
      <td>100.0</td>
      <td>94.27</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.07</td>
      <td>80.97</td>
      <td>89.18</td>
      <td>100.0</td>
      <td>94.59</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Ford High School</td>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>77.10</td>
      <td>80.75</td>
      <td>89.30</td>
      <td>100.0</td>
      <td>94.65</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.42</td>
      <td>83.85</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
  </tbody>
</table>
</div>




```python
# This section begins the Top Performing School Summary Analysis
```


```python
sch_summary=school_sum.sort_values(["High School Name"])
#sch_summary.head()
```


```python
sum_sch=sch_summary.reset_index()
#sum_sch.head()
```


```python
school_summary=school_sum[["High School Name","School Type","Total Students","Total School Budget","Per Student Budget","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading", "% Overall Passing"]]
#school_summary.head()
```


```python
tops=school_summary.sort_values(["% Overall Passing"],ascending=False)
tops["Total Score"]=tops["Average Math Score"]+tops["Average Reading Score"]
#tops.head(28)
```


```python
#trend 1: Charter Schools appear to out perform District schools
#trend 2: All Charter Schools tend to have a 100% passing rate.
```


```python
top5=tops.sort_values(["Total Score"],ascending=False).head()
```


```python
top_five=top5.reset_index()
#top_five.head()
```


```python
print("Top Performing Schools (By Passing Rate)")
```

    Top Performing Schools (By Passing Rate)



```python
top_5=top_five[["High School Name","School Type","Total Students","Total School Budget","Per Student Budget","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading", "% Overall Passing"]]
top_5.round(2).head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>High School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.84</td>
      <td>84.04</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.68</td>
      <td>83.96</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.80</td>
      <td>83.81</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.42</td>
      <td>83.85</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.27</td>
      <td>83.99</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# This section begins the Bottom 5 performing school summary.
```


```python
bottom=tops.sort_values(["Total Score"])
#bottom.head()
```




```python
bottom_five=bottom.reset_index()
#bottom_five.head()
```


```python
print("**Least Successful Schools (By Passing Rate)**")
```

    **Least Successful Schools (By Passing Rate)**



```python
bottom_5=bottom_five[["High School Name","School Type","Total Students","Total School Budget","Per Student Budget","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading", "% Overall Passing"]]
bottom_5.round(2).head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>High School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.84</td>
      <td>80.74</td>
      <td>88.55</td>
      <td>100.0</td>
      <td>94.27</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.63</td>
      <td>81.18</td>
      <td>88.86</td>
      <td>100.0</td>
      <td>94.43</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Ford High School</td>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>77.10</td>
      <td>80.75</td>
      <td>89.30</td>
      <td>100.0</td>
      <td>94.65</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.71</td>
      <td>81.16</td>
      <td>88.44</td>
      <td>100.0</td>
      <td>94.22</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.07</td>
      <td>80.97</td>
      <td>89.18</td>
      <td>100.0</td>
      <td>94.59</td>
    </tr>
  </tbody>
</table>
</div>




```python
# This section begins the analysis of math scores by grade
```


```python
mthbygrade=merge_school.groupby(["High School Name","grade"])
mth_by_grade=pd.DataFrame(mthbygrade.mean())
#mth_by_grade.head()
```




```python
math_by_grade=mth_by_grade.rename(columns={"math_score":"Avg Math Score by Grade"})
#math_by_grade.head()
```


```python
print("Math Scores by Grade")
```

    Math Scores by Grade



```python
math_by_grades=math_by_grade["Avg Math Score by Grade"]
avg_math_by_grade=pd.DataFrame(math_by_grades).round(2)
avg_math_by_grade.head(80)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Avg Math Score by Grade</th>
    </tr>
    <tr>
      <th>High School Name</th>
      <th>grade</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="4" valign="top">Bailey High School</th>
      <th>10th</th>
      <td>77.00</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>77.52</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>76.49</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>77.08</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Cabrera High School</th>
      <th>10th</th>
      <td>83.15</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>82.77</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.28</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.09</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Figueroa High School</th>
      <th>10th</th>
      <td>76.54</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>76.88</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>77.15</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>76.40</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Ford High School</th>
      <th>10th</th>
      <td>77.67</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>76.92</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>76.18</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>77.36</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Griffin High School</th>
      <th>10th</th>
      <td>84.23</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.84</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.36</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>82.04</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Hernandez High School</th>
      <th>10th</th>
      <td>77.34</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>77.14</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>77.19</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>77.44</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Holden High School</th>
      <th>10th</th>
      <td>83.43</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>85.00</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>82.86</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.79</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Huang High School</th>
      <th>10th</th>
      <td>75.91</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>76.45</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>77.23</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>77.03</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Johnson High School</th>
      <th>10th</th>
      <td>76.69</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>77.49</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>76.86</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>77.19</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Pena High School</th>
      <th>10th</th>
      <td>83.37</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.33</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.12</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.63</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Rodriguez High School</th>
      <th>10th</th>
      <td>76.61</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>76.40</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>77.69</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>76.86</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Shelton High School</th>
      <th>10th</th>
      <td>82.92</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.38</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.78</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.42</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Thomas High School</th>
      <th>10th</th>
      <td>83.09</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.50</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.50</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.59</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Wilson High School</th>
      <th>10th</th>
      <td>83.72</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.20</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.04</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.09</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Wright High School</th>
      <th>10th</th>
      <td>84.01</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.84</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.64</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.26</td>
    </tr>
  </tbody>
</table>
</div>




```python
# This section begins the Reading scores by grade analysis
```


```python
rdbygrade=merge_school.groupby(["High School Name","grade"])
rd_by_grade=pd.DataFrame(rdbygrade.mean())
#rd_by_grade.head()
```


```python
read_by_grade=rd_by_grade.rename(columns={"reading_score":"Avg Reading Score by Grade"})
#read_by_grade.head()
```


```python
print("Reading Scores by Grade")
```

    Reading Scores by Grade



```python
read_by_grades=read_by_grade["Avg Reading Score by Grade"]
avg_read_by_grade=pd.DataFrame(read_by_grades)
avg_read_by_grade.round(2).head(80)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Avg Reading Score by Grade</th>
    </tr>
    <tr>
      <th>High School Name</th>
      <th>grade</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="4" valign="top">Bailey High School</th>
      <th>10th</th>
      <td>80.91</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.95</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.91</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>81.30</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Cabrera High School</th>
      <th>10th</th>
      <td>84.25</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.79</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.29</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.68</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Figueroa High School</th>
      <th>10th</th>
      <td>81.41</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.64</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>81.38</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>81.20</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Ford High School</th>
      <th>10th</th>
      <td>81.26</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.40</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.66</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>80.63</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Griffin High School</th>
      <th>10th</th>
      <td>83.71</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.29</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.01</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.37</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Hernandez High School</th>
      <th>10th</th>
      <td>80.66</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>81.40</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.86</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>80.87</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Holden High School</th>
      <th>10th</th>
      <td>83.32</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.82</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.70</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.68</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Huang High School</th>
      <th>10th</th>
      <td>81.51</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>81.42</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.31</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>81.29</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Johnson High School</th>
      <th>10th</th>
      <td>80.77</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.62</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>81.23</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>81.26</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Pena High School</th>
      <th>10th</th>
      <td>83.61</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.34</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.59</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.81</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Rodriguez High School</th>
      <th>10th</th>
      <td>80.63</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.86</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.38</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>80.99</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Shelton High School</th>
      <th>10th</th>
      <td>83.44</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.37</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>82.78</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>84.12</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Thomas High School</th>
      <th>10th</th>
      <td>84.25</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.59</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.83</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.73</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Wilson High School</th>
      <th>10th</th>
      <td>84.02</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.76</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.32</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.94</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Wright High School</th>
      <th>10th</th>
      <td>83.81</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.16</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.07</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.83</td>
    </tr>
  </tbody>
</table>
</div>




```python
# This section begins the binning process, starting with binning scores by school spending.
```


```python
score_by_spending=school_sum[["High School Name","School Type","Per Student Budget","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading", "% Overall Passing"]]
#score_by_spending.head()
```


```python
print("Scores by School Spending")
```

    Scores by School Spending



```python
bin1=[560,585,610,635,660]
group_names=["Least Costly","Low Investment","Higher Investment","Costly"]
score_by_spending["Expense"]=pd.cut(score_by_spending["Per Student Budget"],bin1,labels=group_names)
sbs=pd.DataFrame(score_by_spending[["Expense","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading","% Overall Passing"]])

#sbs.head()
```

    /anaconda/envs/PythonData/lib/python3.6/site-packages/ipykernel/__main__.py:3: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      app.launch_new_instance()



```python
sbs1=sbs.groupby(["Expense"]).mean()
sbs1.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing</th>
    </tr>
    <tr>
      <th>Expense</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Costly</th>
      <td>77.866721</td>
      <td>81.368774</td>
      <td>90.487284</td>
      <td>100.0</td>
      <td>95.243642</td>
    </tr>
    <tr>
      <th>Higher Investment</th>
      <td>80.199966</td>
      <td>82.425360</td>
      <td>94.764871</td>
      <td>100.0</td>
      <td>97.382436</td>
    </tr>
    <tr>
      <th>Least Costly</th>
      <td>83.455399</td>
      <td>83.933814</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Low Investment</th>
      <td>83.599686</td>
      <td>83.885211</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
print("Scores by School Size")
```

    Scores by School Size



```python
score_by_size=school_sum[["High School Name","School Type","Total Students","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading", "% Overall Passing"]]
bin2=[0,1700,3400,5100]
group_names2=["Small","Medium","Large"]
score_by_size["School Size"]=pd.cut(score_by_size["Total Students"],bin2,labels=group_names2)
#score_by_size.head(20)
sbs2=pd.DataFrame(score_by_size[["School Size","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading","% Overall Passing"]])
#sbs2.head()
```

    /anaconda/envs/PythonData/lib/python3.6/site-packages/ipykernel/__main__.py:4: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy



```python
sbs3=sbs2.groupby(["School Size"]).mean()
sbs3.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing</th>
    </tr>
    <tr>
      <th>School Size</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Large</th>
      <td>77.063340</td>
      <td>80.919864</td>
      <td>89.085722</td>
      <td>100.0</td>
      <td>94.542861</td>
    </tr>
    <tr>
      <th>Medium</th>
      <td>80.545935</td>
      <td>82.676142</td>
      <td>95.228263</td>
      <td>100.0</td>
      <td>97.614131</td>
    </tr>
    <tr>
      <th>Small</th>
      <td>83.603261</td>
      <td>83.881343</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
score_by_type=school_sum[["School Type","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading", "% Overall Passing"]]
sbt=score_by_type.groupby(["School Type"]).mean()
sbt.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing</th>
    </tr>
    <tr>
      <th>School Type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.473852</td>
      <td>83.896421</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>District</th>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>88.991533</td>
      <td>100.0</td>
      <td>94.495766</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
