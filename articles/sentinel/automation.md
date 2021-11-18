---
title: Microsoft 센티널의 자동화 소개 | Microsoft Docs
description: 이 문서에서는 Microsoft 센티널의 보안 오케스트레이션, 자동화 및 응답 (대화 충성도) 기능을 소개 하 고 대화 충성도 구성 요소 (Automation 규칙 및 플레이 북)에 대해 설명 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d9c547ed30bf0c580526721cdaa48078a4538315
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730510"
---
# <a name="security-orchestration-automation-and-response-soar-in-microsoft-sentinel"></a>Microsoft 센티널의 보안 오케스트레이션, 자동화 및 응답 (대화 충성도)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Microsoft 센티널의 보안 오케스트레이션, 자동화 및 응답 (대화 충성도) 기능에 대해 설명 하 고, 보안 위협에 대 한 응답으로 Automation 규칙과 플레이 북를 사용 하 여 SOC의 효율성을 높이고 시간과 리소스를 절약 하는 방법을 보여 줍니다.

## <a name="microsoft-sentinel-as-a-soar-solution"></a>대화 충성도 솔루션으로 Microsoft 센티널

### <a name="the-problem"></a>문제

일반적으로 SIEM/SOC 팀은 가용 직원을 압도할 만큼 아주 많은 양의 보안 경고 및 인시던트를 정기적으로 받습니다. 그 결과 수많은 경고를 무시하고 많은 인시던트를 미처 조사할 수 없게 되어 조직이 파악하지 못한 공격에 취약한 상태로 노출됩니다.

### <a name="the-solution"></a>솔루션

Microsoft 센티널는 SIEM (보안 정보 및 이벤트 관리) 시스템 뿐만 아니라 보안 오케스트레이션, 자동화 및 응답 (대화 충성도)에 대 한 플랫폼 이기도 합니다. 주요 목적 중 하나는 보안 운영 센터 및 직원(SOC/SecOps)이 수행해야 하는 모든 반복 및 예측 가능한 보강, 대응, 수정 작업을 자동화하여 고급 위협에 대한 심층 조사 및 헌팅을 위한 시간과 리소스를 확보하는 것입니다. Automation은 인시던트 처리 및 응답의 자동화를 중앙에서 관리 하는 automation 규칙부터, 위협 대응 작업에 강력 하 고 유연한 고급 자동화를 제공 하기 위해 미리 결정 된 작업 시퀀스를 실행 하는 플레이 북까지 Microsoft 센티널에서 몇 가지 다른 형태를 사용 합니다.

## <a name="automation-rules"></a>자동화 규칙

Automation 규칙은 Microsoft 센티널의 새로운 개념입니다. 이 기능을 통해 사용자는 인시던트 처리의 자동화를 중앙에서 관리할 수 있습니다. 자동화 규칙을 사용하면 이전처럼 경고가 아닌 인시던트에 플레이북을 할당하는 것 외에도, 한 번에 여러 분석 규칙에 대한 응답을 자동화하고, 플레이북 없이도 인시던트에 자동으로 태그를 지정하거나, 할당하거나, 종료하고, 실행되는 작업의 순서를 제어할 수 있습니다. Automation 규칙은 Microsoft 센티널에서 자동화 사용을 간소화 하 고 인시던트 오케스트레이션 프로세스에 대 한 복잡 한 워크플로를 단순화할 수 있습니다.

이러한 [자동화 규칙에 대한 전체 설명](automate-incident-handling-with-automation-rules.md)을 통해 자세히 알아보세요.

> [!IMPORTANT]
>
> - **자동화 규칙** 기능은 현재 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="playbooks"></a>플레이북

플레이 북은 Microsoft 센티널에서 루틴으로 실행할 수 있는 응답 및 수정 작업 및 논리의 모음입니다. 플레이북은 위협 대응을 자동화하고 오케스트레이션하는 데 도움이 될 수 있습니다. 다른 시스템 내부 및 외부와 모두 통합할 수 있고, 각각 분석 규칙 또는 자동화 규칙에 따라 트리거되는 경우 특정 경고나 인시던트에 대응하여 플레이북을 자동으로 실행하도록 설정할 수 있습니다. 인시던트 페이지에서 경고에 대응하여 요청 시 수동으로 실행할 수도 있습니다.

Microsoft 센티널의 플레이 북은 기업 전체 시스템에서 작업 및 워크플로를 예약, 자동화 및 오케스트레이션 하는 데 도움이 되는 클라우드 서비스인 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 빌드된 워크플로를 기반으로 합니다. 즉, 플레이북은 Logic Apps의 통합 및 오케스트레이션 기능과 사용하기 쉬운 디자인 도구, 계층 1 Azure 서비스의 스케일링 성능, 안정성, 서비스 수준을 모두 활용하고 사용자 지정할 수 있습니다.

이러한 [플레이북에 대한 전체 설명](automate-responses-with-playbooks.md)을 통해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 센티널에서 자동화를 사용 하 여 SOC가 보다 효과적이 고 효율적으로 작동 하는 방법을 배웠습니다.

- 인시던트 처리 자동화에 대 한 자세한 내용은 [Microsoft 센티널에서 인시던트 처리 자동화](automate-incident-handling-with-automation-rules.md)를 참조 하세요.
- 고급 자동화 옵션에 대 한 자세한 내용은 [Microsoft 센티널에서 플레이 북으로 위협 대응 자동화](automate-responses-with-playbooks.md)를 참조 하세요.
- Automation 규칙 및 플레이 북 구현에 대 한 도움말은 [자습서: 플레이 북를 사용 하 여 Microsoft 센티널에서 위협 응답 자동화](tutorial-respond-threats-playbook.md)를 참조 하세요.
