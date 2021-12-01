# Instalar ArgoCD

https://argo-cd-docs.readthedocs.io/en/latest/#getting-started

`kubectl create namespace argocd`
`kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml`

Acceder a la UI:

`kubectl port-forward svc/argocd-server -n argocd 8080:443`

User: admin
Password: `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

## Instalar SSO/OIDC:

ConfigMap: argocd-cm

data:
  url: https://argocd.k8sdesa.um.es

  oidc.config: |
    name: cas
    issuer: https://entrada.um.es/cas/oidc
    clientID: argo-client-id
    clientSecret: $oidc.cas.clientSecret

    # Optional set of OIDC scopes to request. If omitted, defaults to: ["openid", "profile", "email", "groups"]
    requestedScopes: ["openid", "profile", "email", "groups"]

    # Optional set of OIDC claims to request on the ID token.
    #requestedIDTokenClaims: {"groups": {"essential": true}}

Secret: argocd-secret

data:
    oidc.case.clientSecret: {client_secret | base64_encoded}

## Instalar Image Updater

https://argocd-image-updater.readthedocs.io/en/stable/install/start/

`kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj-labs/argocd-image-updater/stable/manifests/install.yaml`

ConfigMap: argocd-image-updater-config

data:
  registries.conf: |
    registries:
    - name: Docker Hub
      api_url: https://registry-1.docker.io
      prefix: docker.io
      ping: yes
      defaultns: library
    - name: Gitlab UMU Registry
      api_url: https://registry.um.es:443
      prefix: registry.um.es
      ping: no
      pullsecret: argocd/gitlab-registry
    - name: Google Container Registry
      api_url: https://gcr.io
      prefix: gcr.io
      ping: no
    - name: RedHat Quay
      api_url: https://quay.io
      ping: no
      prefix: quay.io
    - name: GitHub Container Registry
      api_url: https://ghcr.io
      ping: no
      prefix: gcr.io

Anotaciones:

    argocd-image-updater.argoproj.io/image-list: myalias=dockerimage:~1
    argocd-image-updater.argoproj.io/myalias.force-update: 'true'
    argocd-image-updater.argoproj.io/write-back-method: git:secret:argocd/gitlab-secret
    argocd-image-updater.argoproj.io/myalias.helm.image-spec: mychart.demoImage
    argocd-image-updater.argoproj.io/myalias.ignore-tags: "*" <-- Ignore updates for a while
