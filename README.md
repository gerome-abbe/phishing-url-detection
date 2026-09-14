# Détection d'URL de phishing : quand 100 % ne veut rien dire

Classification supervisée sur 235 795 URLs. Les modèles atteignent un score parfait.
Le vrai travail a été de comprendre pourquoi ce score ne prouve rien.

---

## Contexte

Projet réalisé en trinôme dans le cadre du module d'algorithmes en apprentissage
artificiel, M2 Recherche Opérationnelle et Optimisation, Université Toulouse III Paul
Sabatier et ENAC. Module suivi à l'ISAE-SUPAERO, année 2025-2026.

Contribution partagée sur l'ensemble des étapes.

## Le problème

Distinguer une URL de phishing d'une URL légitime à partir de caractéristiques extraites
de l'URL et du code source de la page.

Le jeu de données est le PhiUSIIL Phishing URL Dataset (UCI Machine Learning Repository).
Il contient 235 795 URLs et 54 variables : longueurs, compteurs de caractères, indicateurs
de structure HTML, scores de similarité.

Les erreurs n'ont pas le même coût. Un faux négatif expose l'utilisateur à une attaque.
Un faux positif bloque un site légitime. Ce déséquilibre oriente le choix des métriques
vers le rappel et le F1 plutôt que vers la seule exactitude.

## La démarche

Exploration du jeu de données, typologie des variables, contrôle des valeurs manquantes.

Création de trois variables interprétables : nombre de sous-domaines, ratio entre la
longueur de l'URL et celle du domaine, proportion de chiffres dans le domaine.

Préparation via un `ColumnTransformer` intégré dans un `Pipeline`. L'encodage et la
normalisation sont appris uniquement sur les données d'entraînement, ce qui évite toute
fuite vers le jeu de test.

Comparaison de deux modèles : une régression logistique comme référence interprétable, et
une forêt aléatoire pour capter les interactions entre variables.

Réglage d'hyperparamètres par validation croisée, puis analyse de l'importance des
variables sur le meilleur modèle.

## Ce que l'analyse a révélé

**Les deux modèles atteignent un score quasi parfait.** F1 et ROC-AUC valent 1,0000 pour
la forêt aléatoire. Ce résultat n'est pas un succès, c'est un signal d'alerte.

**Le jeu de données est très peu bruité.** Plusieurs variables sont des indicateurs de
sécurité presque parfaits. Dans ce jeu de données, la totalité des sites légitimes sont
en HTTPS, contre la moitié des sites de phishing. Quatre sites légitimes sur cinq portent
des mentions de copyright, contre un sur vingt côté phishing. J'ai quantifié ce phénomène
avec un score de séparation par variable, qui confirme le très faible recouvrement entre
classes.

**La convention d'annotation est contre-intuitive.** Dans le fichier source, le label 1
désigne une URL légitime et le label 0 une URL de phishing, à rebours de l'usage courant
en détection. Une lecture non vérifiée inverse toute l'interprétation métier sans dégrader
aucune métrique, puisque le modèle apprend aussi bien la relation inverse. Le notebook
ré-encode donc explicitement la cible après vérification sur des exemples réels.

C'est le point central du projet. Un score parfait ne valide jamais la compréhension du
problème.

## Ce que ce projet démontre

Chaîne complète de traitement de données tabulaires, du nettoyage à l'interprétation.

Construction de pipelines scikit-learn sans fuite de données.

Choix de métriques guidé par le coût métier des erreurs plutôt que par l'exactitude brute.

Lecture critique d'un jeu de données public et de sa documentation.

## Périmètre

Travail en trinôme, contribution partagée sur toutes les étapes.

Le réglage d'hyperparamètres reste volontairement réduit. Son objet est méthodologique, il
ne cherche pas à gagner des points sur un score déjà saturé.

Sur un trafic web réel, les performances seraient nettement inférieures. Le recouvrement
entre classes serait plus fort, les variables expertes pas toujours disponibles, et la
dérive des techniques d'attaque imposerait un réentraînement régulier.

## Outils

Python, pandas, scikit-learn, seaborn, matplotlib, Jupyter.

## Code

Notebook complet, exécuté, avec les figures et les résultats.

[Voir le notebook](TP_phishing_corrige.ipynb)

Les données ne sont pas incluses dans le dépôt. Elles sont disponibles sur le UCI Machine Learning Repository : https://archive.ics.uci.edu/dataset/967/phiusiil+phishing+url+dataset
