![Designer](Designer.jpeg)

# ChinaCarOwnersNationWideTeamCharlieKT

This script cleans and processes a CSV file containing data on car owners in China. The steps include translating column headers, removing duplicates, dropping unnecessary columns, validating emails, checking for invalid alphanumeric values, splitting data into chunks, and merging address fields.

# #Requirements
The following Python libraries are required to run the script:

pandas
re
os
You can install them via pip:

bash
Copy code
pip install pandas

## Data Overview
The original dataset includes information about car owners such as:

车架号: Vehicle Identification Number (VIN)
姓名: Name
身份证: ID Card
性别: Gender
手机: Mobile Phone
邮箱: Email
省: Province
城市: City
地址: Address
邮编: Postal Code
生日: Date of Birth
行业: Industry
月薪: Monthly Salary
婚姻: Marital Status
教育: Education
BRAND: Brand
车系: Vehicle Series
车型: Vehicle Model
配置: Configuration
颜色: Color
发动机号: Engine Number
Steps Performed

# Translate the Column Headers
The original column names are in Chinese. We translated them into English using a dictionary.

'''translation_dict = {
    "车架号": "Vehicle Identification Number (VIN)",
    "姓名": "Name",
    "身份证": "ID Card",
    "性别": "Gender",
    "手机": "Mobile Phone",
    "邮箱": "Email",
    "省": "Province",
    "城市": "City",
    "地址": "Address",
    "邮编": "Postal Code",
    "生日": "Date of Birth",
    "行业": "Industry",
    "月薪": "Monthly Salary",
    "婚姻": "Marital Status",
    "教育": "Education",
    "BRAND": "Brand",
    "车系": "Vehicle Series",
    "车型": "Vehicle Model",
    "配置": "Configuration",
    "颜色": "Color",
    "发动机号": "Engine Number"
}
'''

# Remove Duplicates
We remove duplicates based on the ID Card, Vehicle Identification Number (VIN), and Email columns and save the duplicates to a file called dump 0.csv.

'''duplicate_rows = df[df.duplicated(subset=['ID Card', 'Vehicle Identification Number (VIN)', 'Email'], keep=False)]
    duplicate_rows.to_csv('/content/dump 0.csv', index=False)
    df_deduped = df.drop_duplicates(subset=['ID Card', 'Vehicle Identification Number (VIN)', 'Email'], keep='first')
'''

# Drop Unnecessary Columns
We drop columns that are not needed for analysis and save the dropped columns to dump 1.csv.

'''columns_to_drop = [
    'gender', 'industry', 'monthly_salary', 'marital_status', 
    'education', 'brand', 'car_series', 'car_model', 
    'configuration', 'color', 'engine_number', 'Unnamed: 21'
]

df_dropped = df.drop(columns=columns_to_drop, errors='ignore')
df_dropped.to_csv('/content/dump 1.csv', index=False)
'''

# Split CSV into Chunks
We split the dataset into 8 chunks to make the processing manageable.

'''def split_csv(file_path, num_chunks):
    df = pd.read_csv(file_path)
    chunk_size = len(df) // num_chunks
    for i in range(num_chunks):
        chunk = df.iloc[i * chunk_size : (i + 1) * chunk_size]
        chunk.to_csv(f'/content/chunk_{i}.csv', index=False)
'''

# Check for Invalid Emails
We loop through the chunks and check for invalid email addresses. Invalid emails are extracted and saved to dumpinvalidemails.csv.

'''def is_valid_email(email):
    pattern = r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
    return re.match(pattern, email) is not None
'''

# Merge the Chunks
We merge all chunks back into a single file called merged_China_CarOwners.csv.

'''def merge_chunks(folder_path, output_file_name):
    all_chunks = []
    for filename in os.listdir(folder_path):
        if filename.endswith(".csv"):
            df = pd.read_csv(os.path.join(folder_path, filename))
            all_chunks.append(df)
    merged_df = pd.concat(all_chunks, ignore_index=True)
    merged_df.to_csv(output_file_name, index=False)
'''

# Replace 'noemail' and Check VIN and ID Card

We replace occurrences of "noemail" in the Email column with Null and check the VIN and ID Card columns for non-alphanumeric characters. Rows with invalid data are saved to dump 2.csv.


'''df['Email'] = df['Email'].replace('noemail', 'Null')

def check_alphanumeric(df):
    dump2_rows = []
    for index, row in df.iterrows():
        vin = str(row['Vehicle Identification Number (VIN)'])
        id_card = str(row['ID Card'])
        if not vin.isalnum() or not id_card.isalnum():
            dump2_rows.append(row)
    return dump2_rows
'''

# Merge Province, City, and Postal Code into Address

We merge the Province, City, and Postal Code columns into a single Address column.

'''df['Address'] = df['Province'].astype(str) + ', ' + df['City'].astype(str) + ', ' + df['Postal Code'].astype(str)
df = df.drop(['Province', 'City', 'Postal Code'], axis=1)
df.to_csv("/content/Valid_data.csv", index=False)
Output Files
translated_car_owners.csv: The translated CSV with English headers.
dump 0.csv: Rows containing duplicate values for ID Card, VIN, and Email.
dump 1.csv: Dropped columns from the dataset.
dumpinvalidemails.csv: List of invalid email addresses.
dump 2.csv: Rows missing alphanumeric data for VIN or ID Card.
Valid_data.csv: The final cleaned dataset with merged address fields.
'''

# How to Run
Clone the repository or copy the script.
Install the required libraries.
Run the script step by step, or modify it as needed for your data.
