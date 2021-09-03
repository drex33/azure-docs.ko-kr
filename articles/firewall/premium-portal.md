---
title: Azure Portal의 Azure Firewall 프리미엄
description: Azure Portal의 Azure Firewall 프리미엄에 대해 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: victorh
ms.openlocfilehash: cfb9f76751bbf5966b4aaa29e382a0946bf7cfd6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440433"
---
# <a name="azure-firewall-premium-in-the-azure-portal"></a>Azure Portal의 Azure Firewall 프리미엄


 Azure Firewall 프리미엄은 매우 민감하고 통제된 환경에 필요한 기능을 갖춘 차세대 방화벽입니다. 이 관리 팩에는 다음과 같은 기능이 포함되어 있습니다.

- **TLS 검사** - 아웃바운드 트래픽을 해독하고, 데이터를 처리한 다음 데이터를 암호화하고 대상에 보냅니다.
- **IDPS** - 네트워크 침입 감지 및 방지 시스템(IDPS)을 사용하면 악의적인 활동의 네트워크 활동을 모니터링 하고, 이 활동에 대한 정보를 기록하고, 보고하고, 필요 시 차단할 수 있습니다.
- **URL 필터링** - 전체 URL을 고려하도록 Azure Firewall의 FQDN 필터링 기능을 확장합니다. 예를 들어 `www.contoso.com`이 아닌 `www.contoso.com/a/c`입니다.
- **웹 범주** - 관리자는 도박 웹 사이트, 소셜 미디어 웹 사이트 등의 웹 사이트 범주에 대한 사용자 액세스를 허용하거나 거부할 수 있습니다.

자세한 내용은 [Azure Firewall 프리미엄 기능](premium-features.md)을 참조하세요.

## <a name="deploy-the-firewall"></a>방화벽 배포

Azure Firewall 프리미엄 배포는 표준 Azure Firewall을 배포하는 것과 비슷합니다.

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="포털 배포":::

**방화벽 계층** 에서 **프리미엄** 을 선택한 다음, **방화벽 정책** 에서 기존 프리미엄 정책을 선택하거나 새로 만듭니다.

## <a name="configure-the-premium-policy"></a>프리미엄 정책 구성

프리미엄 방화벽 정책을 구성하는 것은 표준 방화벽 정책을 구성하는 것과 비슷합니다. 프리미엄 정책을 사용하면 프리미엄 기능을 구성할 수 있습니다.

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="프리미엄 정책 배포":::

### <a name="rule-configuration"></a>규칙 구성

프리미엄 정책에서 애플리케이션 규칙을 구성하는 경우 추가 프리미엄 기능을 구성할 수 있습니다.

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="프리미엄 규칙":::

## <a name="next-steps"></a>다음 단계

Azure Firewall 프리미엄 기능을 실제로 확인하려면 [Azure Firewall 프리미엄 배포 및 구성](premium-deploy.md)을 참조하세요.