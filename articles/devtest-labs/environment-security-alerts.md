---
title: 환경의 보안 경고
description: 이 문서에서는 DevTest Labs에서 환경에 대한 보안 경고를 확인하고 적절한 조치를 취하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 437117e29ac09e52d2cd15740d60d942170b9c0d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654198"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure DevTest Labs의 환경에 대한 보안 경고
이제 랩 사용자는 랩 환경에 대한 Azure Security Center 경고를 볼 수 있습니다. 보안 센터는 방화벽 및 엔드포인트 보호 솔루션과 같은 Azure 리소스, 네트워크 및 연결된 파트너 솔루션의 로그 데이터를 자동으로 수집하고 분석하며 통합하여 실제 위협을 감지하고 가양성을 줄입니다. 우선 순위가 지정된 보안 경고의 목록은 문제를 신속하게 조사해야 하는 정보 및 공격을 해결하는 방법에 대한 권장 사항과 함께 보안 센터에 표시됩니다. [Azure Security Center의 보안 경고에 대해 자세히 알아봅니다.](../security-center//security-center-alerts-overview.md)  


## <a name="prerequisites"></a>필수 구성 요소
현재 랩에 배포된 PaaS(Platform as a Service) 환경에 대한 보안 경고만 볼 수 있습니다. 이 기능을 테스트하거나 사용하려면 [랩에 환경을 배포](devtest-lab-create-environment-from-arm.md)합니다. 

## <a name="view-security-alerts-for-an-environment"></a>환경에 대한 보안 경고 보기

1. 랩 홈페이지의 왼쪽 메뉴에서 **보안 경고** 를 선택합니다. 보안 경고(높음, 중간 및 낮음)의 수가 표시되어야 합니다. [경고가 분류되는 방법](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)에 대해 자세히 알아봅니다.

    ![보안 경고 - 개요](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 마지막 열에서 세 개의 점(...)을 마우스 오른쪽 단추로 클릭하고 **보안 경고 보기** 를 선택합니다. 

    ![‘보안 경고 보기’가 선택된 보안 경고 페이지를 보여 주는 스크린샷입니다.](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. 경고 및 관리자 권장 사항에 대한 자세한 내용이 표시됩니다. [Azure Security Center에서 보안 경고 관리 및 대응](../security-center/security-center-managing-and-responding-alerts.md)에 대해 자세히 알아봅니다.

    ![보안 경고 보기](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>다음 단계
환경에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)
