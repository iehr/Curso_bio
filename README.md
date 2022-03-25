# Curso_bio
# Practico 2:  Prediction and anotations
Bienvenido al práctico de predicción y anotación en genes procariontes. En este práctico será la continuación de su práctico de ensamble, por lo que utilizaremos los resultados de sus ensambles.

##Materiales

* -Ensambles mediante OLC y De Bruijn

* -tRNA-scanSE para predecir los tRNAs

* -barrnap para predecir los rRNA

* -Prodigal para predecir los CDS

* -BLAST+

* -Base de datos SwissProt contenida en UniProtKB formateada para BLAST+.

* -Scripts custom para asignar función a los péptidos.



## Paso 1: Definir Workspace
-Crearemos una nueva carpeta dentro de nuestro directorio home.

```
mkdir anotacion
```
Entraremos al directorio
```
cd anotacion
```
⚠️ En los próximos comandos, recuerde reemplazar la N por el número de su grupo.

Copiaremos los resultados de nuestros ensambles en el directorio

Ejemplo: 

Output .fasta de assambly Canu(Pac-Bio/long reads) de practico anterior
```
cp -r home/dbs/bio-hpc/Curso_Cosas/G1_Aureimonas_altamirensis/Pac-Bio/btN.contigs.fasta canu.fasta .

```
Output .fasta de assambly Spades(Ilumina/short reads) de practico anterior
```
cp -r home/dbs/bio-hpc/Curso_Cosas/G1_Aureimonas_altamirensis/Ilumina/btN.contigs.fasta spades.fasta .

```

## Paso 2: Cargar los modulos necesarios en la consola (saltar si ya tienen el entorno/ambiente cargado):

```
ml Java/1.8.0_202
ml FastQC
ml Anaconda3/2020.02
conda init bash
bash
conda activate cursobio
```

### Desde ahora en adelante, su ambiente de Anaconda ya esta activo en su home, por lo que la próxima ves que desee usar programas, solo debera cargar:
```
ml Java/1.8.0_202
ml FastQC
conda activate cursobio
```
## Paso 3: Modificar archivo de lanzamiento de job (slurm) del cluster  (ejemplo_job.sh)
Para lanzar los siguientes comandos sera necesario preparar nuestros jobs


```
#!/bin/bash
#SBATCH --job-name=TareasCurso
#SBATCH --partition=slims
#SBATCH --output=out.%a.%N.%j.out
#SBATCH --error=out.%a.%N.%j.err
#SBATCH --mail-user=example@corre.com
#SBATCH --mail-type=ALL
#SBATCH -n 1 # number of jobs
#SBATCH -c 5 # number of cpu
##SBATCH --array=1-24
#SBATCH --mem=20G

[COMMANDO DEL JOB]” 
```

Ejemplo comando: 
``` 
prodigal -a canu.faa -d canu.fna -f gff -o canu.gff -i canu.fasta

```
Tips a recordar:
-Ejecucion de job
```
sbatch ejemplo_job.sh
```
-Seguimiento de job
```
squeue
```

## Paso 4: Prediccion. CDS, tRNA y rRNA.

Ejecutaremos prodigal para predecir los CDS:

```
prodigal -a canu.faa -d canu.fna -f gff -o canu.gff -i canu.fasta
```
Ejecutaremos tRNAscan-SE para identificar los tRNA:
```
tRNAscan-SE -B -o canu.trna canu.fasta
tRNAscan-SE -B -o spades.trna spades.fasta
```
Ejecutaremos barrnap para identificar los rRNAs:
```
barrnap canu.fasta -o canu.rrna
barrnap spades.fasta -o spades.rrna
```
Ejecutamos blastp para anotar nuestros genes:
```
blastp -num_threads 10 -db home/dbs/blast/uniprot_sprot.fasta -num_descriptions 5 -num_alignments 2 -evalue 1e-5 -query canu.faa -out canu.bp
blastp -num_threads 10 -db home/dbs/blast/uniprot_sprot.fasta -num_descriptions 5 -num_alignments 2 -evalue 1e-5 -query spades.faa -out spades.bp


```
Script de anotación:








