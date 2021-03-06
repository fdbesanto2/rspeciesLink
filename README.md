# Downloading speciesLink data from R

Installing and loading the package:

`devtools::install_github("saramortara/rspeciesLink")`

```{r setup}
library(rspeciesLink)
#devtools::load_all() # for development
```

This package downloads information from the [speciesLink API](http://api.splink.org.br/). It generates the desired url and uses functions from `jsonlite` package GET the url and save the output as a csv file. The speciesLink API is a courtesy of Sidnei de Souza from [CRIA](http://www.cria.org.br/) (Centro de Referência em Informação Ambiental) :)


See `?rspeciesLink` for all search options. 

## Example 1: basic search

Search for records of *Eugenia platyphylla* and *Chaetocalyx acutifolia* in speciesLink API. Same as: [http://api.splink.org.br/records/ScientificName/Eugenia%20platyphylla/Chaetocalyx%20acutifolia/scope/plants](http://api.splink.org.br/records/ScientificName/Eugenia%20platyphylla/Chaetocalyx%20acutifolia/scope/plants)

```{r sp}
sp1 <- "Eugenia platyphylla"
sp2 <- "Chaetocalyx acutifolia"
```

Setting scope `"plants"`. 

```{r ex01}
ex01 <- rspeciesLink(filename = "ex01",
                   scientificname =  c(sp1, sp2),
                   Scope="plants")
```

Checking search output. 

```{r ex01-check01}
head(ex01$data)
dim(ex01$data)
str(ex01$data)
```

Checking if required species are in the output. 

```{r ex01-check02}
# especies requisitadas estao no banco
## lista de especies da busca
ex01.sp <- unique(ex01$data$scientificname)
## checando se as sp requisitadas estão no banco --> TRUE :)
c(sp1, sp2)%in%ex01.sp
```

Checking colnames in data output.

```{r ex01-check03, eval=FALSE}
names(ex01$data)
```

```{r colnames, echo=FALSE}
knitr::kable(data.frame(columns=sort(names(ex01$data))))
```

## Example 2: specifying collection of origin and specimens with image

Search for *Rauvolfia selowii* and *Cantinoa althaeifolia*. Now using `collectioncode` and `Images` arguments.

Same as: [http://api.splink.org.br/records/CollectionCode/uec/scientificname/Rauvolfia%20sellowii/Cantinoa%20althaeifolia/Images/yes](http://api.splink.org.br/records/CollectionCode/uec/scientificname/Rauvolfia%20sellowii/Cantinoa%20althaeifolia/Images/yes)

```{r ex02}
ex02 <- rspeciesLink(filename = "ex02",
                   collectioncode = "uec",
                   scientificname = c("Rauvolfia sellowii", "Cantinoa althaeifolia"),
                   Images="Yes")
```

Checking again if species are in the search. 

```{r ex02-check01}
# de novo especies nao estao no output
c("Rauvolfia sellowii", "Cantinoa althaeifolia")%in%ex02$data$scientificname
```

Checking url used in the search. 

```{r ex02-check02}
ex02$url
# faz a busca
head(ex02$data)
dim(ex02$data)
str(ex02$data)
```

Is data only from UEC collection?

```{r ex02-check03}
# checando o campo collectioncode
unique(ex02$data$collectioncode)
```

## Example 3: testing coordinates quality selection

For species *Tillandsia stricta*. 

```{r ex03}
ex03 <- rspeciesLink(filename = "ex03",
                   scientificname = "Tillandsia stricta",
                   Coordinates = "Yes",
                   CoordinatesQuality = "Good")
```

Checking if species is in the output.

```{r ex03-check01}
"Tillandsia stricta"%in%ex03$data$scientificname
```

Checking url and output.

```{r ex03-check02}
ex03$url
# faz a busca
dim(ex03$data) # 1623
head(ex03$data)
```

Now with another selection of coordinate quality. 

```{r ex03b}
# outra selecao de qualidade de coordenadas
ex03b <- rspeciesLink(filename = "ex03b",
                   scientificname = "Tillandsia stricta")
                   #coordinatesQuality = "Bad")
```

Checking url and output.

```{r ex03b-check01}
ex03b$url
# faz a busca
dim(ex03b$data) # 1762
head(ex03b$data)
```

## Example 4: Only plant species in IUCN Red List in a particular geographic area

This example searches for 100 herbarium plants collected in Mariana county (Minas Gerais state, Brazil) that are in the IUCN Red List. It also checks for synonyms on [Flora do Brasil 2020](http://floradobrasil.jbrj.gov.br/reflora/listaBrasil/PrincipalUC/PrincipalUC.do;jsessionid=4887DC37EAB2ECF4A6754924CFD60AFB#CondicaoTaxonCP), the official plant list for taxonomic nomenclature.  

```{r ex04}
ex04 <- rspeciesLink(filename = "ex04",
                     Scope="plants", 
                     basisofrecord = "PreservedSpecimen",
                     county="Mariana", 
                     stateprovince = c("Minas Gerais", "MG"),
                     country=c("Brazil", "Brasil"),
                     Synonyms = "flora2020",
                     RedList=TRUE,
                     MaxRecords = 100)
```


## General check 

### Checking if files were written on disk

Listing files on list. 

```{r check-resu}
resu <- list.files("results", pattern = '.csv', full.names=TRUE)
```

All outputs are readable.

```{r read-resu}
all.resu <- lapply(resu, read.csv)
```


