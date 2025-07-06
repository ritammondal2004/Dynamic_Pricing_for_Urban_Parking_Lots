# **🚗 Dynamic Pricing for Urban Parking Lots**

#### Capstone Project of Summer Analytics 2025
#### hosted by Consulting & Analytics Club × Pathway

## 🌐 Project Overview and problem statement
Urban parking spaces are a limited and highly demanded resource. Prices that remain static
throughout the day can lead to inefficiencies — either overcrowding or underutilization.
To improve utilization, dynamic pricing based on demand, competition, and real-time
conditions is crucial. 

This project implements a real-time dynamic pricing system for urban parking lots based on real-time occupancy, traffic, queue length, and contextual features. It utilizes Pathway for real-time stream processing, Bokeh for interactive visualizations, and Python/Colab for feature engineering and simulation.

The primary objective is to optimize parking lot pricing to balance supply and demand, improve space utilization, and ensure fairness during special conditions like traffic surges, special events, or vehicle-specific needs.

get data sets here:
- [Raw dataset.csv](Data_sets/Raw_data/dataset.csv)
- [Parkin_stream.csv](Data_sets/parking_stream.csv)

---

### 🪜 Tech Stack
- Python — core programming and preprocessing
- Google Colab — Jupyter-based development
- Pathway — real-time streaming and window-based computation
- Bokeh + Panel — interactive visualizations
- Pandas — preprocessing of initial data
- Git + GitHub — version control and collaboration

### 📁 Project Structure
```
Repositary/
├── Data_sets/  
│   ├── Raw_data/
│   |   └── dataset.csv            # Raw data
│   ├── featured_data.csv       # Processed features
│   └── parking_stream.csv      # Final streamable data
├── Model_1_imgs/                 # Bokeh plots for Model 1
│   ├── bokeh_plot1.png
|   └── bokeh_plot1_model.png
├── Model2_imgs/                   # Bokeh plots for Model 2
│   ├── bokeh_plot_model2.png
|   └── other_daywise and system wise_imgs.png
├── Dynamic_Pricing_Model_1.ipynb   # OccRate + Volatility model
├── Dynamic_Pricing_Model_2.ipynb   # Full-feature demand model
└── README.md
```
### Architecture & Workflow
```mermaid
graph TD
    A[Raw CSV Data] --> B[Preprocessing: Pandas/Colab]
    B --> H[featured_data.csv]
    B --> C[parking_stream.csv]
    H --> C
    C --> D[Pathway Streaming Engine]
    D --> E[Feature Engineering in Real-Time]
    E --> F1[Model 1: OccRate + Volatility]
    E --> F2[Model 2: Demand Function w/ Contextual Features]
    F1 --> G1[Bokeh Line Plots1]
    F2 --> G2[Bokeh Line Plots2]
   
```
---

### 🔄 Real-Time Simulation Logic

- Data ingestion via `replay_csv()` simulates delayed streaming
- Feature engineering:
   - `Occ_rate = Occupancy / Capacity`
   - `Volatility = (occ_max - occ_min) / Capacity`
   - `Demand rate = (max_occ - min_occ) / time_span`
  
- **Model 1** uses basic features for price estimation [Model1 Notebook](Dynamic_Pricing_Model_1.ipynb)

<table>
  <tr>
    <td><img src="Model_1_imgs/bokeh_plot_model1.png" width="550"/></td>
    <td><img src="Model_1_imgs/bokeh_plot1.png" width="550"/></td>  
  </tr>
</table>

   ```price = base_price + α * Occ_rate + demand_rate```
  - **Where:**
    - `base_price = 10`
    - `Occ_rate = Occupancy / Capacity`
    - `demand_rate = (latest_occ - earliest_occ) / 35`  ⬅️
    - `α = 0.5` (weight for Occ_rate)
    - `price` was capped between ₹8 and ₹20 for stability.

---
- **Model 2** builds a linear demand function with weights for:  [Model2 Notebook](Dynamic_Pricing_Model_2.ipynb)

<table>
  <tr>
    <td><img src="Model2_imgs/bokeh_plot2_all.png" width="550"/></td>
    <td><img src="Model2_imgs/bokeh_plot2_oct29.png" width="550"/></td>
  </tr>
</table>

 check more [model2 day and systemwise plots](Model2_imgs)
                          
  - Queue Length
  - Traffic Level
  - Vehicle Type
  - Special Day
  - Demand Rate & Volatility
```
    demand_raw = (
          a * pw.this.occ_rate        # more full → more demand
        + b * pw.this.queue           # longer queue → more demand
        - c * traffic_norm            # traffic congestion reduces parking attractiveness
        + d * pw.this.special         # special event? raise demand
        + e * vehicle_weight          # heavier vehicle → more price willingness
        + f * demand_rate             # faster filling → higher urgency
        + g * volatility              # rapid fluctuation → higher scarcity signal
        
    )
```







                 
