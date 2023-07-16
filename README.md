# SchoolMgmt
Pet Project
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

staff_data = pd.read_csv("C:\Pavani-python\Staff.csv", encoding='latin1')
attendance_data = pd.read_csv("C:\Pavani-python\StaffAttendance.csv", encoding='latin1')
school_data = pd.read_csv("C:\Pavani-python\School.csv", encoding='latin1')
council_data = pd.read_csv("C:\Pavani-python\Council.csv", encoding='latin1')

# Merge staff and attendance data based on staff ID
merged_data = pd.merge(attendance_data, staff_data, on='StaffId')

# Merge merged_data with school data based on school ID
merged_data = pd.merge(merged_data, school_data, on='SchoolID')

# Merge merged_data with council data based on council ID
merged_data = pd.merge(merged_data, council_data, on='SchoolID')

# Average count of teachers in a particular year
years = [2018, 2019, 2020]
average_teacher_count_year = []
for year in years:
    filtered_data_year = merged_data[(pd.to_datetime(merged_data['Date of Attendance'], dayfirst=True).dt.year == year)
                                     & (merged_data['TeacherAttendance'] == 'Present')]
    teacher_count = filtered_data_year.groupby('CouncilName')['StaffId'].nunique()
    average_teacher_count_year.append(teacher_count.mean())

# Average count of teachers in a particular council
Council_dta = ['XYZ', 'ABC', 'SDFJ', 'SDLFJ', 'WEUR', 'KVDSFSC', 'SPEOWRIS', 'ZDOFSD', 'XCVM', 'WERUY']
average_teacher_count_council = []
for council in Council_dta:
    filtered_data_council = merged_data[(merged_data['CouncilName'] == council)
                                        & (merged_data['SchoolName'].isin(['Primary', 'Secondary', 'Special']))
                                        & (merged_data['TeacherAttendance'] == 'Present')]
    teacher_count = filtered_data_council.groupby('SchoolName')['StaffId'].nunique()
    average_teacher_count_council.append(teacher_count.mean())

# Create figure and axis objects
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(8, 8))

# Bar plot - Average teacher count by year
ax1.bar(years, average_teacher_count_year, color='blue')
ax1.set_xlabel('Year')
ax1.set_ylabel('Average Teacher Count')
ax1.set_title('Average Teacher Count by Year')

# Line plot - Average teacher count by council
ax2.plot(Council_dta, average_teacher_count_council, color='red', marker='o')
ax2.set_xlabel('Council')
ax2.set_ylabel('Average Teacher Count')
ax2.set_title('Average Teacher Count by Council')

# Adjust spacing between subplots
plt.tight_layout()

# Display the graph
plt.show()
