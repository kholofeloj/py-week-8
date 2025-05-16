# 📊 COVID-19 Data Analysis & Visualization using Python

This notebook explores and visualizes COVID-19 data across selected countries: **South Africa**, **Botswana**, **Kenya**, and **Zambia**. It includes cleaning, transformation, and a variety of visual insights from the dataset.

---

## 📦 1. Data Cleaning & Preparation

```python
import pandas as pd

# Load data
df = pd.read_csv('owid-covid-data.csv')

# Convert date column to datetime
df['date'] = pd.to_datetime(df['date'], errors='coerce')

# Drop rows with missing dates or critical columns
df = df.dropna(subset=['date', 'total_cases', 'total_deaths'])

# Filter for selected countries
countries = ['Kenya', 'USA', 'India']
df = df[df['location'].isin(countries)]

# Sort data
df = df.sort_values(['location', 'date'])

# Handle missing numeric values
df['total_cases'] = df['total_cases'].interpolate()
df['total_deaths'] = df['total_deaths'].interpolate()

```

---

## 📈 2. Cases and Deaths Over Time (Line Charts)

### ✅ Total Cases Over Time

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(10, 6))
for country in countries:
    country_data = df[df['location'] == country]
    plt.plot(country_data['date'], country_data['total_cases'], label=country)

plt.title('Total COVID-19 Cases Over Time')
plt.xlabel('Date')
plt.ylabel('Total Cases')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()
```

### ✅ Total Deaths Over Time

```python
plt.figure(figsize=(10, 6))
for country in countries:
    country_data = df[df['location'] == country]
    plt.plot(country_data['date'], country_data['total_deaths'], label=country)

plt.title('Total COVID-19 Deaths Over Time')
plt.xlabel('Date')
plt.ylabel('Total Deaths')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()
```

---

## 📊 3. Top Countries by Total Cases (Bar Chart)

```python
latest_date = df['date'].max()
latest_data = df[df['date'] == latest_date]

top_cases = latest_data.groupby('location')['total_cases'].max().sort_values(ascending=False).head(10)

top_cases.plot(kind='bar', figsize=(10, 6), color='orange')
plt.title('Top 10 Countries by Total COVID-19 Cases')
plt.xlabel('Country')
plt.ylabel('Total Cases')
plt.xticks(rotation=45)
plt.grid(axis='y')
plt.tight_layout()
plt.show()
```

---

## 📉 4. Daily New Cases Comparison

```python
df['new_cases'] = df.groupby('location')['total_cases'].diff()

plt.figure(figsize=(10, 6))
for country in countries:
    country_data = df[df['location'] == country]
    plt.plot(country_data['date'], country_data['new_cases'], label=country)

plt.title('Daily New COVID-19 Cases')
plt.xlabel('Date')
plt.ylabel('New Cases')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()
```

---

## 💀 5. Death Rate Over Time

```python
df['death_rate'] = df['total_deaths'] / df['total_cases']
df['death_rate'] = df['death_rate'].fillna(0).replace([float('inf'), -float('inf')], 0)

plt.figure(figsize=(10, 6))
for country in countries:
    country_data = df[df['location'] == country]
    plt.plot(country_data['date'], country_data['death_rate'], label=country)

plt.title('COVID-19 Death Rate Over Time')
plt.xlabel('Date')
plt.ylabel('Death Rate')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()
```

---

## 💉 6. Visualizing Vaccination Progress

### ✅ Cumulative Vaccinations Over Time

```python
plt.figure(figsize=(10, 6))
for country in countries:
    country_data = df[df['location'] == country]
    plt.plot(country_data['date'], country_data['total_vaccinations'], label=country)

plt.title('Cumulative COVID-19 Vaccinations Over Time')
plt.xlabel('Date')
plt.ylabel('Total Vaccinations')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()
```

### ✅ Percentage of Population Vaccinated

```python
latest_data['%_vaccinated'] = (latest_data['people_vaccinated'] / latest_data['population']) * 100
vaccinated_data = latest_data[latest_data['location'].isin(countries)]

plt.figure(figsize=(8, 5))
plt.bar(vaccinated_data['location'], vaccinated_data['%_vaccinated'], color='green')
plt.title('Percentage of Population Vaccinated')
plt.ylabel('% Vaccinated')
plt.xlabel('Country')
plt.ylim(0, 100)
plt.tight_layout()
plt.show()
```

### 🥧 Optional: Pie Chart – Vaccinated vs. Unvaccinated

```python
kenya_data = vaccinated_data[vaccinated_data['location'] == 'Kenya'].iloc[0]
vaccinated = kenya_data['people_vaccinated']
unvaccinated = kenya_data['population'] - vaccinated

labels = ['Vaccinated', 'Unvaccinated']
sizes = [vaccinated, unvaccinated]

plt.figure(figsize=(6, 6))
plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=['green', 'red'], startangle=140)
plt.title('Vaccinated vs Unvaccinated - Kenya')
plt.axis('equal')
plt.show()
```

---

## 🔥 7. Optional: Heatmap of Correlations

```python
import seaborn as sns

corr = df[['total_cases', 'total_deaths', 'new_cases', 'new_deaths']].corr()

plt.figure(figsize=(8, 6))
sns.heatmap(corr, annot=True, cmap='coolwarm', fmt=".2f")
plt.title('Correlation Heatmap')
plt.tight_layout()
plt.show()
```

---

## 📌 Conclusion

This analysis helps track and compare the progression of COVID-19 and the vaccine rollout across key countries. You can extend this to more countries, add moving averages, or even build a dashboard with Plotly or Dash for interactive visualizations.



## Author

Kholofelo Mocheko