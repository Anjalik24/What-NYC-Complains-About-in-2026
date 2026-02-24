# What-NYC-Complains-About-in-2026
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Load dataset
df = pd.read_csv("311_2026.csv", low_memory=False)

print("Dataset Shape:", df.shape)
df.head()
# Convert to datetime
df['created_date'] = pd.to_datetime(df['created_date'], errors='coerce')
df['closed_date'] = pd.to_datetime(df['closed_date'], errors='coerce')

# Ensure only 2026 data (extra safety check)
df = df[df['created_date'].dt.year == 2026]

print("Filtered 2026 Data Shape:", df.shape)
print(df.columns.tolist())
df = pd.read_csv("311_2026.csv", low_memory=False)

# Clean column names
df.columns = df.columns.str.strip()          # Remove leading/trailing spaces
df.columns = df.columns.str.lower()          # Make lowercase
df.columns = df.columns.str.replace(" ", "_")  # Replace spaces with underscores

print(df.columns.tolist())
df['created_date'] = pd.to_datetime(df['created_date'], errors='coerce')
df['closed_date'] = pd.to_datetime(df['closed_date'], errors='coerce')
top5_problems = df['problem_(formerly_complaint_type)'].value_counts().head(5)
print(top5_problems)

# Visualization
plt.figure()
top5_problems.plot(kind='bar')
plt.title("Top 5 Complaint Types - 2026")
plt.xlabel("Complaint Type")
plt.ylabel("Number of Complaints")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
top10_details = df['problem_detail_(formerly_descriptor)'].value_counts().head(10)
print(top10_details)

plt.figure()
top10_details.plot(kind='bar')
plt.title("Top 10 Complaint Details - 2026")
plt.xticks(rotation=60)
plt.tight_layout()
plt.show()
# Remove 'Unspecified' borough
df = df[df['borough'].str.lower() != 'unspecified']
borough_counts = df['borough'].value_counts()
print(borough_counts)

plt.figure()
borough_counts.plot(kind='bar')
plt.title("Complaints by Borough - 2026")
plt.xlabel("Borough")
plt.ylabel("Number of Complaints")
plt.tight_layout()
plt.show()
# Resolution Time in hours
df['resolution_time_hours'] = (df['closed_date'] - df['created_date']).dt.total_seconds() / 3600

avg_resolution_time = df['resolution_time_hours'].mean()
print("Average Resolution Time (hours):", round(avg_resolution_time, 2))
unresolved = df[df['status'] != 'Closed']
print("Number of Unresolved Complaints:", unresolved.shape[0])
daily_counts = df.groupby(df['created_date'].dt.date).size()

max_date = daily_counts.idxmax()
max_count = daily_counts.max()

day_name = pd.to_datetime(max_date).day_name()

print("Highest Complaint Date:", max_date)
print("Number of Complaints:", max_count)
print("Day of Week:", day_name)
top5_days = daily_counts.sort_values(ascending=False).head(5)
print(top5_days)
agency_counts = df['agency_name'].value_counts().head(10)
agency_counts = agency_counts.sort_values()

plt.figure(figsize=(13, 6))
bars = plt.barh(agency_counts.index, agency_counts.values)

plt.title("Top 10 Agencies by Complaints (2026)")
plt.xlabel("Number of Complaints")

# Add value labels
for i, v in enumerate(agency_counts.values):
    plt.text(v, i, f" {v:,}", va='center')

plt.tight_layout()
plt.show()
animal_keywords = ['dog', 'raccoon', 'animal', 'poop', 'pet', 'rat', 'Horse', 'dogs','rats', 'animals','cat', 'cats']

animal_complaints = df[
    df['problem_(formerly_complaint_type)'].str.contains('|'.join(animal_keywords), case=False, na=False) |
    df['problem_detail_(formerly_descriptor)'].str.contains('|'.join(animal_keywords), case=False, na=False)
]

print("Total Animal Related Complaints:", animal_complaints.shape[0])

animal_summary = animal_complaints['problem_(formerly_complaint_type)'].value_counts()
print(animal_summary)
# Define keywords
dog_poop_keywords = ['dog poop', 'dog waste', 'dog feces','dog','dog potty', 'dog dumps']

dog_poop_df = df[
    df['problem_(formerly_complaint_type)'].str.contains('|'.join(dog_poop_keywords), case=False, na=False) |
    df['problem_detail_(formerly_descriptor)'].str.contains('|'.join(dog_poop_keywords), case=False, na=False)
]

print("Total Dog Poop Complaints in 2026:", dog_poop_df.shape[0])
borough_dog_poop = dog_poop_df['borough'].value_counts()

print(borough_dog_poop)
highest_borough = borough_dog_poop.idxmax()
lowest_borough = borough_dog_poop.idxmin()

print("Borough with MOST dog poop complaints:", highest_borough)
print("Borough with LEAST dog poop complaints:", lowest_borough)
plt.figure(figsize=(8, 5))
borough_dog_poop.sort_values().plot(kind='barh')

plt.title("Dog Poop Complaints by Borough (2026)")
plt.xlabel("Number of Complaints")
plt.ylabel("Borough")

plt.tight_layout()
plt.show()
