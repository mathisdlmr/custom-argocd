# custom-argocd

Patch personnalisé d'ArgoCD ajoutant un breadcrumb vers l'application parente dans la vue "Application Tree".

## Contexte

Dans une architecture GitOps App-of-Apps, la vue détail d'une application ArgoCD n'affiche pas de lien de retour vers son application parente dans le fil d'Ariane. Ce patch comble ce manque en lisant le label `argocd.argoproj.io/instance` de l'application.

## Ce que fait le patch

`breadcrumbs-application-tree.patch` modifie `ui/src/app/applications/components/application-details/application-details.tsx` pour insérer, entre "Applications" et le nom de l'app courante, un lien vers l'application parente détectée via son label.

**Avant :** `Applications > mon-app`

**Après :** `Applications > app-of-apps > mon-app`

## Utilisation de l'image

L'image Docker patchée est automatiquement construite et publiée sur GHCR via GitHub Actions.

```yaml
# Dans votre values.yaml ArgoCD (Helm)
global:
  image:
    repository: ghcr.io/mathisdlmr/argocd
    tag: v3.3.3
```

## Build manuel

```bash
git clone https://github.com/argoproj/argo-cd.git
cd argo-cd
git checkout v3.3.3
git apply < ../breadcrumbs-application-tree.patch
docker build -t custom-argocd .
```

## CI/CD

La GitHub Action `.github/workflows/build.yml` :
1. Clone ArgoCD à la version cible (configurable via `workflow_dispatch`)
2. Applique le patch
3. Build l'image Docker
4. Pousse vers GHCR : `ghcr.io/<owner>/argocd:<version>`

## Stack

`ArgoCD` · `TypeScript` · `React` · `Docker` · `GitHub Actions`
