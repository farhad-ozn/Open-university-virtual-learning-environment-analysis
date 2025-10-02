# Open University Virtual Learning Environment (VLE) Analysis  

---

## 1. Introduction  

The Open University (OU) has introduced a new virtual learning environment (VLE) designed to enhance the learning experience through online tools and resources.  

This report answers two key questions:  

1. **Is the VLE improving students’ grades?**  
2. **Can we predict students’ grades using data from the VLE?**  

The analysis involves **data preprocessing, exploratory data analysis (EDA), feature engineering, model training, and evaluation**.  

### Datasets  

Seven interconnected CSV files were downloaded and preprocessed:  

- **courses.csv**: Lists all modules and presentations (`code_module`, `code_presentation`).  
- **assessments.csv**: Assessment details (type, submission dates, weightings).  
- **vle.csv**: Materials in the VLE (`id_site`, `activity_type`, weeks available).  
- **studentInfo.csv**: Demographic info (gender, region, education, final result).  
- **studentAssessment.csv**: Assessment scores linked to students.  
- **studentVle.csv**: Logs of student VLE interactions (click counts).  
- **studentRegistration.csv**: Registration status and dates.  

The **main indicator of VLE interaction** is the **daily sum of clicks**.  

We first performed a **hypothesis test**:  

- **Hypothesis**: Students’ total VLE clicks are related to their final grades.  

We then developed a **linear regression model** to predict grades. For this report, results are shown for **module FFF (2013 presentation)**.  

### Table Linking  

- `studentInfo` → `studentAssessment`, `studentVle`, `studentRegistration` (via `id_student`)  
- `courses` → `assessments`, `studentRegistration`, `vle`, `studentInfo` (via `code_module`, `code_presentation`)  
- `assessments` → `studentAssessment` (via `id_assessment`)  
- `vle` → `studentVle` (via `id_site`)  

Extensive cleaning and merging was required. Insights from EDA, hypothesis testing, Pearson correlations, and k-tests were used to analyze **VLE interaction data, module patterns, and assessment types**.  

**Key assumption:** Since OU modules include TMA, CMA, and Exams, but exams had missing or inconsistent data, only **TMA + CMA weighted scores** were used to calculate final grades.  

---

## 2. Data Exploration  

### 2.1 Data Cleaning and Wrangling  

The dataset required significant transformation. Steps included:  

#### a. studentAssessment.csv  
- Removed 173 rows with score = `"?"`.  
- Dropped rows where `is_banked = True`.  
- Converted `score` to float.  

#### b. assessments.csv  
- Filled 11 missing exam dates using `module_presentation_length`.  
- Missing keys identified (mostly `Exam` type).  
- Removed `Exam` assessments (only TMA and CMA kept).  
- Created new weighted score columns.  
- Created new feature `submission_timeliness`:  
  - `date_submitted – date_assessment`  
  - Positive = on-time, Negative = late.  

#### c. vle.csv  
- Dropped redundant `code_module` and `code_presentation`.  
- Removed `week_from` and `week_to` ( >80% null).  
- Checked `id_site` for duplicates (none found).  

#### d. studentVle.csv  
- Merged with `vle`.  
- Created features:  
  - `avg_week_clicks`, `total_clicks` (per student/module/presentation).  
  - Separate columns for different `activity_type` clicks.  
- Grouped clicks into weeks using `date // 7`.  

#### e. courses.csv  
- Added module-presentation info to merged dataset.  

#### f. studentInfo.csv  
- Removed sensitive features (gender, region, disability, imd_band).  
- One-hot encoded `highest_education` and `age_band`.  
- Removed students with `final_result = Withdrawn`.  
  - Before: 25,521 rows → After: 20,964 rows.  

#### g. studentRegistration.csv  
- Excluded (low added value).  

#### h. Final Merged Dataset  
- **20964 entries**, **39 columns**, **no nulls**.  

#### i. Feature Removal  
- Removed low-value features:  
  - `repeatactivity_sum` (only 2 records).  
  - `sharedsubpage_sum` (near zero mean/std).  

---

### 2.2 Summary of the Dataset  

- **Final dataset**: 23,415 rows × 37 columns.  
- **Columns include**:  
  - `code_module`, `code_presentation`, `id_student`, `final_score`,  
  - `avg_timeliness`, `total_clicks`, `avg_week_clicks`,  
  - Click counts for each activity type (`forumng_clicks`, `quiz_clicks`, etc.),  
  - `module_presentation_length`, `num_of_prev_attempts`, `studied_credits`,  
  - Encoded education/age features.  

---

### 2.3 Important Aspects  

- **7 unique modules**: [AAA, BBB, CCC, DDD, EEE, FFF, GGG]  
- **4 presentations**: [2013J, 2014J, 2013B, 2014B]  
- **22 unique module+presentation combinations**  

---

## Key Insights  

- The **main relationship studied** is between VLE interaction (clicks) and grades.  
- Data required **extensive cleaning, merging, and wrangling**.  
- **Final dataset** is well-structured for regression modeling.  
- Initial results with **linear regression** show that grades can be predicted effectively from VLE interaction data.  

---
