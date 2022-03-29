
#For Linux tutorial by SBU team

#1. Open FastQ file and Check QC

#1.1 Open FastQ file
samtools view Fastq_file/Cherry_R1.fq.gz | head
samtools view Fastq_file/Cherry_R1.fq.gz | head

#1.2 Check sequence quality by fastqc
fastqc Fastq_file/Cherry_R1.fq.gz
fastqc Fastq_file/Cherry_R2.fq.gz

#2. Identify mutations

#2.1 Open VCF file
more VCF_file/Cherry_tumor.vcf
cat VCF_file/Cherry_tumor.vcf
head VCF_file/Cherry_tumor.vcf

#2.2 Count mutation 
grep -v "^#" VCF_file/Cherry_tumor.vcf | wc -l

#2.3 Count MISSENSE mutation 
grep -v "^#" VCF_file/Cherry_tumor.vcf | grep "MISSENSE" |wc -l

#2.4 Count MISSENSE mutation that PASS filter
grep -v "^#" VCF_file/Cherry_tumor.vcf | grep "MISSENSE" |grep "PASS" |wc -l
grep -v "^#" VCF_file/Cherry_tumor.vcf | grep "MISSENSE" |awk '$7 == "PASS"'| wc -l

#3. Search identified mutations in database e.g. VEP, COSMIC, ClinVar databases
#Known mutation is in position 

grep "7674220" VCF_file/Cherry_tumor.vcf


#4. Blast HLA from Cherry sequence

makeblastdb -in Database/hla_nuc.fasta -dbtype nucl

#print output header with blast result
blastn -query Cherry/Cherry_nuc.fasta -db Database/hla_nuc.fasta -outfmt 7 

#sort by sequence identity
blastn -query Cherry/Cherry_nuc.fasta -db Database/hla_nuc.fasta -outfmt 6 | sort -k3


#5. Blast HLA from Patient cohort sequence
#blast sequences to the HLA database and select only the most similar sequence as HLA type of that query patient

for i in {1..100};
	do
	echo $i "Patients/patient_${i}.fa"
	blastn -query "Patients/patient_${i}.fa" -db Database/hla_nuc.fasta -outfmt 6 | sort -k3 |tail -n1
done
	
