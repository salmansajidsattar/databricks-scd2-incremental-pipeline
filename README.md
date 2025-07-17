# Databricks Data Pipeline Project

## Overview
This project implements an end-to-end data pipeline using Databricks with incremental streaming architecture. The pipeline follows a medallion architecture pattern with four distinct volumes for data processing and transformation.

## Architecture

### Data Volumes Structure
The project utilizes four Databricks volumes organized as follows:

- **Raw Volume**: Initial data ingestion point
- **Bronze Volume**: Raw data storage after auto-loading
- **Silver Volume**: Transformed data with SCD Type 2 implementation
- **Gold Volume**: Final analytical data (dimension and fact tables)

## Data Flow

### 1. Raw to Bronze Layer
- **Auto Loader Configuration**: Set up on the raw folder for automatic file detection
- **Incremental Streaming**: New files trigger automatic processing
- **Data Ingestion**: Files are automatically loaded and saved to the bronze volume

### 2. Bronze to Silver Layer
- **Delta Live Tables (DLT)**: Implemented for data transformation
- **SCD Type 2**: Slowly Changing Dimensions Type 2 implementation for historical data tracking
- **Data Transformation**: Raw data is cleaned, validated, and transformed
- **Storage**: Processed data is saved in the silver volume

### 3. Silver to Gold Layer
- **Dynamic Scripts**: Two separate dynamic transformation scripts
  - **Dimension Script**: Processes dimension data
  - **Fact Script**: Processes fact data
- **Final Output**: Results saved in gold volume as analytical-ready tables

## Key Features

### Incremental Streaming
- Real-time data processing using Databricks Auto Loader
- Automatic trigger mechanism for new file arrivals
- Efficient handling of streaming data workloads

### Delta Live Tables
- Declarative data pipeline framework
- Built-in data quality checks
- Automatic dependency management
- Real-time monitoring and alerting

### SCD Type 2 Implementation
- Historical data preservation
- Tracks changes over time with effective dates
- Maintains data lineage and audit trail

### Dynamic Processing
- Flexible dimension and fact table generation
- Scalable architecture for multiple data sources
- Automated data modeling

## Volume Details

| Volume | Purpose | Technology | Data Type |
|--------|---------|------------|-----------|
| Raw | Data Ingestion | Auto Loader | Raw Files |
| Bronze | Initial Storage | Delta Tables | Raw Data |
| Silver | Transformation | Delta Live Tables | Transformed Data |
| Gold | Analytics | Dynamic Scripts | Dimension/Fact Tables |

## Technologies Used

- **Databricks**: Primary platform for data processing
- **Delta Lake**: Storage format for ACID transactions
- **Auto Loader**: Incremental file processing
- **Delta Live Tables**: ETL pipeline framework
- **Apache Spark**: Distributed computing engine

## Benefits

- **Scalability**: Handles large volumes of streaming data
- **Reliability**: Built-in error handling and data quality checks
- **Efficiency**: Incremental processing reduces computational overhead
- **Flexibility**: Dynamic scripts adapt to changing data requirements
- **Observability**: Real-time monitoring and pipeline visibility

## Getting Started

### Prerequisites
- Databricks workspace with appropriate permissions
- Unity Catalog enabled (recommended)
- Compute cluster with Delta Live Tables runtime
- Storage account configured for volumes

### Setup Instructions

1. **Create Databricks Volumes**
   ```sql
   CREATE VOLUME IF NOT EXISTS raw;
   CREATE VOLUME IF NOT EXISTS bronze;
   CREATE VOLUME IF NOT EXISTS silver;
   CREATE VOLUME IF NOT EXISTS gold;
   ```

2. **Configure Auto Loader**
   - Set up Auto Loader on raw volume
   - Configure file format and schema inference
   - Set trigger intervals and checkpointing

3. **Deploy Delta Live Tables**
   - Create DLT pipeline configuration
   - Define transformation logic with SCD Type 2
   - Set up data quality expectations

4. **Implement Dynamic Scripts**
   - Deploy dimension processing script
   - Deploy fact table processing script
   - Configure scheduling and dependencies

5. **Monitor and Validate**
   - Verify data flow through all layers
   - Test incremental processing
   - Validate SCD Type 2 implementation

## Configuration

### Auto Loader Configuration
```python
# Example Auto Loader setup
df = spark.readStream \
    .format("cloudFiles") \
    .option("cloudFiles.format", "json") \
    .option("cloudFiles.schemaLocation", "/path/to/schema") \
    .load("/path/to/raw/volume")
```

### Delta Live Tables Example
```python
# Bronze to Silver transformation
@dlt.table(
    comment="Silver layer with SCD Type 2"
)
def silver_table():
    return (
        dlt.read_stream("bronze_table")
        .select("*")
        .withColumn("effective_date", current_timestamp())
        .withColumn("end_date", lit(None))
        .withColumn("is_current", lit(True))
    )
```

## Pipeline Architecture Diagram

```
┌─────────────┐    Auto Loader    ┌─────────────┐
│    Raw      │ ──────────────────→│   Bronze    │
│   Volume    │                   │   Volume    │
└─────────────┘                   └─────────────┘
                                         │
                                         │ Delta Live Tables
                                         │ (SCD Type 2)
                                         ▼
┌─────────────┐                   ┌─────────────┐
│    Gold     │                   │   Silver    │
│   Volume    │                   │   Volume    │
│             │                   │             │
├─────────────┤                   └─────────────┘
│ Dimension   │                          │
│   Tables    │                          │
├─────────────┤    Dynamic Scripts       │
│    Fact     │ ◄────────────────────────┘
│   Tables    │
└─────────────┘
```

## File Structure

```
## Data Quality & Validation

### SCD Type 2 Validation
- Verify historical records preservation
- Check effective and end date consistency
- Validate current record flags

### Data Quality Checks
- Schema validation
- Null value constraints
- Business rule validation
- Data freshness monitoring

## Troubleshooting

### Common Issues
1. **Auto Loader not triggering**
   - Check file permissions
   - Verify checkpoint locations
   - Review trigger configurations

2. **DLT Pipeline failures**
   - Check data quality expectations
   - Review transformation logic
   - Verify dependencies

3. **Performance issues**
   - Optimize file sizes
   - Adjust cluster configuration
   - Review partitioning strategy
## Screenshots
![screenshots](https://github.com/salmansajidsattar/databricks-scd2-incremental-pipeline/blob/main/Screenshort/DLT-1.png)
![screenshots](https://github.com/salmansajidsattar/databricks-scd2-incremental-pipeline/blob/main/Screenshort/DLT-2.png)
![screenshots](https://github.com/salmansajidsattar/databricks-scd2-incremental-pipeline/blob/main/Screenshort/DLT-3.png)
