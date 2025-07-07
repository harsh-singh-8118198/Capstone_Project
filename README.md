# Capstone_Project
Dynamic Pricing for Urban Parking Lots
This project simulates an intelligent, data-driven dynamic pricing engine for urban parking spaces. It aims to optimize parking lot utilization by adjusting prices in real-time based on various factors such as demand, competition, and environmental conditions. The system is designed to process streaming data, apply sophisticated pricing logic, and visualize the price changes dynamically.

Project Overview
The core objective is to build a dynamic pricing model for 14 parking spaces. The price updates are based on historical occupancy, queue length, nearby traffic, special events, vehicle type, and competitor prices. The system starts from a base price of $10 and ensures smooth, explainable price variations within defined bounds.

Tech Stack
Python: The primary programming language for all logic and models.

Pandas: Used for data manipulation, cleaning, and processing the dataset.

NumPy: Utilized for numerical operations, especially within the pricing models.

Bokeh: Employed for real-time, interactive data visualization within the Google Colab environment.

Pathway (Conceptual): Mentioned as a framework for real-time data ingestion and processing, although its full integration is outlined conceptually due to the scope of a single Colab notebook.

Architecture Diagram
graph TD
    A[dataset.csv] --> B(Data Simulation & Ingestion);
    B --> C{Column Standardization & Preprocessing};
    C --> D{Pricing Models};
    D -- Model 1: Baseline Linear --> E1(Price Calculation);
    D -- Model 2: Demand-Based --> E2(Price Calculation);
    D -- Model 3: Competitive Pricing --> E3(Price Calculation);
    E1 & E2 & E3 --> F[Real-time Price Output];
    F --> G[Bokeh Real-time Visualization];
    G -- Updates --> H[Google Colab Display];

Detailed Project Architecture and Workflow
The project's architecture is designed to simulate a real-time dynamic pricing system, processing data in sequential batches and updating visualizations accordingly.

Data Ingestion and Simulation
The dataset.csv file serves as the source of historical parking data.

The run_simulation function simulates real-time data streaming by reading the entire dataset and then iterating through it time-step by time-step (or by date and time point if available).

Each iteration represents a new "batch" of real-time data arriving for all parking spaces at a specific moment.

Column Standardization and Preprocessing
A robust column name handling mechanism is implemented to account for variations in CSV headers. It attempts to standardize column names like 'ID', 'SystemCodeNumber', 'LastUpdatedDate', 'LastUpdatedTime', 'VehicleType', 'TrafficConditionNearby', 'QueueLength', 'IsSpecialDay', 'Capacity', 'Occupancy', 'Latitude', and 'Longitude' to their expected counterparts in the code (e.g., 'Parking Space ID', 'Date', 'Time', 'Type of incoming vehicle', 'Nearby traffic congestion level', etc.).

It also handles the creation of a 'Time Point' column (a sequential integer for plotting) and a 'FullTimestamp' (combining 'Date' and 'Time' for accurate chronological sorting) if they are not explicitly present in the raw data.

Data types for 'Date' and 'Time Point' are converted to appropriate formats, with error handling for missing values.

Pricing Models
The system implements three distinct pricing models, increasing in complexity:

Model 1: Baseline Linear Model (model1_pricing_logic)

Logic: Price_t+1 = Price_t + α * (Occupancy / Capacity)

Description: This is a simple reactive model where the price increases linearly as the occupancy rate of the parking space goes up. It serves as a fundamental reference point.

State Management: It maintains a previous_price for each parking space to calculate the next price, simulating a continuous adjustment.

Model 2: Demand-Based Price Function (model2_pricing_logic)

Logic: Price = Base Price * (1 + λ * NormalizedDemand)

Demand Calculation (calculate_demand): A mathematical demand function is constructed using weighted features:

Occupancy Rate

Queue Length (normalized using tanh)

Nearby Traffic Congestion Level (normalized using tanh)

Special Day Indicator (converted to 0 or 1)

Vehicle Type Weight (different weights for 'car', 'bike', 'truck', 'cycle')

Normalization (normalize_demand): The calculated demand is normalized to a 0-1 range to ensure consistent scaling.

Description: This model provides a more intelligent pricing strategy by directly linking price adjustments to a calculated demand score, which considers multiple real-time factors.

Model 3: Competitive Pricing Model (model3_pricing_logic)

Logic: Builds upon Model 2 by incorporating competitive factors.

Proximity Calculation: Uses the Haversine formula (calculate_distance) to determine the geographical distance to nearby parking spaces based on their latitude and longitude.

Competitive Analysis: For each parking space, it identifies nearby competitors (within a 1km radius) and calculates their average predicted price.

Price Adjustment:

If the current lot is highly occupied (e.g., >90%) and nearby competitors are cheaper, the price is slightly reduced to encourage flow.

If nearby competitors are significantly more expensive, the current lot's price can be slightly increased while remaining attractive.

Description: This model introduces a real-world business dimension, allowing the pricing engine to react to the competitive landscape, aiming for optimal revenue and utilization in a multi-lot environment.

Real-time Visualization with Bokeh
Setup: Bokeh is configured to output interactive plots directly within the Google Colab notebook (output_notebook()).

Dynamic Plotting: A figure is created with ColumnDataSource instances for each parking space.

Streaming Updates: As each batch of simulated data is processed, the predicted_price for each parking space is appended (stream) to its respective ColumnDataSource.

Live Updates: push_notebook(handle=handle) ensures that the Bokeh plot updates in real-time within the Colab output, providing a live visualization of price changes for each parking space.

Interactivity: The Bokeh plot includes a legend with a click_policy="hide" feature, allowing users to toggle the visibility of individual parking space price lines.

Conceptual Pathway Integration
The code includes a conceptual section (run_pathway_app) that outlines how Pathway, a real-time data processing framework, would be integrated for a production-grade streaming application.

This section describes how Pathway's pw.io.csv.read could ingest streaming data, and how pw.map, pw.reduce, and stateful transforms (pw.state) would handle the feature engineering, model application, and state management (like previous_price) in a true streaming environment.

It acknowledges that direct integration of a full Pathway server with Bokeh within a single Colab notebook is complex due to execution model differences, hence the simulation approach.

How to Run
Google Colab: Open a new Google Colab notebook.

Upload dataset.csv: Upload your dataset.csv file to your Colab environment (e.g., to the /content/ directory).

Install Libraries: Run the !pip install pandas numpy bokeh pathway command in a code cell.

Copy Code: Copy the entire Python code from the dynamic-pricing-python-code Canvas document into a new code cell.

Select Model: In the if __name__ == "__main__": block at the end of the script, uncomment the model_to_run variable for the model you wish to simulate (1, 2, or 3). Model 3 is recommended for the most comprehensive features.

Execute: Run all the code cells. The Bokeh plot will appear below the code, updating dynamically as the simulation progresses.

Assumptions and Considerations
Dataset Structure: The code assumes the dataset.csv contains columns that can be mapped to: 'Parking Space ID', 'Date', 'Time', 'Latitude', 'Longitude', 'Capacity', 'Occupancy', 'Queue length', 'Nearby traffic congestion level', 'Special day indicator', and 'Type of incoming vehicle'. Robust renaming logic is included to handle common variations.

Demand Function: The demand function weights are empirically set. These would ideally be determined through statistical analysis or machine learning model training on historical data.

Competitive Radius: The 1km radius for competitive analysis in Model 3 is an adjustable parameter.

Price Bounds: The MIN_PRICE_FACTOR (0.5x base price) and MAX_PRICE_FACTOR (2.0x base price) ensure prices remain realistic and smooth.

Real-time Simulation: The time.sleep(0.05) introduces a small delay to simulate real-time data arrival for visualization purposes. This can be adjusted or removed for faster execution.

Pathway Integration: The Pathway section is conceptual. A full Pathway implementation would require a more dedicated setup and potentially a separate Bokeh server.
