1 Follow the steps in the [OpenShift GitOps documentation](https://docs.openshift.com/gitops/1.10/installing_gitops/installing-openshift-gitops.html#installing-gitops-operator-using-cli_installing-openshift-gitops) and install OpenShift GitOps.
2 Install [ArgoCD CLI](https://argo-cd.readthedocs.io/en/stable/cli_installation/) on the workstation
3 Using OC CLI on the workstation, access the OpenShift project `openshift-gitops` and get the password for the OpenShift GitOps 'admin' user from the Secret `openshift-gitops-cluster`
4 Login to OpenShift GitOps from the workstation using the following command:
  
```
argocd login --insecure --grpc-web $argoURL  --username admin --password <PASSWORD>
```
5 Get the OpenShift GitOps server 

5 Create a new OpenShift GitOps application using the following command:

```
argocd app create aap-install --repo https://github.com/bbalakriz/aap-openshift-gitops \
                              --path ./ --dest-namespace default \
                              --dest-server https://kubernetes.default.svc
```
6 Sync the app on OpenShift GitOps using the command given below to start the AAP operator provisioning followed by installation of AAP. 

```
argocd app sync aap-install
```
It should produce an output like the one shown below. 

```
TIMESTAMP                  GROUP                                   KIND             NAMESPACE                                   NAME                    STATUS    HEALTH        HOOK  MESSAGE
2023-10-17T13:08:35+08:00                                     Namespace                                         ansible-automation-platform             Synced                        
2023-10-17T13:08:35+08:00  automationcontroller.ansible.com  AutomationController  ansible-automation-platform        aap-controller                  OutOfSync  Missing              
2023-10-17T13:08:35+08:00  operators.coreos.com              OperatorGroup         ansible-automation-platform  ansible-automation-platform-operator  OutOfSync  Missing              
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
URL:                https://openshift-gitops-server-openshift-gitops.apps.cluster-7vkpc.7vkpc.sandbox1825.opentlc.com/applications/aap-install
Repo:               https://github.com/bbalakriz/aap-openshift-gitops
Target:             
Path:               ./
SyncWindow:         Sync Allowed
Sync Policy:        <none>
Sync Status:        Synced to  (fb76f2d)
Health Status:      Healthy

Operation:          Sync
Sync Revision:      fb76f2dded5089da060af08ebc4f61687a3a3b95
Phase:              Succeeded
Start:              2023-10-17 13:08:33 +0800 +08
Finished:           2023-10-17 13:08:38 +0800 +08
Duration:           5s
Message:            successfully synced (all tasks run)

GROUP                             KIND                  NAMESPACE                    NAME                                  STATUS     HEALTH  HOOK  MESSAGE
                                  Namespace             default                      ansible-automation-platform           Succeeded  Synced        namespace/ansible-automation-platform created
operators.coreos.com              OperatorGroup         ansible-automation-platform  ansible-automation-platform-operator  Synced                   operatorgroup.operators.coreos.com/ansible-automation-platform-operator created
automationcontroller.ansible.com  AutomationController  ansible-automation-platform  aap-controller                        Synced                   automationcontroller.automationcontroller.ansible.com/aap-controller created
                                  Namespace                                          ansible-automation-platform           Synced                   

```

NOTE: Steps 4, 5 and 6 could be done from OpenShift GitOps web console as well using the "New App" option

7 Log in to the OpenShift GitOps console, 


8 AAP should be up and running in the namespace `ansible-automation-platform`
