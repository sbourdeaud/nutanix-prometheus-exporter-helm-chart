# nutanix-prometheus-exporter-helm-chart

To install this chart (which deploys the nutanix-prometheus-exporter available [here](https://github.com/sbourdeaud/nutanix-prometheus-exporter)):  

> [!TIP]
> For `kubectl` and `helm` to work properly with the correct k8s instance, you will need to have configured your KUBECONFIG environment variable:  
>  ```sh
>    export KUBECONFIG=~/.kube/your_k8s_cluster_kubeconfig_file.conf
>   ```

1. Add this repository to helm:  
    ```sh
    helm repo add sbourdeaud https://sbourdeaud.github.io/nutanix-prometheus-exporter-helm-chart
    ```
2. Search for the chart to check everything is ok:
    ```sh
    helm search repo nutanix-prometheus-exporter
    ```
3. Copy example values to a local values.yaml file in a working directory:
    ```sh
    helm show values sbourdeaud/nutanix-prometheus-exporter > values.yaml
    ```
4. Edit the values.yaml file with your specifications.
5. Create the necessary secrets (the ones you referenced in your values.yaml for each instance) on your k8s cluster in the namespace you want:
    ```sh
    #adding secret for redfish operations mode
    export IPMI_CONFIG='[\n  {"ip":"<your_ipmi_ip>","name":"your_node1_name","username":"ADMIN","password":"<your_ipmi_password>"},\n  {"ip":"<your_ipmi_ip>","name":"your_node2_name","username":"ADMIN","password":"<your_ipmi_password>"},\n  {"ip":"<your_ipmi_ip>","name":"your_node3_name","username":"ADMIN","password":"<your_ipmi_password>"},\n  {"ip":"<your_ipmi_ip>","name":"your_node4_name","username":"ADMIN","password":"<your_ipmi_password>"}\n]'
    kubectl create secret generic clustername-ipmi --from-literal=IPMI_CONFIG="$IPMI_CONFIG" -n your_namespace
    #adding secret for v4 or legacy operations mode
    kubectl create secret generic prism --from-literal=secret=<your_password> --from-literal=username=admin
    ```
5. Deploy a release on your k8s cluster
    ```sh
    helm install --values ./values.yaml your_release_name -n your_namespace sbourdeaud/nutanix-prometheus-exporter
    ```
6. Check that your pods have been deployed correctly:  
    ```sh
    kubectl get pods -n your_namespace
    ```