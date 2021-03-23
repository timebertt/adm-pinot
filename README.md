# adm-pinot

## Setup JupyterHub

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

## Setup Pinot

More or less following https://docs.pinot.apache.org/basics/recipes/github-events-stream.  
Ref: https://medium.com/apache-pinot-developer-blog/using-apache-pinot-and-kafka-to-analyze-github-events-93cdcb57d5f7

1. Create namespace.
    ```bash
    kubectl create namespace pinot-test
    kubens pinot-test
    ```

1. Add helm repo.
    ```bash
    helm repo add pinot https://raw.githubusercontent.com/apache/incubator-pinot/master/kubernetes/helm
    ```

1. Update chart dependencies:
    ```bash
    helm repo update
    helm dependency update --skip-refresh pinot
    helm dependency update --skip-refresh superset
    ```

1. Optionally adapt `values.yaml`.

1. Create a [personal access token for GitHub](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token)
for accessing the GitHub events API (source of data for this test scenario).
No extra scopes need to be selected.
Copy the generated token from GitHub and store it in `pinot/values.secret.yaml` (next to the default [`pinot/values.yaml`](./pinot/values.yaml)) like this:
    ```yaml
    github:
      accessToken: "my-access-token"
    ```

1. Install chart:
    ```bash
    helm install pinot -f ./pinot/values.secret.yaml ./pinot
    ```

1. Connect to controller and execute some test queries:
    ```bash
    kubectl port-forward pinot-controller-0 9000
    ```
    ```sql
    select organization, sum(count) as pr_count from pullRequestMergedEvents group by organization order by pr_count desc limit 10
    select userId, sum(count) as pr_count from pullRequestMergedEvents group by userId order by pr_count desc limit 10
    ```

1. Install Superset:
    ```bash
    helm install superset -f ./superset/values.overwrite.yaml ./superset
    ```
