---
title: 클래식 미디어용 Video Analyzer 계정을 ARM에 커넥트
description: 이 항목에서는 기존 클래식 유료 미디어용 Azure Video Analyzer 계정을 ARM 기반 계정에 연결하는 방법에 대해 설명합니다.
ms.topic: how-to
ms.author: itnorman
ms.date: 10/19/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 14ab4e2b3c95823359dc1f1f88b8368ce58afef9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312598"
---
# <a name="connect-an-existing-classic-paid-video-analyzer-for-media-account-to-arm-based-account"></a>기존 클래식 유료 Media용 Video Analyzer 계정을 ARM 기반 계정에 커넥트  

이 문서에서는 기존 클래식 유료 미디어용 Azure Video Analyzer 계정을 ARM(Azure Resource Manager) 기반 계정에 연결하는 방법을 자세히 설명합니다.
현재 미디어용 Azure Video Analyzer(이전의 Video Indexer)는 Azure의 ARM 리소스가 아닌 GA(일반 공급) 제품입니다.
이 문서에서는 **기존** 미디어용 Video Analyzer 계정을 [ARM에][docs-arm-overview]연결하는 옵션을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 미디어 계정(클래식 계정)용 무제한 유료 Video Analyzer.
  * ARM에 연결 작업을 수행하려면 미디어용 Video Analyzer 계정에 대한 소유자 권한이 있어야 합니다.
* Azure 구독.
* 사용자 할당 관리 ID(흐름을 따라 만들 수 있습니다.)

#### <a name="transition-state-and-recommended-steps-before-connecting-a-classic-account-to-be-arm-based"></a>클래식 계정을 ARM 기반 계정으로 연결하기 전 전환 상태 및 권장 단계

연결 프로세스에서 계정 관리는 ARM에 연결되며, 이는 계정에 대한 전환 상태 30일을 트리거합니다. 이 상태에서는 API Management(클래식 방식)를 통해 생성된 액세스 토큰 또는 ARM을 [통해 생성된](https://aka.ms/avam-dev-portal)액세스 토큰을 통해 API를 통해 연결된 계정에 액세스할 수 있습니다. ARM에서 관리하도록 모든 계정 관리를 이동하는 전환 상태는 Azure [RBAC에서][docs-rbac-overview]계정 관리가 처리되기 때문에 Media용 Video Analyzer 포털에서 초대 사용자 기능을 사용하지 않도록 설정합니다. 이렇게 하면 이 계정의 초대된 모든 사용자가 미디어용 Video Analyzer 계정 미디어 포털에 대한 액세스 권한을 잃게 됩니다. 물론 Azure RBAC를 통해 이러한 모든 사용자에게 올바른 역할 할당을 할당하여 RBAC를 [할당하는 방법을][docs-rbac-assignment]통해 쉽게 해결할 수 있습니다. 연결 작업을 수행한 계정 소유자만 연결된 계정의 소유자로 자동으로 할당됩니다. 사용자가 Azure RBAC를 통해 계정에 추가되지 않으면 전환 상태가 종료된 후 더 이상 APIM(클래식 방식)을 통해서만 유효한 액세스 토큰을 생성할 수 없으므로 30일 후에는 API를 통해서도 액세스 권한이 손실됩니다. Azure RBAC를 계정에서 역할 기반 액세스 제어를 관리하는 단독 방법 만들기

> [!NOTE]
> 전환 상태 30일이 끝나기 전에 액세스 권한을 제거하려는 초대된 사용자가 있는 경우 ARM에 계정을 **연결하기 전에** 미디어용 Azure Video Analyzer 계정 설정의 계정 설정을 통해 액세스 권한을 제거해야 합니다. 

## <a name="get-started"></a>시작

### <a name="browse-to-video-analyzer-for-media-portal"></a>미디어 [포털용 Video Analyzer로](https://aka.ms/vi-portal-link) 이동

1. AAD 계정을 사용하여 로그인합니다.
1. 오른쪽 위 막대에서 *사용자 계정을* 눌러 사이드 창 계정 목록을 엽니다.
3. ARM에 연결하려는 Media용 Video Analyzer 클래식 계정을 선택합니다(클래식 *계정에는 클래식 태그가* 지정됩니다).
4. **설정** 을 클릭합니다.

  ![account-settings](media/connect-classic-account-to-arm/user-account-settings.png)
   
5. **ARM 기반 계정 에 대한 커넥트** 클릭합니다.

  ![connect-button-portal](media/connect-classic-account-to-arm/connect-button.png)

7. Azure Portal에 로그인합니다.
8. 미디어 만들기 블레이드용 Video Analyzer가 열립니다.
10. 미디어 **계정용 Video Analyzer 만들기** 섹션에서 필수 값을 입력합니다.
    * 필드를 자동으로 채워야 하는 단계를 따른 경우 적합한 값의 유효성을 검사해야 합니다.

 ![connect-to-arm](media/connect-classic-account-to-arm/connect-blade-new.png)

 | 이름 | 설명 |
 | ---|---|
 |**구독**| 구독에는 현재 클래식 계정 및 Media Services 같은 기타 관련 리소스가 포함되어 있습니다.|
 |**리소스 그룹**|기존 리소스를 선택하거나 새 리소스를 만듭니다. 리소스 그룹은 연결된 클래식 계정과 동일한 위치여야 합니다.|
 |**미디어 계정용 Video Analyzer(라디오** 단추)| *"기존 클래식 계정 연결" 을* 선택합니다.|
 |**기존 계정 ID**| 미디어 클래식 계정에 대한 기존 Video Analyzer의 ID를 입력합니다.|
 |**리소스 이름**|미디어용 새 Video Analyzer 계정의 이름을 입력합니다. 기본값은 계정이 클래식과 동일한 이름입니다.|
 |**위치**|연결 프로세스에서 지리적 지역을 변경할 수 있으며 연결된 계정은 동일한 지역에 있어야 합니다. |
 |**Media Services 계정 이름**|클래식 계정과 연결된 원래 Media Services 계정 이름입니다.|
 |**사용자 할당 관리 ID**|사용자 할당 관리 ID를 선택하거나 새 관리 ID를 만듭니다. Media용 Video Analyzer 계정은 Media Services에 액세스하는 데 사용합니다. 사용자 할당 관리 ID에는 Media Service 계정에 대한 기여자 역할이 할당됩니다.|

1. 양식 아래쪽에서 **검토 + 만들기** 를 클릭합니다.

### <a name="next-steps"></a>다음 단계

[C#을 사용하여 비디오를 업로드](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/ApiUsage/ArmBased)방법에 대해 알아봅니다.
  
<!-- links -->
[docs-arm-overview]: ../../azure-resource-manager/management/overview.md
[docs-rbac-overview]: ../../role-based-access-control/overview.md
[docs-rbac-assignment]: ../../role-based-access-control/role-assignments-portal.md
