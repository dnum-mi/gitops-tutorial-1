#### Ce tutoriel permet de tester le déploiment d'un flux gitops et manipuler un paramètre et observer l'impact sur le déploiement.
Cet example est décliné du cours de Techword with nana (vous êtes invités à le regarder) </br> 
https://www.youtube.com/watch?v=MeU5_k9ssrs

## Prérequis :
Disposer d'un cluster kubernetes avec un ingres controler installé. 
</br>L'exemple a été testé avec le controleur d'ingres traefik sur un cluster kubernetes managé. https://www.scaleway.com
</br>Note : Note cette configuration ne fonctionne pas sur processeur Arm (ex: M1) car l'image n'est pas compilée en multi architecture.
</br>Le cluster doit accéder à internet pour récupérer la configuration .yaml et la registry d'image (quay.io qui ne semble pas limité en téléchargement).
</br>Vous devez avoir installer la commande kubectl qui pointe sur votre cluster local ou distant. ( A été testé sur cluster mutualisé de l'offre Scaleway et OpenShift )
</br>L'image utilisée permet juste de renvoyer la requête http.

```bash
# installation d'ArgoCD dans votre cluster k8s
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# récupérer le mot de passe d'Argo qui a été créer automatiquement à l'installation
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode && echo

# access ArgoCD UI 
kubectl get svc -n argocd
kubectl port-forward svc/argocd-server 8080:443 -n argocd
#> il suffit de se rendre à l'url http://localhost:8080

#tips : vous pouvez aussi utiliser un outils plus convivial tel que Lens https://k8slens.dev/ (à installer sur votre ordinateur )

# Comme le domaine n'existe pas, il est nécessaire d'ajouter dans votre fichier /etc/hosts le nom de domaine utilisé dans l'exemple. 

sudo vi /etc/hosts
<enter password>
#ajouter à la fin du fichier la ligne suivante
<ip-of-your-cluster> echo.fake-domain.local

```
</br>

## Manipulation d'exemple du tutoriel 

## jouez sur les valeurs et regarder le cluster se modifier directement dans l'interface
Tips : cloner ce repository afin de pouvoir jouer avec les valeurs.

```bash
# Pointez argo sur le fichier "application.yaml" du repository source (vous devrez surcharger dans Argo Directement)
kubectl apply -f https://raw.githubusercontent.com/dnum-mi/gitops-tutorial-1/main/application.yaml
# Pointez argo sur le fichier 'application.yaml' de votre repertoire et ensuite modifiez une valeur
# observez dans l'interface de Argo le comportement.
kubectl apply -f https://<votre-repo-url>/raw/application.yaml

# activité : modifiez par exemple la valeur replicas de la ligne 7 dans le ficher 'deployment.yaml',
# par exemple passez de 'replicas: 3'  à  'replicas: 10'  puis revenez à 'replicas: 3
# Vous devrez faire un commit entre chaque changement sur le fichier.
# Argo se synchronise toute les 3 minutes, vous pouvez forcer avec le bouton "SYNC"

# le service doit repondre à l'url suivante http://echo.fake-domain.local
# note : l'ingress peut mettre quelques secondes - minutes à se mettre en place.

```

![argo CD](https://raw.githubusercontent.com/dnum-mi/gitops-tutorial-1/main/argo%20CD.png)
#### Links

* Install Multipass + k3s : https://jyeee.medium.com/kubernetes-on-your-macos-laptop-with-multipass-k3s-and-rancher-2-4-6e9cbf013f58

* Config repo: [https://gitlab.com/nanuchi/argocd-app-config](https://gitlab.com/nanuchi/argocd-app-config)

* Docker repo: [https://hub.docker.com/repository/docker/nanajanashia/argocd-app](https://hub.docker.com/repository/docker/nanajanashia/argocd-app)

* Install ArgoCD: [https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argo-cd](https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argo-cd)

* Login to ArgoCD: [https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli](https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli)

* ArgoCD Configuration: [https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/)
