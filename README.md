# Clicface Infrastructure Public SSH Keys

🔐 Ce dépôt contient les clés publiques SSH utilisées pour la gestion transparente des installations automatisées et déploiements.

## Structure

- `keys/clicface.pub` : Clé publique principale à inclure dans les `authorized_keys` des systèmes Clicface.

## Exemple d'utilisation

Dans un fichier `autoinstall.yaml` :

```yaml
ssh:
  authorized-keys:
    - https://raw.githubusercontent.com/Clicface/infra-keys/main/keys/clicface.pub
```

⚠️ Aucune clé privée n'est stockée dans ce dépôt.
