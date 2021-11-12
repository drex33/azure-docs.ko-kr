---
title: Azure 관리 ID를 사용하여 환경 만들기
description: Azure에서 관리 ID를 사용하여 Azure DevTest Labs의 랩에 환경을 배포하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 220937d20c63420501e3ef6bb1b6c5f8a820613d
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400322"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Azure 관리 ID를 사용하여 랩에 환경 배포 

랩 소유자는 관리 ID를 사용하여 랩에 환경을 배포할 수 있습니다. 이 기능은 환경에 환경의 리소스 그룹 외부에 있는 Azure 리소스를 포함하거나 참조하는 시나리오에 도움이 됩니다. 이러한 리소스에는 키 자격 증명 모음, 공유 이미지 갤러리 및 네트워크가 포함됩니다. 관리 ID를 사용하면 해당 환경의 리소스 그룹으로 제한되지 않는 샌드박스 환경을 만들 수 있습니다. 

기본적으로 환경을 만들 때 랩은 ARM 템플릿(Azure Resource Manager 템플릿)을 배포하는 동안 시스템 할당 ID를 만듭니다. 시스템 할당 ID는 랩 사용자를 대신하여 Azure 리소스 및 서비스에 액세스합니다. DevTest Labs는 랩 환경을 처음 만들 때 기본적으로 시스템 할당 ID를 만듭니다. [랩에서 시스템 할당 ID를 만드는 이유](configure-lab-identity.md#scenarios-for-using-labs-system-assigned-identity)에 대해 자세히 알아봅니다. 

랩 소유자는 랩 외부의 Azure 리소스에 액세스할 수 있는 랩의 시스템 할당 ID 권한을 부여하도록 선택할 수 있습니다. 시나리오에 사용자 할당 ID를 사용할 수도 있습니다. 랩의 시스템 할당 ID는 랩의 수명 동안만 유효합니다. 랩을 삭제하면 시스템 할당 식별자도 삭제됩니다. ID를 사용해야 하는 여러 랩에 환경이 있는 경우 사용자 할당 ID를 사용하는 것이 좋습니다.  

> [!NOTE]
> 현재 랩별로 단일 사용자 할당 ID가 지원됩니다. 

## <a name="prerequisites"></a>사전 요구 사항

- [Azure Portal을 사용하여 사용자 할당 관리 ID를 생성, 나열, 삭제 또는 할당](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    관리 ID가 랩과 동일한 지역 및 구독에 만들어졌는지 확인합니다. 관리 ID는 동일한 리소스 그룹에 있을 필요가 없습니다.

## <a name="use-azure-portal"></a>Azure Portal 사용

이 섹션에서 랩 소유자는 Azure Portal을 사용하여 랩에 사용자 관리 ID를 추가합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **DevTest Labs** 를 검색합니다.
1. 랩 목록에서 원하는 랩을 선택합니다.
1. **구성 및 정책** -> **ID(미리 보기)** 를 선택합니다. 
1. 사용자 할당 ID를 추가하려면 **사용자 할당** 탭을 선택합니다.
1. **추가** 를 누릅니다.
1. 드롭다운에서 만들거나 액세스할 수 있는 기존 사용자를 선택합니다.
 
    ![사용자 관리 ID 추가](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. 페이지 위쪽에서 **저장** 을 누릅니다.

    저장되면 랩은 모든 랩 환경을 배포하는 동안 이 ID를 사용합니다. 목록에서 ID를 선택하여 Azure에서 ID 리소스에 액세스할 수도 있습니다. 

랩 소유자는 환경을 배포하기 위해 특별한 작업을 수행할 필요가 없습니다. 랩에 추가된 ID에는 환경에서 액세스해야 하는 외부 리소스에 대한 권한이 있어야 합니다. 

랩에 할당된 사용자 관리 ID를 변경하려면 먼저 랩에 연결된 ID를 제거한 다음, 랩에 다른 ID를 추가합니다. 랩에 연결된 ID를 제거하려면 **...(줄임표)** 를 클릭하고 **제거** 를 클릭합니다. 

## <a name="use-api"></a>API 사용

1. ID를 만든 후에는 이 ID의 리소스 ID를 확인합니다. 다음 샘플과 유사해야 합니다. 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}`.

1. 랩 리소스에서 PUT HTTPS 메서드를 수행하여 사용자 할당 ID를 추가하거나 랩에 시스템 할당 ID를 사용하도록 설정합니다.

   > [!NOTE]
   > 사용자 할당 ID를 만들지 여부에 관계없이 랩 환경이 처음 만들어질 때 랩에서 시스템 할당 ID를 자동으로 만듭니다. 그러나 사용자 할당 ID가 랩에 대해 이미 구성된 경우 DevTest Lab 서비스는 해당 ID를 계속 사용하여 랩 환경을 배포합니다. 
 
    ```json
    
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{labname}

    {
        "location": "{location}",
        "properties": {
          **lab properties**
         } 
        "identity":{
            "type": "SystemAssigned,UserAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}":{}
            }
        } 
    }
    
    ```
 
랩에 사용자 할당 ID를 추가하면 DevTest Labs 서비스는 Azure Resource Manager 환경을 배포할 때 ID를 사용합니다. 예를 들어 공유 이미지 갤러리 이미지에 액세스하기 위해 Resource Manager 템플릿이 필요한 경우 ID에 공유 이미지 갤러리 리소스에 필요한 권한이 있는지 확인합니다. 
