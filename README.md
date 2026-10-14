# My Landing Page — Jeff

Version locale préparée pour Jeff le 14 octobre 2026.

- Domaine prévu : `jeff.mylandingpage.click` (publication et DNS non configurés par cette préparation).
- `origin` : `https://github.com/charlesbgr/mylandingpage-jeff.git` (adresse prévue ; dépôt distant non créé).
- `upstream` : `https://github.com/charlesbgr/mylandingpage.git`.
- Identifiant GTM laissé vide : les deux blocs GTM sont conditionnels et ne se chargent pas tant que cet identifiant reste vide. Renseigner le conteneur de Jeff pour les activer.
- Webhook Make conservé comme dans le modèle ; aucun formulaire soumis.
- Version exclue de `../sync.sh` pour préserver le travail de formation.

## Guide commun

Site d'exercice pour les formations Google Tag Manager (GTM) et Google Analytics 4
(GA4). Un modèle commun fournit les pages, boutons et formulaires ; chaque
participant dispose d'un dépôt indépendant, de son nom affiché, de son conteneur
GTM et de son sous-domaine.

## Fonctionnement général

```text
mylandingpage (modèle)
    ├── mylandingpage-agnes
    ├── mylandingpage-fred
    └── mylandingpage-test

Navigateur → site → conteneur GTM → destinations configurées dans GTM (ex. GA4)
                 → formulaires → webhook Make
```

Les copies ne se mettent pas à jour automatiquement avec le modèle.
Le script externe `../sync.sh` permet de les remplacer par sa dernière version,
avec les limites décrites plus bas.

| Fichier | Rôle |
| --- | --- |
| `index.html` | Accueil, boutons, formulaire de demande et newsletter |
| `contact.html` | Formulaire de contact |
| `thank-you.html` | Page de remerciement |
| `_config.yml` | Nom affiché (`logo`) et identifiant GTM (`gtm_id`) |
| `CNAME` | Domaine personnalisé du site |
| `styles.css`, `images/` | Présentation et images |
| `js/config.js` | Adresse du webhook Make et affectation aux formulaires |
| `js/gtm-ga4-interceptor.js` | Diagnostic des requêtes de tracking |
| `js/consent-reader.js` | Lecture de l'état de consentement ; ne recueille pas le consentement |
| `js/global-submit-handler.js` | Gestion de l'état des boutons à la soumission |

Les trois pages utilisent Jekyll/Liquid : `{{ site.logo }}` et
`{{ site.gtm_id }}` sont remplacés à la génération à partir de `_config.yml`.
GTM est déjà intégré dans le `<head>` et via un bloc `<noscript>` dans le `<body>`.
Les balises GA4 et leurs déclencheurs se configurent dans le conteneur GTM ;
changer son identifiant ne crée pas cette configuration.

## Get started : préparer une version participant

### 1. Préparer une copie indépendante

Créer un dépôt GitHub dédié, par exemple `mylandingpage-jeff`, à partir du modèle,
puis le placer à côté des autres dépôts. Ne pas réutiliser le dépôt d'un autre
participant ni conserver son adresse `origin` par erreur.

Organisation locale actuelle :

```text
/Users/charles/Dropbox/Cowork/projects/250530_mylandingpage/
    mylandingpage/          # dépôt modèle
    mylandingpage-agnes/    # dépôt indépendant
    mylandingpage-fred/     # dépôt indépendant
    mylandingpage-test/     # dépôt indépendant
    sync.sh                # outil externe aux dépôts
```

La copie locale de Jeff est créée ; sa publication reste à réaliser.
Avant tout envoi, contrôler `git status --short` et `git remote -v` dans la copie.
Si une synchronisation avec le modèle est prévue, le remote `upstream` doit
pointer vers `https://github.com/charlesbgr/mylandingpage.git`.

### 2. Personnaliser le site

Dans `_config.yml`, remplacer les valeurs par celles du participant :

```yaml
gtm_id: "GTM-REMPLACER"
logo: "Jeff"
```

`GTM-REMPLACER` est un exemple à remplacer par un vrai identifiant.
Dans `CNAME`, renseigner uniquement le domaine choisi, par exemple :

```text
jeff.mylandingpage.click
```

Le fichier `CNAME` ne suffit pas à créer le DNS ou à activer l'hébergement.
Configurer et vérifier GitHub Pages, le domaine personnalisé, le DNS et HTTPS
avant de partager l'adresse. Les paramètres des comptes en ligne n'ont pas été
audités pour ce guide.

### 3. Choisir le point de départ du cours

- **Installation de GTM** : retirer les deux blocs GTM des trois pages dans la
  copie pédagogique, puis les installer avec le participant pendant l'exercice.
- **Événements et GA4** : conserver les blocs, utiliser le conteneur du participant
  et préparer les exercices de balises, déclencheurs et variables.

Ne pas laisser le conteneur du modèle ou d'un autre participant par défaut.

### 4. Vérifier la destination des formulaires

`MAKE_WEBHOOK` dans `js/config.js` fournit la destination aux formulaires dont
l'attribut `action` est vide. La newsletter utilise un envoi JavaScript ; les
autres formulaires déclarent un POST HTML.

Les copies locales d'Agnès et Fred partagent actuellement le même webhook.
Choisir explicitement si la nouvelle copie utilise ce circuit de démonstration
ou un circuit dédié. Le traitement Make, ses éventuels effets et la redirection
vers la page de remerciement restent à vérifier avant un essai réel.
Utiliser des données fictives pour les exercices.

Après une modification de `js/config.js`, augmenter le paramètre `?v=` de son
inclusion dans les pages concernées pour éviter une ancienne version en cache.

### 5. Prévisualiser et valider

Un simple double-clic sur les fichiers HTML ou un serveur statique ne remplace
pas les variables Liquid : utiliser une génération Jekyll pour tester la version
personnalisée.

Le dépôt ne fournit actuellement ni `Gemfile` ni environnement local verrouillé.
Préparer Ruby/Bundler et Jekyll selon le guide officiel ci-dessous ; une fois cet
environnement configuré, lancer depuis le dépôt :

```sh
bundle exec jekyll serve
```

Cette commande suppose que les dépendances ont été configurées et installées ;
elle n'est pas une installation autonome. Un site local peut tout de même
charger GTM et envoyer des formulaires à Make.

Avant la séance :

- Vérifier le nom, le domaine et l'identifiant GTM du participant.
- Ouvrir les trois pages et contrôler styles, images et liens.
- Vérifier que les variables `{{ site.* }}` ont été remplacées dans le HTML rendu.
- Pour une version instrumentée, vérifier le conteneur en prévisualisation GTM,
  puis les événements attendus dans GA4 si cette destination est configurée.
- Tester les formulaires seulement après confirmation du circuit Make et de ses
  effets ; contrôler la réponse, les erreurs et la navigation après soumission.

La console de diagnostic du site est une aide ; elle ne prouve pas à elle seule
la réception des événements dans GA4.

## How to : mettre à jour les copies

Modifier et vérifier d'abord le modèle. Le script `../sync.sh` récupère
`upstream/main` sur GitHub : les modifications uniquement locales du modèle ne
sont donc pas propagées.

**Attention : ce script effectue un `git reset --hard`, puis un
`git push --force-with-lease`. Il ne réalise pas une fusion des exercices.**

Pour chaque dépôt présent dans sa liste `COPIES`, il :

1. récupère les changements de la branche distante de la copie ;
2. sauvegarde `_config.yml` et `CNAME` ;
3. remplace le code par `upstream/main` ;
4. restaure ces deux fichiers, crée un commit et pousse vers `origin`.

Les personnalisations des autres fichiers, y compris le webhook dans
`js/config.js` et les changements réalisés pendant les exercices, peuvent être
écrasées. Conserver une sauvegarde récupérable des travaux avant toute exécution.
Vérifier aussi les remotes, les branches et l'absence de travail non sauvegardé.

La liste actuelle contient Fred, Agnès et Test. L'ajout d'un participant à cette
liste doit être volontaire. Garder sa copie hors synchronisation pendant les
exercices si ses modifications doivent être conservées.

Le script n'a pas de mode simulation ni d'arrêt global sur chaque erreur ; il
utilise aussi des fichiers temporaires communs dans le dossier parent. Ne pas
lancer plusieurs synchronisations en parallèle et contrôler les résultats dépôt
par dépôt. La synchronisation et les envois GitHub sont des actions distinctes
de la simple préparation locale du site.

## Références

- [Tester GitHub Pages localement avec Jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll)
- [Configurer un domaine personnalisé GitHub Pages](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)

Guide établi à partir du code local le 14 octobre 2026. Les domaines configurés
dans les fichiers ne constituent pas une vérification de leur disponibilité en ligne.
