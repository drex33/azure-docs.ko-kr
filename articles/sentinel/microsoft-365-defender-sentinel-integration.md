---
title: Microsoft Sentinel | Microsoft 365 Defender 통합 Microsoft Docs
description: Microsoft Sentinel과 함께 Microsoft 365 Defender 사용하면 유니버설 인시던트 큐로 Microsoft Sentinel을 사용하는 동시에 Microsoft 365 Defender 장점을 원활하게 적용하여 Microsoft 365 보안 인시던트를 조사하는 방법을 알아봅니다. 또한 Defender 구성 요소의 고급 헌팅 데이터를 Microsoft Sentinel에 수집하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: a5a969397ce63568c95891206d95275af5caf6a8
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520487"
---
# <a name="microsoft-365-defender-integration-with-microsoft-sentinel"></a>Microsoft Sentinel과 Microsoft 365 Defender 통합

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Microsoft 365 Defender connector는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

> [!IMPORTANT]
>
> **Microsoft 365 Defender** 의 이전 이름은 **MTP**(**Microsoft Threat Protection**)였습니다.
>
> **엔드포인트용 Microsoft Defender** 의 이전 이름은 **MDATP**(**Microsoft Defender Advanced Threat Protection**)였습니다.
>
> **microsoft Defender for Office 365** 이전에는 Office 365 **Advanced Threat Protection으로** 알려져 있었습니다.
>
> 이러한 이전 이름이 일정 기간 동안 계속 사용될 수도 있습니다.

## <a name="incident-integration"></a>인시던트 통합

Microsoft Sentinel의 [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) 인시던트 통합을 사용하면 모든 Microsoft 365 Defender 인시던트 를 Microsoft Sentinel로 스트리밍하고 두 포털 간에 동기화된 상태로 유지할 수 있습니다. Microsoft 365 Defender 인시던트(이전의 Microsoft Threat Protection 또는 MTP)에는 관련된 모든 경고, 엔터티 및 관련 정보가 포함되며, Microsoft Sentinel에서 심사 및 예비 조사를 수행하기에 충분한 컨텍스트를 제공합니다. Sentinel에서는 인시던트가 Microsoft 365 Defender와 양방향 동기화된 상태로 유지되므로 인시던트 조사에서 두 포털의 이점을 모두 활용할 수 있습니다.

이 통합을 통해 Microsoft 365 보안 인시던트를 전체 조직의 기본 인시던트 큐의 일부로 Microsoft Sentinel 내에서 관리하여 다른 모든 클라우드 및 온-프레미스 시스템의 인시던트와 함께 Microsoft 365 인시던트를 보고 상관 관계를 파악할 수 있습니다. 이와 동시에 Microsoft 365 에코시스템 전체에서 심층 조사 및 Microsoft 365 관련 환경을 위해 Microsoft 365 Defender의 고유한 강점과 기능을 활용할 수 있습니다. Microsoft 365 Defender는 여러 Microsoft 365 제품의 경고를 강화하고 그룹화함으로써 SOC의 인시던트 큐 크기를 줄이고 해결 시간을 단축시킵니다. Microsoft 365 Defender 스택의 일부인 구성 요소 서비스는 다음과 같습니다.

- **엔드포인트용 Microsoft Defender**(이전 이름: Microsoft Defender ATP)
- **Microsoft Defender for Identity**(이전 이름: Azure ATP)
- **Microsoft Defender for Office 365**(이전 이름: Office 365 ATP)
- **Cloud Apps용 Microsoft Defender**

이러한 구성 요소에서 경고를 수집하는 것 외에도 Microsoft 365 Defender는 자체 경고를 생성합니다. 이러한 모든 경고에서 인시던트 생성 및 Microsoft Sentinel로 보냅니다.

### <a name="common-use-cases-and-scenarios"></a>일반적인 사용 사례 및 시나리오

- Microsoft 365 Defender 구성 요소의 모든 경고 및 엔터티를 포함하여 Microsoft 365 Defender 인시던트를 Microsoft Sentinel에 한 번 클릭으로 연결합니다.

- 상태, 소유자 및 종결 이유로 Sentinel과 Microsoft 365 Defender 인시던트 간에 양방향 동기화를 수행합니다.

- Microsoft Sentinel에서 Microsoft 365 Defender 경고 그룹화 및 보강 기능을 적용하여 해결 시간을 단축합니다.

- 두 포털에서 조사를 용이하게 하기 위해 Microsoft Sentinel 인시던트와 병렬 Microsoft 365 Defender 인시던트 간의 컨텍스트 내 딥 링크입니다.

### <a name="connecting-to-microsoft-365-defender"></a>Microsoft 365 Defender에 연결

Microsoft 365 Defender 데이터 커넥터를 사용하도록 설정하여 [인시던트 및 경고를 수집하면](connect-microsoft-365-defender.md)Microsoft 365 Defender 인시던트 큐에 Microsoft Sentinel 인시던트 큐에 표시되고 제품 **이름** 필드에 **Microsoft 365 Defender** 생성되는 즉시 표시됩니다. Microsoft 365 Defender.
- 인시던트 생성 Microsoft 365 Defender Microsoft Sentinel에 표시되는 시간까지 최대 10분이 걸릴 수 있습니다.

- 인시던트는 추가 비용 없이 수집 및 동기화됩니다.

Microsoft 365 Defender 통합이 연결되면 모든 구성 요소 경고 커넥터(엔드포인트용 Defender, Defender for Identity, Defender for Office 365, Cloud Apps용 Defender)가 백그라운드에서 자동으로 연결됩니다(아직 연결되지 않은 경우). Microsoft 365 Defender 연결된 후 구성 요소 라이선스를 구매한 경우에도 새 제품의 경고 및 인시던트도 추가 구성 또는 비용 없이 Microsoft Sentinel로 전달됩니다.

### <a name="microsoft-365-defender-incidents-and-microsoft-incident-creation-rules"></a>Microsoft 365 Defender 인시던트 및 Microsoft 인시던트 만들기 규칙

- Microsoft 365 보안 제품의 경고를 기반으로 Microsoft 365 Defender에서 생성되는 인시던트는 사용자 지정 Microsoft 365 Defender 논리에 따라 생성됩니다.

- Microsoft Sentinel의 Microsoft 인시던트 생성 규칙은 (다른) 사용자 지정 Microsoft Sentinel 논리를 사용하여 동일한 경고에서 인시던트를 만듭니다.

- 두 메커니즘을 함께 사용하는 것이 완벽하게 지원되며, 이에 따라 새로운 Microsoft 365 Defender 인시던트 만들기 논리로의 전환이 촉진됩니다. 하지만 이렇게 하면 동일한 경고에 대해 **중복 인시던트** 가 생성됩니다.

- 동일한 경고에 대해 중복 인시던트 생성을 방지하려면 고객이 Microsoft 365 Defender 연결할 때 Microsoft 365 제품에 대한 모든 **Microsoft 인시던트 생성** 규칙(엔드포인트용 Defender, Defender for Identity 및 defender for Office 365 및 Cloud Apps용 Defender)을 해제하는 것이 좋습니다. 이렇게 하려면 커넥터 페이지에서 인시던트 만들기를 사용하지 않도록 설정하면 됩니다. 이렇게 하면 인시던트 만들기 규칙에 의해 적용된 필터가 Microsoft 365 Defender 인시던트 통합에 전혀 적용되지 않습니다.

    > [!NOTE]
    > 이제 모든 Microsoft Defender for Cloud Apps 경고 유형이 Microsoft 365 Defender 온보딩됩니다.

### <a name="working-with-microsoft-365-defender-incidents-in-microsoft-sentinel-and-bi-directional-sync"></a>Microsoft Sentinel 및 양방향 동기화에서 Microsoft 365 Defender 인시던트 작업

Microsoft 365 Defender 인시던트 는 제품 이름이 **Microsoft 365 Defender** Microsoft Sentinel 인시던트 큐에 표시되며 다른 Sentinel 인시던트와 유사한 세부 정보 및 기능이 있습니다. 각 인시던트에는 Microsoft 365 Defender 포털의 병렬 인시던트 링크가 포함되어 있습니다.

인시던트 Microsoft 365 Defender 진화하고 더 많은 경고 또는 엔터티가 추가되면 Microsoft Sentinel 인시던트도 그에 따라 업데이트됩니다.

Microsoft 365 Defender 또는 Microsoft Sentinel에서 Microsoft 365 인시던트의 상태, 닫는 이유 또는 할당에 대한 변경 내용도 마찬가지로 다른 인시던트 큐에서 업데이트됩니다. 동기화는 인시던트 변경 내용이 적용된 직후 두 포털에서 지체 없이 수행됩니다. 최신 변경 내용을 보려면 새로 고침이 필요할 수 있습니다.

Microsoft 365 Defender에서는 한 인시던트의 모든 경고를 다른 인시던트로 전송할 수 있으며, 이렇게 하면 인시던트가 병합됩니다. 이 병합이 발생하면 Microsoft Sentinel 인시던트에서 변경 내용을 반영합니다. 한 인시던트에 두 원래 인시던트의 모든 경고가 포함되고, 다른 인시던트는 자동 종결되며 "리디렉션됨" 태그가 추가됩니다.

> [!NOTE]
> Microsoft Sentinel의 인시던트 수는 최대 150개의 경고를 포함할 수 있습니다. Microsoft 365 Defender 인시던트에는 이보다 많은 수의 경고를 포함할 수 있습니다. 150개 이상의 경고가 있는 Microsoft 365 Defender 인시던트와 Microsoft Sentinel이 동기화되면 Sentinel 인시던트에서 "150개 이상" 경고가 있는 것으로 표시되고 전체 경고 집합이 표시되는 Microsoft 365 Defender 병렬 인시던트 링크를 제공합니다.

## <a name="advanced-hunting-event-collection"></a>고급 헌팅 이벤트 수집

또한 Microsoft 365 Defender 커넥터를 사용하면 원시 이벤트 데이터 형식인 **고급 헌팅** 이벤트를 Microsoft 365 Defender 및 해당 구성 요소 서비스에서 Microsoft Sentinel로 스트리밍할 수 있습니다. 현재 엔드포인트용 Microsoft Defender에서 [고급 헌팅](/microsoft-365/security/defender/advanced-hunting-overview) 이벤트를 *수집하고(2021년 10월부터)* Microsoft Defender for Office 365 Microsoft Sentinel 작업 영역의 용도에 맞게 빌드된 테이블로 바로 스트리밍할 수 있습니다. 이 테이블은 Microsoft 365 Defender 포털에서 사용되는 것과 동일한 스키마를 기반으로 작성되므로 고급 헌팅 이벤트의 전체 세트에 대한 완전한 액세스가 제공되며 다음을 수행할 수 있습니다.

- 기존 엔드포인트용 Microsoft Defender/Office 365 고급 헌팅 쿼리를 Microsoft Sentinel에 쉽게 복사합니다.

- 원시 이벤트 로그를 사용하여 경고, 헌팅 및 조사에 대한 추가 인사이트를 제공하고 이러한 이벤트를 Microsoft Sentinel의 다른 데이터 원본의 이벤트와 상호 연결합니다.

- 엔드포인트용 Microsoft Defender/Office 365 또는 Microsoft 365 Defender 기본 보존 기간(30일)을 초과하여 보존 기간을 늘려 로그를 저장합니다. 작업 영역의 보존을 구성하거나 Log Analytics에서 테이블당 보존을 구성하여 해당 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 365 Defender 커넥터를 사용하여 Microsoft Sentinel과 함께 Microsoft 365 Defender 사용하는 이점을 활용하는 방법을 배웠습니다.

- [Microsoft 365 Defender 커넥터를 사용하도록 설정](connect-microsoft-365-defender.md)하는 방법을 알아봅니다.
- [사용자 지정 경고](detect-threats-custom.md)를 만들고 [인시던트를 조사](investigate-cases.md)합니다.
