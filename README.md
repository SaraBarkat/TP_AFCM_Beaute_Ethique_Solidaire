# README - TP AFCM : Beauté Éthique & Solidaire


## Objectif

L'objectif de ce TP est de déterminer des **associations pertinentes entre les questions et les réponses** du questionnaire IHM sur le e-commerce cosmétique éthique afin de comprendre les habitudes, attentes et préférences des utilisateurs, et proposer des recommandations pour la plateforme.

## Contenu du dépôt

* `IHM_responses.csv` : dataset des réponses du questionnaire
* `AFCM_analysis.R` : script R contenant toutes les étapes d'analyse
* `README.md` : ce fichier explicatif

## Questionnaire utilisé

* Titre : *Vers un e-commerce cosmétique éthique et ergonomique*
* Lien : [Questionnaire Google Forms](https://docs.google.com/forms/d/e/1FAIpQLSfr1t9diMw25555ApLf0lg3a_L2W-gW5Iu_k42jtYu4JphaSA/viewform)
* Questions principales :

  * Tranche d’âge
  * Genre
  * Fréquence d’achat de produits cosmétiques
  * Lieu principal d’achat
  * Influence de l’éthique sur les achats
  * Intérêt pour une plateforme éthique
  * Types de produits souhaités
  * Fonctionnalités utiles
  * Éléments importants pour l’interface
  * Suggestions pour améliorer l’expérience utilisateur

## Étapes pour réaliser le TP

### 1. Préparation de l'environnement

```R
install.packages("FactoMineR")
install.packages("factoextra")
install.packages("ggplot2")
library(FactoMineR)
library(factoextra)
library(ggplot2)
```

### 2. Importation des données

```R
data <- read.csv("IHM_responses.csv", header = TRUE, sep = ",")
head(data)
str(data)
```

### 3. Prétraitement des données

* Sélectionner uniquement les colonnes pertinentes
* Convertir toutes les colonnes en facteurs

```R
data_mca <- data[, c("Quelle.est.votre.tranche.d.âge.",
                     "Quel.est.votre.genre.",
                     "À.quelle.fréquence.achetez.vous.des.produits.cosmétiques.",
                     "Achetez.vous.vos.produits.cosmétiques.principalement.",
                     "L.aspect.éthique.des.produits..influence.t.il.vos.achats.",
                     "Seriez.vous.intéressé.e.par.une.plateforme.vendant.unequement.des.produits.non.boycottés.et.éthiques.",
                     "Quels.types.de.produits.souhaiteriez.vous.trouver.dans.l.application.",
                     "Selon.vous,.quelles.fonctionnalités.seraient.utiles.sur.ce.type.d.application.",
                     "Quels.éléments.de.l.interface.vous.semble.nt.les.plus.importants.pour.une.bonne.expérience.utilisateur.")]

data_mca[] <- lapply(data_mca, as.factor)
```

### 4. Statistiques descriptives

```R
summary(data_mca)
sapply(data_mca, table)
```

* Visualisation des fréquences

```R
for (col in colnames(data_mca)) {
  ggplot(data_mca, aes_string(x = col)) +
    geom_bar(fill="skyblue") +
    theme_minimal() +
    ggtitle(paste("Fréquence de", col)) +
    xlab(col) + ylab("Nombre de réponses") -> p
  print(p)
}
```

### 5. Transformation en tableau disjonctif

```R
data_disj <- tab.disjonctif(data_mca)
head(data_disj)
```

### 6. Réalisation de l'AFCM

```R
res_mca <- MCA(data_mca, graph = FALSE)
summary(res_mca)
```

### 7. Analyse des valeurs propres

```R
eig_values <- res_mca$eig
print(eig_values)
fviz_screeplot(res_mca, addlabels = TRUE, ylim = c(0, 50))
```

### 8. Visualisation des résultats

* Biplot individus-modalités

```R
fviz_mca_biplot(res_mca, repel = TRUE)
```

* Nuage des individus par âge

```R
fviz_mca_ind(res_mca, col.ind = data_mca$Quelle.est.votre.tranche.d.âge., palette = "jco", repel = TRUE)
```

* Nuage des modalités (cos²)

```R
fviz_mca_var(res_mca, col.var = "cos2", gradient.cols = c("#00AFBB", "#E7B800", "#FC4E07"), repel = TRUE)
```

### 9. Analyse des contributions

```R
contrib_var <- res_mca$var$contrib
contrib_ind <- res_mca$ind$contrib
print(contrib_var)
print(contrib_ind)
```

### 10. Associations entre modalités

```R
coord_mod <- res_mca$var$coord
print(coord_mod)
```

### 11. Tableau de contingence et AFC

```R
tab_cont <- table(data_mca$À.quelle.fréquence.achetez.vous.des.produits.cosmétiques.,
                  data_mca$Seriez.vous.intéressé.e.par.une.plateforme.vendant.unequement.des.produits.non.boycottés.et.éthiques.)
print(tab_cont)
res_ca <- CA(tab_cont, graph = FALSE)
fviz_ca_biplot(res_ca, repel = TRUE)
```

### 12. Visualisations additionnelles avec factoextra

```R
fviz_mca_var(res_mca, geom = "arrow", gradient.cols = c("blue", "red"))
fviz_mca_ind(res_mca, geom = "point", col.ind = data_mca$À.quelle.fréquence.achetez.vous.des.produits.cosmétiques., palette = c("blue", "red"))
```

### 13. Interprétation et conclusions

* Identifier les axes principaux et questions les mieux représentées
* Déterminer les associations pertinentes entre modalités
* Proposer des recommandations pour l’application e-commerce (ex. filtres éthiques, recommandations personnalisées, interface claire et moderne)

---

## Notes importantes

* Vérifier les modalités rares (<2 réponses) et les regrouper si nécessaire
* Toutes les variables utilisées doivent être qualitatives/factorielles
