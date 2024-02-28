# ssas-documentation-python-example
example of py script 

import adodbapi
import pandas as pd

def run_ssas_query(server, database, query):
    # Connection string with ADOMD.NET provider
    conn_str = f"Provider=MSOLAP;Data Source={server};Initial Catalog={database};"

    # Establishing the connection
    conn = adodbapi.connect(conn_str)

    try:
        # Creating a cursor
        cursor = conn.cursor()

        # Executing the query
        cursor.execute(query)

        # Fetching the results
        result = cursor.fetchall()

        # Get the column names from the cursor description
        column_names = [column[0] for column in cursor.description]

        # Convert the result to a DataFrame
        result_df = pd.DataFrame(result, columns=column_names)

        return result_df

    finally:
        # Closing the connection
        conn.close()

def save_results_to_csv(result_dfs, csv_file_path):
    # Concatenate all individual DataFrames into a final DataFrame
    final_result_df = pd.concat(result_dfs, ignore_index=True)

    # Save the final DataFrame to a CSV file
    final_result_df.to_excel(csv_file_path, index=False)

def run_queries_for_databases(ssas_server, databases, mdx_query):
    # Create an empty list to store individual DataFrames
    result_dfs = []

    # Iterate through each database
    for database in databases:
        # Running the SSAS query
        result_df = run_ssas_query(ssas_server, database, mdx_query)

        # Add a 'Database' column to the DataFrame
        result_df['Database'] = database

        # Append the DataFrame to the list
        result_dfs.append(result_df)

    # Specify the path to save the CSV file on your desktop
    csv_file_path = r"C:\\Users\\m.safiri\\Desktop\\Data Sources.xlsx"

    # Save the results to a single CSV file
    save_results_to_csv(result_dfs, csv_file_path)

if __name__ == "__main__":
    # Replace 'bitabular\\tabular' with your actual SSAS server
    ssas_server = "bitabular\\tabular"

    # List of databases to query
    databases = [
   # list of databases

        # Add all other database names here
    ]

    # Example MDX query (replace with your actual query)
    mdx_query = """
SELECT [ID], [ModelID], [Name], [Description], [Type], [ConnectionString], [ImpersonationMode], [Account], [ModifiedTime] from $SYSTEM.TMSCHEMA_DATA_SOURCES  """

    # Run queries for each database
    run_queries_for_databases(ssas_server, databases, mdx_query)

