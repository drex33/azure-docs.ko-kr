---
title: Azure Web Application Firewall 및 Azure Policy
description: Azure WAF(Web Application Firewall)를 Azure Policy와 결합하면 조직 표준을 적용하고 WAF 리소스에 대한 규정 준수를 규모에 맞게 평가할 수 있음
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 7738976cbc3f7b87862cab73a5b5f52c6d499d47
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606115"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure Web Application Firewall 및 Azure Policy

Azure WAF(Web Application Firewall)를 Azure Policy와 결합하면 조직 표준을 적용하고 WAF 리소스에 대한 규정 준수를 규모에 맞게 평가할 수 있습니다. Azure Policy는 리소스별, 정책별 세분성으로 드릴다운할 수 있는 기능을 사용하여 환경의 전체 상태를 평가할 수 있는 집계된 보기를 제공하는 거버넌스 도구입니다. 또한 Azure Policy는 기존 리소스에 대한 대량 수정 및 새 리소스에 대한 자동 수정을 통해 리소스를 규정 준수 상태로 전환할 수 있습니다.

## <a name="azure-policy-for-web-application-firewall"></a>웹 애플리케이션 방화벽에 대한 Azure Policy

WAF 리소스 관리를 위한 여러 가지 기본 제공 Azure Policy 정의가 있습니다. 정책 정의 및 해당 기능 내역은 다음과 같습니다.

1. **WAF(웹 애플리케이션 방화벽)는 Azure Front Door 서비스에서 사용하도록 설정해야 합니다**: Azure Front Door 서비스는 리소스를 만들 때 WAF가 있는 경우에 평가됩니다. 정책 정의에는 감사, 거부, 사용 안 함이라는 세 가지 효과가 있습니다. 감사는 Azure Front Door 서비스에 WAF가 없을 때 추적하며 Azure Front Door 서비스가 준수하지 않는 사항을 사용자가 볼 수 있도록 합니다. 거부는 WAF가 연결되지 않은 경우 Azure Front Door 서비스를 만들지 못하도록 합니다. 사용 안 함으로 설정하면 정책 할당을 끕니다.

2. **WAF(Web Application Firewall)는 Application Gateway에서 사용하도록 설정해야 합니다**: Application Gateway는 리소스를 만들 때 WAF가 있는 경우에 평가됩니다. 정책 정의에는 감사, 거부, 사용 안 함이라는 세 가지 효과가 있습니다. 감사는 Application Gateway에 WAF가 없을 때 추적하며 Application Gateway가 준수하지 않는 사항을 사용자가 볼 수 있도록 합니다. 거부는 WAF가 연결되지 않은 경우 Application Gateway를 만들지 못하도록 합니다. 사용 안 함으로 설정하면 정책 할당을 끕니다.

3. **WAF(Web Application Firewall)는 Azure Front Door 서비스에 대해 지정된 모드를 사용해야 합니다**: Azure Front Door 서비스에 대한 모든 Web Application Firewall 정책에서 '검색' 또는 '방지' 모드의 사용을 활성으로 지시합니다. 정책 정의에는 감사, 거부, 사용 안 함이라는 세 가지 효과가 있습니다. 감사는 WAF가 지정된 모드에 맞지 않을 때 추적합니다. 거부는 올바른 모드가 아닌 경우 WAF를 만들지 못하도록 합니다. 사용 안 함으로 설정하면 정책 할당을 끕니다.

4. **WAF(Web Application Firewall)는 Application Gateway에 대해 지정된 모드를 사용해야 합니다**: Application Gateway에 대한 모든 Web Application Firewall 정책에서 '검색' 또는 '방지' 모드의 사용을 활성으로 지시합니다. 정책 정의에는 감사, 거부, 사용 안 함이라는 세 가지 효과가 있습니다. 감사는 WAF가 지정된 모드에 맞지 않을 때 추적합니다. 거부는 올바른 모드가 아닌 경우 WAF를 만들지 못하도록 합니다. 사용 안 함으로 설정하면 정책 할당을 끕니다.

## <a name="launch-an-azure-policy"></a>Azure Policy 시작

1.  Azure 홈페이지의 검색창에 정책을 입력하고 Azure Policy 아이콘 클릭

2.  Azure Policy 서비스의 **작성** 에서 **할당** 을 선택합니다.

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Azure Policy의 할당 탭":::

3.  할당 페이지 상단에 있는 **정책 할당** 아이콘을 선택합니다.

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="정책 할당 페이지의 기본 탭":::

4.  정책 할당 페이지의 기본 탭에서 다음 필드를 업데이트합니다.
    1.  **범위**: 정책 정의에 영향을 받을 Azure 구독 및 리소스 그룹을 선택합니다.
    2.  **제외 사항**: 정책 할당에서 제외할 범위에서 리소스를 선택합니다.
    3.  **정책 정의**: 제외된 범위에 적용할 정책 정의를 선택합니다. 관련 웹 애플리케이션 방화벽 Azure Policy를 선택하려면 검색 창에 “웹 애플리케이션 방화벽”을 입력합니다.

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="’사용 가능한 정의' 페이지에서 ‘정책 정의' 탭을 표시하는 스크린샷.":::

5.  **매개 변수** 탭을 선택하고 정책 할당 매개 변수를 업데이트합니다. 매개 변수의 기능을 자세히 알아보려면 매개 변수 이름 옆의 정보 아이콘 위로 마우스를 가리켜 자세한 설명을 확인합니다.

6.  **검토 + 만들기** 를 선택하여 정책 할당을 마무리합니다. 정책 할당은 새 리소스에 대해 활성화될 때까지 약 15분이 걸립니다.
