# Credit Card Approval Prediction

**Type:** Classification

## Goal
Predict whether a credit card application will be **approved (1)** or **rejected (0)**.

**Target:** `approval_status` (1 = Approved, 0 = Rejected)

## Data
The data is **synthetic** (fake, created with Python) so it can be big and have all the problems we want to practice on.

- **Generator:** `generate_data.py` (random seed = 123, so you always get the same data)
- **File:** `data/credit_card_applications.csv` - about 120,000 rows (+0.5% duplicate rows) and 53 columns
- **Data types in the file:** int, float, text (object) and dates saved as text

Re-create (or change) the data:
```bash
python generate_data.py
```
Change `N_ROWS` (100,000 - 300,000) or `SEED` at the top of `generate_data.py` to get a bigger / different dataset.

### Problems added on purpose
- **ID / personal columns** (IDs, names, e-mail, phone, date of birth)
- **Messy column names** (`Annual Income ($)`, `CreditScore`, `No. of Dependents`, ...)
- **Short codes** that should be full values (`M`/`F`, `Y`/`N`, `T`/`F`, `FT`/`PT`, ...)
- **Numbers saved as text** (`$1,250.00`, `12,500`, `34 yrs`, `12.5 km`, `15%`)
- **Missing values**, also hidden as text (`N/A`, `unknown`, `?`, `-`, a space) and one **mostly empty** column
- **Outliers** (extreme values) and **impossible values** (age = 150, negative mileage, 9999 codes, ...)
- **Dates saved as text** in different formats
- **Messy text** (`New York`, `NEW YORK`, `  new york`)
- **Categories saved as numbers** (`region_code`)
- **Useless columns**: a constant column, an almost-constant column, a duplicate column
- **Duplicate rows** (~0.5%)

### Columns
| Column in the file | Clean name | Problems |
|---|---|---|
| `Application ID` | `application_id` | ID / personal detail -> drop |
| `Full Name` | `full_name` | ID / personal detail -> drop |
| `E-mail` | `email` | ID / personal detail -> drop |
| `Phone #` | `phone_number` | ID / personal detail -> drop |
| `DOB` | `date_of_birth` | ID / personal detail -> drop |
| `Age (Years)` | `age` | number saved as text (`12 yrs`); 2% missing; wrong values: `-5`, `0`, `150`, `210` |
| `Gender` | `gender` | short codes: `M`, `F` |
| `Marital Status` | `marital_status` | short codes: `S`, `M`, `D`, `W` |
| `Education` | `education` | short codes: `HS`, `BSC`, `MSC`, `PHD` |
| `City` | `city` | 1% missing; mixed upper / lower case, extra spaces |
| `Region Code` | `region_code` | category saved as a number |
| `Preferred Language` | `preferred_language` | - |
| `Marketing Opt-In` | `marketing_opt_in` | short codes: `Y`, `N` |
| `Application Date` | `application_date` | 1% missing; date saved as text (`%d/%m/%Y`) |
| `Employment Type` | `employment_type` | short codes: `FT`, `PT`, `SE`, `UE`, `RT` |
| `Industry` | `industry` | 2% missing |
| `Years Employed` | `years_employed` | 5% missing; wrong values: `-1`, `99` |
| `Annual Income ($)` | `annual_income` | number saved as text (`$1,250.00`); 3% missing; extreme outliers |
| `Monthly Expenses` | `monthly_expenses` | - |
| `Existing Debt` | `existing_debt` | - |
| `Debt-to-Income Ratio` | `debt_to_income` | - |
| `Late Payments 12m` | `late_payments_12m` | - |
| `CreditScore` | `credit_score` | 4% missing; wrong values: `0`, `9,999` |
| `Num Credit Cards` | `num_credit_cards` | - |
| `Num Open Loans` | `num_open_loans` | - |
| `Credit Utilization %` | `credit_utilization` | number saved as text (`12.5%`) |
| `Previous Default` | `previous_default` | short codes: `Y`, `N` |
| `Bankruptcy Flag` | `bankruptcy_flag` | - |
| `Savings Balance` | `savings_balance` | number saved as text (`1,250.00`); 8% missing |
| `Checking Balance` | `checking_balance` | number saved as text (`$1,250.00`); extreme outliers |
| `Has Mortgage?` | `has_mortgage` | short codes: `Y`, `N` |
| `Owns Car` | `owns_car` | - |
| `Home Ownership` | `home_ownership` | 2% missing |
| `Residence Years` | `residence_years` | 3% missing |
| `Requested Limit` | `requested_limit` | number saved as text (`$1,250.00`) |
| `Card Type` | `card_type` | mixed upper / lower case, extra spaces |
| `Application Channel` | `application_channel` | mixed upper / lower case, extra spaces |
| `Num Inquiries 6m` | `num_inquiries_6m` | - |
| `Bank Relationship Years` | `bank_relationship_years` | - |
| `Is Existing Customer` | `is_existing_customer` | short codes: `T`, `F` |
| `Num Household Members` | `num_household_members` | - |
| `No. of Dependents` | `num_dependents` | 3% missing |
| `Time On Application (min)` | `time_on_application_min` | extreme outliers |
| `Documents Uploaded` | `documents_uploaded` | - |
| `Referral Source` | `referral_source` | - |
| `Device Type` | `device_type` | - |
| `Zip Code` | `zip_code` | ID / personal detail -> drop |
| `Secondary Income` | `secondary_income` | 75% missing (mostly empty) |
| `Data Source` | `data_source` | only one value |
| `Country` | `country` | only one value |
| `Is Test Record` | `is_test_record` | almost only one value |
| `Credit Cards Count` | `credit_cards_count` | exact copy of `num_credit_cards` |
| `Approval Status` | `approval_status` | **TARGET** (text: 'Approved', 'Rejected') |

## Files
| File | Description |
|---|---|
| `generate_data.py` | Creates the dataset |
| `credit_card_approval.ipynb` | The full project notebook |
| `data/credit_card_applications.csv` | The dataset |
| `data/credit_card_applications_metadata.xlsx` | Metadata (data dictionary): description, role, data types, missing values, statistics, codes and problems of every column |
| `models/` | Created when you run the notebook (best model, scaler, selected features) |

## Steps in the notebook
**A. Data cleaning**
- A0 Drop columns that are not useful (IDs, names, ...)
- A1 Rename columns
- A2 Data types: numbers saved as text, messy text, short codes, dates, categories saved as numbers, target to 0/1
- A3 Missing values - % per column, drop columns > 50% missing, fill the rest (mean / mode)
- A4 Outliers - box plots, IQR table, wrong values -> median, extreme values -> capping
- A5 Duplicate rows and duplicate columns
- A6 Columns with only one value

**B. EDA**
- B1 Target count and percentage
- B2 Correlation heatmap and correlation with the target
- B3 Charts for the independent features (and features vs target)

**C. Feature engineering**
- C1 Binning / new features
- C2 Label encoding and one-hot encoding
- C3 Data splitting: 80% train / 15% test / 5% unseen (random_state = 123)
- C4 Feature scaling (StandardScaler)
- C5 Feature selection (SelectKBest + Random Forest importance)
- C6 20,000-row training sample for comparing / tuning the models

**D. Feature extraction**
- D1 PCA
- D2 Factor Analysis
- D3 Cluster analysis (K-Means with elbow and silhouette)

**E. Machine learning (20 models)**
- E1 Define the models
- E2 Evaluation function (all metrics)
- E3 Cross validation (5 folds)
- E4 Training and testing evaluation
- E5 Hyperparameter dictionary
- E6 Grid search tuning
- E7 Compare the models, select the best, re-train it on the full training data
- E8 Save the best model (`joblib`)
- E9 Load the model
- E10 Predict and evaluate on the 5% unseen data

## How to run
```bash
pip install pandas numpy matplotlib seaborn scikit-learn xgboost joblib jupyter
python generate_data.py
```
Then open `credit_card_approval.ipynb` **from this folder** and run all cells (the full notebook takes about 10-20 minutes).
