### Temas

Tema 1. Introducción al SO Unix/Linux. (aprox. 8 horas)

	1.1. Comandos Linux (@AR)
		¿Qué es un sistema operativo?
		¿Qué es Linux? Breve introducción histórica
		Entornos gráficos
		¿Cómo usar Linux? WSL, VirtualBox, install in your PC, etc. (Mac es Linux)
		El terminal: esa caja oscura --> comandos
		¿Qué es un cluster de cómputo?. ¿Conexión a un servidor (HPC).

	1.2. R y RStudio: repositorios, instalar paquetes (@MZ)

	Evaluación: Tarea comandos linux con entregra?

Tema 2. Obtención de datos biológicos masivos. Bases de datos biológicas. (aprox. 2 horas)

	@MZ

		- Breve intro tecnologías secuenciación
		- Formatos secuencias (fasta, fastq, ab1). Concepto de calidad de secuenciación (escala Phred)

	@AR

		- Ensembl, NCBI, DDBJ --> muy básico (ftp?)
		- SRA y ENA (BioMart)
		- Intro anaconda y entornos virtuales
		- Descargamos con fasterq-dump lecturas de SARS


		ex: SRR34798504


	Evaluación: tarea de descargar algún genoma, contar el número de cromosomas, logitud total, etc.

Tema 3. Alineamiento de secuencias y filogenias. (aprox. 4 horas) - UCM

Tema 4. Análisis avanzado e integración de datos -ómicos (de tecnologías ómicas). (aprox. 22 horas)

		4.1 Genómica (8 horas): 

		@MZ

			Explicar algoritmo alineamiento (clustal, blast, etc.)

		@AR

			- Ensamblaje de novo de genomas: calidad de las lecturas (Fastqc), filtrado de la calidad (Trimmomatic, CutAdapt). Herramientas de ensamblaje (OLC o de Brujjn). Control de calidad de los ensamblajes (Quast). Remapeo de lecturas con Bowtie2 y visualización en IGV. Descontaminación (mencionar)

		@MZ-AR
			- Anotación estructural y anotación funcional de genomas (Prodigal, Prokka, Pharokka, Bakfta). Formatos .gff, .gtf y .bed.Visualizar en IGV


		@MZ
			- Análisis mutaciones: alineamiento (formato .sam .bam) y detección de SNPs/Indels (FreeBayes, Lofreq, VarScan, GATK, etc..). Mapeo de secuencias. Formato .bam/.sam. Identificación y anotación de variantes genómicas Formato .bcf/.vcf. Estudios de asociación genómica (GWAS) teórico.  

		@MZ
			- dRADseq y ddRADseq. Conceptos básicos - teórico.  

		Evaluación: ensamblar un genoma y anotarlo. Presentar ensamblaje conseguido (Tamaño total, N50, número de genes predichos (cuantos codificantes, tRNA, rRNAs, etc…)), cobertura media, etc….). Detectar mutaciones más importantes? Yo usaría genomas virales (de fagos, que son relativamente grandes y no tienen demasiadas repeticiones)

			4.2 Transcriptómica - UCM
			4.3 Epigenómica y regulación - UCM

		4.4 Metagenómica y metatranscriptómica (6 horas) --> yo creo que no da tiempo. 

			@MZ

				-Amplicon Sequencing – metabarcoding. 16S-seq e ITS-seq. Conceptos básicos. Mapeo de secuencias contra bases de datos específicas. Asignación taxonómica en base a similitud de secuencia. Análisis de diversidad microbiana/fúngica.

			@AR

			- Metagenoma shotgun. Conceptos básicos (teórico)

			Evaluación: obtención del perfil taxonómico de una muestra? Podemos simular datos con insilicoseq con proporciones variables de X amplicones V3-V4 (incluyendo los primers). Podríamos generar 5 muestras control y 5 pacientes para comparar, obviamente con alguna diferencia. O podríamos tomar datos reales y hacer subsamples con seqtk (puede usar pareados?) usando semillas diferentes (por ejemplo los datos de soja y suelo). 

Tema 5. Nuevas herramientas genéticas adaptadas a la biología celular. (aprox. 4 horas) -  UCM

