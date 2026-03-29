# Clicface Infrastructure Public SSH Keys

🔐 Ce dépôt contient les clés publiques SSH utilisées pour la gestion transparente des installations automatisées et déploiements.

## Structure

- `keys/main-key0584.pub` : Clé publique principale actuelle.
- `keys/clicface-legacy.pub` : Ancienne clé publique (dépréciée).

## Exemple d'utilisation

Dans un fichier `autoinstall.yaml` :

```yaml
ssh:
  authorized-keys:
    - https://raw.githubusercontent.com/Clicface/infra-keys/master/keys/main-key0584.pub
```

⚠️ Aucune clé privée n'est stockée dans ce dépôt.

## Protection contre les clés privées

Trois mécanismes complémentaires empêchent toute clé privée SSH d'être ajoutée au dépôt :

### 1. Hook pre-commit (protection locale)

Un hook Git bloque automatiquement tout commit contenant un header de clé privée SSH (`BEGIN OPENSSH PRIVATE KEY`, `BEGIN RSA PRIVATE KEY`, etc.).

**Installation :**

```bash
chmod +x .git/hooks/pre-commit
```

> Le fichier `.git/hooks/pre-commit` est déjà en place après le clone. Il suffit de le rendre exécutable.

### 2. Hook pre-push (protection locale avant envoi)

Un second hook Git scanne **tous les fichiers du dépôt** juste avant le push. Même si une clé privée a échappé au pre-commit, elle ne quittera pas la machine.

**Installation :**

```bash
chmod +x .git/hooks/pre-push
```

### 3. GitHub Actions workflow + branch ruleset (protection serveur)

Le workflow `.github/workflows/check-no-private-keys.yml` s'exécute sur chaque push et pull request. Il analyse **tous les fichiers du dépôt** et fait échouer le pipeline si une clé privée est détectée. Il tourne sur un runner **self-hosted** (Debian/Ubuntu).

Pour que ce workflow **bloque réellement** les push/merge (et pas seulement signale le problème après coup), il faut configurer un **ruleset** sur GitHub :

1. Aller dans **Settings > Rules > Rulesets** du dépôt
2. Cliquer **New ruleset > New branch ruleset**
3. Nommer le ruleset (ex: `Protection clés privées`)
4. Dans **Target branches**, ajouter `master` (et toute autre branche à protéger)
5. Cocher **Require status checks to pass**, puis ajouter le check `Détecter les clés privées SSH`
6. Optionnel mais recommandé : cocher **Block force pushes**
7. Sauvegarder

> Les rulesets sont disponibles gratuitement sur les dépôts publics d'organisation GitHub.

### Pourquoi trois protections ?

| Couche | Quand | Contournable ? |
|---|---|---|
| **pre-commit** | Avant le commit | Oui (`--no-verify`) |
| **pre-push** | Avant le push | Oui (`--no-verify`) |
| **Workflow + ruleset** | Côté serveur GitHub | Non |

Les hooks locaux protègent le développeur au quotidien. Le workflow + ruleset est le **filet de sécurité incassable** côté serveur.
