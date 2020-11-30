# kubeflow-rancher-config
Working Kubernetes Config for Kubeflow v1.1 with Rancher 2.x. Below solution code is added to the kubeconfig file in rancher from Racher UI -> Click on Cluster -> Edit Cluster -> Kubernetes options location. Search for the respective kube-api and kube-controller. 

You can Copy paste the kubeflow.yml but you need to rename the value YOURCLUSTERNAME to match the names of the cluster you created in Rancher UI. 

# Solution
The is configuration should resolve most of the errors except for AVX error caused by metadata-writer deployment as that is related to CPU. Old CPU does not support AVX read below for more information. 
    kube-api:
      always_pull_images: false
      extra_args:
        service-account-issuer: kubernetes.default.svc
        service-account-signing-key-file: /etc/kubernetes/ssl/kube-service-account-token-key.pem
      pod_security_policy: false
      service_node_port_range: 30000-32767
    kube-controller:
      extra_args:
        cluster-signing-cert-file: /etc/kubernetes/ssl/kube-ca.pem
        cluster-signing-key-file: /etc/kubernetes/ssl/kube-ca-key.pem

# Successful deployment sequence for kubeflow in Rancher 2.x
Knative-Serving namespace deployment --> Istio-sytem namspace deployment --> Kubeflow namespace deployment

# Possible Errors
# Kubeflow Illegal instruction (core dumped) this only happen for metadata-writer
With this configuration metadata-writer pods will still fail if your CPU does not support AVX(Advanced Vector Execution). Deploying kubeflow on old CPU's that does not support AVX will result in this error. <br />
To Verify AVX Support Run below command <br />

```cat /proc/cpuinfo | grep avx ```


# cache-deployer cache-server ml-pipeline ml-pipeline ml-pipeline-persistenceagent ml-pipeline-ui ml-pipeline-scheduledworkflow ml-pipeline-viewer-crd ml-pipeline-visulizationserver mysql istio-policy -- All these deployments will fails will below error. Some deployment will fail in istio-system namespace and kubeflow namespace also.  
MountVolume.SetUp failed for volume "istio-token" : failed to fetch token: the API server does not have TokenRequest endpoints enabled
containers with incomplete status: [istio-init]
Unable to attach or mount volumes: unmounted volumes=[istio-token], unattached volumes=[sds-uds-path istio-token ml-pipeline-token-g5m62 istio-envoy]: timed out waiting for the condition




