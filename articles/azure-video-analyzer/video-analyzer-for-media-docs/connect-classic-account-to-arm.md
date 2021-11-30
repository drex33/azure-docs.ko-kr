---
title: ARM에 대 한 미디어 계정에 대 한 클래식 비디오 분석기 커넥트
description: 이 항목에서는 기존에 사용 되는 미디어 계정을 ARM 기반 계정에 연결 하는 방법을 설명 합니다.
ms.topic: how-to
ms.author: itnorman
ms.date: 10/19/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 24a09ddcdd18608010815aad486e209c62d8a25f
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133288357"
---
# <a name="connect-an-existing-classic-paid-video-analyzer-for-media-account-to-arm-based-account"></a>미디어 계정에 대 한 기존 클래식 유료 비디오 분석기를 ARM 기반 계정으로 커넥트  

이 문서에서는 기존에 사용 되는 미디어 계정을 Azure Resource Manager (ARM) 기반 계정에 연결 하는 방법에 대해 자세히 설명 합니다.
오늘날 미디어 (이전의 Video Indexer) 용 Azure Video Analyzer는 Azure의 ARM 리소스가 아닌 GA (일반 공급) 제품입니다.
이 문서에서는 미디어 계정에 대 한 **기존** 비디오 분석기를 [ARM][docs-arm-overview]에 연결 하는 옵션을 안내 합니다.

## <a name="prerequisites"></a>필수 조건

* 미디어 계정에 대 한 무제한 유료 비디오 분석기 (클래식 계정).
  * ARM에 연결 작업을 수행 하려면 미디어 계정에 대 한 비디오 분석기에 대 한 소유자 권한이 있어야 합니다.
* Azure 구독.
* 사용자 할당 관리 id (흐름을 따라 만들 수 있음)

#### <a name="transition-state-and-recommended-steps-before-connecting-a-classic-account-to-be-arm-based"></a>클래식 계정을 ARM 기반으로 연결 하기 전의 전환 상태 및 권장 단계

연결 프로세스에서 계정 관리는 ARM에 연결 되며이는 계정에 대 한 30 일의 전환 상태를 트리거합니다. 이 상태에서 연결 된 계정은 API Management (클래식 방식)을 [통해 생성](https://aka.ms/avam-dev-portal)된 액세스 토큰 또는 ARM을 통해 생성 된 액세스 토큰을 통해 API를 통해 액세스할 수 있습니다. ARM에서 관리 하도록 모든 계정 관리를 이동 하는 전환 상태는 [AZURE RBAC][docs-rbac-overview]에서 처리 하므로 미디어 포털에 대 한 비디오 분석기의 사용자 초대 기능을 사용 하지 않습니다. 그러면 초대 된 모든 사용자가이 계정에 대해 Media Analyzer 용 비디오 분석기 미디어 포털에 대 한 액세스 권한을 잃게 됩니다. 물론 Azure RBAC를 통해 이러한 모든 사용자에 게 올바른 역할 할당을 할당 하 여 쉽게 해결할 수 있습니다 ([RBAC를 할당 하는 방법][docs-rbac-assignment]). 연결 작업을 수행한 계정 소유자만 연결 된 계정에서 소유자로 자동 할당 됩니다. 또한 [Azure 정책이][docs-governance-policy] 적용 되는 경우이 계정의 설정이 재정의 됩니다.     
Azure RBAC를 통해 계정에 사용자를 추가 하지 않은 경우, 30 일 후에 API를 통해 액세스 권한을 잃게 됩니다. 전환 상태가 종료 된 후에는 사용자가 APIM (클래식 방식)을 통해 더 이상 유효한 액세스 토큰을 생성할 수 없으며 ARM을 통해서만 생성할 수 있습니다. Azure RBAC를 사용 하 여 계정에 대 한 역할 기반 액세스 제어를 관리할 수 있습니다.

> [!NOTE]
> 초대 된 사용자가 30 일이 지난 후에 액세스를 제거 하려는 경우 계정을 ARM에 연결 **하기 전에** 미디어 계정 설정에 대 한 Azure Video Analyzer의 계정 설정을 통해 액세스를 제거 해야 합니다. 

## <a name="get-started"></a>시작하기

### <a name="browse-to-video-analyzer-for-media-portal"></a>[미디어 포털로 비디오 분석기](https://aka.ms/vi-portal-link) 로 이동 합니다.

1. AAD 계정을 사용 하 여 로그인 합니다.
1. 오른쪽 위 막대에서 *사용자 계정* 을 눌러 왼쪽 창 계정 목록을 엽니다.
3. ARM에 연결 하려는 미디어 클래식 계정에 대 한 비디오 분석기를 선택 합니다 (클래식 계정은 *클래식 태그로* 태그가 지정 됨).
4. **설정** 을 클릭합니다.

  ![계정-설정](media/connect-classic-account-to-arm/user-account-settings.png)
   
5. **ARM 기반 계정에 커넥트를** 클릭 합니다.

  ![연결-단추-포털](media/connect-classic-account-to-arm/connect-button.png)

7. Azure Portal에 로그인합니다.
8. 미디어 만들기 블레이드에 대 한 비디오 분석기가 열립니다.
10. **미디어 계정에 대 한 비디오 분석기 만들기** 섹션에서 필요한 값을 입력 합니다.
    * 필드를 자동으로 채우는 단계를 수행한 경우 적격 값의 유효성을 검사 해야 합니다.

 ![arm 연결](media/connect-classic-account-to-arm/connect-blade-new.png)

 | Name | Description |
 | ---|---|
 |**구독**| 현재 구독에는 클래식 계정 및 Media Services 같은 기타 관련 리소스가 포함 되어 있습니다.|
 |**리소스 그룹**|기존 리소스를 선택하거나 새 리소스를 만듭니다. 리소스 그룹은 연결 된 클래식 계정과 동일한 위치에 있어야 합니다.|
 |**미디어 계정에 대 한 비디오 분석기** (라디오 단추)| *"기존 클래식 계정 연결"* 을 선택 합니다.|
 |**기존 계정 ID**| 미디어 클래식 계정에 대 한 기존 비디오 분석기의 ID를 입력 합니다.|
 |**리소스 이름**|미디어 계정에 대 한 새 비디오 분석기의 이름을 입력 합니다. 기본값은 계정에서 클래식으로 사용한 이름과 동일 합니다.|
 |**위치**|연결 프로세스에서 지리적 지역을 변경할 수 없습니다. 연결 된 계정은 동일한 지역에 유지 되어야 합니다. |
 |**Media Services 계정 이름**|클래식 계정과 연결 된 원래 Media Services 계정 이름입니다.|
 |**사용자 할당 관리 ID**|사용자 할당 관리 id를 선택 하거나 새로 만듭니다. 미디어 계정에 대 한 비디오 분석기는 미디어 서비스에 액세스 하는 데 사용 합니다. 사용자 할당 관리 id에는 미디어 서비스 계정에 대 한 참가자 역할이 할당 됩니다.|

1. 양식 아래쪽에서 **검토 + 만들기** 를 클릭합니다.

### <a name="next-steps"></a>다음 단계

[C#을 사용하여 비디오를 업로드](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/ApiUsage/ArmBased)방법에 대해 알아봅니다.
  
<!-- links -->
[docs-arm-overview]: ../../azure-resource-manager/management/overview.md
[docs-rbac-overview]: ../../role-based-access-control/overview.md
[docs-rbac-assignment]: ../../role-based-access-control/role-assignments-portal.md
[docs-governance-policy]: ../../governance/policy/overview.md
