# QuickTips
This repository shares quick tips for better data management and terminal efficiency on servers and HPC systems. Also, provides a comprehensive collection of commands and scripts commonly used while analyzing sequencing data. The content is derived from my daily usage and experience.

1. [How I Organize My Data on the Server?](#question1)
2. [How to calculate number of reads in fastq file?](#question2)
3. [How to calculate the average read depth after aligning reads to a reference genome?](#question3)
4. [How to fetch mapped and unmapped reads from BAM file using samtools?](#question4)
5. [How to Remove Lines in a Text File Above a Specific Sentence using sed in Bash? Example sentence : ">>>>>>> Coverage per contig"](#question5)
6. [How to check the delimiter of a file in bash?](#question6)
7. [How to verify data integrity using md5sum?](#question7)
8. [How to convert multi-line fasta to single-line fasta?](#question8) 


# How I Organize My Data on the Server? <a name="question1"></a>

## Step 1: Create Unique Project Folders
I create unique project folders for each project. Each project folder contains a set of subfolders that organize the different stages of analysis. This structured approach ensures that all data and results related to a specific project are kept together, making it easier to manage, access, and navigate through the project's lifecycle.

 **Example:** Genome assembly project: `Ma_gasm`, 
 Population genomics project: `Ma_popgen`

## Step 2: Use Numerical Prefixes
Using numerical values before the folder name helps maintain order and enables efficient use of the TAB key for quick access to any folder through terminal.  
**Example:** `01_Ma_gasm`, `02_Ma_popgen`

## Step 3: Avoid Spaces in Folder Names
Using spaces in folder names can lead to issues with command-line operations and scripts. Instead, use underscores (`_`) as word separators.  
  **Example:** `Genome Assembly` should be `Genome_Assembly`.

## Step 4: Use Short Forms
I prefer using short forms instead of lengthy titles in places where possible.  
**Example:** `01_Maethiopoides_genome_assembly` (very long) becomes `01_Ma_gasm` (short and simple)

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

![image](https://github.com/meeranhussain/QuickTips/blob/main/Data_organisation.png)

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

# How to calculate number of reads in fastq file? <a name="question2"></a>

In a Fastq file, each read is represented by four lines, with each read beginning with the "@" symbol. This command functions by counting the occurrences of "@" at the start of each read.

**For a single file:**
```bash
grep -c "^@" input.fastq
```
**For multiple files:**
```bash
grep -c "^@" *.fastq > read_count.txt
```
**For zipped files:**
```bash
zcat *fastq.gz | grep -c "^@" > read_count.txt
```
# How to calculate the average read depth after aligning reads to a reference genome? <a name="question3"></a>
Depth generally refers to the number of times a particular nucleotide in the genome is read during the sequencing process. It reflects the number of reads aligned to a specific position in the genome. Evaluating read depth helps determine if there are a sufficient number of reads aligned over a given base position, which is crucial for our analysis. Whereas coverage means to check if the reads are aligned over the genome by checking the percentage.
This is a common way to evaluate the quality of samples by checking read depth after alignment.

**Step 1: Convert SAM file to BAM file**
```bash
samtools view -bS --threads <number_of_threads> input.sam > output.bam
```
**Step 2: Sort BAM file based on position** 
```bash
samtools sort input.bam -o output_sorted.bam -@ <number_of_threads>
```
**Step 3: Calculating Average Depth**  
There are two tools either “samtools depth” or “Mosdepth”
```bash
samtools depth -a output_sorted.bam |  awk '{sum+=$3} END { print "Average = ",sum/NR}'
```

OR 

Index Sorted BAM file and use mosdepth to calculate average read depth
```bash
samtools index input_sorted.bam -@ <number_threads>
mosdepth  sample_depth input_sorted.bam
awk '$1=="total"{print $4;}'  sample_depth.mosdepth.summary.txt
```
# How to fetch mapped and unmapped reads from BAM file using samtools? <a name="question4"></a>
To fetch mapped reads:
```bash
samtools view -b -F 4 input.bam > output.bam
```
“-F” flag to fetch mapped reads & “-b” to produce output BAM file
To fetch unmapped reads:
```bash
samtools view -b -f 4 input.bam > output.bam
```
“-f” flag to fetch unmapped reads

**OR**

Fetching mapped reads from BAM and output as Fastq files
```bash
samtools fastq -F 4 <input.bam> > <output.fq>
```

# How to Remove Lines in a Text File Above a Specific Sentence using sed in Bash? [Example sentence : ">>>>>>> Coverage per contig"]  <a name="question5"></a>
1. Edit within same file
```bash
sed -i '1,/>>>>>>> Coverage per contig/d' yourfile.txt
```
2. Create new file with edits
```bash
sed '1,/>>>>>>> Coverage per contig/d' yourfile.txt > newfile.txt
```
# How to check the delimiter of a file in bash? <a name="question6"></a>
1. Save the following code as "delim_check.awk"
```bash
BEGIN {
   sep[","]   = "comma"
   sep["\\|"] = "pipe"
   sep["\t"]  = "tab"
}

{
    for (x in sep) {
        c = gsub(x,"&",$0)
        if (c) cnt[sep[x] " " (c+1)]++
    }
}

END {
    for (x in cnt) {
        if (max == "" || cnt[x] > max) {
            max = cnt[x]
            est = x
        }
    }
    print est
}
```
2. Run the code as following:
```bash
awk -f delim_check.awk <file_to_check>
```
Prints type delimiter(tab, pipe, comma) with number of columns (int) 

# How to verify data integrity using md5sum?  <a name="question7"></a>
## Syntax
```bash
md5sum -c <list_of_files.md5>
```
**Example:**
Fastq files from SRA:
![image](https://github.com/meeranhussain/QuickTips/assets/40800675/28d2602a-72f4-41ed-b28b-fdb2d8d956e0)

1. Md5sum check on the fastq files and store in ".md5":
```bash
md5sum *.fastq.gz > md5sum_check.md5
```
Output of md5sum_check.md5:
![image](https://github.com/meeranhussain/QuickTips/assets/40800675/5fd7ae9a-5a81-408f-a392-bc0fc0c07a25)

2. Verify files
```bash
md5sum -c md5sum_check.md5
```
**Results: If "OK" files are good**
![image](https://github.com/meeranhussain/QuickTips/assets/40800675/98f44190-f2ee-48f7-81c0-123c76bd8d3e)

# How to convert multi-line fasta to single-line fasta? <a name="question8"></a>
**Usage:** 
```bash
multi2singlefa.pl <input_multi.fasta> > <output_single.fasta>
```
## Perl script
Save the following code as a Perl script
```perl
#!/usr/bin/perl -w
use strict;

my $input_fasta=$ARGV[0];
open(IN,"<$input_fasta") || die ("Error opening $input_fasta $!");

my $line = <IN>; 
print $line;

while ($line = <IN>)
{
chomp $line;
if ($line=~m/^>/gi) { print "\n",$line,"\n"; }
else { print $line; }
}

print "\n";
```
