---
title: 클라우드용 Microsoft Defender의 향상된 보안 기능
description: 클라우드용 Microsoft Defender의 향상된 보안 기능을 사용하는 방법을 알아봅니다.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: quickstart
ms.date: 11/09/2021
ms.author: memildin
ms.custom: mode-other
ms.openlocfilehash: 291e88a8b53bb7614a176cfd3f434431133bfd2f
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133049769"
---
# <a name="quickstart-set-up-microsoft-defender-for-cloud"></a>빠른 시작: 클라우드용 Microsoft Defender 설정

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud는 하이브리드 클라우드 및 다중 워크로드 전체에서 통합 보안 관리 및 위협 방지 기능을 제공합니다. 체험 기능은 Azure 리소스에 대한 제한된 보안만 제공하지만 향상된 보안 기능을 사용하면 이러한 기능이 온-프레미스 및 기타 클라우드로 확장됩니다. Defender for Cloud를 사용하면 보안 취약성을 찾아서 수정하고, 액세스 및 애플리케이션 제어를 적용하여 악성 활동을 차단하고, 분석 및 인텔리전스를 사용하여 위협을 검색하고, 공격을 받을 때 신속하게 대응할 수 있습니다. 향상된 보안 기능은 비용 없이 사용해 볼 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

이 빠른 시작 섹션에서는 클라우드용 Microsoft Defender 및 향상된 보안 기능을 사용하도록 설정하는 모든 권장 단계를 안내합니다. 모든 빠른 시작 단계를 완료한 후에는 다음을 수행할 수 있습니다.

> [!div class="checklist"]
> * Azure 구독에서 사용된 Defender for Cloud
> * Azure 구독에서 사용된 [향상된 보안 기능](enhanced-security-features-overview.md)
> * 자동 데이터 수집 설정
> * 보안 경고를 위한 [이메일 알림 설정](configure-email-notifications.md)
> * Azure에 연결된 하이브리드 및 다중 클라우드 머신

## <a name="prerequisites"></a>사전 요구 사항
Defender for Cloud를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)에 가입할 수 있습니다.

구독에서 향상된 보안 기능을 사용하려면 구독 소유자, 구독 기여자 또는 보안 관리자 역할을 할당 받아야 합니다.

## <a name="enable-defender-for-cloud-on-your-azure-subscription"></a>Azure 구독에서 Defender for Cloud 사용

> [!TIP]
> 관리 그룹 내의 모든 구독에서 Defender for Cloud를 사용하려면 [여러 Azure 구독에서 Defender for Cloud 사용](onboard-management-group.md)을 참조하세요.

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에 로그인합니다.

1. 포털의 메뉴에서 **Defender for Cloud** 를 선택합니다. 

    Defender for Cloud의 개요 페이지가 열립니다.

    :::image type="content" source="./media/get-started/overview.png" alt-text="Defender for Cloud의 개요 대시보드" lightbox="./media/get-started/overview.png":::

    **Defender for Cloud - 개요** 는 하이브리드 클라우드 워크로드의 보안 상태에 대한 통합 보기를 제공하여 워크로드의 보안을 발견 및 평가하고 위험을 식별 및 완화하는 데 도움이 됩니다. [클라우드용 Microsoft Defender의 개요 페이지](overview-page.md)에서 자세히 알아보세요.

    Defender for Cloud는 사용자 또는 다른 구독 사용자가 이전에 온보딩하지 않은 Azure 구독을 무료로 자동으로 사용하도록 설정합니다.

**구독** 메뉴 항목을 선택하여 구독 목록을 보고 필터링할 수 있습니다. Defender for Cloud는 선택한 구독의 보안 상태를 반영하도록 디스플레이를 조정합니다. 

Defender for Cloud를 처음 시작한 후 몇 분 내에 다음이 표시될 수 있습니다.

- 연결된 리소스의 보안을 개선하는 방법에 대한 **권장 사항**
- 현재 Defender for Cloud에서 평가되는 리소스의 인벤토리와 각 리소스의 보안 태세.

Defender for Cloud를 최대한 활용하려면 빠른 시작 섹션의 다음 단계를 계속 진행합니다.



## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 Defender for Cloud를 사용했습니다. 다음 단계는 하이브리드 클라우드 워크로드 전반에서 통합 보안 관리 및 위협 방지를 위해 향상된 보안 기능을 사용하는 것입니다.

> [!div class="nextstepaction"]
> [빠른 시작: 향상된 보안 기능 사용](enable-enhanced-security.md)
