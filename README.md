# data-quality-assessor

Creating a comprehensive data quality assessment tool involves multiple components such as reading data, analyzing it for various quality aspects, and generating a report. Below, I provide a basic but complete version of such a tool, including reading data from a CSV file, assessing its quality, and generating a simple report.

```python
import pandas as pd
import numpy as np

def read_data(file_path):
    """
    Reads a CSV file and returns a DataFrame.

    Parameters:
    - file_path (str): The path to the CSV file.

    Returns:
    - DataFrame: Pandas DataFrame containing the data from the CSV file.

    Raises:
    - Exception: If there is an error reading the CSV file.
    """
    try:
        data = pd.read_csv(file_path)
        print(f"Data successfully read from {file_path}")
        return data
    except Exception as e:
        print(f"Error reading the data from {file_path}: {e}")
        raise

def assess_data_quality(data):
    """
    Assesses the data quality and returns a dictionary with metrics.

    Parameters:
    - data (DataFrame): The data to assess.

    Returns:
    - dict: A dictionary containing data quality metrics.
    """
    report = {}
    
    try:
        # Total rows and columns
        report['total_rows'] = data.shape[0]
        report['total_columns'] = data.shape[1]

        # Missing values
        report['missing_values'] = data.isnull().sum().sum()

        # Duplicate entries
        report['duplicate_rows'] = data.duplicated().sum()

        # Data types
        report['data_types'] = data.dtypes.to_dict()

        # Outliers based on IQR (Inter-Quartile Range method)
        report['outliers'] = {}
        for col in data.select_dtypes(include=[np.number]).columns:
            Q1 = data[col].quantile(0.25)
            Q3 = data[col].quantile(0.75)
            IQR = Q3 - Q1
            outliers = ((data[col] < (Q1 - 1.5 * IQR)) | (data[col] > (Q3 + 1.5 * IQR))).sum()
            report['outliers'][col] = outliers

    except Exception as e:
        print(f"Error assessing data quality: {e}")
        raise

    return report

def generate_report(quality_report, output_path):
    """
    Generates a simple text report from the data quality metrics.

    Parameters:
    - quality_report (dict): The data quality metrics.
    - output_path (str): The path where the report will be saved.

    Raises:
    - Exception: If there is an error writing the report.
    """
    try:
        with open(output_path, 'w') as report_file:
            report_file.write("Data Quality Report\n")
            report_file.write("===================\n\n")
            for key, value in quality_report.items():
                if isinstance(value, dict):
                    report_file.write(f"{key}:\n")
                    for sub_key, sub_value in value.items():
                        report_file.write(f"  {sub_key}: {sub_value}\n")
                else:
                    report_file.write(f"{key}: {value}\n")
                report_file.write("\n")
        
        print(f"Report successfully generated at {output_path}")
    
    except Exception as e:
        print(f"Error generating the report: {e}")
        raise

def main(file_path, output_path):
    """
    Main function to execute the data quality assessment.

    Parameters:
    - file_path (str): Path to the input CSV file.
    - output_path (str): Path to save the output report.
    """
    try:
        data = read_data(file_path)
        quality_report = assess_data_quality(data)
        generate_report(quality_report, output_path)
    except Exception as e:
        # General exception catch to ensure program does not crash unexpectedly
        print(f"An unexpected error occurred: {e}")

if __name__ == "__main__":
    # Example usage
    csv_file_path = 'data.csv'  # Specify your CSV file path
    report_file_path = 'report.txt'  # Specify where to save the report

    main(csv_file_path, report_file_path)
```

This program includes:

- **Reading CSV data**: The `read_data` function reads data into a Pandas DataFrame, handling errors if the file cannot be read.
- **Assessing data quality**: The `assess_data_quality` function collects basic metrics such as missing values, duplicate rows, data types, and detects outliers using the IQR method for numerical columns.
- **Generating a report**: The `generate_report` function writes these metrics into a simple text file.
- **Error handling**: Each function includes basic error handling to prevent the program from crashing and to provide informative messages in case of issues.
- **Flexibility to extend**: You can enhance the tool with additional data validation and cleaning functionalities.

Replace `'data.csv'` and `'report.txt'` with your actual CSV file path and desired output path for running this script in a real scenario.