---
title: AKS (Azure Kubernetes Service) 암호에 대 한 암호 저장소 CSI 드라이버에 대 한 액세스 id를 Azure Key Vault 공급자에 게 제공 합니다.
description: AKV (CSI)를 사용 하 여 암호 저장소에 대 한 Azure Key Vault 공급자를 Azure Key Vault ()와 통합할 수 있도록 하는 다양 한 방법에 대해 알아봅니다.
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: article
ms.date: 10/13/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e9e0741cd9f1f90efc4184891401d1d05fc5b5b2
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511688"
---
# <a name="provide-an-identity-to-access-azure-key-vault-provider-for-secrets-store-csi-driver"></a>비밀 저장소 CSI 드라이버에 대 한 Azure Key Vault 공급자에 액세스할 수 있는 id를 제공 합니다.

AKS (Azure Kubernetes Service)의 CSI Driver (암호 저장소)를 사용 하면 다양 한 id 기반 액세스 방법으로 Azure Key Vault (AKV)에 액세스할 수 있습니다. 이 문서에서는 이러한 방법 및 이러한 메서드를 사용 하 여 클러스터에서 AKV 인스턴스 및 해당 콘텐츠에 액세스 하는 방법을 설명 합니다. 자세한 내용은 [암호 저장소 CSI 드라이버 사용][csi-secrets-store-driver]을 참조 하세요.

## <a name="use-pod-identity"></a>Pod id 사용

Azure Active Directory Pod 관리 ID는 Kubernetes 기본 형식을 사용하여 Azure 리소스에 대한 관리 ID 및 AAD(Azure Active Directory)의 ID를 Pod와 연결합니다. 이러한 id를 사용 하 여 암호 저장소 CSI 드라이버에 대 한 Azure Key Vault 암호 공급자에 게 액세스 권한을 부여할 수 있습니다.

### <a name="prerequisites"></a>필수 구성 요소

- 클러스터에서 [AAD pod id 추가][aad-pod-identity] 기능이 사용 하도록 설정 되었는지 확인 합니다.
- Linux 기반 클러스터를 사용 해야 합니다.

### <a name="usage"></a>사용량

1. [AAD pod 관리 id 사용][aad-pod-identity-create] 의 단계에 따라 클러스터 id를 만들고, 사용 권한을 할당 하 고, pod id를 만듭니다. 새로 만든 id `clientId` 와을 기록해 둡니다 `name` .

2. 다음을 실행 하 여 AKV 인스턴스를 읽고 해당 콘텐츠를 볼 수 있도록 하는 새 id에 사용 권한을 할당 합니다.

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <pod-identity-client-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <pod-identity-client-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <pod-identity-client-id>
```

3. 다음 YAML을 사용 하 여 SecretProviderClass를 만들고,, 및 개체에 대 한 값을 입력 `aadpodidbinding` `tenantId` 하 여 AKV 인스턴스에서 검색 합니다.

```yml
# This is a SecretProviderClass example using aad-pod-identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-podid
spec:
  provider: azure
  parameters:
    usePodIdentity: "true"               # Set to true for using aad-pod-identity to access keyvault
    keyvaultName: <key-vault-name>       # Set to the name of your Azure Key Vault instance
    cloudName: ""                        # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret             # object types: secret, key or cert
          objectVersion: ""              # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-Id>                # The tenant ID of the Azure Key Vault instance
```

4. 클러스터에 SecretProviderClass를 적용 합니다.

```bash
kubectl apply -f secretproviderclass.yaml
```

5. 다음 YAML을 사용 하 여 pod를 만들고 id의 이름을 입력 합니다.

```yml
# This is a sample pod definition for using SecretProviderClass and aad-pod-identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-podid
  labels:
    aadpodidbinding: <name>                   # Set the label value to the name of your pod identity
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
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
          secretProviderClass: "azure-kvname-podid"
```

6. 클러스터에 pod를 적용 합니다.

```bash
kubectl apply -f pod.yaml
```

## <a name="use-a-user-assigned-managed-identity"></a>사용자 할당 관리 id 사용

1. [AKS 클러스터에서 관리 id][use-managed-identity] 를 사용 하도록 설정할 때 생성 된 사용자 할당 관리 id를 사용 하 여 AKV 인스턴스에 액세스할 수 있습니다.

```azurecli-interactive
az aks show -g <resource-group> -n <cluster-name> --query identityProfile.kubeletidentity.clientId -o tsv
```

또는 새 항목을 만들어 가상 머신 확장 집합 또는 가용성 집합의 각 VM 인스턴스에 할당할 수 있습니다.

```azurecli-interactive
az identity create -g <resource-group> -n <identity-name> 
az vmss identity assign -g <resource-group> -n <agent-pool-vmss> --identities <identity-resource-id>
az vm identity assign -g <resource-group> -n <agent-pool-vm> --identities <identity-resource-id>
```

2. 다음을 실행 하 여 AKV 인스턴스를 읽고 해당 콘텐츠를 볼 수 있도록 id 사용 권한을 부여 합니다.

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-client-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-client-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-client-id>
```

3. 다음 yaml을 사용 하 여 SecretProviderClass를 만들고,, 및 개체에 대 한 값을 입력 `userAssignedIdentityID` `keyvaultName` 하 여 `tenantId` AKV 인스턴스에서 검색 합니다.

```yml
# This is a SecretProviderClass example using user-assigned identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-user-msi
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    useVMManagedIdentity: "true"          # Set to true for using managed identity
    userAssignedIdentityID: <client-id>   # Set the clientID of the user-assigned managed identity to use
    keyvaultName: <key-vault-name>        # Set to the name of your Azure Key Vault instance
    cloudName: ""                         # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret              # object types: secret, key or cert
          objectVersion: ""               # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-id>                 # The tenant ID of the Azure Key Vault instance
```

4. 클러스터에 SecretProviderClass를 적용 합니다.

```bash
kubectl apply -f secretproviderclass.yaml
```

5. 다음 YAML을 사용 하 여 pod를 만들고 id의 이름을 입력 합니다.

```yml
# This is a sample pod definition for using SecretProviderClass and user-assigned identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-user-msi
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
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
          secretProviderClass: "azure-kvname-user-msi"
```

6. 클러스터에 pod를 적용 합니다.

```bash
kubectl apply -f pod.yaml
```

## <a name="use-a-system-assigned-managed-identity"></a>시스템 할당 관리 id 사용

### <a name="prerequisites"></a>필수 구성 요소

- 클러스터의 Vm 또는 확장 집합에서 [시스템 할당 관리 Id 사용][enable-system-assigned-identity]

### <a name="usage"></a>사용량

1. 가상 머신 확장 집합 또는 가용성 집합 노드에 시스템 할당 id가 있는지 확인 합니다.

```azurecli-interactive
az vmss identity show -g <resource group>  -n <vmss scalset name> -o yaml
az vm identity show -g <resource group> -n <vm name> -o yaml
```

출력에는가 포함 되어야 합니다 `type: SystemAssigned` . `principalId`을 기록해 둡니다.

2. 다음을 실행 하 여 AKV 인스턴스를 읽고 해당 콘텐츠를 볼 수 있게 하는 id에 사용 권한을 할당 합니다.

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-principal-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-principal-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-principal-id>
```

3. 다음 YAML을 사용 하 여 SecretProviderClass를 만들고,, 및 개체에 대 한 값을 입력 `keyvaultName` `tenantId` 하 여 AKV 인스턴스에서 검색 합니다.

```yml
# This is a SecretProviderClass example using system-assigned identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-system-msi
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    useVMManagedIdentity: "true"    # Set to true for using managed identity
    userAssignedIdentityID: ""      # If empty, then defaults to use the system assigned identity on the VM
    keyvaultName: <key-vault-name>
    cloudName: ""                   # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret        # object types: secret, key or cert
          objectVersion: ""         # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-id>           # The tenant ID of the Azure Key Vault instance
```

4. 클러스터에 SecretProviderClass를 적용 합니다.

```bash
kubectl apply -f secretproviderclass.yaml
```

5. 다음 YAML을 사용 하 여 pod를 만들고 id의 이름을 입력 합니다.

```yml
# This is a sample pod definition for using SecretProviderClass and system-assigned identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-system-msi
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
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
          secretProviderClass: "azure-kvname-system-msi"
```

## <a name="next-steps"></a>다음 단계

Pod의 YAML에 지정 된 볼륨 경로에 암호가 탑재 되는지 [확인][validate-secrets] 합니다.

<!-- LINKS INTERNAL -->

[csi-secrets-store-driver]: ./csi-secrets-store-driver.md
[aad-pod-identity]: ./use-azure-ad-pod-identity.md
[aad-pod-identity-create]: ./use-azure-ad-pod-identity.md#create-an-identity
[use-managed-identity]: ./use-managed-identity.md
[validate-secrets]: ./csi-secrets-store-driver.md#validate-the-secrets
[enable-system-assigned-identity]: ../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-azure-vm

<!-- LINKS EXTERNAL -->
