# Week 1 Progress Report
## Data Collection and Curation for a Multilingual Public Service Announcement (PSA) Dataset

**Project Title:** Development of a Multilingual Public Service Announcement (PSA) Dataset for Low-Resource Neural Machine Translation in Kenyan Languages

**Group Members:**
- Harina Chohan – 666983
- Susan Otieno – 670501
- Ilham Mohamed – 670152
- Kevin Korir – 670656

---

## Introduction

Public Service Announcements (PSAs) are an important communication channel for disseminating information on health, education, agriculture, security, and governance. This project aims to develop a multilingual PSA dataset for low-resource neural machine translation. Week 1 focused on collecting, curating, and preparing the dataset.

---

## Week 1 Objectives

- Identify reliable sources
- Collect multilingual PSA data
- Construct a structured dataset
- Perform initial cleaning and basic validation
- Produce a curated dataset exceeding 5,000 sentence pairs

---

## Data Sources

WHO Kenya, UNICEF Kenya, Kenya Red Cross, KEMRI, KALRO, Kenya Meteorological Department, Kenya News Agency, Daily Nation, Citizen TV, Reuters, AP News, UNFPA Kenya, and official government portals.

---

## Data Collection Methodology

A hybrid approach combining manual collection and automated scraping with **BeautifulSoup** was used, while respecting website access policies.

---

## Dataset Construction

**Fields:** `PSA_ID`, `Domain`, `English`, `Kiswahili`, `Dholuo (placeholder)`, `Source`, `Date`, `Metadata`

---

## Data Cleaning and Initial Validation

- Duplicate removal
- Text standardisation
- Basic language verification
- Initial relevance filtering

Records requiring further assessment were marked for detailed PSA validation.

---

## Dataset Summary Statistics

| Stage | Count |
|---|---|
| Initial dataset | 6,648 records |
| Combined dataset | 9,551 records |
| After duplicate removal | 9,540 unique records |

**Domains:** Health, Education, Agriculture, Security, Governance
**Languages:** English and Kiswahili (Dholuo placeholder)

---

## Sample Dataset Entries

| PSA_ID | Domain | English | Kiswahili | Source | Date |
|---|---|---|---|---|---|
| PSA000003 | Health | PRESS RELEASE: JUNE 29, 2020 — The EU through its Civil Protection and Humanitarian Aid Operations department (ECHO) has donated KES 270 million (2,573,105 Million Euros) to WHO Kenya. | TAARIFA KWA VYOMBO VYA HABARI: JUNI 29, 2020 — EU kupitia idara yake ya Ulinzi wa Raia na Uendeshaji wa Misaada ya Kibinadamu (ECHO) imetoa KES milioni 270 (Euro milioni 2,573,105) kwa WHO Kenya. | WHO Kenya | 6/29/2020 |
| PSA000004 | Health | This grant will be used by the WHO to support the Government of Kenya's efforts to control the spread of the pandemic. | Ruzuku hii itatumiwa na WHO kuunga mkono juhudi za Serikali ya Kenya za kudhibiti kuenea kwa janga hili. | WHO Kenya | 6/29/2020 |
| PSA000009 | Health | Strengthening clinical care for high-consequence infectious diseases in East Africa. Following successive Ebola and Marburg outbreaks, countries are shifting how preparedness is defined. | Kuimarisha huduma ya kimatibabu kwa magonjwa ya kuambukiza yenye matokeo makubwa Afrika Mashariki. | WHO Kenya | 4/30/2026 |
| PSA000012 | Health | Yet patient outcomes continue to vary, pointing to a different challenge. | Hata hivyo matokeo ya mgonjwa yanaendelea kutofautiana, yakiashiria changamoto tofauti. | WHO Kenya | 4/30/2026 |
| PSA000013 | Health | Clinical management has emerged as the determining factor. | Usimamizi wa kimatibabu umeibuka kama kigezo cha kuamua. | WHO Kenya | 4/30/2026 |

*(Full dataset available in the project data files — duplicate rows removed from this table for readability.)*

---

## Challenges Encountered

- Distinguishing genuine PSAs from general news articles
- Duplicate records
- Inconsistent formatting across sources
- Limited multilingual resources
- Time required for manual validation
- Each source had a different page structure, requiring scraping scripts to be adapted per website — increasing collection time

---