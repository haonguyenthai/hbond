# hbond
HBond visualization
import matplotlib.pyplot as plt
import numpy as np
import csv
import glob
import fileinput
import pandas as pd
import os
#save into csv file"
file = ""
data = pd.read_csv(file,delim_whitespace=True, header=None, skiprows=25)
data
data.columns=['Time','H Bonds', 'Pair within 0.35nm']
csv_file = "/work9/haont/copy-update/500/glu/trial1/hbond/hbond19_308.csv"
data.to_csv(csv_file)
#loading data in order
all_data={}
for i in range(1, 21):
    dir_path = f"/work9/haont/copy-update/500/glu/trial{i}/hbond"
    file_pattern = f"hbond19_*.xvg"
    file_names = [f for f in os.listdir(dir_path) if f.startswith('hbond19_') and f.endswith('.xvg')]
    file_names = sorted(file_names, key=lambda x: int(x.split("_")[1].split(".")[0]) if x.split("_")[1].split(".")[0].isdigit() else float('inf'))
    data = pd.DataFrame()
    for file_name in file_names:
        file_path = os.path.join(dir_path, file_name)
        file_data = pd.read_csv(file_path, delim_whitespace=True, skiprows=25, header=None, usecols=[1])
        file_data.rename(columns={1: file_name}, inplace=True)
        data = pd.concat([data, file_data], axis=1)
    all_data[f"trial{i}"] = data
    #loading the second column
all_data2={}
for i in range(1, 21):
    dir_path = f"/work9/haont/copy-update/500/glu/trial{i}/hbond"
    file_pattern = f"hbond19_*.xvg"
    file_names = [f for f in os.listdir(dir_path) if f.startswith('hbond19_') and f.endswith('.xvg')]
    file_names = sorted(file_names, key=lambda x: int(x.split("_")[1].split(".")[0]) if x.split("_")[1].split(".")[0].isdigit() else float('inf'))
    data = pd.DataFrame()
    for file_name in file_names:
        file_path = os.path.join(dir_path, file_name)
        file_data = pd.read_csv(file_path, delim_whitespace=True, skiprows=25, header=None, usecols=[2])
        file_data.rename(columns={2: file_name}, inplace=True)
        data = pd.concat([data, file_data], axis=1)
    all_data2[f"trial{i}"] = data
    #calulate the sum of each column
    sum_df = pd.DataFrame()

for trial, data in all_data.items():
    trial_sum = data.iloc[1:].sum()
    sum_df[trial] = trial_sum
    
# calculate the sum for 2nd data
sum_df2 = pd.DataFrame()

for trial, data in all_data2.items():
    trial_sum = data.iloc[1:].sum()
    sum_df2[trial] = trial_sum
    
#final sum
result_df = sum_df.add(sum_df2)
#plot data
import matplotlib.pyplot as plt

# assuming your DataFrame is named df
num_rows = plotdata.shape[0]

# set up a 5x4 grid of subplots
fig, axs = plt.subplots(5, 4, figsize=(20, 20))
axs = axs.ravel()
# loop through each row
for i in range(num_rows):
    # plot the data for the current row on the corresponding subplot
    axs[i].plot(plotdata.iloc[i])
    # add a title to the subplot
    axs[i].set_title(f"Trial {i+1}")
    # extract the numeric value of the pattern name from the column name
    # show x-tick labels only if corresponding y-value is greater than 200
    xtick_labels = [col.split('_')[1].split('.')[0] for col in plotdata.columns if plotdata.iloc[i][col] > 1000]
    axs[i].set_xticks([col for col in plotdata.columns if plotdata.iloc[i][col] > 1000])
    axs[i].set_xticklabels(xtick_labels)
    # rotate x-tick labels 90 degrees
    axs[i].tick_params(axis='x', labelrotation=90)
    axs[i].set_ylim([0,7500])
    # show the plot
    plt.tight_layout()

# save the plot
plt.savefig("subplot.png", dpi=300)
