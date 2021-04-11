# adm-pinot

Some resources from our term paper on Apache Pinot for the lecture "Advanced Data Management" at DHBW CAS.

## Setup JupyterHub

Setup JupyterHub for easily working on Kubernetes cluster together.

1. Create namespace:
    ```bash
    kubectl create namespace jupyter
    ```
   
1. Update chart dependencies
    ```bash
    helm dependency update jupyter
    ```

1. Customize `values.yaml`.

1. Create a [GitHub OAuth application](https://github.com/settings/applications/new) and add client ID and client secret
to the corresponding values in `values.secret.yaml`.

1. Install JupyterHub:
    ```bash
    helm -n jupyter install jupyter -f jupyter/values.secret.yaml ./jupyter
    ```

1. Navigate to [JupyterHub](https://jupyter.ingress.pinot.adm2021.shoot.canary.k8s-hana.ondemand.com).

## Setup Pinot

1. Create namespace.
    ```bash
    kubectl create namespace pinot
    kubectl config set-context --current --namespace pinot
    ```

1. Update chart dependencies:
    ```bash
    helm repo update
    helm dependency update --skip-refresh pinot
    helm dependency update --skip-refresh superset
    ```

1. Optionally adapt `values.yaml`.

1. Install chart:
    ```bash
    helm install pinot -f ./pinot/values.secret.yaml ./pinot
    ```
1. Open Pinot dashboard running at Ingress (https://pinot.ingress.pinot.adm2021.shoot.canary.k8s-hana.ondemand.com/)  
    or port-forward to `pinot-controller` and navigate to http://localhost:9000/
    ```bash
    kubectl port-forward svc/pinot-controller 9000 &
    ```
