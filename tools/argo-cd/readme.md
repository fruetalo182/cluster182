* Keep in count this document before to apply the install.yaml *

If you are installing everything from scratch, you should download the latest install.yaml manifest to deploy ArgoCD.
You can do it by running the following: 

curl -LO https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

Before to run the install.yaml, create the argocd namespace

kubectl create namespace argocd
kubectl apply -f install.yaml -n argocd

- Customizations -

1. Inside Deployment section of argocd-server, under "- command: \n - argocd-server" definition it is a "- --insecure" flag added to avoid TLS DNS redirection problems. (In our case CloudFlare takes care of that)
    example:
    ...
                    matchLabels:
                  app.kubernetes.io/part-of: argocd
              topologyKey: kubernetes.io/hostname
            weight: 5
      containers:
      - command:
        - argocd-server
        - --insecure
        env:
    ...
    
2. Inside ConfigMap section of argocd-cm configure the oauth app to allow Github SSO login.
    example:
    ...
    apiVersion: v1
    data:
        dex.config: |
            connectors:
            - type: github
                id: github
                name: GitHub
                config:
                clientID: xxxxxxxxxxxxx
                clientSecret: yyyyyyyyyyyyyyyyyyy
                orgs:
                - name: my-org
      url: https://argo.domain
    kind: ConfigMap
    metadata:
      annotations:
      ...


- First admin password -
To get the first admin password generated run:

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

3.
Una vez configurado el dex.config deberas loguearte como admin y en settings.projects.roles crear un nuevo rol permitiendo todo al grupo <org:team> de github