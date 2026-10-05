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

## Key Insights

### Findings

1. **Overall scale.** The dashboard covers 2,671 visits from 1,649 patients, with $927,492 in total clinical cost. That is $347.25 per visit and $562.46 per patient, or about 1.6 visits per patient.

2. **Three chronic conditions drive more than half of the cost.** Hypertension, Asthma, and Diabetes account for 1,220 of 2,671 visits (45.7%) but $514,428 of the total cost (55.5%). They average $418 to $430 per visit, versus $262 to $300 for the other five diagnoses.

3. **Doctor workload is heavily concentrated.** The 4 General Practice doctors handle 1,451 visits (54.3%), about 363 visits each. The other 15 doctors with recorded visits average about 81 each. All 7 Neurology doctors have no recorded visits.

4. **Patients with lower health scores cost much more.** Among patients with at least one visit, those with a health score of 40 to 59 average $798.86 in clinical cost and 1.93 visits. Patients scoring 75 to 95 average $288.78 and 1.27 visits, about 2.8 times less. Across all 1,649 patients, health score has a moderate negative correlation with cost (r = -0.41) and with visit frequency (r = -0.32). These are descriptive relationships, not proof of cause.

5. **Outpatient visits are the most common, but checkups cost the most.** Outpatient makes up 1,285 visits (48.1%), Checkup 899 (33.7%), and Emergency 487 (18.2%). Checkups average $418.86 per visit, Outpatient $341.92, and Emergency only $229.09. Emergency being the cheapest is unexpected and worth reviewing.

6. **Cost peaks in January, August, and April.** Monthly cost, with all years combined, is highest in January ($87,158), August ($85,679), and April ($82,589). It is lowest in November ($63,941) and July ($66,429).

7. **Wellness engagement is steady and evenly spread.** There are 49,776 logged activities (about 15 per patient), averaging 67.5 minutes, with 40.3% rated Good, 50.2% Average, and 9.5% Poor. Volume is stable at about 2,000 to 2,250 activities per full month. Activity types (6,920 to 7,275 each) and cities (39.4% to 41.2% Good) look almost identical, so no single activity or city stands out.

8. **Wellness status follows health score, not activity volume.** Patients with a health score below 60 (632 patients, 19.5% of all patients) have no Good activities, and about half of their activities are rated Poor. Patients scoring 60 or above have no Poor activities. How many activities a patient logs is unrelated to their health score (r = 0.01).

9. **Health scores and patient mix are similar across groups.** The average health score is 73.7 overall. It ranges only from 73.1 to 74.2 by city and from 73.4 to 74.4 by insurance type. Half of patients have Public insurance (1,622), 25% have Private (810), and 25% have no insurance recorded (811).

### Recommendations

1. **Target the lowest health-score patients first.** The 632 patients with scores below 60 cost about 2.8 times more than the healthiest group and have no Good wellness activities. Expand the wellness program for this group first, then track whether their scores, visit counts, and costs improve.

2. **Rebalance doctor capacity.** Add or redistribute General Practice capacity, since four doctors carry 54% of visits. Also check why the 7 Neurology doctors have no recorded visits: it may be a data gap or a scheduling and assignment issue.

3. **Manage chronic conditions proactively.** Hypertension, Asthma, and Diabetes make up 55.5% of cost. Prevention and follow-up programs for these conditions have the biggest cost-saving potential.

4. **Plan staffing and budget around seasonal peaks.** Check that the January, August, and April cost peaks repeat each year before scheduling extra capacity for those months.

5. **Improve data quality at the source.** Fix the missing insurance values (25% of patients), the 269 zero-cost visits, the 47 duplicate visit rows, the 116 impossible ages, and the visit dates before 2020 (2,002 visits). Keep "Unknown" visible as an insurance category until it is corrected.

### Data Notes

These figures include the zero-cost visits, duplicate rows, and impossible ages listed above, because the dashboard includes them. Monthly cost patterns combine all years.
