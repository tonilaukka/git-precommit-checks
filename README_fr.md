# Vérifie les contenus ajoutés à Git avant le _commit_

![version](https://img.shields.io/github/release/mbrehin/git-precommit-checks.svg)
![travis build](https://img.shields.io/travis/com/mbrehin/git-precommit-checks.svg)
![node version](https://img.shields.io/node/v/git-precommit-checks.svg)
![dev dependencies](https://img.shields.io/david/dev/mbrehin/git-precommit-checks.svg)
![vulnerabilities](https://img.shields.io/snyk/vulnerabilities/npm/git-precommit-checks.svg)
![MIT license](https://img.shields.io/github/license/mbrehin/git-precommit-checks.svg)
[![semantic-release](https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg)](https://github.com/semantic-release/semantic-release)

Cette documentation est également disponible en : [![English translation](https://img.shields.io/badge/EN-English%20translation-blue.svg)](/README.md)

Git et npm sont deux outils merveilleux et qui nous épaulent pour travailler et partager du code/des contenus de qualité.

`git-precommit-checks` vise à automatiser le contrôle des contenus avant de commiter. Il analyse les fichier _stagés_
(c-à-d ajouté via `git add…`). L’intérêt de son intégration via npm et le _package.json_ et qu'il est ainsi partagé
au sein d'un projet. Chaque contributeur au projet est alors soumis aux mêmes règles de contrôle.

Il peut être chargé manuellement ou à l'aide d'outils tiers permettant une façade d'enrobage des hooks Git.

Comme vous pouvez le voir [plus bas](#lancer-git-precommit-checks-avec-husky) nous recommandons d’utiliser Husky.

[Voici un aperçu rapide de ce que peut faire `git-precommit-checks`.](https://asciinema.org/a/224134)

## Installation

`npm install --save-dev git-precommit-checks`

## Comment définir mes règles de vérification ?

La configuration est chargée depuis le fichier _package.json_. Vous pouvez donc la personnaliser à votre guise.

Voici un exemple :

```js
"git-precommit-checks": {
  "rules": [
    {
      "filter": "\\.js$",
      "nonBlocking": "true",
      "message": "🤫 Oula, aurais-tu oublié des `console.log` inopportuns ?",
      "regex": "console\\.log"
    },
    {
      "message": "😨 On dirait que tu as oublié des marqueurs de conflits",
      "regex": "/^[<>|=]{4,}/m"
    },
    {
      "message": "🤔 Aurais-tu oublié de finir des développement ?",
      "nonBlocking": "true",
      "regex": "(?:FIXME|TODO)"
    }
  ]
}
```

Chaque entrée décrit une règle de vérification avec le motif de texte à rechercher dans les contenus
ajouté à Git (clé `regex` décrite sous la forme d’une expression régulière).
Le message associé sera affiché sur la sortie standard dès lors qu’un motif est détecté et le commit
sera stoppé.

Vous pouvez cependant renseigner une clé `nonBlocking` à `true` pour indiquer qu’une règle est
non bloquante. Ceci affichera le message comme une alerte simple et continuera le processus de commit.

Enfin vous pouvez appliquer les filtres à certains fichiers et/ou répertoires en renseignant un motif
de filtrage (clé `filter`, là aussi en utilisant une expression régulière).

Seules les clés `message` et `regex` sont obligatoires.

⚠️ _Il n’y a aucune règle par défaut, soyez donc conscient que rien ne se passera tant que vous n'en aurez pas ajouté 😊 !_

## Options d’affichage

Vous pouvez utiliser l’option `display` pour gérer plus précisemment certaines règles d’affichage :

```js
"git-precommit-checks": {
  "display": {
    "notifications": true,
    "offending-content": true,
    "rules-summary": true,
    "short-stats": true,
    "verbose": true
  },
  …
```

- `notifications`: utilise les notifications du système d’exploitation pour afficher le résumé en cas d’erreur ou d’alerte ;
- `offending-content`: affiche les chemins des fichiers et numéros de lignes ainsi que les contenus défaillants ;
- `rules-summary`: affiche un tableau récapitulatif des règles utilisées ;
- `short-stats`: affiche des statistiques simplifiées (exemple : `1 error, 1 warning.`) ;
- `verbose`: affiche le détail de chaque action executée, les fichiers analysés, le résumé des opérations.

## Utilisation

### Déclencher le script directement depuis les hooks git

Après avoir installé ce module en local dans votre projet ou au global sur votre machine, ajoutez le code suivant
(ou équivalent) dans le fichier `.git/hooks/pre-commit` depuis la racine de votre projet.

```bash
#!/bin/sh
scriptName="git-precommit-checks"
scriptPath="$(npm bin)/$scriptName"

if [ -f $scriptPath ]; then
  $scriptPath
else
  echo "Impossible de trouver le module $scriptName"
  echo "Vous pouvez le ré-installer avec 'npm install $scriptName --save-dev' ou vous pouvez supprimer ce hook."
fi
```

### Lancer _git-precommit-checks_ avec Husky

[Husky](https://github.com/typicode/husky) vous permet de centraliser vos appels de scripts de hooks depuis le fichier `package.json`.

Ainsi `git-precommit-checks` s'intègre très bien en `pre-commit`:

```js
  "husky": {
    "hooks": {
      "pre-commit": "git-precommit-checks"
    }
  }
```

NB: vous pouvez parfaitement chaîner vos appels si vous utilisiez Husky précédemment : `"pre-commit": "mon_appel_de_script_existant && git-precommit-checks"`.

## J’aimerai contribuer à cet outil formidable 😜 !

Toute contribution est la bienvenue. Vous pouvez consulter notre [guide de contribution](CONTRIBUTING.md) pour connaître la procédure recommandée.
