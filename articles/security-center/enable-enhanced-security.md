---
title: 클라우드용 Microsoft Defender의 통합 워크로드 보호 사용
description: 향상된 보안 기능을 사용하여 클라우드용 Microsoft Defender의 보호 기능을 하이브리드 및 다중 클라우드 리소스로 확장하는 방법을 알아봅니다.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 06/07/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 44acbc3ace06af2e741b3daf5c6afe9a50a2efe0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029637"
---
# <a name="quickstart-enable-enhanced-security-features"></a>빠른 시작: 향상된 보안 기능 사용

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

향상된 보안 기능의 이점에 대해 알아보려면 [클라우드용 Microsoft Defender의 향상된 보안 기능](enhanced-security-features-overview.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Defender for Cloud 빠른 시작 및 자습서를 활용하기 위해 향상된 보안 기능을 사용하도록 설정해야 합니다. 

Defender for Cloud의 향상된 보안 기능을 사용하여 전체 Azure 구독을 보호할 수 있으며, 보호는 구독 내의 모든 리소스에 상속됩니다.

30일 평가판을 사용할 수 있습니다. 선택한 통화와 지역에 따른 가격 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

## <a name="enable-enhanced-security-features-from-the-azure-portal"></a>Azure Portal의 향상된 보안 기능 사용

위협 방지 기능을 포함하여 모든 Defender for Cloud 기능을 사용하려면 해당 워크로드가 포함된 구독에서 향상된 보안 기능을 사용하도록 설정해야 합니다. 작업 영역 수준에서 사용하도록 설정해도 Just-In-Time VM 액세스, 적응형 애플리케이션 제어 및 Azure 리소스에 대한 네트워크 검색 기능은 사용하도록 설정되지 않습니다. 또한 작업 영역 수준에서 사용할 수 있는 유일한 Microsoft Defender 플랜은 서버용 Microsoft Defender와 컴퓨터의 SQL Server용 Microsoft Defender입니다.

- **스토리지용 Microsoft Defender 계정** 은 구독 수준 또는 리소스 수준에서 사용할 수 있습니다.
- **SQL용 Microsoft Defender** 는 구독 수준 또는 리소스 수준에서 사용하도록 설정할 수 있습니다.
- 리소스 수준에서만 **오픈 소스 관계형 데이터베이스용 Microsoft Defender** 를 사용하도록 설정할 수 있습니다.

### <a name="to-enable-enhanced-security-features-on-your-subscriptions-and-workspaces"></a>구독 및 작업 영역에서 향상된 보안 기능을 사용하도록 설정하려면 다음을 수행합니다.

- 하나의 구독에서 향상된 보안 기능을 사용하도록 설정하려면 다음을 수행합니다.

    1. Defender for Cloud 주 메뉴에서 **환경 설정** 을 선택합니다.
    1. 보호할 구독 또는 작업 영역을 선택합니다.
    1. **모든 클라우드용 Microsoft Defender 플랜 사용** 을 선택하여 업그레이드합니다.
    1. **저장** 을 선택합니다.

    > [!TIP]
    > Microsoft Defender 플랜은 별도로 가격이 책정되며 개별적으로 설정 또는 해제할 수 있습니다. 예를 들어 연결된 App Service 요금제가 없는 구독에서 App Service용 Azure Defender를 해제할 수 있습니다. 

    :::image type="content" source="./media/enhanced-security-features-overview/pricing-tier-page.png" alt-text="포털의 Defender for Cloud 가격 책정 페이지":::

- 여러 구독 또는 작업 영역에서 향상된 보안을 사용하려면 다음을 수행합니다.

    1. Defender for Cloud의 메뉴에서 **시작** 을 선택합니다.

        **업그레이드** 탭에는 온보딩에 적합한 구독 및 작업 영역이 나열되어 있습니다.

        :::image type="content" source="./media/enable-enhanced-security/get-started-upgrade-tab.png" alt-text="시작 페이지의 업그레이드 탭."::: 

    1. **클라우드용 Microsoft Defender로 보호할 구독 및 작업 영역 선택** 목록에서 업그레이드할 구독 및 작업 영역을 선택하고 **업그레이드** 를 선택하여 모든 클라우드용 Microsoft Defender 보안 기능을 사용합니다.

       - 평가판에 적합하지 않은 구독 및 작업 영역을 선택하면 다음 단계에서 해당 구독 및 작업 영역을 업그레이드하고 요금이 부과되기 시작합니다.
       - 평가판에 적합한 작업 영역을 선택하면 다음 단계에서 평가판이 시작됩니다.

        :::image type="content" source="./media/enable-enhanced-security/upgrade-selected-workspaces-and-subscriptions.png" alt-text="시작 페이지에서 선택한 모든 작업 영역 및 구독을 업그레이드합니다.":::


## <a name="disable-enhanced-security-features"></a>향상된 보안 기능 사용 안 함

구독에 대해 향상된 보안 기능을 사용하지 않아야 하는 경우 절차는 동일하지만 **향상된 보안 해제** 를 선택합니다.
 
1. Defender for Cloud의 메뉴에서 **환경 설정** 을 선택합니다.
1. 관련 구독을 선택합니다.
1. **Defender 플랜** 을 선택하고 **향상된 보안 해제** 를 선택합니다.

    :::image type="content" source="./media/enable-enhanced-security/disable-plans.png" alt-text="Defender for Cloud의 향상된 보안 기능을 사용하거나 사용하지 않도록 설정합니다.":::

1. **저장** 을 선택합니다.

> [!NOTE]
> 한 번에 단일 플랜 또는 모든 플랜을 사용하지 않도록 설정하는지 여부에 관계없이 향상된 보안 기능을 사용하지 않도록 설정한 후 데이터 수집이 짧은 기간 동안 계속될 수 있습니다. 

## <a name="next-steps"></a>다음 단계

향상된 보안 기능을 사용하도록 설정했으므로 [자동 프로비전 에이전트 및 확장 프로그램](enable-data-collection.md)에 설명된 대로 자동 데이터 수집을 수행하는 데 필요한 에이전트 및 확장 프로그램을 사용합니다.
