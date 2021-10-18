---
title: Azure Red Hat OpenShift (ARO)에서 고객이 관리 하는 키 (CMK)를 사용 하 여 OS 디스크 암호화
description: Azure Red Hat OpenShift (ARO)에서 고객이 관리 하는 키 (CMK)를 사용 하 여 OS 디스크 암호화
author: sayjadha
ms.author: suvetriv
ms.service: azure-redhat-openshift
keywords: 암호화, byok, aro, 배포, openshift, red hat
ms.topic: how-to
ms.date: 10/18/2021
ms.custom: template-how-to
ms.openlocfilehash: f9b60767f929f8fc9d40836daa6435d0c69d110f
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134437"
---
# <a name="encrypt-os-disks-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Azure Red Hat OpenShift (ARO) (미리 보기)에서 고객이 관리 하는 키 (CMK)를 사용 하 여 OS 디스크 암호화

기본적으로 Azure Red Hat OpenShift 클러스터에 있는 가상 머신의 OS 디스크는 Microsoft Azure에서 관리 하는 자동 생성 키로 암호화 되었습니다. 추가 보안을 위해 고객은 ARO 클러스터를 배포할 때 자체 관리 키를 사용 하 여 OS 디스크를 암호화할 수 있습니다. 이 기능을 통해 고객 관리 키를 사용 하 여 기밀 데이터를 암호화 하 여 더 많은 제어를 할 수 있습니다.

고객 관리 키를 사용 하 여 만든 클러스터에는 해당 키를 사용 하는 기본 저장소 클래스가 있습니다. 따라서 이러한 키로 OS 디스크와 데이터 디스크를 모두 암호화 합니다. 고객 관리 키는 Azure Key Vault에 저장 됩니다. Azure Key Vault를 사용 하 여 키를 만들고 유지 관리 하는 방법에 대 한 자세한 내용은 Microsoft Azure 설명서의 [Azure 디스크 저장소의 서버 쪽 암호화](../key-vault/general/basic-concepts.md) 를 참조 하세요.

> [!IMPORTANT]
> ARO 미리 보기 기능은 셀프 서비스에서 사용할 수 있습니다(옵트인 방식). 미리 보기 기능은 "있는 그대로" 제공 되며 "사용 가능"으로 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. 미리 보기 기능은 최고의 노력으로 고객 지원팀에서 부분적으로 다룹니다. 따라서 이러한 기능은 프로덕션 용도로 사용할 수 없습니다.

## <a name="limitation"></a>제한 사항
Azure에서 설정 된 Key Vault 및 디스크 암호화를 유지 관리 하는 것은 고객의 책임입니다. 키를 유지 하지 못하면 ARO 클러스터가 손상 됩니다. Vm 작동이 중지 되므로 전체 ARO 클러스터의 작동이 중지 됩니다. Azure Red Hat OpenShift 엔지니어링 팀은 키에 액세스할 수 없습니다. 따라서 키는 키를 백업, 복제 또는 검색할 수 없습니다. 디스크 암호화 집합을 사용 하 여 암호화 키를 관리 하는 방법에 대 한 자세한 내용은 Microsoft Azure 설명서에서 [Azure 디스크 저장소의 서버 쪽 암호화](../virtual-machines/disk-encryption.md) 를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항
* [사용 권한을 확인](tutorial-create-cluster.md#verify-your-permissions)합니다. 참가자 및 사용자 액세스 관리자 권한 또는 소유자 권한이 있어야 합니다.
* 여러 Azure 구독이 있는 경우 리소스 공급자를 등록 합니다. 등록에 대 한 자세한 내용은 [리소스 공급자 등록](tutorial-create-cluster.md#register-the-resource-providers)을 참조 하세요.

## <a name="install-the-preview-azure-cli-extension"></a>Preview Azure CLI 확장을 설치 합니다.
CLI (Azure Command-Line 인터페이스)를 설치 하 고 사용 하 여 Key Vault를 만듭니다. Azure CLI를 사용 하면 대화형 명령줄 프롬프트 또는 스크립트를 사용 하 여 터미널을 통해 명령을 실행할 수 있습니다.
> [!NOTE]
> CLI 확장은 미리 보기 기능에만 필요 합니다.

1. Python 휠 및 CLI 확장을 모두 다운로드 하려면 다음 URL을 클릭 합니다.  [https://aka.ms/az-aroext-latest.whl](https://aka.ms/az-aroext-latest.whl)
1. 다음 명령을 실행합니다.
    ```azurecli-interactive
    az extension add --upgrade -s <path to downloaded .whl file>
    ```
1. CLI 확장이 사용 중인지 확인 합니다.
    ```azurecli-interactive
    az extension list
    [
      {
        "experimental": false,
        "extensionType": "whl",
        "name": "aro",
        "path": "<path may differ depending on system>",
        "preview": true,
        "version": "1.0.1"
      }
    ]
    ```

## <a name="create-a-virtual-network-containing-two-empty-subnets"></a>두 개의 빈 서브넷이 있는 가상 네트워크 만들기
두 개의 빈 서브넷을 포함 하는 가상 네트워크를 만듭니다. 요구 사항에 맞는 기존 가상 네트워크가 있는 경우이 단계를 건너뛸 수 있습니다. 가상 네트워크를 만드는 절차를 검토 하려면 [두 개의 빈 서브넷이 포함 된 가상 네트워크 만들기](tutorial-create-cluster.md#create-a-virtual-network-containing-two-empty-subnets)를 참조 하세요.

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault 인스턴스 만들기
Azure Key Vault 인스턴스를 사용 하 여 키를 저장 해야 합니다. 제거 보호를 사용하는 새 키 자격 증명 모음을 만듭니다. 그런 다음 Key Vault 내에 새 키를 만들어 사용자 고유의 사용자 지정 키를 저장 합니다.
1. 추가 환경 권한 설정:
    ```
    export KEYVAULT_NAME=$USER-enckv
    export KEYVAULT_KEY_NAME=$USER-key
    export DISK_ENCRYPTION_SET_NAME=$USER-des
    ```
1. Key Vault에서 Key Vault 및 키를 만듭니다.
    ```azurecli-interactive
    az keyvault create -n $KEYVAULT_NAME \
                   -g $RESOURCEGROUP \
                   -l $LOCATION \
                   --enable-purge-protection true \
                   --enable-soft-delete true

    az keyvault key create --vault-name $KEYVAULT_NAME \
                           -n $KEYVAULT_KEY_NAME \
                           --protection software

    KEYVAULT_ID=$(az keyvault show --name $KEYVAULT_NAME --query "[id]" -o tsv)

    KEYVAULT_KEY_URL=$(az keyvault key show --vault-name $KEYVAULT_NAME \
                                            --name $KEYVAULT_KEY_NAME \
                                            --query "[key.kid]" -o tsv)
    ```

## <a name="create-an-azure-disk-encryption-set"></a>Azure Disk Encryption 집합 만들기
Azure Disk Encryption 집합은 ARO 클러스터의 디스크에 대 한 참조 지점으로 사용 됩니다. 이전 단계에서 만든 Azure Key Vault에 연결 되 고 해당 위치에서 고객이 관리 하는 키를 가져옵니다.
```azurecli-interactive
az disk-encryption-set create -n $DISK_ENCRYPTION_SET_NAME \
                              -l $LOCATION \
                              -g $RESOURCEGROUP \
                              --source-vault $KEYVAULT_ID \
                              --key-url $KEYVAULT_KEY_URL

DES_ID=$(az disk-encryption-set show -n $DISK_ENCRYPTION_SET_NAME -g $RESOURCEGROUP --query 'id' -o tsv)

DES_IDENTITY=$(az disk-encryption-set show -n $DISK_ENCRYPTION_SET_NAME \
                                           -g $RESOURCEGROUP \
                                           --query "[identity.principalId]" \
                                           -o tsv)
```

## <a name="grant-permissions-for-the-disk-encryption-set-to-access-the-key-vault"></a>Key Vault에 액세스할 수 있도록 설정 된 디스크 암호화에 대 한 사용 권한 부여
이전 단계에서 만든 디스크 암호화 집합을 사용 하 고 Azure Key Vault에 액세스 하 고 사용할 수 있도록 디스크 암호화 집합에 권한을 부여 합니다.
```azurecli-interactive
az keyvault set-policy -n $KEYVAULT_NAME \
                       -g $RESOURCEGROUP \
                       --object-id $DES_IDENTITY \
                       --key-permissions wrapkey unwrapkey get
```

## <a name="create-an-aro-cluster"></a>ARO 클러스터 만들기
고객 관리 키를 사용 하는 ARO 클러스터를 만듭니다.
```azurecli-interactive
az aro create --resource-group $RESOURCEGROUP \
              --name $CLUSTER  \
              --vnet aro-vnet  \
              --master-subnet master-subnet \
              --worker-subnet worker-subnet \
              --disk-encryption-set $DES_ID
```
ARO 클러스터를 만든 후 모든 Vm은 고객이 관리 하는 암호화 키를 사용 하 여 암호화 됩니다.

키를 올바르게 구성 했는지 확인 하려면 다음 명령을 실행 합니다.
1. 클러스터 Vm, 디스크 등이 있는 클러스터 리소스 그룹의 이름을 가져옵니다.
    ```azurecli-interactive
    CLUSTERRESOURCEGROUP=$(az aro show --resource-group $RESOURCEGROUP --name $CLUSTER --query 'clusterProfile.resourceGroupId' -o tsv | cut -d '/' -f 5)
    ```
2. 디스크에 올바른 디스크 암호화 집합이 연결 되어 있는지 확인 합니다.
    ```azurecli-interactive
    az disk list -g $CLUSTERRESOURCEGROUP --query '[].encryption'
    ```
    `diskEncryptionSetId`출력의 필드는 ARO 클러스터를 만드는 동안 지정한 디스크 암호화 집합을 가리켜야 합니다.
