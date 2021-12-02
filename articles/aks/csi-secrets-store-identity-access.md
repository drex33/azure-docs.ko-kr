---
title: AZURE KEY VAULT Provider for Secrets Store CSI Driver for Azure Kubernetes Service(AKS) 비밀에 대한 액세스 ID 제공
description: 비밀 저장소용 Azure Key Vault 공급자 CSI 드라이버가 Azure Key Vault와 통합되도록 허용하는 데 사용할 수 있는 다양한 방법에 대해 알아봅니다.
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: article
ms.date: 10/13/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2abcff80fc7fdfd95f4a204bf221502074a28b9f
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133481730"
---
# <a name="provide-an-identity-to-access-the-azure-key-vault-provider-for-secrets-store-csi-driver"></a>비밀 저장소 CSI 드라이버용 Azure Key Vault 공급자에 액세스하기 위한 ID 제공

AKS(비밀 저장소 CSI Driver on Azure Kubernetes Service)는 Azure Key Vault에 대한 다양한 ID 기반 액세스 방법을 제공합니다. 이 문서에서는 이러한 방법과 이를 사용하여 AKS 클러스터에서 키 자격 증명 모음 및 해당 콘텐츠에 액세스하는 방법을 간략하게 설명합니다. 자세한 내용은 [비밀 저장소 CSI 드라이버 사용을 참조하세요.][csi-secrets-store-driver]

## <a name="use-pod-identities"></a>Pod ID 사용

azure AD(Azure Active Directory) Pod 관리 ID는 AKS 기본형을 사용하여 Azure AD의 Azure 리소스 및 ID에 대한 관리 ID를 Pod와 연결합니다. 이러한 ID를 사용하여 비밀 저장소 CSI용 Azure Key Vault 비밀 공급자 드라이버에 대한 액세스 권한을 부여할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

- 클러스터에서 [Azure AD Pod ID 추가][aad-pod-identity] 기능을 사용하도록 설정했는지 확인합니다.
- Linux 기반 클러스터를 사용해야 합니다.

### <a name="usage"></a>사용량

1. [Azure Kubernetes Service Azure Active Directory Pod 관리 ID 사용(미리 보기)의 지침에][aad-pod-identity-create] 따라 클러스터 ID를 만들고 권한을 할당하고 Pod ID를 만듭니다. 새로 만든 ID의 및 를 기록해 `clientId` `name` 둡다.

1. 새 ID에 권한을 할당하여 다음 명령을 실행하여 키 자격 증명 모음을 읽고 해당 내용을 볼 수 있도록 합니다.

    ```azurecli-interactive
    # set policy to access keys in your key vault
    az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <pod-identity-client-id>
    # set policy to access secrets in your key vault
    az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <pod-identity-client-id>
    # set policy to access certs in your key vault
    az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <pod-identity-client-id>
    ```

1. 키 자격 증명 `SecretProviderClass` 모음에서 검색할 , 및 개체에 대한 고유한 값을 사용하여 다음 YAML을 사용하여 `aadpodidbinding` `tenantId` 을 만듭니다.

    ```yml
    # This is a SecretProviderClass example using aad-pod-identity to access the key vault
    apiVersion: secrets-store.csi.x-k8s.io/v1
    kind: SecretProviderClass
    metadata:
      name: azure-kvname-podid
    spec:
      provider: azure
      parameters:
        usePodIdentity: "true"               # Set to true for using aad-pod-identity to access your key vault
        keyvaultName: <key-vault-name>       # Set to the name of your key vault
        cloudName: ""                        # [OPTIONAL for Azure] if not provided, the Azure environment defaults to AzurePublicCloud
        objects:  |
          array:
            - |
              objectName: secret1
              objectType: secret             # object types: secret, key, or cert
              objectVersion: ""              # [OPTIONAL] object versions, default to latest if empty
            - |
              objectName: key1
              objectType: key
              objectVersion: ""
        tenantId: <tenant-Id>                # The tenant ID of the key vault
    ```

1. `SecretProviderClass`클러스터에 를 적용합니다.

    ```bash
    kubectl apply -f secretproviderclass.yaml
    ```

1. ID 이름을 사용하여 다음 YAML을 사용하여 Pod를 만듭니다.

    ```yml
    # This is a sample pod definition for using SecretProviderClass and aad-pod-identity to access the key vault
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

1. 클러스터에 Pod를 적용합니다.

    ```bash
    kubectl apply -f pod.yaml
    ```

## <a name="use-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 사용

1. 키 자격 증명 모음에 액세스하려면 [AKS 클러스터에서][use-managed-identity]관리 ID를 사용하도록 설정할 때 만든 사용자 할당 관리 ID를 사용할 수 있습니다.

    ```azurecli-interactive
    az aks show -g <resource-group> -n <cluster-name> --query identityProfile.kubeletidentity.clientId -o tsv
    ```

    또는 새 관리 ID를 만들어 VM(가상 머신) 확장 집합 또는 가용성 집합의 각 VM 인스턴스에 할당할 수 있습니다.

    ```azurecli-interactive
    az identity create -g <resource-group> -n <identity-name> 
    az vmss identity assign -g <resource-group> -n <agent-pool-vmss> --identities <identity-resource-id>
    az vm identity assign -g <resource-group> -n <agent-pool-vm> --identities <identity-resource-id>
    ```

1. 키 자격 증명 모음을 읽고 해당 내용을 볼 수 있는 ID 권한을 부여하려면 다음 명령을 실행합니다.

    ```azurecli-interactive
    # set policy to access keys in your key vault
    az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-client-id>
    # set policy to access secrets in your key vault
    az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-client-id>
    # set policy to access certs in your key vault
    az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-client-id>
    ```

1. 키 자격 증명 `SecretProviderClass` 모음에서 검색할 , , 및 개체에 대한 고유한 값을 사용하여 다음 YAML을 사용하여 `userAssignedIdentityID` `keyvaultName` `tenantId` 을 만듭니다.

    ```yml
    # This is a SecretProviderClass example using user-assigned identity to access your key vault
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
        keyvaultName: <key-vault-name>        # Set to the name of your key vault
        cloudName: ""                         # [OPTIONAL for Azure] if not provided, the Azure environment defaults to AzurePublicCloud
        objects:  |
          array:
            - |
              objectName: secret1
              objectType: secret              # object types: secret, key, or cert
              objectVersion: ""               # [OPTIONAL] object versions, default to latest if empty
            - |
              objectName: key1
              objectType: key
              objectVersion: ""
        tenantId: <tenant-id>                 # The tenant ID of the key vault
    ```

1. `SecretProviderClass`클러스터에 를 적용합니다.

    ```bash
    kubectl apply -f secretproviderclass.yaml
    ```

1. ID 이름을 사용하여 다음 YAML을 사용하여 Pod를 만듭니다.

    ```yml
    # This is a sample pod definition for using SecretProviderClass and the user-assigned identity to access your key vault
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

1. 클러스터에 Pod를 적용합니다.

    ```bash
    kubectl apply -f pod.yaml
    ```

## <a name="use-a-system-assigned-managed-identity"></a>시스템 할당 관리 ID 사용

### <a name="prerequisites"></a>사전 요구 사항

>[!IMPORTANT]
> 이 단계를 시작하기 전에 AKS 클러스터의 VM 또는 확장 집합에서 [시스템 할당 관리 ID를 사용하도록 설정합니다.][enable-system-assigned-identity]
>

### <a name="usage"></a>사용량

1. 가상 머신 확장 집합 또는 가용성 집합 노드에 고유한 시스템 할당 ID가 있는지 확인합니다.

    ```azurecli-interactive
    az vmss identity show -g <resource group>  -n <vmss scalset name> -o yaml
    az vm identity show -g <resource group> -n <vm name> -o yaml
    ```

    >[!NOTE]
    > 출력에는 가 포함되어야 `type: SystemAssigned` 합니다. `principalId`을 기록해 둡니다.
    > 
    > IMDS는 먼저 VMSS에서 시스템 할당 ID를 찾은 다음, 사용자 할당 ID를 찾아서 1개만 있으면 끌어오게 됩니다. 사용자 할당 ID IMDS가 여러 개인 경우 끌어올 ID를 알지 못하기 때문에 오류가 발생합니다.
    > 
1. 키 자격 증명 모음을 읽고 해당 내용을 볼 수 있는 ID 권한을 부여하려면 다음 명령을 실행합니다.

    ```azurecli-interactive
    # set policy to access keys in your key vault
    az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-principal-id>
    # set policy to access secrets in your key vault
    az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-principal-id>
    # set policy to access certs in your key vault
    az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-principal-id>
    ```

1. 키 자격 증명 `SecretProviderClass` 모음에서 검색할 , 및 개체에 대한 고유한 값을 사용하여 다음 YAML을 사용하여 `keyvaultName` `tenantId` 을 만듭니다.

    ```yml
    # This is a SecretProviderClass example using system-assigned identity to access your key vault
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
        cloudName: ""                   # [OPTIONAL for Azure] if not provided, the Azure environment defaults to AzurePublicCloud
        objects:  |
          array:
            - |
              objectName: secret1
              objectType: secret        # object types: secret, key, or cert
              objectVersion: ""         # [OPTIONAL] object versions, default to latest if empty
            - |
              objectName: key1
              objectType: key
              objectVersion: ""
        tenantId: <tenant-id>           # The tenant ID of the key vault
    ```

1. `SecretProviderClass`클러스터에 를 적용합니다.

    ```bash
    kubectl apply -f secretproviderclass.yaml
    ```

1. ID 이름을 사용하여 다음 YAML을 사용하여 Pod를 만듭니다.

    ```yml
    # This is a sample pod definition for using SecretProviderClass and system-assigned identity to access your key vault
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

비밀이 Pod의 YAML에 지정된 볼륨 경로에 탑재되어 있는지 확인하려면 [AKS 클러스터에서 비밀 저장소 CSI 드라이버에 Azure Key Vault 공급자 사용을][validate-secrets]참조하세요.

<!-- LINKS INTERNAL -->

[csi-secrets-store-driver]: ./csi-secrets-store-driver.md
[aad-pod-identity]: ./use-azure-ad-pod-identity.md
[aad-pod-identity-create]: ./use-azure-ad-pod-identity.md#create-an-identity
[use-managed-identity]: ./use-managed-identity.md
[validate-secrets]: ./csi-secrets-store-driver.md#validate-the-secrets
[enable-system-assigned-identity]: ../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-azure-vm

<!-- LINKS EXTERNAL -->
