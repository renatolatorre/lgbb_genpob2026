# Escaneos genómicos
Con los escaneos genómicos podemos explorar parámetros a lo largo de un genoma a fin de encontrar regiones con valores extremos. Cuando buscamos posibles señales de selección, se recomienda no excluir regiones codificantes o reguladoras. Por otro lado, si se recomienda incluir solo regione de alta calidad, obviando regiones con bajos puntajes de mapeo, profundidad de secuenciación excesivamente baja/alta, y regiones de baja complejidad que puedan confundir los análisis.

## Estimación de 2d-SFS
El espectro de frecuencia de sitios de dos dimensiones (2d SFS) es una extensión del 1d SFS para dos poblaciones. Primero, se obtiene el SFS para cada población.
```bash
bash 1-1fsfs.sh
```
Repetir este proceso para los _n_ grupos en evaluación. Luego, construimos el 2d SFS para comparar en pares de la siguiente manera:
```bash
bash 2-2dsfs.sh
```
De manera similar al análisis de cada población, el 2d SFS nos permite estimar parámetros de diversidad genética entre poblaciones, tanto a escala global como en ventanas.
```bash
bash 3-differentiation.sh
```
El análisis por ventanas nos permite comparar la tendencia general (_background_) con casos anómalos locales (_outliers_).

## Análisis de enriquecimiento funcional de genes
Un análisis de enriquecimiento funcional de genes se refiere a la exploración de patrones biológicos sobrerrepresentados en genes seleccionados bajo algún criterio a lo largo del genoma. En este caso, el criterio es que se encuentren cerca a alguna señal de selección, por lo que el análisis nos permitirá explorar las tendencias de mecanismos moleculares y procesos biológicos que podrían estar bajo selección.

```r
### Packages
library(topGO)
### Working dir
setwd("/path/to/working_directory/")

## Upload data
significant_genes <- "significant_list.txt"
geneID2GO <- readMappings(file = "gene_terms.txt")
GO2geneID <- inverseList(geneID2GO)
geneNames <- names(geneID2GO)

## Gene functional enrichment analysis
Significant <- read.table(file = significant_genes, sep = "\t")
Significant <- as.character(Significant[,1])
geneList <- factor(as.integer(geneNames %in% Significant))
names(geneList) <- geneNames

# BP terms
BPGOdata <- new("topGOdata", ontology = "BP", allGenes = geneList, annot = annFUN.gene2GO, gene2GO = geneID2GO, nodeSize = 10)
resultBPFisher <- runTest(BPGOdata, algorithm = "weight01", statistic = "fisher")
# resultBPFisher # Put the number of GO terms in topNodes to show all results
allBPRes <- GenTable(BPGOdata, classicFisher = resultBPFisher, orderBy = "classicFisher", ranksOf = "classicFisher", topNodes = 30)
write.table(allBPRes, file = "BP_Table_Significant.txt", sep = "\t", quote = FALSE)

# MF terms
MFGOdata <- new("topGOdata", ontology = "MF", allGenes = geneList, annot = annFUN.gene2GO, gene2GO = geneID2GO, nodeSize = 10)
resultMFFisher <- runTest(MFGOdata, algorithm = "weight01", statistic = "fisher")
# resultMFFisher # Put the number of GO terms in topNodes to show all results
allMFRes <- GenTable(MFGOdata, classicFisher = resultMFFisher, orderBy = "classicFisher", ranksOf = "classicFisher", topNodes = 30)
write.table(allMFRes, file = "MF_Table_Significant.txt", sep = "\t", quote = FALSE)

# CC terms
CCGOdata <- new("topGOdata", ontology = "CC", allGenes = geneList, annot = annFUN.gene2GO, gene2GO = geneID2GO, nodeSize = 10)
resultCCFisher <- runTest(CCGOdata, algorithm = "weight01", statistic = "fisher")
# resultCCFisher # Put the number of GO terms in topNodes to show all results
allCCRes <- GenTable(CCGOdata, classicFisher = resultCCFisher, orderBy = "classicFisher", ranksOf = "classicFisher", topNodes = 30)
write.table(allCCRes, file = "CC_Table_Significant.txt", sep = "\t", quote = FALSE)
```
Visualizar los resultados e identificar los términos de Ontología de Genes significativos.
