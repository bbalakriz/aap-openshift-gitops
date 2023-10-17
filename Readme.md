AAP Installation on OpenShift using OpenShift GitOps
======================================================

0) Clone this repository and make required changes to the AAP resources and manifests contained in this repository

1a) Follow the steps in the [OpenShift GitOps documentation](https://docs.openshift.com/gitops/1.10/installing_gitops/installing-openshift-gitops.html#installing-gitops-operator-using-cli_installing-openshift-gitops) and install OpenShift GitOps.

1b) Ensure that the ServiceAccount `openshift-gitops-argocd-application-controller` that's created by OpenShift GitOps, has sufficient rights to modify/administer resources in other projects (i.e. any namespaces that would be created later) using the command:

```
oc adm policy add-cluster-role-to-user admin -z openshift-gitops-argocd-application-controller -n openshift-gitops --rolebinding-name gitops-admin
```

2) Install [ArgoCD CLI](https://argo-cd.readthedocs.io/en/stable/cli_installation/) on the workstation

3) Access the OpenShift project `openshift-gitops` and get the password for the OpenShift GitOps 'admin' user using the command:

```
argoPass=$(oc get secret/openshift-gitops-cluster -n openshift-gitops -o jsonpath='{.data.admin\.password}' | base64 -d)
```

4) Login to OpenShift GitOps from the workstation using the following command:
  
```
argoURL=$(oc get route openshift-gitops-server -n openshift-gitops -o jsonpath='{.spec.host}{"\n"}')

argocd login --insecure --grpc-web $argoURL  --username admin --password $argoPass
```

5) Create a new OpenShift GitOps application using the following command:

```
argocd app create aap-install --repo https://github.com/bbalakriz/aap-openshift-gitops\
                              --path ./ --dest-namespace default \
                              --dest-server https://kubernetes.default.svc
```

6) Sync the app on OpenShift GitOps using the command given below to start the AAP operator provisioning followed by installation of AAP. 

```
argocd app sync aap-install
```

It should produce an output like the one shown below. 

```
TIMESTAMP                  GROUP                                   KIND             NAMESPACE                                   NAME                    STATUS    HEALTH        HOOK  MESSAGE
2023-10-17T13:08:35+08:00                                     Namespace                                         ansible-automation-platform             Synced                        
2023-10-17T13:08:36+08:00          Namespace     default  ansible-automation-platform   Running   Synced              namespace/ansible-automation-platform created
2023-10-17T13:08:36+08:00  operators.coreos.com  OperatorGroup  ansible-automation-platform  ansible-automation-platform-operator    Synced  Missing              
2023-10-17T13:08:38+08:00                         Namespace        default                   ansible-automation-platform           Succeeded   Synced              namespace/ansible-automation-platform created
2023-10-17T13:08:38+08:00  operators.coreos.com  OperatorGroup  ansible-automation-platform  ansible-automation-platform-operator    Synced   Missing              operatorgroup.operators.coreos.com/ansible-automation-platform-operator created
2023-10-17T13:08:38+08:00  automationcontroller.ansible.com  AutomationController  ansible-automation-platform        aap-controller  OutOfSync  Missing              automationcontroller.automationcontroller.ansible.com/aap-controller created
2023-10-17T13:08:38+08:00  automationcontroller.ansible.com  AutomationController  ansible-automation-platform        aap-controller    Synced  Missing              automationcontroller.automationcontroller.ansible.com/aap-controller created

Name:               openshift-gitops/aap-install
Project:            default
Server:             https://kubernetes.default.svc
Namespace:          default
URL:                https://openshift-gitops-server-xxx.xxx.xxx.abc.com/applications/aap-install
Repo:               https://github.com/bbalakriz/aap-openshift-gitops
....
Operation:          Sync
Sync Revision:      fb76f2dded5089da060af08ebc4f61687a3a3b95
Phase:              Succeeded
Duration:           5s
Message:            successfully synced (all tasks run)
.....
```

**TIP**: Steps 4, 5 and 6 could be done from OpenShift GitOps web console as well using the "New App" option a shown in the Appendix section below. 

7) Log in to the OpenShift GitOps console with the 'admin' user credentials and verify that the aap-install project and all resources are created successfully.
   
![image](https://github.com/bbalakriz/aap-openshift-gitops/assets/37283315/b24cf34f-84ac-48b2-8c99-ec76e44e034c)

9) AAP should be up and running in the namespace `ansible-automation-platform` and can be accessed from the route given below. 

```
oc get route aap-controller -n ansible-automation-platform -o jsonpath='{.spec.host}{"\n"}'
```

![image](https://github.com/bbalakriz/aap-openshift-gitops/assets/37283315/bb4eebf9-9ed9-452d-93e4-206b0ae788bc)




Appendix:
=========

Creating ArgoCD project from OpenShift GitOps web console. 

![image](https://github.com/bbalakriz/aap-openshift-gitops/assets/37283315/79d37100-0ca1-4aac-b410-30fda315d5f3)

![image](https://github.com/bbalakriz/aap-openshift-gitops/assets/37283315/0553d1a9-ffb8-48a4-95d4-564e46de511d)
