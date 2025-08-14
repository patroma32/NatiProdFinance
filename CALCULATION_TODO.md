**AVERTISSEMENT IMPORTANT :** Le fichier `previsionnel_nettoye.json` qui accompagne ce document a une **structure corrigée** et des **erreurs d'arrondi numériques ont été nettoyées**. Cependant, les calculs décrits ci-dessous (**Compte de Résultat**, **salaires**, etc.) n'ont **PAS** été implémentés car la logique métier (ex: taux d'imposition, taux de charges sociales) n'est pas disponible. La section `results` de ce fichier ne doit donc **PAS** être considérée comme financièrement valide.

---

# Liste des Corrections à Apporter au Script de Génération

Ce document liste les corrections et les calculs à implémenter dans le script qui génère le fichier `belnati_previsionnel_corrige.json`.

## 1. Compléter le Compte de Résultat (`incomeStatement`)

Le compte de résultat est actuellement incomplet. Il est nécessaire d'implémenter la logique pour calculer les champs suivants (qui sont actuellement `null`) pour chaque année.

-   **`charges_externes`**: Doit être la somme de tous les champs dans `charges_externes_detail`.
-   **`amortissements`**: La valeur de l'amortissement (`1750`) est présente dans `parameters.fixedCosts` mais n'est pas reportée ici.
-   **`charges_personnel`**: Doit être la somme de `salaires_bruts` et `charges_sociales`.
-   **`salaires_bruts`**: Doit être calculé à partir des données de la section `parameters.payroll` pour l'année correspondante.
-   **`charges_sociales`**: Doit être calculé sur la base des salaires bruts (un taux de charges patronales doit probablement être défini dans les paramètres).
-   **`total_impots_taxes`**: Doit être la somme des champs dans `impots_taxes`.
-   **`valeur_ajoutee` (VA)**: `production_totale` - `consommations_matieres` - `autres_achats` - `charges_externes`.
-   **`ebe` (Excédent Brut d'Exploitation)**: `valeur_ajoutee` - `charges_personnel` - `total_impots_taxes`.
-   **`resultat_exploitation`**: `ebe` + `autres_produits_exploitation` - `autres_charges_exploitation` - `amortissements`.
-   **`resultat_courant`**: `resultat_exploitation` + `resultat_financier`.
-   **`resultat_net`**: `resultat_courant` + `resultat_exceptionnel` - `impot_benefices` (l'impôt sur les bénéfices doit aussi être calculé).
-   **`caf` (Capacité d'autofinancement)**: `resultat_net` + `amortissements` (simplifié).

## 2. Corriger les Problèmes de Précision Numérique

Dans le tableau `monthlyTreasury`, de nombreux nombres ont des problèmes de précision.

-   **Action :** Utiliser un type de données `Decimal` ou gérer les montants en centimes (entiers) pour tous les calculs financiers afin d'éviter les erreurs d'arrondi. Tous les chiffres monétaires dans le JSON final devraient être arrondis à 2 décimales, comme cela a été fait dans le fichier `belnati_previsionnel_corrige.json`.

## 3. Calculs de Trésorerie (`monthlyTreasury`)

-   **`decaissements_salaires`**: Ce champ est toujours à `0`. Il devrait être calculé sur la base des salaires nets mensuels (Salaires bruts - charges salariales).
