# Healthcare-Insights-Dashboard-Patient-Demographics-Doctor-Workload-Health-Outcomes
Interactive Excel dashboard analyzing patient demographics, health scores, and doctor visit patterns. Built with Power Query, pivot tables, charts, and slicers to filter by city, insurance type, and specialty.

Healthcare Insights Dashboard:An Excel dashboard that explores patient and doctor data to uncover patterns in demographics, health outcomes, and visit activity.

What it covers
Patient counts by insurance type and blood type
Average health score by city and insurance type
Doctor headcount and share of visits by specialty
Relationship between health score, cost, and visit frequency
Interactive slicers for city, insurance type, and specialty

Tools and techniques
Excel: pivot tables, charts, slicers, XLOOKUP, conditional formatting
Power Query: data cleaning and transformation (handling nulls and invalid values, fixing data types, creating custom columns)

Challenges and what I learned
Cleaning inconsistent data (nulls, "?", NA, None), connecting slicers across pivot tables, and planning the dashboard layout before building.

## Data Dictionary

Source: Regional Health Network - Patient & Wellness Analytics (`powerquery_4.xlsx`)

### patient_mapping (one row per patient)

| Column | Type | Key | Description | Known Values / Range |
|---|---|---|---|---|
| `patient_id` | Text | PK | Unique patient ID (P0001 to P3243) | 3,243 unique |
| `patient_name` | Text | | Patient's full name | 3,243 unique |
| `health_score` | Whole number | | Overall health score | 40 to 95, average 73.7 |
| `insurance_type` | Text | | Insurance category | Private, Public, Govt, Gov, blank |
| `primary_care_physician_id` | Text | FK | Assigned primary care doctor (DOC0001 to DOC0026) | 26 doctors |
| `blood_type` | Text | | Blood group | A+, A-, B+, B-, AB+, AB-, O+, O- |
| `city` | Text | | City where the patient lives | Rubavu, Huye, Kigali, Gisenyi, Musanze, Muhanga |

### patient_records_fact (one row per visit)

| Column | Type | Key | Description | Known Values / Range |
|---|---|---|---|---|
| `patient_id` | Text | FK | Patient the visit belongs to | 1,649 distinct patients |
| `visit_date.` | Date | | Date of the visit | 1946-06-07 to 2025-09-11 |
| `visit_time` | Time | | Time of the visit | 00:00:17 to 23:59:18 |
| `age` | Whole number | | Patient's age at the visit | -5 to 150 |
| `gender` | Text | | Patient's gender | M, F |
| `diagnosis` | Text | | Diagnosis recorded for the visit | Hypertension, Asthma, Diabetes, Migraine, Bronchitis, UTI, Malaria, Flu |
| `cost` | Decimal number | | Cost of the visit | 0 to 1,250, average 347.25 |
| `doctor_id` | Text | FK | Doctor who handled the visit | 19 of 26 doctors |
| `treatment_type` | Text | | Type of treatment given | Outpatient, Checkup, Emergency |

### wellness_activity (one row per logged activity)

| Column | Type | Key | Description | Known Values / Range |
|---|---|---|---|---|
| `patient_id` | Text | FK | Patient who did the activity | 3,243 distinct patients |
| `activity_type` | Text | | Type of wellness activity | Exercise, Hydration, Diet, Sleep, Checkup, Meditation, Yoga |
| `activity_date.` | Date | | Date of the activity | 2023-09-17 to 2025-09-16 |
| `activity_time` | Time | | Time of the activity | 00:00:00 to 23:59:56 |
| `duration_minutes` | Whole number | | Length of the activity in minutes | 15 to 120, average 67.5 |
| `wellness_status` | Text | | Rating of the activity | Good, Average, Poor |
| `Percentage_good` | Whole number (0 or 1) | | 1 if status is Good, otherwise 0 | Average 40.3% |
| `City` | Text | | Patient's city (XLOOKUP from `patient_mapping`) | Same 6 cities as above |

### doctors (one row per doctor)

| Column | Type | Key | Description | Known Values / Range |
|---|---|---|---|---|
| `doctor_id` | Text | PK | Unique doctor ID (DOC0001 to DOC0026) | 26 unique |
| `doctor_name` | Text | | Doctor's name | 26 unique |
| `specialty` | Text | | Doctor's medical specialty | Neurology, Cardiology, Pulmonology, General Practice, Endocrinology |

### Data Quality Notes

- 116 visits have impossible ages (-5 or 150).
- 2,002 visits are dated before 2020, while wellness data covers Sep 2023 to Sep 2025.
- 47 visit rows exactly repeat an earlier row.
- 269 visits have a cost of 0.
- 811 patients have blank insurance (shown as "Unknown" in the dashboard); `Gov` and `Govt` appear to be the same category.
- 80% of `activity_time` values are 00:00:00 (time is mostly missing).

## Business Questions

### Overall Goal

How do clinical costs, doctor workload, and patient wellness engagement relate to each other, and how can leadership use that to plan staffing and expand the wellness program?

### Page 1: Clinical Overview

1. How many visits and distinct patients does the network serve, and what is the total clinical cost?
2. What is the average cost per visit?
3. Which diagnoses drive the most visits?
4. How does clinical cost change from month to month?
5. How are visits split across treatment types (Checkup, Outpatient, Emergency)?
6. Which doctors handle the most visits?

### Page 2: Wellness & Engagement

1. How many wellness activities are logged, and how long does a typical activity last?
2. What share of activities have a "Good" wellness status?
3. Which activity types are the most popular (Exercise, Sleep, Meditation, Yoga, and others)?
4. How do the Good, Average, and Poor ratings break down?
5. How does activity volume change over time?
6. How do activity volume and duration differ by city?

### Page 3: Patient & Doctor Deep Dive

1. How are patients distributed by insurance type and by blood type?
2. How does the average health score differ by city and by insurance type?
3. How are doctors and visit share distributed across specialties?
4. Is a patient's health score related to their clinical cost or how often they visit?

### Decisions These Questions Support

- Where should the network focus staffing, based on high-volume diagnoses and busy doctors?
- Which patient groups should be targeted for wellness program expansion?
