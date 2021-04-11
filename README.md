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
    helm install pinot -f ./pinot/values.secret.yaml -f ./pinot/values.shoot.yaml ./pinot
    ```
   
    Or to install using the default values (independent of a Gardener shoot):
    ```bash
    helm install pinot ./pinot
    ```
1. Open Pinot dashboard running at Ingress (https://pinot.ingress.pinot.adm2021.shoot.canary.k8s-hana.ondemand.com/)  
    or port-forward to `pinot-controller` and navigate to http://localhost:9000/
    ```bash
    kubectl port-forward svc/pinot-controller 9000 &
    ```

1. Package helm chart for hand over:
    ```bash
    helm package --dependency-update -d assets pinot
    # Successfully packaged chart and saved it to: assets/pinot-0.1.0.tgz
    ```

    Install the packaged helm chart using:
    ```bash
    helm install pinot https://raw.githubusercontent.com/timebertt/adm-pinot/master/assets/pinot-0.1.0.tgz
    ```
