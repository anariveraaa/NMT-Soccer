# NMT-Soccer
import os
import pandas as pd

# Rearrange Name/Group x-axis
# TRIAL 1,2,3
# NO MAX, MEAN, SD. CoV, SEM
# NEED PATIENT ID
# NEED NAME
# NEED GROUP
# NEED CONCENTRIC
# NEED PERFORMANCE

# Define the root directory where your subfolders are stored
root_dir = '/Users/anarivera/Desktop/FORCE DECKS'
output_file = '/Users/anarivera/Desktop/consolidated_participant_data.xlsx'

# Initialize an empty list to store dataframes
all_data = []

# Function to extract the numeric part from folder names
def extract_numeric(folder_name):
    try:
        return int(folder_name.split('-')[-1])
    except ValueError:
        return float('inf')  # If there's no valid number, send to the end of sorting

# Filter and sort the folders by the numeric part, ignoring non-participant folders like '.DS_Store'
folders = sorted([folder for folder in os.listdir(root_dir) if folder.startswith("SOCCER-")], key=extract_numeric)

# Print initial feedback
print(f"Looking for Excel files in: {root_dir}")

# Loop through each folder in the root directory
for folder in folders:
    folder_path = os.path.join(root_dir, folder)
    
    if os.path.isdir(folder_path):  # Check if it's a directory (participant folder)
        print(f"Processing folder: {folder}")
        
        # Loop through each Excel file in the participant folder
        for file in os.listdir(folder_path):
            if file.endswith('.xlsx'):  # Ensure it is an Excel file
                file_path = os.path.join(folder_path, file)
                
                print(f"Reading file: {file_path}")
                
                # Read the Excel file
                try:
                    df = pd.read_excel(file_path)
                except Exception as e:
                    print(f"Error reading {file_path}: {e}")
                    continue
                
                # Add a new column to keep track of the participant ID and file name
                df['Participant'] = folder  # Participant folder name
                df['File'] = file  # Excel file name
                
                # Append the dataframe to the list
                all_data.append(df)

# Concatenate all the dataframes into one
if all_data:
    print("Combining data from all files...")
    final_df = pd.concat(all_data, ignore_index=True)

    # Export the final dataframe to a single Excel file
    final_df.to_excel(output_file, index=False)

    print(f"Data has been consolidated and saved to {output_file}")
else:
    print("No Excel files were found or processed.")

