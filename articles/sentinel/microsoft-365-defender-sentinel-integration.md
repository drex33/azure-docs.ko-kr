---
title: Microsoft 센티널과 Microsoft 365 Defender 통합 | Microsoft Docs
description: microsoft 센티널과 함께 Microsoft 365 Defender를 사용 하 여 Microsoft 365 보안 인시던트를 조사 하는 데 도움이 되는 Microsoft 365 Defender의 기능을 원활 하 게 적용 하면서 microsoft 센티널을 universal 인시던트 큐로 사용할 수 있습니다. 또한 Microsoft 센티널로 Defender 구성 요소의 고급 구하기 데이터를 수집 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 34b39bcac7f852bedcfd1b03d399f5ef4987e0be
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132713159"
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
> **Microsoft Defender for Office 365** 는 이전의 **Office 365 Advanced Threat Protection** 이라고 했습니다.
>
> 이러한 이전 이름이 일정 기간 동안 계속 사용될 수도 있습니다.

## <a name="incident-integration"></a>인시던트 통합

microsoft 센티널의 [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) 인시던트 통합을 사용 하면 모든 Microsoft 365 Defender 인시던트를 Microsoft 센티널로 스트리밍하 고 두 포털 간에 동기화 된 상태를 유지할 수 있습니다. Microsoft 365 Defender (이전에는 microsoft Threat Protection 또는 MTP)에서 제공 하는 인시던트에는 관련 된 모든 경고, 엔터티 및 관련 정보가 포함 되어 있으므로 Microsoft 센티널에서 심사 및 예비 조사를 수행 하는 데 충분 한 컨텍스트를 제공 합니다. Sentinel에서는 인시던트가 Microsoft 365 Defender와 양방향 동기화된 상태로 유지되므로 인시던트 조사에서 두 포털의 이점을 모두 활용할 수 있습니다.

이러한 통합을 통해 전체 조직에서 기본 인시던트 큐의 일부로 Microsoft 센티널 내에서 관리할 수 있는 보안 인시던트를 Microsoft 365 수 있습니다. 따라서 다른 모든 클라우드 및 온-프레미스 시스템의 인시던트를 사용 하 여 Microsoft 365 인시던트를 확인 하 고 상관 관계를 지정할 수 있습니다. 이와 동시에 Microsoft 365 에코시스템 전체에서 심층 조사 및 Microsoft 365 관련 환경을 위해 Microsoft 365 Defender의 고유한 강점과 기능을 활용할 수 있습니다. Microsoft 365 Defender는 여러 Microsoft 365 제품의 경고를 강화하고 그룹화함으로써 SOC의 인시던트 큐 크기를 줄이고 해결 시간을 단축시킵니다. Microsoft 365 Defender 스택의 일부인 구성 요소 서비스는 다음과 같습니다.

- **엔드포인트용 Microsoft Defender**(이전 이름: Microsoft Defender ATP)
- **Microsoft Defender for Identity**(이전 이름: Azure ATP)
- **Microsoft Defender for Office 365**(이전 이름: Office 365 ATP)
- **Microsoft Defender for Cloud 앱**

이러한 구성 요소에서 경고를 수집하는 것 외에도 Microsoft 365 Defender는 자체 경고를 생성합니다. 이러한 모든 경고에서 인시던트를 만들어 Microsoft 센티널에 보냅니다.

### <a name="common-use-cases-and-scenarios"></a>일반적인 사용 사례 및 시나리오

- Microsoft 365 Defender 구성 요소의 모든 경고 및 엔터티를 포함 하 여 Microsoft 365 Defender 인시던트에 연결 하는 한 번 클릭으로 Microsoft 센티널에 연결 합니다.

- 상태, 소유자 및 종결 이유로 Sentinel과 Microsoft 365 Defender 인시던트 간에 양방향 동기화를 수행합니다.

- Microsoft 센티널에서 경고 그룹화 및 보강 기능 Microsoft 365 Defender의 응용 프로그램 때문에 해결할 시간을 단축할 수 있습니다.

- 두 포털에서 조사를 용이 하 게 하는 Microsoft 센티널 사건과 병렬 Microsoft 365 Defender 인시던트 간의 상황에 맞는 딥 링크입니다.

### <a name="connecting-to-microsoft-365-defender"></a>Microsoft 365 Defender에 연결

Microsoft 365 Defender data connector를 사용 하도록 설정 하 여 [인시던트 및 경고를 수집](connect-microsoft-365-defender.md)하 고 나면 Microsoft 센티널 인시던트 **Microsoft 365 Defender** 큐에 Microsoft 365 Defender 인시던트가 표시 됩니다.  Microsoft 365 Defender.
- Microsoft 365 Defender에서 인시던트가 생성 된 시간부터 Microsoft 센티널에 표시 되는 시간까지 최대 10 분이 걸릴 수 있습니다.

- 인시던트는 추가 비용 없이 수집 및 동기화됩니다.

Microsoft 365 Defender 통합이 연결 되 면 모든 구성 요소 경고 커넥터 (defender for Endpoint, defender for Identity, defender for Office 365, defender for Cloud Apps)가 백그라운드에서 아직 연결 되지 않은 경우 자동으로 연결 됩니다. Microsoft 365 Defender 연결 된 후 구성 요소 라이선스가 구매한 경우 새 제품의 경고와 인시던트는 추가 구성 또는 요금 없이 Microsoft 센티널로 계속 전달 됩니다.

### <a name="microsoft-365-defender-incidents-and-microsoft-incident-creation-rules"></a>Microsoft 365 Defender 인시던트 및 Microsoft 인시던트 만들기 규칙

- Microsoft 365 보안 제품의 경고를 기반으로 Microsoft 365 Defender에서 생성되는 인시던트는 사용자 지정 Microsoft 365 Defender 논리에 따라 생성됩니다.

- Microsoft 센티널의 microsoft 인시던트 생성 규칙은 또한 (a 다른) 사용자 지정 Microsoft 센티널 논리를 사용 하 여 동일한 경고에서 인시던트를 만듭니다.

- 두 메커니즘을 함께 사용하는 것이 완벽하게 지원되며, 이에 따라 새로운 Microsoft 365 Defender 인시던트 만들기 논리로의 전환이 촉진됩니다. 하지만 이렇게 하면 동일한 경고에 대해 **중복 인시던트** 가 생성됩니다.

- 동일한 경고에 대해 중복 인시던트를 만들지 않으려면 Microsoft 365 Defender 연결할 때 고객에 게 Microsoft 365 제품 (defender for Endpoint, id 용 defender, for Office 365 및 defender for Cloud Apps)에 대 한 모든 **Microsoft 인시던트 생성 규칙** 을 해제 하는 것이 좋습니다. 이렇게 하려면 커넥터 페이지에서 인시던트 만들기를 사용하지 않도록 설정하면 됩니다. 이렇게 하면 인시던트 만들기 규칙에 의해 적용된 필터가 Microsoft 365 Defender 인시던트 통합에 전혀 적용되지 않습니다.

    > [!NOTE]
    > 이제 클라우드 앱 용 Microsoft Defender 경고 유형을 모두 Microsoft 365 Defender 등록 됩니다.

### <a name="working-with-microsoft-365-defender-incidents-in-microsoft-sentinel-and-bi-directional-sync"></a>Microsoft 센티널 및 양방향 동기화에서 Microsoft 365 Defender 인시던트 사용

Microsoft 365 Defender 인시던트는 제품 이름 **Microsoft 365 Defender** 와 함께 Microsoft 센티널 인시던트 큐에 표시 되며, 다른 센티널 인시던트에 대해 비슷한 세부 정보와 기능을 포함 합니다. 각 인시던트에는 Microsoft 365 Defender 포털의 병렬 인시던트 링크가 포함되어 있습니다.

Microsoft 365 Defender에서 인시던트가 진화 하 고 추가 경고 또는 엔터티가 추가 되 면 Microsoft 센티널 인시던트가 그에 따라 업데이트 됩니다.

Microsoft 365 Defender 또는 Microsoft 센티널에서 상태, 종결 이유 또는 Microsoft 365 인시던트의 할당에 대 한 변경 내용은 다른의 인시던트 큐에서 적절 하 게 업데이트 됩니다. 동기화는 인시던트 변경 내용이 적용된 직후 두 포털에서 지체 없이 수행됩니다. 최신 변경 내용을 보려면 새로 고침이 필요할 수 있습니다.

Microsoft 365 Defender에서는 한 인시던트의 모든 경고를 다른 인시던트로 전송할 수 있으며, 이렇게 하면 인시던트가 병합됩니다. 이러한 병합이 발생 하면 Microsoft 센티널 인시던트에 변경 내용이 반영 됩니다. 한 인시던트에 두 원래 인시던트의 모든 경고가 포함되고, 다른 인시던트는 자동 종결되며 "리디렉션됨" 태그가 추가됩니다.

> [!NOTE]
> Microsoft 센티널의 인시던트는 최대 150 개의 경고를 포함할 수 있습니다. Microsoft 365 Defender 인시던트에는 이보다 많은 수의 경고를 포함할 수 있습니다. 150 이상의 경고를 포함 하는 Microsoft 365 Defender 인시던트가 Microsoft 센티널에 동기화 되는 경우 센티널 인시던트는 "150 +" 개의 경고를 포함 하는 것으로 표시 되며 전체 경고 집합을 볼 수 있는 Microsoft 365 Defender의 병렬 인시던트에 대 한 링크를 제공 합니다.

## <a name="advanced-hunting-event-collection"></a>고급 헌팅 이벤트 수집

또한 Microsoft 365 Defender 커넥터를 사용 하면 Microsoft 365 Defender 및 해당 구성 요소 서비스에서 Microsoft 센티널로 **고급 구하기** 이벤트를 스트리밍할 수 있습니다. 현재 microsoft에서 끝점에 대 한 microsoft defender 및 *(10 월 2021 Office 365)* 의 [고급 구하기](/microsoft-365/security/defender/advanced-hunting-overview) 이벤트를 수집 하 고 microsoft 센티널 작업 영역에서이를 목적에 맞게 작성 된 테이블로 직접 스트리밍할 수 있습니다. 이 테이블은 Microsoft 365 Defender 포털에서 사용되는 것과 동일한 스키마를 기반으로 작성되므로 고급 헌팅 이벤트의 전체 세트에 대한 완전한 액세스가 제공되며 다음을 수행할 수 있습니다.

- 기존 microsoft Defender for Endpoint/Office 365 고급 구하기 쿼리를 microsoft 센티널로 쉽게 복사 합니다.

- 원시 이벤트 로그를 사용 하 여 경고, 구하기 및 조사에 대 한 추가 정보를 제공 하 고 이러한 이벤트를 Microsoft 센티널에 있는 다른 데이터 원본의 이벤트와 상관 관계를 지정 합니다.

- 끝점의/Office 365 또는 Microsoft 365 Defender의 기본 보존 인 30 일 동안 Microsoft Defender 외에도 보존 기간이 증가 한 로그를 저장 합니다. 작업 영역의 보존을 구성하거나 Log Analytics에서 테이블당 보존을 구성하여 해당 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 365 Defender 커넥터를 사용 하 여 Microsoft 센티널과 함께 Microsoft 365 Defender를 활용 하는 방법을 배웠습니다.

- [Microsoft 365 Defender 커넥터를 사용하도록 설정](connect-microsoft-365-defender.md)하는 방법을 알아봅니다.
- [사용자 지정 경고](detect-threats-custom.md)를 만들고 [인시던트를 조사](investigate-cases.md)합니다.
