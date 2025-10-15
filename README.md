# NYC Taxi Yellow Project
## 1. Introduction:
This project helps tracking the Taxi System through Power BI dashboard. The data will be updated regularly using my designed pipeline in Microsoft Fabric, followed by SQL script to create some data tables to record logs and create procedures.

## 2. Preview Dashboard: 
These are the screenshots of my dashboard. My full working steps and report can be viewed here: [Report Link](https://github.com/minhD03/NYCTaxi_Yellow-Project/blob/cf2e4579d42d79dbeb3748a0bd6376449f71965e/NYCTaxi%20-%20Nhat%20Minh%20Dang.pdf)

![alt text](https://github.com/minhD03/NYCTaxi_Yellow-Project/blob/cf2e4579d42d79dbeb3748a0bd6376449f71965e/Images/Dashboard%201.png)
![alt text](https://github.com/minhD03/NYCTaxi_Yellow-Project/blob/a202044c0b1a00f632828f4454ae10cccc486d5c/Images/Dashboard%202.png)

## 3. About Dataset:

The datasets contains yellow trip records in 2024, which include pickup and drop-off
dates/times, pickup and drop-off locations, trip distances, itemized fares, rate types, payment
types and driver-reported passenger counts. The dataset was collected and provided to NYC
Taxi and Limousine Commission (TLC) by technology providers authorized under the Taxicab
& Livery Passenger Enhancement Programs (TPEP/LPEP). The data contains parquet files of
yellow trip recorded within each month followed by a taxi zone lookup table. Furthermore,
there was also a taxi zone lookup file that helps explaining the location id with borough, zone
and service zone. The datasource can be accessed [Here](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page) or download in my [GitHub Repository](https://github.com/minhD03/NYCTaxi_Yellow-Project/tree/cf2e4579d42d79dbeb3748a0bd6376449f71965e/Data)

Below is the description about columns in dataset:

![alt text](https://github.com/minhD03/NYCTaxi_Yellow-Project/blob/fa478965d9e2c18bf68c236202b911107396f464/Images/About%20Dataset.png)

## 4. Tool used: 
### a) Microsoft Fabric:

Microsoft Fabric is an end-to-end analytics platform that unifies data engineering, data
science, real-time analytics and business intelligence under one roof. It integrates services like
Power BI, Data Factory and Synapse into a single SaaS experience, enabling seamless data
movement, transformation and visualization. Designed for scalability and collaboration, Fabric
empowers organizations to build robust data pipelines, model complex datasets and deliver
impactful insights—all without switching between fragmented tools.
When it comes to processing Parquet files, Microsoft Fabric supports them across various
components, especially within Data Factory pipelines and Dataflow Gen2. You can configure
Parquet as both a source and destination format in copy activities, with options to set
compression types (e.g., gzip, snappy, Brotli), control file naming patterns and optimize write
performance using V-Order. In Dataflows, Fabric uses Power Query connectors to ingest
Parquet files, allowing for transformation and enrichment before loading into Lakehouses or
other destinations. This makes it ideal for handling large-scale, columnar data efficiently across
your analytics workflows.

### b) Power BI:

Power BI is a powerful business analytics tool developed by Microsoft that enables users to visualize data, uncover insights and make informed decisions. It allows seamless integration with various data sources—ranging from Excel spreadsheets to cloud-based databases—and transforms raw data into interactive dashboards and reports. With features like real-time data updates, natural language queries and customizable visuals, Power BI empowers organizations to monitor key performance indicators (KPIs), identify trends and share insights across teams. Its intuitive interface and robust analytical capabilities make it a go-to solution for both technical analysts and business stakeholders seeking data-driven clarity. Therefore, this tool is suitable for live tracking taxi trips records.

## 5. Pipeline creations:
These are the main pipelines that I used for continuously importing data based on scheduled:
![alt text](https://github.com/minhD03/NYCTaxi_Yellow-Project/blob/df5b27197e483a282b51d650ed4c8005183f2971/Images/pl_stage_processing_nyctaxi.png)

The picture above demonstrates the steps to load data from the Lakehouse. The process
include five steps from getting the latest processed date to load staging metadata. In the first two steps, the latest processing entry based on timestamp for table staging
nyctaxi was extracted, followed by adding one month to that date and converting into full
string. Once the start date was defined and stored as v_date, the next step will start copying the
files from nyctaxi_yellow using the prefix “yellow_tripdata_” combining with increased month
and end with “.parquet” to a table called “stg.nyctaxi_yellow”. Then, the end date will be set
at the start of the next month to help the next procedure removing the outlier date between start
and end date. Finally, the pipeline run id, processing data and table name were recorded for
later monitors.

![alt text](https://github.com/minhD03/NYCTaxi_Yellow-Project/blob/df5b27197e483a282b51d650ed4c8005183f2971/Images/pl_presentation_processing_nyctaxi.png)

Before loading the nyc taxi data for presentation, another log was recorded include run
id, process data and table name.

## 6. SQL scripts:
The SQL code will be attached along with this report. In summary, these are the steps
that I have made:
- Retrieve the earliest and latest pickup date from the staging dataset for checking.
- Defined a store procedure to remove outlier records outside valid data range using
start and end date and then check again as above.
- Create a metadata schema to store table processing log that records the pipeline run
id, table processed, row processed, latest process date time in data and process
datetime.
- Generate a manual snapshot for testing.
- Insert metadata staging for logging.
- Drop and recreated if have a nyctaxi_yellow table.
- Transform staging data into presentation format, including mapping vendor id to
names, format dates, joins with zone lookup table and translate payment type.
- Insert presentation metadata.
- View all processing log.

## 7. Insights gained:

### A. 🚕 NYC Yellow Taxi – Citywide Overview
- **Scale & Reach**: 44M trips, 53M passengers, $1.23B profit.
- **Efficiency**: $23.01 avg fare, 4.89 miles/trip, $5.68 revenue/mile.
- 💡 *Insight*: Stable profit despite seasonal distance dips suggests optimized pricing and routing.
- 🛠 *Recommendations*:
  - Implement dynamic pricing tied to demand.
  - Incentivize digital payments for speed and data capture.
  - Use predictive analytics for fleet deployment.

---

#### 1) 🚖 Myle Technologies Inc. – Summary

##### 📊 Key Metrics
- **Performance**: 2,329 trips, $98.64K profit.
- **Efficiency**: $4.09/mile — below benchmark ($5.68), 10.35 miles/trip.
  
💡 *Insight*: Focus on longer-haul routes; pricing may need refinement.
- 🛠 *Recommendations*:
  - Improve ETL validation to restore passenger metrics.
  - Identify profitable corridors for better fleet allocation.
  - Test dynamic pricing for long-distance trips.
  - Compare with peers to uncover pricing gaps.
  - Add fare and occupancy metrics to enhance reporting.
 
#### a. 🚖 Creative Mobile Technologies
- **Performance**: 10M trips, $282.08M profit, 11M passengers.
- **Efficiency**: $25.03 fare, 3.45 miles/trip, $7.82 revenue/mile.
- 💡 *Insight*: High fare efficiency via short, dense-zone trips (e.g., Midtown).
- 🛠 *Recommendations*:
  - Expand into adjacent high-yield zones.
  - Promote shared rides to boost occupancy.
  - Maintain ETL integrity for benchmarking advantage.

---

#### b. 🚖 Curb Mobility
- **Market Leader**: 34M trips, $943.13M profit, 42M passengers.
- **Efficiency**: $22.47 fare, 5.33 miles/trip, $5.25 revenue/mile, 1.24 occupancy.
- 💡 *Insight*: Dominates in scale with balanced pricing and broad coverage.
- 🛠 *Recommendations*:
  - Micro-segment Manhattan for premium fare zones.
  - Launch loyalty programs leveraging digital payment adoption.
  - Blend scale with Creative’s fare efficiency for hybrid optimization.

---

#### c. 🚖 Helix (Emerging Vendor)
- **Pilot Phase**: 230 trips, $4.97K profit, 310 passengers (Dec 2024 only).
- **Efficiency**: $16.03 fare, 1.82 miles/trip, $11.85 revenue/mile, 1.35 occupancy.
- 💡 *Insight*: Strong monetization of short trips in Yorkville West.
- 🛠 *Recommendations*:
  - Expand coverage and capture full-year data.
  - Promote digital payments to streamline ops.
  - Use fare modeling and time-of-day overlays to refine deployment.

### B. Region and Payment:

#### 1) 🚖 Vendor Market Share
- **Curb Mobility, LLC**:
  - 67.37% of total profit.
  - 68.26% of total trip distance.
  - 67.37% of total passenger count.
  - ✅ Dominant scale and strategic coverage across urban and long-haul zones.

- **Myle Technologies & Creative Mobile Technologies**:
  - Each hold ~15–17% across key metrics.
  - 📌 Niche or regionally concentrated service models.

#### 🗺️ Top-Earning Zones
- **Times Sq/Theatre District**: $12.23M — highest revenue zone.
- **Other high-yield zones**: Upper East Side South, Midtown Center.
- **Airport Corridors**: JFK and LaGuardia remain vital for premium-fare trips.

---

#### 📈 Regional Optimization Recommendations
- **Dynamic Fleet Allocation**:
  - Balance short-haul density with long-haul airport routes.
  - 🛠 Use zone-level profitability mapping for deployment.

- **Vendor Expansion Strategy**:
  - Myle should target high-yield zones like Times Square and Midtown.
  - 💡 Unlock higher trip frequency and fare efficiency.

---

#### 2) 💳 Payment Behavior Summary

##### 💰 Market-Wide Trends
- **Credit Cards**: $1.04B (~85% of total revenue)
  - ✅ Reflects rider convenience and cashless mobility push.

- **Cash Payments**: $140M — declining share
  - 📉 Indicates shift toward digital transactions.

- **Minor Categories**: Dispute, no charge, voided trips (~$1.4M each)
  - ⚠️ Placeholder values suggest ETL or classification issues.

---

##### 📈 Payment Optimization Recommendations
- **Digital Infrastructure**:
  - Promote mobile payments, loyalty apps and streamline dispute handling.

- **Behavioral Segmentation**:
  - Analyze payment trends by zone, time, and rider profile.
  - 💡 Tailor promotions and service tiers accordingly.

---

#### 3) 🧠 Vendor-Specific Regional & Payment Insights

#### a. 🚖 Myle Technologies
- **Top Zone**: East New York — underserved, predictable demand.
- **Borough Revenue**:
  - Queens: $34.5K, Manhattan: $27.9K, Brooklyn: $24.2K, Bronx: $9K
- **Payment**: 100% via “Flex fare trip” — non-standard category
  - ⚠️ Limits fare analysis and segmentation
- ❌ Missing passenger count data
- 🛠 Recommendations:
  - Audit fare tagging and introduce granular fare types.
  - Expand to Brownsville, Jamaica.
  - Benchmark against peers and integrate passenger metrics.

#### b) 🚖 Creative Mobile Technologies
- **Top Zone**: Upper East Side North — affluent, short-trip density.
- **Manhattan Revenue**: $214M+ (76% of total).
- **Payment**:
  - Cash: 72.95% ($231.5M), Credit: 27.05% ($85.2M).
  - ⚠️ Skewed toward cash; potential ETL issues in minor categories.
- 🛠 Recommendations:
  - Accelerate digital payment adoption.
  - Expand to Midtown East, Lenox Hill.
  - Apply fare elasticity and time-of-day overlays.

#### c) 🚖 Curb Mobility:
- **Top Zone**: Times Sq/Theatre District — high turnover, premium fares.
- **Manhattan Trips**: 6,023; balanced presence across boroughs.
- **Payment**:
  - Credit: 98.41% ($732.5M), Cash: 1.59% ($108.6M)
  - ✅ Strong digital integration.
- 🛠 Recommendations:
  - Micro-segment Manhattan for pricing tiers.
  - Launch loyalty programs and predictive analytics.
  - Audit minor payment categories for accuracy.

#### d) 🚖 Helix
- **Top Zone**: Yorkville West — stable residential demand.
- **Manhattan Revenue**: $4.5K+ (90%+ of total).
- **Payment Discrepancy**:
  - Zone-level: 100% cash; Overall: Credit leads ($1.57K).
  - ⚠️ Indicates localized behavior or classification issues.
- 🛠 Recommendations:
  - Expand to Lenox Hill, Carnegie Hill.
  - Promote digital payments and reconcile payment data.
  - Apply zone-level profitability and time-of-day modeling.
  - Integrate ride-level metadata for service refinement.
