---
title: 비밀 공급자 확장 Azure Key Vault(미리 보기)
description: 비밀 저장소 CSI 드라이버 인터페이스에 대한 Azure Key Vault 공급자를 Azure Arc 사용하도록 설정된 Kubernetes 클러스터의 확장으로 설정하는 자습서
services: azure-arc
ms.service: azure-arc
ms.date: 11/15/2021
ms.topic: article
author: mayurigupta13
ms.author: mayg
ms.openlocfilehash: 0046fcb5a148aca0e39ed2e04e636dd9a677a0a2
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133219039"
---
# <a name="using-azure-key-vault-secrets-provider-extension-to-fetch-secrets-into-arc-clusters-preview"></a>Azure Key Vault 비밀 공급자 확장을 사용하여 Arc 클러스터로 비밀 가져오기(미리 보기)

Azure Key Vault Provider for Secrets Store CSI 드라이버를 사용하면 [CSI 볼륨을](https://kubernetes-csi.github.io/docs/)통해 Kubernetes 클러스터와 비밀 저장소로 Azure Key Vault 통합할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
1. [여기](extensions.md#prerequisites)에 나열된 클러스터 확장에 대한 모든 일반적인 필수 구성 요소가 충족되어야 합니다.
2. az k8s-extension CLI 버전 v0.4.0 이상 사용

### <a name="support-limitations-for-azure-key-vault-akv-secrets-provider-extension"></a>AKV(Azure Key Vault) 비밀 공급자 확장에 대한 지원 제한 사항
- 현재 지원되는 Kubernetes 배포는 다음과 같습니다
    - Cluster API Azure
    - Google Kubernetes Engine
    - OpenShift Kubernetes Distribution
    - Canonical Kubernetes Distribution


[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="features"></a>기능

- CSI 인라인 볼륨을 사용하여 비밀/키/인증서를 Pod에 탑재
- SecretProviderClass CRD를 사용하여 Pod 이식성 지원
- Linux 및 Windows 컨테이너 지원
- Kubernetes 비밀과의 동기화 지원
- 비밀 자동 회전 지원


## <a name="install-akv-secrets-provider-extension-on-an-arc-enabled-kubernetes-cluster"></a>Arc 지원 Kubernetes 클러스터에 AKV 비밀 공급자 확장 설치

다음 단계에서는 Azure Arc에 연결된 지원되는 Kubernetes 배포를 사용하는 클러스터가 이미 있다고 가정합니다.

환경 변수를 설정합니다.
```azurecli-interactive
export CLUSTER_NAME=<arc-cluster-name>
export RESOURCE_GROUP=<resource-group-name>
```
AKV 비밀 공급자 확장은 미리 보기로 제공되고 있지만 `az k8s-extension create` 명령은 플래그에 대해서만 `preview` 허용합니다. `--release-train`

```azurecli-interactive
az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.AzureKeyVaultSecretsProvider --release-train preview --name akvsecretsprovider
```

위의 내용은 클러스터 노드에 비밀 저장소 CSI 드라이버 및 Azure Key Vault 공급자를 설치합니다. 다음과 유사한 결과가 나타나야 합니다. 실제 AKV 비밀 공급자 helm 차트가 클러스터에 배포되는 데 3~5분이 걸릴 수 있습니다.

AKV 비밀 공급자 확장의 인스턴스 하나만 Arc 연결 Kubernetes 클러스터에 배포할 수 있습니다.

```json
{
  "aksAssignedIdentity": null,
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": {},
  "configurationSettings": {},
  "customLocationSettings": null,
  "errorInfo": null,
  "extensionType": "microsoft.azurekeyvaultsecretsprovider",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/akvsecretsprovider",
  "identity": {
    "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tenantId": null,
    "type": "SystemAssigned"
  },
  "location": null,
  "name": "sscsi",
  "packageUri": null,
  "provisioningState": "Succeeded",
  "releaseTrain": "preview",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "kube-system"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": {
    "createdAt": "2021-11-15T18:55:33.952130+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2021-11-15T18:55:33.952130+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
  },
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "1.0.0"
}
```

### <a name="install-akv-secrets-provider-extension-using-arm-template"></a>ARM 템플릿을 사용하여 AKV 비밀 공급자 확장 설치
클러스터를 Azure Arc 연결한 후 다음 형식의 json 파일을 만들고 값을 업데이트해야 합니다. \<cluster-name\>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "ConnectedClusterName": {
            "defaultValue": "<cluster-name>",
            "type": "String",
            "metadata": {
                "description": "The Connected Cluster name."
            }
        },
        "ExtensionInstanceName": {
            "defaultValue": "akvsecretsprovider",
            "type": "String",
            "metadata": {
                "description": "The extension instance name."
            }
        },
        "ExtensionVersion": {
            "defaultValue": "",
            "type": "String",
            "metadata": {
                "description": "The version of the extension type."
            }
        },
        "ExtensionType": {
            "defaultValue": "Microsoft.AzureKeyVaultSecretsProvider",
            "type": "String",
            "metadata": {
                "description": "The extension type."
            }
        },
        "ReleaseTrain": {
            "defaultValue": "preview",
            "type": "String",
            "metadata": {
                "description": "The release train."
            }
        }
    },
    "functions": [],
    "resources": [
        {
            "type": "Microsoft.KubernetesConfiguration/extensions",
            "apiVersion": "2021-09-01",
            "name": "[parameters('ExtensionInstanceName')]",
            "properties": {
                "extensionType": "[parameters('ExtensionType')]",
                "releaseTrain": "[parameters('ReleaseTrain')]",
                "version": "[parameters('ExtensionVersion')]"
            },
            "scope": "[concat('Microsoft.Kubernetes/connectedClusters/', parameters('ConnectedClusterName'))]"
        }
    ]
}
```
환경 변수를 설정합니다.
```azurecli-interactive
export TEMPLATE_FILE_NAME=<template-file-path>
export DEPLOYMENT_NAME=<desired-deployment-name>
```

마지막으로 이 명령을 실행하여 az CLI를 통해 AKV 비밀 공급자 확장을 설치합니다.

```azurecli-interactive
az deployment group create --name $DEPLOYMENT_NAME --resource-group $RESOURCE_GROUP --template-file $TEMPLATE_FILE_NAME
```
이제 AKV 공급자 리소스를 보고 클러스터에서 확장을 사용할 수 있습니다.

## <a name="validate-the-extension-installation"></a>확장 설치 유효성 검사

다음 명령을 실행합니다.

```azurecli-interactive
az k8s-extension show --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name akvsecretsprovider
```

다음과 유사한 JSON 출력이 표시됩니다.
```json
{
  "aksAssignedIdentity": null,
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": {},
  "configurationSettings": {},
  "customLocationSettings": null,
  "errorInfo": null,
  "extensionType": "microsoft.azurekeyvaultsecretsprovider",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/akvsecretsprovider",
  "identity": {
    "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tenantId": null,
    "type": "SystemAssigned"
  },
  "location": null,
  "name": "akvsecretsprovider",
  "packageUri": null,
  "provisioningState": "Succeeded",
  "releaseTrain": "preview",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "kube-system"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": {
    "createdAt": "2021-11-15T21:17:52.751916+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2021-11-15T21:17:52.751916+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
  },
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "1.0.0"
}
```

## <a name="create-or-use-an-existing-azure-key-vault"></a>Azure Key Vault 만들기 또는 기존 Azure Key Vault 사용

환경 변수를 설정합니다.
```azurecli-interactive
export AKV_RESOURCE_GROUP=<resource-group-name>
export AZUREKEYVAULT_NAME=<AKV-name>
export AZUREKEYVAULT_LOCATION=<AKV-location>
```

비밀 콘텐츠를 포함하는 Azure Key Vault 리소스가 필요합니다. Key Vault의 이름은 전역적으로 고유해야 합니다.

```azurecli
az keyvault create -n $AZUREKEYVAULT_NAME -g $AKV_RESOURCE_GROUP -l $AZUREKEYVAULT_LOCATION
```

Azure Key Vault는 키, 비밀 및 인증서를 저장할 수 있습니다. 이 예제에서는 `DemoSecret`이라는 일반 텍스트 비밀을 설정합니다.

```azurecli
az keyvault secret set --vault-name $AZUREKEYVAULT_NAME -n DemoSecret --value MyExampleSecret
```

다음 섹션에서 사용하기 위해 다음 속성을 기록해 둡니다.

- Key Vault의 비밀 개체 이름
- 개체 유형(비밀, 키 또는 인증서)
- Azure Key Vault 리소스의 이름
- 구독이 속한 Azure 테넌트 ID

## <a name="provide-identity-to-access-azure-key-vault"></a>Azure Key Vault에 액세스할 ID 제공

Arc 연결 클러스터의 비밀 저장소 CSI 드라이버를 사용하면 현재 다음 메서드가 Azure Key Vault 인스턴스에 액세스할 수 있습니다.
- 서비스 주체

Azure Key Vault 액세스하기 위한 ID를 제공하려면 다음 단계를 수행합니다.

1. [여기의](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 단계에 따라 Azure에서 서비스 주체를 만듭니다. 이 단계에서 생성된 클라이언트 ID 및 클라이언트 비밀을 기록해 둡다.
2. [여기의](../../key-vault/general/assign-access-policy.md)단계에 따라 만든 서비스 주체에게 Azure Key Vault GET 권한을 제공합니다.
3. 1단계의 클라이언트 ID 및 클라이언트 비밀을 사용하여 Arc 연결 클러스터에 Kubernetes 비밀을 만듭니다.
```bash
kubectl create secret generic secrets-store-creds --from-literal clientid="<client-id>" --from-literal clientsecret="<client-secret>"
```
4. 만든 비밀에 레이블을 지정합니다.
```bash
kubectl label secret secrets-store-creds secrets-store.csi.k8s.io/used=true
```
5. 다음 YAML을 사용하여 SecretProviderClass를 만들고 AKV 인스턴스에서 검색할 키 자격 증명 모음 이름, 테넌트 ID 및 개체의 값을 입력합니다.
```yml
# This is a SecretProviderClass example using service principal to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: akvprovider-demo
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    keyvaultName: <key-vault-name>
    objects:  |
      array:
        - |
          objectName: DemoSecret
          objectType: secret             # object types: secret, key or cert
          objectVersion: ""              # [OPTIONAL] object versions, default to latest if empty
    tenantId: <tenant-Id>                # The tenant ID of the Azure Key Vault instance
```
6. 클러스터에 SecretProviderClass를 적용합니다.

```bash
kubectl apply -f secretproviderclass.yaml
```
7. 다음 YAML을 사용하여 Pod를 만들고 ID 이름을 입력합니다.

```yml
# This is a sample pod definition for using SecretProviderClass and service principal to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29
      command:
        - "/bin/sleep"
        - "10000"
      volumeMounts:
      - name: secrets-store01-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "akvprovider-demo"
        nodePublishSecretRef:                       
          name: secrets-store-creds
```
8. 클러스터에 Pod를 적용합니다.

```bash
kubectl apply -f pod.yaml
```

## <a name="validate-the-secrets"></a>비밀 유효성 검사
Pod가 시작된 후에는 배포 YAML에 지정된 볼륨 경로에 탑재된 콘텐츠를 사용할 수 있습니다.
```Bash
## show secrets held in secrets-store
kubectl exec busybox-secrets-store-inline -- ls /mnt/secrets-store/

## print a test secret 'DemoSecret' held in secrets-store
kubectl exec busybox-secrets-store-inline -- cat /mnt/secrets-store/DemoSecret
```

## <a name="additional-configuration-options"></a>추가 구성 옵션
Azure Key Vault 비밀 공급자 확장에 사용할 수 있는 구성 설정은 다음과 같습니다.

| 구성 설정 | 기본값 | Description |
| --------- | ----------- | ----------- |
| enableSecretRotation | false | 부울 형식; 외부 비밀 저장소의 최신 콘텐츠로 Pod 탑재 및 Kubernetes 비밀을 주기적으로 업데이트합니다. |
| rotationPollInterval | 2m | 가 인 경우 비밀 회전 폴링 간격 `enableSecretRotation` `true` 기간입니다. 모든 Pod 및 Kubernetes 비밀에 대해 탑재된 콘텐츠를 최신으로 다시 사용해야 하는 빈도에 따라 조정할 수 있습니다. |
| syncSecret.enabled | false | 부울 입력; 경우에 따라 탑재된 콘텐츠를 미러하는 Kubernetes 비밀을 만들 수 있습니다. 이 구성 설정을 사용하면 SecretProviderClass에서 secretObjects 필드가 동기화된 Kubernetes 비밀 개체의 원하는 상태를 정의할 수 있습니다. |

이러한 설정은 명령을 사용하여 확장 설치 시 또는 명령을 사용하여 설치 후 변경할 수 `az k8s-extension create` `az k8s-extension update` 있습니다.

확장 인스턴스를 만드는 동안 다음 명령을 사용하여 구성 설정을 추가합니다.
```azurecli-interactive
az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.AzureKeyVaultSecretsProvider --release-train preview --name akvsecretsprovider --configuration-settings secrets-store-csi-driver.enableSecretRotation=true secrets-store-csi-driver.rotationPollInterval=3m secrets-store-csi-driver.syncSecret.enabled=true
```

다음 명령을 사용하여 기존 확장 인스턴스의 구성 설정을 업데이트합니다.
```azurecli-interactive
az k8s-extension update --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --name akvsecretsprovider --configuration-settings secrets-store-csi-driver.enableSecretRotation=true secrets-store-csi-driver.rotationPollInterval=3m secrets-store-csi-driver.syncSecret.enabled=true
```

## <a name="uninstall-azure-key-vault-secrets-provider-extension"></a>Azure Key Vault 비밀 공급자 확장 제거
아래 명령을 사용합니다.
```azurecli-interactive
az k8s-extension delete --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name akvsecretsprovider
```
제거는 확장 설치 시 생성된 CRD를 삭제하지 않습니다.

확장 인스턴스가 삭제되었는지 확인합니다.
```azurecli-interactive
az k8s-extension list --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP
```
이 출력에는 AKV 비밀 공급자가 포함되지 않아야 합니다. 클러스터에 다른 확장을 설치하지 않은 경우에는 빈 배열입니다.

## <a name="reconciliation-and-troubleshooting"></a>조정 및 문제 해결
Azure Key Vault 비밀 공급자 확장은 자체 복구됩니다. 확장 설치 시 클러스터에 배포된 모든 확장 구성 요소는 누군가가 의도적으로 또는 의도치 않게 변경하거나 삭제하려는 경우 원래 상태로 조정됩니다. 유일한 예외는 CRD입니다. CRD가 삭제되는 경우 조정되지 않습니다. 'az k8s-exstension create' 명령을 다시 사용하고 기존 확장 인스턴스 이름을 제공하여 다시 가져올 수 있습니다.

Azure Key Vault 비밀 공급자에 대한 몇 가지 일반적인 문제 및 문제 해결 단계는 참조를 위해 [여기의](https://azure.github.io/secrets-store-csi-driver-provider-azure/troubleshooting/) 오픈 소스 설명서에서 캡처됩니다.

비밀 저장소 CSI 드라이버 인터페이스와 관련된 추가 문제 해결 단계는 [여기에서](https://secrets-store-csi-driver.sigs.k8s.io/troubleshooting.html)참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> **작업을 시도해보시겠습니까?**  
> 클러스터 API를 사용하여 [Azure Arc Jumpstart 시나리오를](https://aka.ms/arc-jumpstart-akv-secrets-provider) 빠르게 시작합니다.
