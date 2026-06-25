## CaisseOS 3.0 est arrivé ##

╔══════════════════════════════════════════════════════════════════════╗
║  VERSION 3.0  ·  Écran Client  ·  Pad Numérique  ·  Import Backup  ║
║  ────────────────────────────────────────────────────────────────── ║
║  Un seul fichier HTML  ·  Zéro serveur  ·  Zéro abonnement         ║
╚══════════════════════════════════════════════════════════════════════╝


> **CaisseOS** est un système de caisse enregistreuse complet qui tient dans un seul fichier HTML.  
> Pas d'installation. Pas de connexion internet. Pas d'abonnement. Ouvrez le fichier, travaillez.

---

## Ce qui est nouveau dans la version 3.0

La version 3.0 apporte trois nouvelles fonctionnalités majeures qui transforment CaisseOS en une solution encore plus professionnelle et adaptée aux commerces du quotidien.

### 📺 Écran client en temps réel

Un second affichage dédié au client, synchronisé instantanément avec la caisse. Le client voit exactement ce qui est scanné, le total en direct, le mode de paiement et un écran de remerciement après chaque vente.

### 🔢 Pad numérique tactile

Un clavier numérique virtuel pensé pour les écrans tactiles et les tablettes. Modifiez les quantités, saisissez une remise ou changez un prix à la volée, sans jamais toucher au clavier physique.

### 📂 Import de sauvegarde

Restaurez votre compte en quelques secondes depuis votre fichier de sauvegarde JSON. Glissez-déposez le fichier sur l'écran de connexion et reconnectez-vous immédiatement — toutes vos données, tous vos articles, tout votre historique.

---

## Table des matières

- [Présentation générale](#présentation-générale)
- [Fonctionnalités v3.0 — Détail complet](#fonctionnalités-v30--détail-complet)
  - [Écran client](#-écran-client)
  - [Pad numérique](#-pad-numérique)
  - [Import de sauvegarde](#-import-de-sauvegarde)
  - [Fonctionnalités v2.0 — Rappel complet](#fonctionnalités-v20--rappel-complet)
  - [Caisse POS](#-caisse-pos)
  - [Moyens de paiement](#-moyens-de-paiement)
  - [Cartes cadeaux](#-cartes-cadeaux)
  - [Programme de fidélité](#-programme-de-fidélité)
  - [Remboursements](#-remboursements)
  - [Tickets de caisse](#-tickets-de-caisse)
  - [Historique et statistiques](#-historique-et-statistiques)
  - [Réglages et multi-comptes](#-réglages-et-multi-comptes)
  - [Mode Noir et Blanc](#-mode-noir-et-blanc)
- [Architecture technique](#architecture-technique)
- [Modèle de données](#modèle-de-données)
- [Cas d'usage par commerce](#cas-dusage-par-commerce)
- [Installation](#installation)
- [Changelog complet](#changelog-complet)


---

## Présentation générale

CaisseOS naît d'un constat simple : la plupart des logiciels de caisse imposent des contraintes inutiles aux commerçants indépendants. Un abonnement mensuel de 30 à 200 euros, un serveur à maintenir, une connexion internet permanente, une formation technique.

CaisseOS fait le choix inverse. Tout tient dans un seul fichier HTML de moins de 170 Ko. On l'ouvre dans un navigateur, on crée un compte en 30 secondes, et on commence à vendre. Les données restent sur l'appareil, dans le stockage local du navigateur, sans jamais transiter par un serveur tiers.

### Chiffres clés

| Indicateur | Valeur |
|:---|:---|
| Format | Un seul fichier `.html` |
| Taille | Moins de 170 Ko |
| Dépendances externes | Zéro |
| Frameworks JavaScript | Aucun — Vanilla JS pur |
| Connexion internet requise | Non |
| Données envoyées à un serveur | Jamais |
| Temps d'installation | Zéro seconde |
| Prix | Gratuit, open-source |

---

## Fonctionnalités v3.0 — Détail complet

### 📺 Écran client

L'écran client est un second affichage entièrement dédié au client. Il se synchronise en temps réel avec la caisse grâce à l'API `BroadcastChannel` du navigateur, sans aucun serveur, sans réseau, sans latence.

#### Activation côté vendeur

Un bouton **📺 Écran client** apparaît dans la barre de navigation principale. Un clic ouvre un panneau de gestion qui génère automatiquement un **code de session à 6 caractères** (format alphanumérique sans caractères ambigus, par exemple `AB3K7P`). Ce code identifie le canal de communication entre la caisse et l'écran client.

Le vendeur peut ensuite ouvrir l'écran client directement dans un nouvel onglet ou une nouvelle fenêtre, idéal pour un second moniteur branché en HDMI ou DisplayPort. L'écran client peut aussi être ouvert sur n'importe quel autre appareil qui partage le même navigateur ou le même réseau avec le même fichier servi localement.

#### Connexion côté client

L'écran client s'ouvre sur une interface de connexion épurée. Le client — ou plus souvent le vendeur lors de la configuration initiale — saisit le code de session. La connexion s'établit instantanément via `BroadcastChannel`. Un délai de 4 secondes sans réponse déclenche un message d'erreur clair pour vérifier le code.

#### Les 5 états de l'écran client

**État Attente (Idle)**
Quand le panier est vide, l'écran affiche le nom et le logo de l'entreprise dans un format grand et lisible, accompagné d'une animation de points pour indiquer que le système est actif. C'est l'état par défaut entre deux ventes.

**État Panier (Cart)**
Dès qu'un article est ajouté au panier côté caisse, l'écran client se met à jour automatiquement. Chaque article apparaît avec son emoji, son nom, sa quantité et son prix ligne dans une liste animée. Un panneau latéral affiche le sous-total, la remise éventuelle en orange, la TVA incluse, et le **total en très grand** (police 54px) pour une lisibilité maximale même depuis une certaine distance. Si un client fidélité est associé à la vente, son nom et son solde de points apparaissent en bas du panneau.

**État Paiement (Payment)**
Quand le vendeur sélectionne un mode de paiement dans la modal d'encaissement, l'écran client bascule immédiatement vers un affichage centré : l'icône du mode de paiement en très grand (💳, 💵, 🎁, 🔀), le libellé du mode, et le total à payer en format 72px. Si le client paye en espèces et que la monnaie à rendre est calculée, elle s'affiche en vert sous le total.

**État Merci (Thanks)**
À la validation du paiement, l'écran client affiche pendant 5 secondes un écran de remerciement avec une animation, le message personnalisé de l'entreprise (configuré dans les réglages), et si un client fidélité était associé, le nombre de points gagnés lors de cette transaction et son nouveau total cumulé.

**État Connexion (Connect)**
L'écran initial de saisie du code, avec un champ de saisie stylisé en grands caractères espacés pour faciliter la lecture du code à distance.

#### Synchronisation en temps réel

Chaque modification du panier déclenche une diffusion immédiate vers l'écran client : ajout d'article, changement de quantité, suppression d'article, application d'une remise, association d'un client fidélité, saisie d'une note de commande. Le délai de synchronisation est inférieur à la milliseconde sur le même appareil.

#### Portée et compatibilité

Sur le même ordinateur avec deux moniteurs, la solution fonctionne parfaitement sans aucune configuration réseau. Pour deux appareils physiquement distincts, ils doivent accéder au même fichier depuis un serveur web partagé sur le même réseau local — par exemple via un simple serveur Python ou Node.js lancé en local. L'API `BroadcastChannel` est supportée par Chrome 54+, Firefox 38+, Edge 79+ et Safari 15.4+.

---

### 🔢 Pad numérique

Le pad numérique est un clavier virtuel conçu pour les environnements tactiles, les tablettes en position verticale et toute situation où le clavier physique est peu accessible. Il remplace avantageusement la saisie directe dans les champs pour les opérations les plus fréquentes.

#### Accès

Un bouton `🔢 Pad numérique` à bordure pointillée apparaît juste au-dessus des champs Remise et Note dans le pied de panier. Un clic fait surgir le pad depuis le bas de l'écran, aligné avec le panneau panier, avec une animation fluide.

#### Quatre modes de saisie

Le pad propose quatre onglets en haut, chacun correspondant à un usage différent.

**Mode Quantité**
La liste des articles du panier s'affiche en haut du pad. Touchez un article pour le sélectionner — il se met en surbrillance avec un liseré accent. Le display affiche la quantité actuelle. Tapez une nouvelle quantité, validez. Si vous entrez zéro, l'article est retiré du panier. Le pad vérifie le stock disponible avant d'appliquer le changement.

**Mode Remise**
Le champ de saisie affiche le pourcentage actuel de remise (0 si aucune). Tapez un nouveau pourcentage entre 0 et 100, validez. La remise s'applique immédiatement au total du panier et se met à jour dans le résumé. Le suffixe `%` s'affiche en permanence à droite du display pour rappeler l'unité.

**Mode Prix**
Permet de modifier le prix unitaire d'un article spécifique pour la vente en cours, sans changer le catalogue. Sélectionnez l'article dans la liste, tapez le nouveau prix avec décimales si nécessaire. Le changement est local à la transaction — le catalogue reste intact.

**Mode Note**
Saisie de la note de commande. Les chiffres s'ajoutent au texte, le bouton de suppression efface le dernier caractère, C efface tout. La note courante est pré-chargée dans le display à l'ouverture du mode.

#### Les touches

Le pad dispose de 13 touches : `7`, `8`, `9`, `4`, `5`, `6`, `1`, `2`, `3`, `C` (effacer tout), `0` (touche large), `⌫` (supprimer dernier), et `✓ Appliquer` (validation pleine largeur). Les touches ont un retour visuel à la pression et une animation de réduction légère pour simuler l'appui physique. La touche `⌫` passe en rouge au survol, la touche `C` est visuellement distincte.

#### Fermeture

Le pad se ferme par un clic sur le fond semi-transparent autour du pad, par le bouton `✕` en haut à droite, ou par la touche `Échap` du clavier. À la validation, il se ferme automatiquement après avoir appliqué le changement.

---

### 📂 Import de sauvegarde

La fonction d'import permet de restaurer un compte complet depuis un fichier de sauvegarde JSON généré par la fonction **Télécharger toutes les données** présente dans les réglages.

#### Zone d'import

Sur l'écran de connexion, en dessous des formulaires d'inscription et de connexion, une zone dédiée à l'import s'affiche avec un séparateur textuel `ou restaurer une sauvegarde`. Elle se compose d'une zone de glisser-déposer visuelle et d'un champ de sélection de fichier au clic.

#### Glisser-déposer

L'utilisateur peut faire glisser son fichier `.json` depuis l'explorateur de fichiers directement sur la zone. Celle-ci réagit visuellement au survol en changeant de couleur de bordure. Le fichier est lu localement via l'API `FileReader`, aucune donnée ne transite par un réseau.

#### Analyse et aperçu

Avant toute restauration, CaisseOS analyse le fichier et affiche un aperçu détaillé :

- Nom de l'entreprise et identifiant du compte
- Nombre d'articles dans le catalogue
- Nombre de transactions dans l'historique
- Nombre de cartes cadeaux
- Nombre de clients fidélité
- Date de la sauvegarde

Si un compte avec le même identifiant existe déjà dans le navigateur, un avertissement orange prévient que ce compte sera écrasé. L'utilisateur garde le choix de continuer ou d'annuler.

#### Formats supportés

L'import reconnaît trois formats de fichier :

Le format standard de `exportAllData()` contient un objet avec les clés `account`, `products`, `transactions`, `giftCards`, `loyaltyCustomers` et `exportDate`. C'est le format produit par CaisseOS lui-même.

L'objet compte direct est un export brut de l'entrée dans `pos_accounts`, contenant directement `username`, `products`, etc.

Le tableau de comptes est un export de la totalité du stockage local ; dans ce cas, CaisseOS charge le premier compte du tableau.

#### Gestion du mot de passe

Si la sauvegarde ne contient pas de champ mot de passe (exports d'anciennes versions ou exports partiels), CaisseOS invite l'utilisateur à en choisir un nouveau avant de finaliser la restauration. Cela garantit que le compte est toujours protégé.

#### Connexion automatique

Après confirmation, le compte est restauré dans le stockage local et la connexion s'effectue automatiquement — sans re-saisir l'identifiant ni le mot de passe. Un message de succès confirme la restauration, et l'interface de caisse s'ouvre directement.

---

## Fonctionnalités v2.0 — Rappel complet

### 🛒 Caisse POS

L'interface principale est divisée en deux zones. À gauche, une grille responsive d'articles avec leur emoji, leur catégorie, leur prix unitaire et leur stock restant. À droite, le panneau panier.

La **recherche instantanée** filtre les articles en temps réel dès la première lettre saisie. Les **filtres par catégorie** génèrent des boutons dynamiques basés sur les catégories réelles du catalogue. Il suffit d'un clic sur un article pour l'ajouter au panier.

Dans le panier, chaque ligne affiche le nom de l'article, des boutons `+` et `−` pour modifier la quantité, et le prix de la ligne. Une **remise en pourcentage** s'applique à l'intégralité du panier. Un champ **note de commande** est disponible pour chaque transaction.

La **gestion du stock** est automatique : chaque vente décrémente le stock, un article à stock nul est grisé et bloqué.

Les articles s'ajoutent directement depuis la caisse via une modal avec sélection d'emoji parmi 30 icônes, saisie du nom, du prix, de la catégorie et du stock initial.

### 💳 Moyens de paiement

Quatre modes d'encaissement sont disponibles, chacun avec son interface dédiée.

**Carte Bancaire** : confirmation simple, le terminal externe prend le relais.

**Espèces** : saisie du montant remis par le client. La monnaie à rendre s'affiche en grand, en vert si le montant est suffisant, en rouge si insuffisant. Le bouton de validation reste désactivé tant que le montant est inférieur au total.

**Carte Cadeau** : saisie du code de la carte. Vérification instantanée du solde disponible. Si le solde couvre exactement ou dépasse le total, la carte est débitée du montant exact et le reste reste disponible. Si le solde est insuffisant, un message indique le complément à payer.

**Paiement Mixte** : répartition libre entre CB, espèces et carte cadeau. Un récapitulatif en temps réel affiche les montants par mode et le reste à couvrir. La validation n'est possible que lorsque le total est entièrement couvert.

Un client fidélité peut être associé à n'importe quel mode de paiement en le recherchant par nom ou téléphone directement depuis la modal d'encaissement.

### 🎁 Cartes Cadeaux

Les cartes cadeaux disposent de leur propre section avec une grille visuelle. Chaque carte affiche son code, son solde restant sur le montant initial sous forme de barre de progression, et son statut : Active en teal, Partielle en orange, Épuisée en gris.

La création d'une carte propose des raccourcis de montants prédéfinis (10, 20, 25, 50, 100, 150, 200 euros) pour accélérer la saisie, ou une valeur libre. Le code est auto-généré au format `GC-XXXX-XXXX` (caractères sans ambiguïté) ou personnalisé. Un bénéficiaire et une occasion peuvent être renseignés. Une prévisualisation s'affiche avant la confirmation.

La fiche détail d'une carte montre l'historique de toutes les transactions où elle a été utilisée, avec les dates et montants débités. Une fonction de recharge permet d'ajouter du crédit à une carte existante.

Les statistiques de la section affichent le nombre de cartes créées, les cartes actives, la valeur totale émise et la valeur déjà consommée.

### ⭐ Programme de Fidélité

Le programme de fidélité est entièrement paramétrable depuis les réglages : nombre de points gagnés par euro dépensé, seuil de points pour déclencher une récompense, valeur en euros de chaque récompense.

Les clients sont inscrits avec leur nom, téléphone et email optionnel. Un aperçu de la formule en cours s'affiche dans les réglages pour visualiser l'impact des paramètres choisis.

Le classement des clients dans la section fidélité est automatique par nombre de points décroissant. Les trois premiers affichent un trophée. Une barre de progression indique l'avancement vers la prochaine récompense pour chaque client. Si un client a dépassé le seuil de points, un badge indique le nombre de récompenses disponibles.

La fiche client détaille son total dépensé, ses points actuels, ses récompenses disponibles, et les 10 dernières transactions. Les points peuvent être ajustés manuellement (positif ou négatif) avec un motif libre.

À chaque vente associée à un client fidélité, les points sont calculés automatiquement et attribués. La déduction est automatique en cas de remboursement.

### ↩ Remboursements

La gestion des remboursements s'ouvre depuis le ticket de caisse en cliquant sur le bouton **↩ Rembourser**, disponible pour toute transaction non entièrement remboursée.

La modal de remboursement affiche la liste des articles de la transaction avec les quantités déjà remboursées soustraites. Chaque article est sélectionnable individuellement via une case à cocher, et sa quantité à rembourser est ajustable. Des boutons Tout sélectionner et Tout désélectionner accélèrent les cas courants. Le montant total du remboursement se met à jour en temps réel.

Trois modes de remboursement sont disponibles. En espèces ou par CB, le remboursement est enregistré directement. En avoir ou carte cadeau, CaisseOS crée automatiquement une nouvelle carte cadeau au montant du remboursement, ou crédite une carte existante dont le code est saisi manuellement.

La restauration du stock est automatique pour chaque article remboursé. Les points fidélité sont déduits au prorata du montant remboursé. Un motif libre peut être saisi.

Dans l'historique, les ventes remboursées affichent un badge coloré : rouge foncé pour les remboursements complets, orange pour les remboursements partiels. Les entrées de remboursement apparaissent en rouge dans la liste avec leur propre bon imprimable.

### 🧾 Tickets de Caisse

Les tickets sont générés en HTML statique au format thermique 80mm avec la police `IBM Plex Mono` pour une fidélité maximale au rendu papier réel.

Chaque ticket contient l'en-tête avec le nom de l'entreprise et le téléphone, le numéro séquentiel sur quatre chiffres, la date et l'heure. Viennent ensuite les articles avec leur emoji, leur nom, leur quantité et le prix de ligne. La section totaux détaille le sous-total, la remise éventuelle, la TVA incluse et le total TTC.

Une section Règlement précise le mode de paiement avec ses détails (monnaie rendue pour les espèces, code carte pour les cadeaux, répartition pour les mixtes). Si un client fidélité était associé, une section Fidélité indique les points gagnés et le nouveau cumul. Si des remboursements ont eu lieu ultérieurement, ils apparaissent sur le ticket réimprimé. Le ticket se termine par le message personnalisé de l'entreprise et un code-barres décoratif.

L'impression utilise `window.print()` avec une règle CSS `@media print` qui masque l'interface et n'imprime que le ticket.

### 📊 Historique et Statistiques

Le tableau de bord statistiques affiche en permanence sept indicateurs : nombre de ventes totales, chiffre d'affaires brut, chiffre d'affaires net (après remboursements), panier moyen, nombre de remboursements, montant total remboursé, et chiffre d'affaires par carte bancaire.

La liste des transactions supporte la recherche textuelle (par nom d'article, numéro de ticket ou note) et le tri par date croissante, date décroissante, montant croissant ou montant décroissant.

Chaque ligne affiche le numéro de ticket, la date et l'heure, un résumé des articles, le total et un badge coloré indiquant le mode de paiement. Les remboursements apparaissent en rouge avec leur propre badge.

L'export CSV génère un fichier encodé en UTF-8 avec BOM pour une ouverture correcte dans Excel français, avec les colonnes numéro, date, articles, sous-total, remise, TVA, total, mode de paiement et note.

### ⚙️ Réglages et multi-comptes

CaisseOS supporte plusieurs comptes indépendants sur le même navigateur. Chaque compte a son propre catalogue, son propre historique, ses propres cartes cadeaux et ses propres clients fidélité. La session est persistante : au rechargement de la page, la reconnexion est automatique.

Les réglages permettent de modifier le nom et le téléphone de l'entreprise, le message de pied de ticket, le taux de TVA, les paramètres du programme de fidélité (taux, seuil, valeur récompense), le thème d'affichage, l'affichage ou non de la TVA sur les tickets, et le mot de passe.

La zone de danger propose l'effacement de l'historique (articles et cartes cadeaux conservés), le téléchargement de toutes les données en JSON et la suppression complète du compte.

### 🎨 Mode Noir et Blanc

Un toggle dans les réglages bascule instantanément l'ensemble de l'interface vers un thème monochrome noir sur blanc, sans aucun rechargement. Ce thème est conçu pour les terminaux fixes de comptoir, les environnements très éclairés et les situations où une esthétique professionnelle épurée est préférée. Toutes les fonctionnalités restent identiques, seules les couleurs d'accent disparaissent au profit du noir pur.

---

## Architecture technique


caisseOS.html                         ← Fichier unique autonome
│
├── <style>                           ← CSS complet intégré
│   ├── Variables CSS                 ← Thème dark (défaut) + thème B&W (.bw)
│   ├── Composants UI                 ← Boutons, inputs, modals, toasts
│   ├── Vues POS                      ← Grille, panier, cart footer
│   ├── Historique & stats            ← Stats bar, liste, badges
│   ├── Cartes cadeaux                ← Grille GC, statuts, barre progression
│   ├── Fidélité                      ← Liste clients, barre progression, médailles
│   ├── Remboursements                ← Items, méthodes, badges historique
│   ├── Réglages                      ← Sections, toggles
│   ├── Pad numérique (v3.0)          ← Overlay, touches, modes
│   ├── Écran client (v3.0)           ← 5 états, header, animations
│   └── Import backup (v3.0)          ← Zone drop, aperçu, preview
│
├── <body>                            ← HTML structuré en vues
│   ├── #view-auth                    ← Login + Register + Import backup
│   ├── #client-display               ← Écran client plein écran (v3.0)
│   ├── #app
│   │   ├── #topbar                   ← Logo · Entreprise · Nav · Écran client · Compte
│   │   └── #main-content
│   │       ├── #view-pos             ← Grille produits + panier + pad numérique
│   │       ├── #view-history         ← Stats + transactions
│   │       ├── #view-giftcards       ← Cartes cadeaux
│   │       ├── #view-loyalty         ← Clients fidélité
│   │       └── #view-settings        ← Paramètres
│   └── Modals
│       ├── #modal-display            ← Gestion écran client (v3.0)
│       ├── #numpad-overlay           ← Pad numérique (v3.0)
│       ├── #modal-payment            ← Encaissement
│       ├── #modal-product            ← Ajout/modif article
│       ├── #modal-receipt            ← Ticket + remboursement
│       ├── #modal-refund             ← Remboursement
│       ├── #modal-create-gc          ← Création carte cadeau
│       ├── #modal-gc-detail          ← Détail carte
│       ├── #modal-add-loy            ← Nouveau client fidélité
│       ├── #modal-loy-detail         ← Fiche client
│       └── #modal-confirm            ← Confirmations critiques
│
└── <script>                          ← JavaScript ES2022 Vanilla
    ├── STATE                         ← Variables globales
    ├── STORAGE                       ← GA(), SA(), getAcc(), saveU()
    ├── AUTH                          ← login(), register(), startSession()
    ├── IMPORT BACKUP (v3.0)          ← handleImportFile(), validateImport(), confirmImport()
    ├── VIEWS                         ← showView() → renderX() par vue
    ├── PRODUCTS                      ← renderProducts(), saveProduct()
    ├── CART                          ← addToCart(), changeQty(), getCartTotals()
    ├── PAYMENT                       ← openPayment(), selectPM(), confirmPayment()
    ├── RECEIPTS                      ← buildReceiptHTML(), printReceipt()
    ├── HISTORY                       ← renderHistory(), filterHistory(), exportCSV()
    ├── GIFT CARDS                    ← renderGC(), createGC(), rechargeGC()
    ├── LOYALTY                       ← renderLoyalty(), adjustPoints()
    ├── REFUNDS                       ← openRefundModal(), confirmRefund()
    ├── SETTINGS                      ← loadSettings(), saveSettings(), toggleBW()
    ├── CLIENT DISPLAY (v3.0)         ← startDisplaySession(), broadcastCart(), initClientMode()
    ├── NUMERIC PAD (v3.0)            ← openNumpad(), setNumpadMode(), npApply()
    └── UTILS                         ← fp(), esc(), toast(), formatPhone()

---

## Modèle de données

Toutes les données sont stockées dans `localStorage` du navigateur sous deux clés : `pos_accounts` (tableau de tous les comptes) et `pos_session` (identifiant du compte connecté).

---

## Cas d'usage par commerce

| Commerce | Fonctionnalités principales |
|:---|:---|
| 🥐 Boulangerie | Articles par catégorie matin/viennoseries/pains, espèces + monnaie automatique, cartes cadeaux Noël et Pâques |
| ☕ Café / Salon de thé | Note de commande (table numéro), panier moyen, fidélité clients réguliers, écran client sur le comptoir |
| 🍕 Restaurant / Pizzeria | Paiement mixte, remboursement sur plat non livré, tickets détaillés, écran client en salle |
| 💇 Salon de coiffure | CB, cartes cadeaux, fidélité avec seuil récompense, remboursement partiel, historique client |
| 🎁 Boutique cadeaux | Cartes cadeaux à montants variés, paiement mixte, export CSV pour comptabilité |
| 🌸 Marché / Vide-grenier | Mode espèces exclusif, pad numérique tactile, fonctionnement hors-ligne total |
| 📚 Librairie / Papeterie | Gestion stock précise, multi-catégories, historique détaillé, export CSV mensuel |
| 🧴 Institut / Spa | Fidélité avec récompenses, cartes cadeaux bien-être, tickets professionnels, écran client accueil |
| 🎵 École de musique | Abonnements (articles récurrents), paiement CB, historique des règlements |
| 🍦 Glacier / Food truck | Pad numérique tactile, espèces rapides, écran client visible du client, mode B&W en plein soleil |

---

## Installation

### Utilisation locale (recommandée)

Téléchargez le fichier `caisseOS.html` et ouvrez-le dans votre navigateur. C'est tout.

---

## Changelog complet

### v3.0. — 2026

**Nouvelles fonctionnalités**
- ✨ Écran client temps réel via BroadcastChannel — 5 états (Attente, Panier, Paiement, Merci, Connexion)
- ✨ Synchronisation automatique de tout changement panier, remise, fidélité, note, paiement, validation
- ✨ Pad numérique tactile — 4 modes : Quantité, Remise %, Prix, Note
- ✨ Import de sauvegarde JSON — glisser-déposer, aperçu détaillé, connexion automatique
- ✨ Génération de code de session sécurisé pour l'écran client

**Améliorations**
- 🎨 Bouton 📺 Écran client dans la barre de navigation principale
- 🎨 Animations fluides sur l'écran client (entrée des articles, paiement, merci)
- 📱 Pad numérique optimisé pour les écrans tactiles
- 🔒 Gestion des mots de passe manquants dans les backups importés

**Corrections**
- 🐛 `exportAllData` et `exportCSV` : ajout de `appendChild`/`removeChild` pour fiabiliser le téléchargement sur Chrome
- 🐛 Pad numérique : validation du stock avant application de la quantité en mode Quantité

---

### v2.0  — 2026

**Nouvelles fonctionnalités**
- ✨ Cartes cadeaux avec création, usage partiel/total, recharge, avoir automatique
- ✨ Programme de fidélité avec points, récompenses, classement, historique
- ✨ Remboursements partiels et totaux, 3 modes, restauration stock, ajustement fidélité
- ✨ Paiement CB, Espèces avec calcul monnaie, Carte Cadeau, Mixte
- ✨ Multi-comptes indépendants sur le même navigateur
- ✨ Mode Noir et Blanc
- ✨ Export sauvegarde JSON complet
- ✨ Formatage automatique numéros de téléphone XX.XX.XX.XX.XX

**Corrections**
- 🐛 `fp()` convertie en function declaration — corrige ReferenceError TDZ
- 🐛 `showView('pos')` appelle maintenant `renderProducts()` — corrige grille vide au retour de réglages

---

### v1.0 — 2026

- 🎉 Version initiale : caisse, historique, réglages, session persistante, thème dark



*CaisseOS 3.0 — Votre caisse, vos données, votre liberté.* 🛍️
# CaisseOS 2.0 EST ARRIVÉ !!!

CaisseOS

CaisseOS est une application de caisse enregistreuse (Point Of Sale - POS) développée pour offrir une solution simple, rapide et totalement autonome aux commerçants, associations, petites entreprises, marchés, événements et toute structure ayant besoin d'encaisser des ventes efficacement.

Contrairement à de nombreuses solutions de caisse modernes qui nécessitent des abonnements mensuels, des serveurs distants ou des logiciels complexes à installer, CaisseOS fonctionne entièrement dans un simple navigateur web grâce aux technologies HTML, CSS et JavaScript.

L'objectif principal du projet est de proposer une caisse complète capable de fonctionner sur pratiquement n'importe quel ordinateur moderne sans installation complexe ni configuration technique avancée.

Philosophie du projet

Le projet repose sur plusieurs principes fondamentaux :

Simplicité d'utilisation
Rapidité d'exécution
Fonctionnement hors ligne
Respect de la vie privée
Aucune dépendance externe obligatoire
Portabilité maximale
Interface claire et moderne
Maintenance simplifiée
Sauvegarde locale des données
Accessibilité pour tous

CaisseOS a été conçu pour permettre à n'importe quel utilisateur de lancer rapidement une caisse fonctionnelle sans connaissances techniques particulières.

Fonctionnement

L'ensemble de l'application est contenu dans un seul fichier HTML.

Cela signifie que :

Aucun serveur n'est nécessaire
Aucune installation n'est requise
Aucun framework n'est obligatoire
Aucun compte utilisateur n'est imposé
Aucune connexion internet n'est indispensable

Il suffit d'ouvrir le fichier dans un navigateur compatible pour commencer à utiliser le logiciel.

Gestion des produits

CaisseOS permet de gérer un catalogue complet de produits.

Chaque produit peut contenir :

Nom
Prix
Référence
Catégorie
Stock
Description
Image
Informations complémentaires

Le catalogue est conçu pour rester simple tout en offrant suffisamment d'options pour la majorité des commerces.

Gestion des stocks

Le suivi des stocks est intégré.

Fonctionnalités :

Ajout de stock
Retrait de stock
Décrémentation automatique lors des ventes
Réapprovisionnement
Contrôle des quantités
Alertes de faible stock
Historique des mouvements

Cette gestion permet de suivre précisément les quantités disponibles.

Interface de vente

L'écran principal permet :

Ajouter des produits au panier
Modifier les quantités
Appliquer des remises
Supprimer des articles
Calculer automatiquement les totaux
Encaisser rapidement

L'interface est optimisée pour une utilisation tactile ou à la souris.

Gestion des paiements

CaisseOS prend en charge plusieurs méthodes de paiement :

Espèces
Carte bancaire
Carte cadeau
Paiement mixte
Chèques
Paiements personnalisés

Chaque transaction est enregistrée dans l'historique.

Paiements mixtes

Les paiements mixtes permettent à un client de régler une commande avec plusieurs moyens de paiement.

Exemples :

Espèces + carte bancaire
Carte cadeau + espèces
Carte cadeau + carte bancaire
Plusieurs cartes cadeaux

Cette fonctionnalité offre une grande flexibilité.

Cartes cadeaux

Le système de cartes cadeaux permet :

Création de cartes
Attribution d'un identifiant unique
Recharge du solde
Consultation du solde
Utilisation partielle ou totale
Historique d'utilisation

Les cartes cadeaux sont directement intégrées à l'écosystème de CaisseOS.

Programme de fidélité

Un système de fidélité est inclus.

Fonctionnalités :

Attribution automatique de points
Conversion des points en récompenses
Historique des gains
Historique des utilisations
Consultation du solde

Cela permet de fidéliser efficacement les clients.

Gestion des remboursements

Le module de remboursement prend en charge :

Remboursements partiels
Remboursements complets
Réintégration du stock
Annulation des points fidélité associés
Historique détaillé

Chaque remboursement est enregistré pour assurer une traçabilité complète.

Historique des ventes

Toutes les ventes sont archivées.

Informations enregistrées :

Date
Heure
Produits
Quantités
Montants
Moyens de paiement
Remises appliquées

Cela permet un suivi précis de l'activité.

Statistiques

Le tableau de bord statistique fournit :

Chiffre d'affaires
Nombre de ventes
Panier moyen
Produits les plus vendus
Répartition des paiements
Évolution de l'activité
Historique des remboursements

Les données sont calculées automatiquement.

Exportation

Les données peuvent être exportées :

CSV
Sauvegardes locales
Archives

L'export CSV permet l'utilisation dans :

Excel
LibreOffice Calc
Google Sheets
Outils comptables
Multi-comptes

Le système permet plusieurs comptes indépendants.

Chaque compte dispose de :

Son catalogue
Ses statistiques
Ses ventes
Ses cartes cadeaux
Son programme fidélité

Les données restent totalement séparées.

Fonctionnement hors ligne

CaisseOS continue de fonctionner même sans connexion internet.

Avantages :

Utilisation en déplacement
Utilisation en salon ou foire
Utilisation en marché
Utilisation en zones sans réseau

Cette caractéristique garantit une disponibilité maximale.

Sécurité des données

Les données sont stockées localement.

Cela offre :

Rapidité
Confidentialité
Contrôle total
Réduction des risques liés aux services tiers

L'utilisateur reste propriétaire de ses informations.

Compatibilité

CaisseOS est compatible avec :

Windows
Linux
macOS
ChromeOS

Navigateurs :

Google Chrome
Microsoft Edge
Firefox
Opera
Brave
Objectifs futurs

Développements envisagés :

Gestion avancée des utilisateurs
Impression thermique améliorée
Synchronisation facultative
Gestion fournisseurs
Gestion commandes
Gestion inventaires avancés
Rapports PDF
Tableaux de bord améliorés
API d'intégration
Support multi-magasins
Pourquoi choisir CaisseOS ?
Gratuit
Léger
Rapide
Moderne
Sans abonnement
Sans serveur
Hors ligne
Facile à utiliser
Facile à modifier
Facile à déployer
Licence

CaisseOS est un projet open source conçu pour être accessible au plus grand nombre et permettre à chacun d'adapter la solution à ses besoins.
CaisseOS est une caisse enregistreuse moderne en HTML, CSS et JavaScript avec gestion des ventes, tickets de caisse, fidélité, cartes cadeaux, clients, historique, sauvegarde/import .caisseOS et paramètres avancés dans une interface sombre et professionnelle.

# CaisseOS 1.0 (ne marche plus)
CaisseOS est une application de caisse enregistreuse moderne et rapide développée en HTML, CSS et JavaScript.
Elle fonctionne directement dans le navigateur sans installation complexe et propose une interface sombre professionnelle pensée pour les commerces, snacks, boutiques et petites entreprises.

Fonctionnalités principales
Gestion complète des produits
Création et impression de tickets de caisse
Historique des ventes
Programme de fidélité intégré
Gestion des clients
Cartes cadeaux
Sauvegarde et restauration des données
Import / export de fichiers .caisseOS
Téléchargement complet des données
Paramètres avancés
Interface moderne responsive
Stockage local rapide et simple
Sauvegarde CaisseOS

Le système permet d’exporter toutes les données du magasin dans un fichier .caisseOS contenant :

produits
paramètres
clients
tickets
fidélité
historique

Les données peuvent ensuite être réimportées facilement depuis l’écran de connexion ou les paramètres.

Technologies utilisées
HTML5
CSS3
JavaScript Vanilla
LocalStorage.
L'objectif:
CaisseOS a été conçu pour offrir une caisse simple, moderne et légère pouvant fonctionner sur presque n’importe quel ordinateur sans serveur complexe ni base de données externe.
