# pinot-test

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
