---
title: Microsoft 센티널로 전송할 수 있는 보안 이벤트 집합 Windows
description: Windows 시스템에서 Microsoft 센티널 작업 영역으로 수집 하 고 스트리밍할 수 있는 미리 작성 된 Windows 보안 이벤트 집합에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 26bee8145176c5cba98286d5df7c2807c808a2c7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346803"
---
# <a name="windows-security-event-sets-that-can-be-sent-to-microsoft-sentinel"></a>Microsoft 센티널로 전송할 수 있는 보안 이벤트 집합 Windows

[Windows 보안 events data connector](data-connectors-reference.md#windows-security-events-via-ama) ( [레거시 버전](data-connectors-reference.md#security-events-via-legacy-agent-windows)포함)를 사용 하 여 Windows 장치에서 보안 이벤트를 수집 경우 다음 집합 중에서 수집할 이벤트를 선택할 수 있습니다.

- **모든 이벤트** - 모든 Windows 보안 및 AppLocker 이벤트입니다.

- **일반** - 감사 용도 이벤트의 표준 집합입니다. 전체 사용자 감사 내역이 해당 집합에 포함됩니다. 예를 들어 사용자 로그인 및 사용자 로그아웃 이벤트(이벤트 ID 4624, 4634)가 모두 포함됩니다. 보안 그룹 변경 사항, 주요 도메인 컨트롤러 Kerberos 작업 및 허용된 모범 사례와 일치하는 기타 이벤트 유형 등도 있습니다.

    **공통** 이벤트 집합에는 일반적이지 않은 일부 이벤트 유형이 포함될 수 있습니다.  **일반적인** 집합에서는 전체 감사 내역 기능을 계속 유지하면서 이벤트 볼륨을 관리하기 쉬운 수준으로 줄이는 것이 중요하기 때문입니다.

- **최소** - 잠재적인 위협을 나타낼 수 있는 작은 이벤트 집합입니다. 이 집합은 전체 감사 내역을 포함하지 않습니다. 보안 위반 이벤트 및 발생 빈도는 낮지만 중요한 이벤트를 다룹니다. 예를 들어 로그온 성공/실패(이벤트 ID 4624, 4625)는 이 집합에 포함되지만 로그아웃 정보(4634)는 포함되지 않습니다. 로그아웃의 경우 감사에서는 중요하지만 위반 검색에서는 중요하지 않으며 양이 비교적 많기 때문입니다. 이 집합에 포함되는 데이터 볼륨 중 대부분은 로그인 이벤트 및 프로세스 만들기 이벤트(이벤트 ID 4688)입니다.

- **사용자 지정** -사용자가 결정 하 고 XPath 쿼리를 사용 하 여 데이터 수집 규칙에 정의 된 일련의 이벤트입니다. [데이터 수집 규칙에 대해 자세히 알아보세요](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries).

## <a name="event-id-reference"></a>이벤트 ID 참조

다음 목록은 각 집합별 전체 보안 내역 및 App Locker 이벤트 ID를 제공합니다.

| 이벤트 집합 | 수집된 이벤트 ID |
| --- | --- |
| **최소** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| **일반** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |
|

## <a name="next-steps"></a>다음 단계

이 문서에서는 Windows 이벤트의 컬렉션을 Microsoft 센티널로 필터링 하는 방법을 배웠습니다.

- [Windows 보안 이벤트 수집](connect-windows-security-events.md)에 대해 자세히 알아보세요.
- [기본 제공](detect-threats-built-in.md) 또는 [사용자 지정](detect-threats-custom.md) 규칙을 사용 하 여 Microsoft 센티널에서 위협을 검색 하기 시작 합니다.
