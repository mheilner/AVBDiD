### Project Details
THis data is from the **Fatality Analysis Reporting System (FARS)**, which provides annual records of **all fatal motor vehicle crashes in the U.S.**.
The data is available at the **state level** and includes information on the **number of fatalities**, **weather conditions**, and **other factors** related to each accident.



## 📌 What This Code Does that I made

### 1️⃣ **Loads Traffic Accident Data (2016-2019)**
- Fars has lots of data for many years and lots of things in each year. I thought these were the main ones we would need.
- Reads in three key datasets for each year:
  - **`accident.csv`** → Main dataset containing accident details.
  - **`DrImpair.csv`** → Information on driver impairment (DUI-related).
  - **`weather.csv`** → Weather conditions during the accident.
- If any of these files are missing for a given year, the code **skips** them.

### 2️⃣ **Merges the Data for Each Year**
- **Joins** the three datasets using a **common accident ID (`ST_CASE`)**.
- Ensures all accident records are kept, even if impairment or weather data is missing.

### 3️⃣ **Cleans and Optimizes the Data**
- **Removes unnecessary columns** (e.g., duplicate names, descriptive text columns).
- **Renames columns** for consistency (`STATENAME_x` → `STATENAME`).
- Keeps only the most relevant information for analysis.






#### Important links
- [FARS Data](https://www.nhtsa.gov/file-downloads?p=nhtsa/downloads/FARS/)
- [FARS Data Manual](https://crashstats.nhtsa.dot.gov/Api/Public/ViewPublication/813556)


### Project Directory
- **Data**: Contains the FARS data files.
- **eda.ipynb**: Contains the py code I used to merge the data and play around a little with it
- **merged_fars_data.csv**: The merged data file we should use for our project

### **(1) Research Question**
**Does the 2019 increase in Utah’s allowable Alcohol by Volume (ABV) to 5% lead to an increase in traffic fatalities and crashes compared to other states?**  

---

### **(2) Why OLS Alone Would Be a Concern**
Using **Ordinary Least Squares (OLS) regression** without a causal identification strategy could lead to **biased estimates** because:
- **Omitted Variable Bias (OVB):** Other **unobserved factors** (e.g., road safety laws, economic trends, enforcement policies) may affect traffic fatalities.
- **Endogeneity:** States that changed ABV laws may have different pre-existing trends in alcohol consumption and traffic patterns compared to those that didn’t.
- **Simultaneity Bias:** Fatalities might influence alcohol laws rather than vice versa.

OLS alone would **not control for pre-existing trends** and **confounding variables**, making it difficult to establish a **causal relationship**.

---

### **(3) Strategy: Difference-in-Differences (DiD)**
To address these concerns, we will use a **Difference-in-Differences (DiD)** approach.  

**Why?**
- It allows us to compare **changes over time** in traffic fatalities in Utah relative to control states that did not change their ABV laws.
- This **removes confounding factors** that are constant over time (e.g., cultural attitudes toward drinking).
- It accounts for **pre-existing trends**, assuming that Utah and the control states followed parallel trends in traffic fatalities before the policy change.

---

### **(4) Data Description**
The data comes from the **Fatality Analysis Reporting System (FARS)**, which provides annual records of **all fatal motor vehicle crashes in the U.S.**.  

#### **Key Variables:**
- **Outcome (Y):** `FATALS` (Number of fatalities per accident)
- **Treatment Variable:** `UTAH` (1 if Utah, 0 for control states)
- **Post-Treatment Indicator:** `POST` (1 for years 2019+, 0 before)
- **Interaction Term (DiD):** `UTAH × POST`
- **Controls:** `WEATHER`, `HARM_EV`, `DRUNK_DR` (DUI-related crashes)

#### **Screenshot of Data Browser**
ADD THIS

---

### **(5) Level of Observation**
The **unit of observation** for our regression is **individual accident cases**, identified by `ST_CASE`, across multiple years (2016-2020).  

---

### **(6) Regression Equation and Code**
#### **Regression Equation (DiD Model)**
\[
FATALS_{ist} = \alpha + \beta_1 \cdot UTAH_s + \beta_2 \cdot POST_t + \delta \cdot (UTAH_s \times POST_t) + X_{ist} \Gamma + \varepsilon_{ist}
\]
where:
- \( s \) indexes **states**
- \( t \) indexes **years**
- \( i \) indexes **accidents**
- \( X_{ist} \) includes **control variables** (e.g., weather, drunk driving, road conditions)
- \( \delta \) is the **DiD coefficient**, measuring the effect of the ABV increase on fatalities.

#### **Stata Code**


