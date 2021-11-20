---
title: AKS (Azure Kubernetes Service) 암호에 대 한 암호 저장소 CSI 드라이버에 대 한 액세스 id를 Azure Key Vault 공급자에 게 제공 합니다.
description: Azure Key Vault와 통합 하기 위해 Azure Key Vault 공급자를 사용 하 여 암호 저장소 CSI 드라이버를 사용 하는 다양 한 방법을 알아봅니다.
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: article
ms.date: 10/13/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 91af197ec48de72351cd41f891418a07cc29756d
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132869424"
---
# <a name="provide-an-identity-to-access-the-azure-key-vault-provider-for-secrets-store-csi-driver"></a>비밀 저장소 CSI 드라이버에 대 한 Azure Key Vault 공급자에 액세스할 수 있는 id를 제공 합니다.

AKS (Azure Kubernetes Service)의 암호 저장소 CSI 드라이버는 Azure key vault에 대 한 다양 한 id 기반 액세스 방법을 제공 합니다. 이 문서에서는 이러한 방법과이를 사용 하 여 AKS 클러스터에서 주요 자격 증명 모음 및 해당 콘텐츠에 액세스 하는 방법을 간략하게 설명 합니다. 자세한 내용은 [Use The 비밀 STORE CSI Driver][csi-secrets-store-driver]을 참조 하세요.

## <a name="use-pod-identities"></a>Pod ID 사용

Azure Active Directory (azure ad) pod 관리 id는 AKS 기본 형식을 사용 하 여 azure ad의 azure 리소스 및 id에 대 한 관리 되는 id를 pod와 연결 합니다. 이러한 id를 사용 하 여 암호 저장소 CSI 드라이버에 대 한 Azure Key Vault 암호 공급자에 게 액세스 권한을 부여할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

- 클러스터에서 [AZURE AD pod identity 추가 기능][aad-pod-identity] 을 사용 하도록 설정 했는지 확인 합니다.
- Linux 기반 클러스터를 사용 해야 합니다.

### <a name="usage"></a>사용량

1. [Azure Kubernetes Service에서 pod 관리 id Azure Active Directory 사용 (미리 보기)][aad-pod-identity-create] 의 지침에 따라 클러스터 id를 만들고, 사용 권한을 할당 하 고, pod id를 만듭니다. 새로 만든 id `clientId` 와을 기록해 둡니다 `name` .

1. 다음 명령을 실행 하 여 새 id에 사용 권한을 할당 하 여 키 자격 증명 모음을 읽고 해당 내용을 볼 수 있도록 합니다.

    ```azurecli-interactive
    # set policy to access keys in your key vault
    az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <pod-identity-client-id>
    # set policy to access secrets in your key vault
    az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <pod-identity-client-id>
    # set policy to access certs in your key vault
    az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <pod-identity-client-id>
    ```

1. `SecretProviderClass`다음 YAML을 사용 하 여 `aadpodidbinding` , `tenantId` 및 키 자격 증명 모음에서 검색할 개체에 대 한 고유한 값을 사용 하 여를 만듭니다.

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

1. 클러스터에를 적용 합니다 `SecretProviderClass` .

    ```bash
    kubectl apply -f secretproviderclass.yaml
    ```

1. Id 이름을 사용 하 여 다음 YAML을 사용 하 여 pod를 만듭니다.

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

1. 클러스터에 pod를 적용 합니다.

    ```bash
    kubectl apply -f pod.yaml
    ```

## <a name="use-a-user-assigned-managed-identity"></a>사용자 할당 관리 id 사용

1. 키 자격 증명 모음에 액세스 하려면 [AKS 클러스터에서 관리 id를 사용 하도록 설정할][use-managed-identity]때 만든 사용자 할당 관리 id를 사용할 수 있습니다.

    ```azurecli-interactive
    az aks show -g <resource-group> -n <cluster-name> --query identityProfile.kubeletidentity.clientId -o tsv
    ```

    또는 새 관리 되는 id를 만들어 VM (가상 머신) 확장 집합 또는 가용성 집합의 각 VM 인스턴스에 할당할 수 있습니다.

    ```azurecli-interactive
    az identity create -g <resource-group> -n <identity-name> 
    az vmss identity assign -g <resource-group> -n <agent-pool-vmss> --identities <identity-resource-id>
    az vm identity assign -g <resource-group> -n <agent-pool-vm> --identities <identity-resource-id>
    ```

1. 키 자격 증명 모음을 읽고 해당 내용을 볼 수 있는 id 사용 권한을 부여 하려면 다음 명령을 실행 합니다.

    ```azurecli-interactive
    # set policy to access keys in your key vault
    az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-client-id>
    # set policy to access secrets in your key vault
    az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-client-id>
    # set policy to access certs in your key vault
    az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-client-id>
    ```

1. `SecretProviderClass`다음 yaml을 사용 하 여 `userAssignedIdentityID` ,, `keyvaultName` `tenantId` 및 키 자격 증명 모음에서 검색할 개체에 대 한 고유한 값을 사용 하 여를 만듭니다.

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

1. 클러스터에를 적용 합니다 `SecretProviderClass` .

    ```bash
    kubectl apply -f secretproviderclass.yaml
    ```

1. Id 이름을 사용 하 여 다음 YAML을 사용 하 여 pod를 만듭니다.

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

1. 클러스터에 pod를 적용 합니다.

    ```bash
    kubectl apply -f pod.yaml
    ```

## <a name="use-a-system-assigned-managed-identity"></a>시스템 할당 관리 id 사용

### <a name="prerequisites"></a>사전 요구 사항

- 이 단계를 시작 하기 전에 AKS 클러스터의 Vm 또는 확장 집합에서 [시스템 할당 관리 id를 사용 하도록 설정][enable-system-assigned-identity] 합니다.

### <a name="usage"></a>사용량

1. 가상 머신 확장 집합 또는 가용성 집합 노드에 시스템 할당 id가 있는지 확인 합니다.

    ```azurecli-interactive
    az vmss identity show -g <resource group>  -n <vmss scalset name> -o yaml
    az vm identity show -g <resource group> -n <vm name> -o yaml
    ```

    출력에는가 포함 되어야 합니다 `type: SystemAssigned` . `principalId`을 기록해 둡니다.

1. 키 자격 증명 모음을 읽고 해당 내용을 볼 수 있는 id 사용 권한을 부여 하려면 다음 명령을 실행 합니다.

    ```azurecli-interactive
    # set policy to access keys in your key vault
    az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-principal-id>
    # set policy to access secrets in your key vault
    az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-principal-id>
    # set policy to access certs in your key vault
    az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-principal-id>
    ```

1. `SecretProviderClass`다음 YAML을 사용 하 여 `keyvaultName` , `tenantId` 및 키 자격 증명 모음에서 검색할 개체에 대 한 고유한 값을 사용 하 여를 만듭니다.

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

1. 클러스터에를 적용 합니다 `SecretProviderClass` .

    ```bash
    kubectl apply -f secretproviderclass.yaml
    ```

1. Id 이름을 사용 하 여 다음 YAML을 사용 하 여 pod를 만듭니다.

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

Pod의 YAML에 지정 된 볼륨 경로에 비밀이 탑재 되었는지 확인 하려면 [AKS 클러스터에서 Azure Key Vault 공급자를 사용 하 여 암호 저장소 CSI 드라이버 사용][validate-secrets]을 참조 하세요.

<!-- LINKS INTERNAL -->

[csi-secrets-store-driver]: ./csi-secrets-store-driver.md
[aad-pod-identity]: ./use-azure-ad-pod-identity.md
[aad-pod-identity-create]: ./use-azure-ad-pod-identity.md#create-an-identity
[use-managed-identity]: ./use-managed-identity.md
[validate-secrets]: ./csi-secrets-store-driver.md#validate-the-secrets
[enable-system-assigned-identity]: ../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-azure-vm

<!-- LINKS EXTERNAL -->
