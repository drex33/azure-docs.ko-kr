---
title: 신뢰할 수 있는 Azure 서비스를 사용하여 네트워크 제한 레지스트리 액세스
description: 신뢰할 수 있는 Azure 서비스 인스턴스가 네트워크 제한 컨테이너 레지스트리에 안전하게 액세스하여 이미지를 끌어오거나 푸시할 수 있도록 합니다.
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: f99215059308c6a2db1e7bce6b9f03580d2b53a4
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286273"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>신뢰할 수 있는 서비스에서 네트워크 제한 컨테이너 레지스트리에 안전하게 액세스하도록 허용(미리 보기)

Azure Container Registry는 신뢰할 수 있는 Azure 서비스를 선택하여 네트워크 액세스 규칙으로 구성된 레지스트리에 액세스할 수 있도록 합니다. 신뢰할 수 있는 서비스를 사용할 수 있는 경우 신뢰할 수 있는 서비스 인스턴스는 레지스트리의 네트워크 규칙을 안전하게 우회하고 이미지 끌어오기 또는 푸시와 같은 작업을 수행할 수 있습니다. 이 문서에서는 네트워크 제한 Azure Container Registry로 신뢰할 수 있는 서비스를 사용하도록 설정하고 사용하는 방법을 설명합니다.

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 문서의 명령 예를 실행합니다. 로컬로 사용하려는 경우 2.18 이상 버전이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

신뢰할 수 있는 Azure 서비스에서 레지스트리 액세스 허용은 **미리 보기** 기능입니다.

## <a name="limitations"></a>제한 사항

* 관리 ID가 필요한 레지스트리 액세스 시나리오의 경우 시스템 할당 ID만 사용할 수 있습니다. 사용자 할당 관리 ID는 현재 지원되지 않습니다.
* 신뢰할 수 있는 서비스 허용은 [서비스 엔드포인트](container-registry-vnet.md)로 구성된 컨테이너 레지스트리에 적용되지 않습니다. 이 기능은 [프라이빗 엔드포인트](container-registry-private-link.md)로 제한되거나 [공용 IP 액세스 규칙](container-registry-access-selected-networks.md)이 적용된 레지스트리에만 영향을 줍니다. 

## <a name="about-trusted-services"></a>신뢰할 수 있는 서비스 정보

Azure Container Registry에는 다음을 포함하여 레지스트리에 대한 액세스를 제한하는 여러 네트워크 구성을 지원하는 계층화된 보안 모델이 있습니다.

* [Azure Private Link가 있는 프라이빗 엔드포인트](container-registry-private-link.md). 구성된 경우에, 레지스트리의 프라이빗 엔드포인트는 개인 IP 주소를 사용하여 가상 네트워크 내의 리소스에만 액세스할 수 있습니다.  
* [레지스트리 방화벽 규칙](container-registry-access-selected-networks.md), 이는 특정 공용 IP 주소 또는 주소 범위에서만 레지스트리의 공용 엔드포인트에 대한 액세스를 허용합니다. 또한 프라이빗 엔드포인트를 사용할 때 방화벽을 구성하여 퍼블릭 엔드포인트에 대한 모든 액세스를 차단할 수도 있습니다.

가상 네트워크에 배포되거나 방화벽 규칙을 사용하여 구성된 경우 레지스트리는 해당 원본 외부의 사용자 또는 서비스에 대한 액세스를 거부합니다. 

여러 다중 테넌트 Azure 서비스는 이러한 레지스트리 네트워크 설정에 포함될 수 없는 네트워크에서 작동하여 레지스트리에 이미지를 끌어오거나 푸시하는 등의 작업을 수행하는 것을 방지합니다. 특정 서비스 인스턴스를 "신뢰할 수 있음"으로 지정하면 레지스트리 소유자가 선택한 Azure 리소스가 레지스트리의 네트워크 설정을 안전하게 바이패스하여 레지스트리 작업을 수행하도록 허용할 수 있습니다. 

### <a name="trusted-services"></a>신뢰할 수 있는 서비스

다음 서비스의 인스턴스는 레지스트리의 **신뢰할 수 있는 서비스 허용** 설정이 사용된 경우(기본값) 네트워크 제한 컨테이너 레지스트리에 액세스할 수 있습니다. 시간이 지남에 따라 더 많은 서비스가 추가될 것입니다.

표시된 경우 신뢰할 수 있는 서비스에서 액세스하려면 서비스 인스턴스에서 관리 ID의 추가 구성, [RBAC 역할](container-registry-roles.md) 할당 및 레지스트리 인증이 필요합니다. 단계 예는 이 문서 뒷부분의 [신뢰할 수 있는 서비스 워크플로](#trusted-services-workflow)를 참조하세요.

|신뢰할 수 있는 서비스  |지원되는 사용 시나리오  | RBAC 역할로 관리 ID 구성
|---------|---------|------|
| Azure Security Center | [컨테이너 레지스트리용 Azure Defender](scan-images-defender.md)를 통한 취약성 검사 | No |
|ACR 작업     | [ACR 작업에서 상위 레지스트리 또는 다른 레지스트리에 액세스](container-registry-tasks-cross-registry-authentication.md)       | Yes |
|Machine Learning | 사용자 지정 Docker 컨테이너 이미지를 사용하여 Machine Learning 작업 영역에서 모델을 [배포](../machine-learning/how-to-deploy-custom-container.md) 또는 [학습](../machine-learning/how-to-train-with-custom-image.md)합니다. | Yes |
|Azure Container Registry | [다른 Azure Container Registry에서 이미지 가져오기](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | No |

> [!NOTE]
> 현재 신뢰할 수 있는 서비스 허용 설정을 사용하는 것은 App Service 및 Azure Container Instances를 비롯한 특정 다른 관리형 Azure 서비스에 적용되지 않습니다.

## <a name="allow-trusted-services---cli"></a>신뢰할 수 있는 서비스 허용 - CLI

기본적으로 새 Azure Container Registry에서 신뢰할 수 있는 서비스 허용 설정이 사용됩니다. [az acr update](/cli/azure/acr#az_acr_update) 명령을 실행하여 설정을 사용하거나 사용하지 않도록 설정합니다.

사용하지 않도록 설정하려면:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

기존 레지스트리 또는 이미 사용하지 않도록 설정된 레지스트리에서 설정을 사용하도록 설정하려면 다음을 수행합니다.

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>신뢰할 수 있는 서비스 허용 - 포털

기본적으로 새 Azure Container Registry에서 신뢰할 수 있는 서비스 허용 설정이 사용됩니다. 

포털에서 설정을 사용하지 않도록 설정하거나 다시 사용하도록 설정하려면:

1. 포털에서 컨테이너 레지스트리로 이동합니다.
1. **설정** 에서 **네트워킹** 을 선택합니다. 
1. **공용 네트워크 액세스 허용** 에서 **선택한 네트워크** 또는 **사용 안 함** 을 선택합니다.
1. 다음 작업 중 하나를 수행합니다.
    * 신뢰할 수 있는 서비스의 액세스를 사용하지 않도록 설정하려면 **방화벽 예외** 에서 **신뢰할 수 있는 Microsoft 서비스에서 이 컨테이너 레지스트리에 액세스할 수 있도록 허용** 을 선택 취소합니다. 
    * 신뢰할 수 있는 서비스를 허용하려면 **방화벽 예외** 에서 **신뢰할 수 있는 Microsoft 서비스에서 이 컨테이너 레지스트리에 액세스할 수 있도록 허용** 을 선택합니다.
1. **저장** 을 선택합니다.

## <a name="trusted-services-workflow"></a>신뢰할 수 있는 서비스 워크플로

신뢰할 수 있는 서비스의 인스턴스에서 네트워크 제한 컨테이너 레지스트리에 액세스할 수 있도록 하는 일반적인 워크플로는 다음과 같습니다. 이 워크플로는 서비스 인스턴스의 관리 ID를 사용하여 레지스트리의 네트워크 규칙을 바이패스할 때 필요합니다.

1. Azure Container Registry에 대한 [신뢰할 수 있는 서비스](#trusted-services) 중 하나의 인스턴스에서 [Azure 리소스에 대한 시스템 할당 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 설정합니다.
1. ID [Azure 역할](container-registry-roles.md)을 레지스트리에 할당합니다. 예를 들어 컨테이너 이미지를 끌어오기 위해 ACRPull 역할을 할당합니다.
1. 네트워크 제한 레지스트리에서 신뢰할 수 있는 서비스의 액세스를 허용하는 설정을 구성합니다.
1. 네트워크 제한 레지스트리를 사용하여 인증하려면 ID의 자격 증명을 사용합니다. 
1. 레지스트리에서 이미지를 끌어오거나 역할에서 허용하는 다른 작업을 수행합니다.

### <a name="example-acr-tasks"></a>예: ACR 작업

다음 예에서는 ACR 작업을 신뢰할 수 있는 서비스로 사용하는 방법을 보여 줍니다. 작업 세부 정보는 [Azure 관리 ID를 사용하는 ACR 작업의 레지스트리 간 인증](container-registry-tasks-cross-registry-authentication.md)을 참조하세요.

1. Azure Container Registry를 만들거나 업데이트합니다.
ACR 작업을 [만듭니다](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity). 
    * 작업을 만들 때 시스템 할당 관리 ID를 사용하도록 설정합니다.
    * 작업의 기본 인증 모드(`--auth-mode None`)를 사용하지 않도록 설정합니다.
1. 작업 ID [레지스트리에 액세스하기 위한 Azure 역할](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources)을 할당합니다. 예를 들어 이미지를 풀하고 푸시할 수 있는 권한이 있는 AcrPush 역할을 할당합니다.
2. 작업에 [레지스트리의 관리 ID 자격 증명을 추가](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task)합니다.
3. 작업이 네트워크 제한을 바이패스하는지 확인하려면 레지스트리에서 [공용 액세스를 사용하지 않도록 설정](container-registry-access-selected-networks.md#disable-public-network-access)합니다.
4. 작업을 실행합니다. 레지스트리 및 작업이 올바르게 구성된 경우 레지스트리가 액세스를 허용하므로 작업이 성공적으로 실행됩니다.

신뢰할 수 있는 서비스에 의한 액세스 사용 안 함을 테스트하려면:

1. 신뢰할 수 있는 서비스의 액세스를 허용하려면 설정을 사용하지 않습니다.
1. 작업을 다시 실행합니다. 이 경우 레지스트리에서 작업의 액세스를 더 이상 허용하지 않기 때문에 작업이 실행되지 않습니다.

## <a name="next-steps"></a>다음 단계

* 가상 네트워크에서 프라이빗 엔드포인트를 사용하여 레지스트리에 대한 액세스를 제한하려면 [Azure 컨테이너에 대한 Azure Private Link 구성](container-registry-private-link.md)을 참조하세요.
* 레지스트리 방화벽 규칙을 설정하려면 [공개 IP 네트워크 규칙 구성](container-registry-access-selected-networks.md)을 참조하세요.