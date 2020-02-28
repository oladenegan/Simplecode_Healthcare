# Simplecode_Healthcare
Simple HealthCare
import csv
import operator
import json
## Sorting file based on timestamp
data = csv.reader(open('../../clean_data/5_25-5_26_ECG.csv', 'r'))
sortedlist = sorted(data, key=lambda row: row[0])
g = csv.writer(open("../../clean_data/5_25-5_26_ECG_sorted.csv", "w"))
g.writerow(['timestamp','ECG','Respiration Rate','SpO2','Pleth','Heart Rate','Airway','Non-invasive Blood Pressure','qos','alarms'])
for el in sortedlist:
	g.writerow(el)

## Removing duplicates and merging values from different rows with same timestamp
g = csv.writer(open("../../clean_data/5_25-5_26_ECG_sorted_unique.csv", "w"))	
data = csv.reader(open('../../clean_data/5_25-5_26_ECG_sorted.csv', 'r'))
next(data)
data_list = next(data)
time = data_list[0]	
alarm = data_list[-1]
merge_data = data_list[:]
data = csv.reader(open('../../clean_data/5_25-5_26_ECG_sorted.csv', 'r'))
next(data)
for row in data:
	if row[0] == time:
		row_list = row[1 : 6]		
		for i in range(len(row_list)):
			if row_list[i]:
				merge_data[i + 1] = row_list[i]
		for i in range(6, 8):
			row[i] = row[i].replace("'",'"').replace("None", "0")
			for key, value in json.loads(row[i]).items():
				merge_data_json = json.loads(merge_data[i].replace("'",'"').replace("None", "0"))
				if merge_data_json[key] is 0:
					merge_data_json[key] = value
					merge_data[i] = json.dumps(merge_data_json).replace('"',"'")
				elif value:
					merge_data_json[key] = value
					merge_data[i] = json.dumps(merge_data_json).replace('"',"'")
		if row[-1]:
			merge_data[-1] = row[-1]
	else:
		g.writerow(merge_data)
		merge_data = row[:]
		time = row[0]
		row_list = row[1 : 6]		
		for i in range(len(row_list)):
			if row_list[i]:
				merge_data[i + 1] = row_list[i]
		for i in range(6, 8):
			row[i] = row[i].replace("'",'"').replace("None", "0")
			for key, value in json.loads(row[i]).items():
				merge_data_json = json.loads(merge_data[i].replace("'",'"').replace("None", "0"))
				if merge_data_json[key] is 0:
					merge_data_json[key] = value
					merge_data[i] = json.dumps(merge_data_json).replace('"',"'")
				elif value:
					merge_data_json[key] = value
					merge_data[i] = json.dumps(merge_data_json).replace('"',"'")				
		if row[-1]:
			merge_data[-1] = row[-1]
g.writerow(merge_data)
g.writerow(['timestamp','ECG','Respiration Rate','SpO2','Pleth','Heart Rate','Airway','Non-invasive Blood Pressure','qos','alarms'])

data = csv.reader(open('../../clean_data/5_25-5_26_ECG_sorted_unique.csv', 'r'))
for row in data:
	# if row[2] or row[3] or row[5] or row[6] != "{'Respiration Rate': None, 'etCO2': None}" or row[7] != "{'mean': None, 'systolic': None, 'diastolic': None}":
	# 	if row[9]:
	# 		print("row['ECG'], row['Respiration Rate'], row['SpO2'], row['Heart Rate'], row['Airway'], row['Non-invasive Blood Pressure'] = ", row[2], row[3], row[5], row[6], row[7], row[9])
	# 	else:
	# 		print("YOOOO")
	if row[1] or row[4]:
		# print("alarms = ", row[9])
		if row[9]:
			print("row['ECG'], row['Respiration Rate'], row['SpO2'], row['Heart Rate'], row['Airway'], row['Non-invasive Blood Pressure'] = ", row['Respiration Rate'], row['SpO2'], row['Heart Rate'], row['Airway'], row['Non-invasive Blood Pressure'])
		if row[2] or row[3] or row[5] or row[6] != "{'Respiration Rate': None, 'etCO2': None}" or row[7] != "{'mean': None, 'systolic': None, 'diastolic': None}":
			print("row['ECG'], row['Respiration Rate'], row['SpO2'], row['Heart Rate'], row['Airway'], row['Non-invasive Blood Pressure'] = ", row['Respiration Rate'], row['SpO2'], row['Heart Rate'], row['Airway'], row['Non-invasive Blood Pressure'])


