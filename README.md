# PARCIAL 1

## Punto 1

### 1.
Descargue los archivos del NCBI y los movi a mi carpeta con el comando ```mv ../../../Downloads/sequence* ./``` puesto que todos se titulaban sequence (n).fasta.

Luego los contactene con el comando ```cat sequence* > sequence_misticetos.fasta```

### 2.
Para cambiar el nombre use el comando sed de la siguiente forma: ```sed -i -E 's/^(>\w+.\w+)\s(\w+)\s(\w+)\s.*/\1_\2_\3/g' sequence_misticetos.fasta```, indicando que editara las lineas que inciaran con > y agrupando canda palabra en un parentesis, para luego pedir que el remplazo fueran el parentesis 1 que contenia el ID, el parentesis 2 que contenia el genero y el parentesis 3 que contenia el epiteto específico, todo separado por _. Con el parametro -i consegui que guardara el reemplazo en el mismo archivo.

### 4.
En el cluster: ```blastn -query query.fasta -task megablast -db trans_misticetos -outfmt 7 -word_size 7 -out blast_misticetos -num_threads 1```

### 5.
Al comparar la secuencia contenida en query.fasta con la base de datos creada con los fasta de misticetos se obtuvo una mayor similitud con la muestra AB201258.1_Balaenoptera_edeni, deduciendo así que la seq 1 proviene de la especie *Balaenoptera edeni*. Lllegue a esta conclución gracias a el resultado del e-value el cual fue de 0, indicando  seguridad de que su alineación no fue por azar. También con el Bit score que fue de 1062, siendo el más alto de todos e indicando una puntuación de alineación muy alta. Los demás parametros también fueron un precedente al tener un alineamiento de 584pb y % de identad del 99%, pero estos parametros fueron altos a su vez con las demás muestras.

## Punto 2

### 7.
Alineamiento muscle
```
#!/bin/bash
#SBATCH -p normal
#SBATCH -N 1
#SBATCH -n 10
#SBATCH -t 0-00:20
#SBATCH -o alineamiento_muscle.out
#SBATCH -e alineamiento_muscle.err

module load muscle/3.8.31
muscle -in sequence_misticetos2.fasta -out misticetos_muscle.fasta
```

### 8.
Código Arbol Máxima Verosimilitud

```
iqtree -s FcC_supermatrix.fas -m GTR+I+G -bb 1000 -pre misticetos_muscle2
```

### 9.
![Arbol](https://github.com/Ana-Osorio-B/parcial_bioinformatica/blob/main/misticetos_muscle2.jpeg)

El arbol se realizo con enraizamiento del punto medio, dejando como grupo más lejano a la especie *Caperea marginata* que se organizó en un grupo monofilético. La siguiente especie en separarse fue *Balaena mysticetus*, quien solo contaba con un representante, pero de l que podemos inferir tien más cercania con el género *Balaenoptera*. El genero *Balaenoptera* conformo un grupo monofilético soportado al 100%. Las distancia evolutivas evidenciadas con el largo de las ramas indican efectivamente que se trata de especies diferentes, pero los cambios empiezan a ser menores para *Balaenoptera borealis, Balaenoptera brydei* y *Balaenoptera enei*. Finalmente en el clado más reciente vemos a *Balaenoptera enei* junto a la seq1, lo que refuerza lo dicho anteriormente con el análisis de BLAST. Todos los bootstrap tiene valores muy buenos (99 y 100) lo que indica robutez de todo el arbol.

## Punto 3

### 1.
Hay 5588 secuencias con un solo exón. Determinado con el codigo: ```grep '^>' orf_final_trans.fasta |awk '!/,/ {print}' | wc -l```

### 2.
```
grep '^>' orf_final_trans.fasta |awk '!/,/ {print}' > exon.txt

sed -E 's/>(\w+\s)\w+:(\w+)\-(\w+.*)/\1\2 \3/g' exon.txt |  sed -E 's/>(\w+\s)\w.*:\w+\-\w+.*(\w+)\-(\w+.*)/\1 \2 \3/g' > exon2.txt

awk 'BEGIN{m=0};{s =($2-$3)};{if (s>m) {m=s;L=$0}};END{{print L};{print m}}' exon2.txt

awk 'BEGIN{m=0};{s =($2-$3)};{if (s<m) {m=s;L=$0}};END{{print L};{print m}}' exon2.txt
YHR099W 302761 313995
```
El primer awk fue para ver los de forward, y el segundo para reverse, puesto que los numeros en el segundo caso serian negativos.

* La más larga es YLR106C con 14732 residuos.

### 3.
Las secuencias de proteinas no tienen intrones ya que han pasado por transducción y traducción, durante estos procesos se reconocen en las secuencias de nucleótidos los codones de inicio y parada, cortando los intrones y no llegando a traducirlos a proteinas.
