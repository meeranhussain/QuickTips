# QuickTips
This repository shares quick tips for better data management and terminal efficiency on servers and HPC systems. The content is derived from my daily usage and experience.

1. [How I Organize My Data on the Server?](#question1)

# How I Organize My Data on the Server? <a name="question1"></a>

## Step 1: Create Unique Project Folders
I create unique project folders for each project.  
**Example:** `Ma_gasm`, `Ma_popgen`

## Step 2: Avoid Spaces in Folder Names
Using spaces in folder names can lead to issues with command-line operations and scripts. Instead, use underscores (`_`) as word separators.  
  **Example:** `Genome Assembly` should be `Genome_Assembly`.

## Step 3: Use Short Forms
I prefer using short forms instead of lengthy titles in places where possible.  
**Example:** `01_Maethiopoides_genome_assembly` (very long) becomes `01_Ma_gasm` (short and simple)

## Step 4: Use Numerical Prefixes
Using numerical values before the folder name helps maintain order and enables efficient use of the TAB key for quick access to any folder through terminal.  
**Example:** `01_Ma_gasm`, `02_Ma_popgen`

## Step 5: Organize Analysis Steps Within Project Folders
Within each project folder, I organize the analysis steps as individual folders. Raw data comes first, followed by subsequent analysis steps.  
**Example:**  
Within `01_Ma_gasm`: 
- `01_raw_data`
- `02_Base_calling`
- `03_ncgnm_asm`  

Further within `03_ncgnm_asm`:
- `01_flye`
- `02_medaka`

Within `flye`, each sample is placed in individual folders, starting with a QC folder: 
- `00_QC`
- `01_Magm1`
- `02_Magm2`
- `03_Magm3`

![image](https://github.com/meeranhussain/CommandHub/assets/40800675/ff3f0490-ae0f-42d1-89da-db0feaba3a69)

## Step 6: Separate QC Files
Quality check (QC) files can be the files generated while assessing the output of a particular dataset. For example, let's say we have FASTQ files which will undergo QC using tools like FastQC and MultiQC. The files generated from these tools should be organized into `00_QC` folders, and separated into individual folders for each sample within the QC folder. This ensures that QC results are clearly organized and easily accessible.

### Example of QC File Organization:
Within `02_Illumina`:
- `00_QC`
  - `01_Sample1`
    - `fastqc_report.html`
    - `multiqc_report.html`
  - `02_Sample2`
    - `fastqc_report.html`
    - `multiqc_report.html`
  - `03_Sample3`
    - `fastqc_report.html`
    - `multiqc_report.html`
