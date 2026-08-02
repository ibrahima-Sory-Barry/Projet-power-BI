[README.md](https://github.com/user-attachments/files/30624967/README.md)
# CongoTopFashionplus — Rapport Power BI

Rapport Power BI (format **PBIP**) pour **Congo Top Fashion+**, une entreprise
de mode/retail. Le projet couvre l'ensemble du cycle de vie retail : produits,
ventes, stocks, clients, fournisseurs, marketing et KPI.

## Structure du projet

```
CongoTopFashionplus.pbip                 → fichier racine (lie le rapport au modèle)
CongoTopFashionplus.SemanticModel/       → modèle sémantique (tables, relations, mesures DAX)
CongoTopFashionplus.Report/              → rapport visuel (5 pages)
Copy of CongoTopFashion_Base_Donnees.xlsx → classeur Excel source (import)
_brief/report-spec.md                    → spec de conception verrouillée (design brief)
```

## Modèle sémantique

24 tables métier en français (fr-FR), importées depuis le classeur Excel :
`Produits`, `Commandes`, `Détails_Commandes`, `Clients`, `Boutiques`, `Stocks`,
`Fournisseurs`, `Achats_Fournisseurs`, `Livraisons`, `Retours`, `Paiements`,
`Promotions`, `Campagnes_Marketing`, `Objectifs_Commerciaux`, `Employés`,
`Marques`, `Catégories`, `Sous_Catégories`, `Inventaires`, `KPI_Global`,
`KPI_Annuel`, `Paramètres`, `Relations`, `Dictionnaire_Données` — plus les
tables de dates locales générées automatiquement par Power BI.

### Relations ajoutées

Le modèle importé n'avait pas toutes les relations nécessaires pour croiser
les ventes/stocks avec les boutiques et les produits. Relations créées :

- `Commandes.ID_Boutique → Boutiques.ID_Boutique`
- `Détails_Commandes.ID_Produit → Produits.ID_Produit`
- `Stocks.ID_Produit → Produits.ID_Produit`
- `Stocks.ID_Boutique → Boutiques.ID_Boutique`
- `Commandes.ID_Promotion → Promotions.ID_Promotion`
- `Commandes.ID_Employé_Vendeur → Employés.ID_Employé`

### Mesures DAX ajoutées

Aucune mesure n'existait dans le classeur d'origine (uniquement des colonnes
brutes). Mesures créées par table :

| Table | Mesures |
|---|---|
| `KPI_Annuel` | Total CA Réalisé, Total Bénéfice Réalisé, Marge Nette %, Nombre de Commandes Annuel, Panier Moyen Annuel, Clients Actifs Annuel, Taux de Retour % Annuel, CA YoY Variance % |
| `Commandes` | CA Réalisé (Total), Bénéfice Réalisé (Total), Nombre de Commandes, Panier Moyen, CA Avec Promotion, CA Sans Promotion, Taux de Retour % |
| `Détails_Commandes` | CA Lignes, Bénéfice Lignes, Quantité Vendue |
| `Stocks` | Nombre de Références Stock, Valeur Stock Totale, Stock Actuel Total, Total Retourné (Stocks), Nb Références en Rupture, Écart au Stock Minimum |
| `Produits` | Marge Totale (Produits), Popularité Totale, Rotation Stock Moyenne |
| `Clients` | Nombre de Clients, Clients Actifs, Valeur Totale Clients |

### Colonnes calculées ajoutées (table `Clients`)

- `Bucket_Récence` — regroupe la récence d'achat en tranches (0-30j, 31-90j, 91-180j, 180j+)
- `Année_Inscription` — année d'inscription du client (dérivée de `Date_Inscription`)

## Rapport — 5 pages

Style visuel : **Dark Atelier** (thème sombre premium, accent or, titres en
serif Georgia). Détail complet du design (palette, layout, justification par
page) dans [`_brief/report-spec.md`](_brief/report-spec.md).

1. **Performance Globale — Vue d'ensemble** : KPI globaux (CA, bénéfice, marge,
   panier moyen), tendance pluriannuelle, variance YoY, table de risque,
   boutons de navigation vers les 4 autres pages.
2. **Ventes** : filtres (période, canal, paiement, boutique), tendance
   mensuelle CA/bénéfice, ventilation par canal/paiement/boutique, impact des
   promotions, top produits.
3. **Stocks** : valeur de stock par boutique, rotation produits, retours,
   alertes de rupture, matrice détaillée des références à risque.
4. **Produits** : tendance de CA par catégorie (petits multiples), marges par
   marque, produits tendance/premium, répartition par genre.
5. **Clients** : segmentation RFM (récence/fréquence/valeur), activité par
   cohorte d'inscription, répartition par ancienneté de récence, top clients.

## État actuel / prochaines étapes

- ✅ Modèle validé, relations et mesures ajoutées.
- ✅ Fichiers PBIR validés (`powerbi-report-author validate` : 0 erreur).
- ⏳ **Vérification visuelle dans Power BI Desktop en attente** — ouvrez le
  fichier `CongoTopFashionplus.pbip` pour charger le modèle mis à jour et
  contrôler le rendu des 5 pages (mise en page, couleurs, filtres).
- Pistes d'amélioration possibles : synchronisation des slicers Boutique
  entre pages, filtre Top-N sur le graphique en petits multiples de la page
  Produits, table dédiée aux mesures (measure table) pour organiser le modèle.

## Outils utilisés pour la construction

- Édition directe des fichiers **TMDL** pour le modèle sémantique.
- Génération programmatique des fichiers **PBIR** (pages/visuels) via un
  script Node.js consommant le design brief verrouillé.
- `powerbi-report-author` (CLI) pour la validation des fichiers PBIR.
- `powerbi-desktop` (CLI) pour le rechargement et les captures d'écran dans
  Power BI Desktop.
