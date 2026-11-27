# Git — cheatsheet du groupe

## 0. Configuration (une seule fois par machine)

```bash
git config --global user.name "Prénom Nom"
git config --global user.email "email-du-compte-github"
git config --global pull.rebase false   # évite l'erreur « divergent branches » au pull
```

## 1. Se connecter à GitHub depuis le terminal

> [!WARNING]
> GitHub n'accepte plus le mot de passe du compte pour `git push` en HTTPS. Il faut passer par GitHub CLI (le plus simple) ou par une clé SSH.

**Prérequis :** le propriétaire du dépôt vous ajoute comme collaborateur (*Settings → Collaborators*) et vous acceptez l'invitation reçue par mail.

### Option A — GitHub CLI (recommandée sur votre machine perso)

```bash
sudo apt install gh          # Ubuntu / Debian
brew install gh              # macOS
winget install GitHub.cli    # Windows

gh auth login
```

Répondre : `GitHub.com` → `HTTPS` → `Yes` (*Authenticate Git with your GitHub credentials*) → `Login with a web browser`, puis coller le code à 8 caractères dans la page qui s'ouvre (sans navigateur : aller sur https://github.com/login/device).

```bash
gh auth status     # vérifier que c'est bon
```

### Option B — clé SSH (machines de l'école, sans droits admin)

```bash
ssh-keygen -t ed25519 -C "email-du-compte-github"   # Entrée à chaque question
cat ~/.ssh/id_ed25519.pub                            # copier la ligne affichée
```

Coller la ligne dans GitHub → *Settings → SSH and GPG keys → New SSH key*, puis tester :

```bash
ssh -T git@github.com    # répondre « yes », doit afficher « Hi <pseudo>! »
```

Avec SSH, cloner avec l'URL `git@github.com:...` et non `https://...`.

## 2. Récupérer le projet

```bash
git clone <url-du-depot>
cd <nom-du-depot>
```

## 3. Le cycle de base

```bash
git pull                                   # 1. récupérer le travail des autres AVANT de commencer
# ... travailler ...
git status                                 # 2. voir ce qui a changé
git add CODE/loss.py                       # 3. sélectionner (ou `git add .` pour tout)
git commit -m "Ajoute la loss spectrale"   # 4. enregistrer localement
git push                                   # 5. envoyer sur GitHub
```

Push refusé (`rejected ... fetch first`) : quelqu'un a poussé avant vous → `git pull`, puis `git push`.

## 4. Travailler sur une branche

```bash
git switch -c nom-feature         # créer la branche et s'y placer
# ... add / commit ...
git push -u origin nom-feature    # premier push de la branche (ensuite `git push` suffit)
```

Puis ouvrir une *Pull Request* sur GitHub pour fusionner dans `main`.
`git switch main` pour revenir sur `main`, `git branch` pour lister les branches.

## 5. Résoudre un conflit

Après un `git pull`, Git affiche `CONFLICT` :

1. Ouvrir le fichier et repérer les blocs :
   ```
   <<<<<<< HEAD
   votre version
   =======
   leur version
   >>>>>>> ...
   ```
2. Garder la bonne version, supprimer les trois marqueurs.
3. `git add fichier` puis `git commit`.

## 6. Inspecter / annuler

| Commande | Effet |
|---|---|
| `git log --oneline` | historique compact |
| `git diff` | modifications pas encore ajoutées |
| `git restore fichier` | jeter les modifs locales du fichier (**irréversible**) |
| `git restore --staged fichier` | annuler le `git add` sans perdre les modifs |
| `git commit --amend -m "..."` | corriger le dernier commit (**seulement s'il n'est pas encore poussé**) |

## Règles du groupe

- Toujours `git pull` avant de commencer à travailler.
- Petits commits, messages explicites.
- Jamais de `git push --force` sur `main`.
- Ne pas committer données, poids de modèles ni gros résultats : GitHub refuse les fichiers de plus de 100 Mo. Les mettre dans `.gitignore` :
  ```
  __pycache__/
  .ipynb_checkpoints/
  data/
  *.pt
  *.pth
  *.ckpt
  ```
- Notebooks : vider les sorties avant de committer (*Clear All Outputs*), sinon les conflits sont illisibles. Version automatique : `pip install nbstripout && nbstripout --install` dans le dépôt (à faire par chacun). Idéalement, un notebook = un propriétaire.
