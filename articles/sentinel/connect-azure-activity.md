---
title: Azure 활동 로그 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: 한 번의 클릭으로 Azure 활동 로그 이벤트를 Azure Sentinel로 스트리밍합니다. 활동 로그 서비스는 Azure에서의 구독 수준 이벤트를 기록하고 표시합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2021
ms.author: yelevin
ms.openlocfilehash: 874c4d520b3d41282d74f047b34fbe7148279a65
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606739"
---
# <a name="connect-data-from-azure-activity-log"></a>Azure 활동 로그에서 데이터 연결

한 번의 클릭으로 Azure의 [**활동 로그** 서비스](../azure-monitor/essentials/activity-log.md)의 이벤트를 Azure Sentinel로 스트리밍할 수 있습니다. **활동 로그** 는 Azure의 [플랫폼 로그](../azure-monitor/essentials/platform-logs-overview.md)Azure Resource Manager 운영 데이터에서 Service Health 이벤트에 대한 업데이트에 이르기까지 구독 수준 이벤트에 대한 인사이트를 제공합니다. **활동 로그** 를 사용하면 구독의 리소스에서 수행된 쓰기 작업(PUT, POST, DELETE)의 '누가, 무엇을, 언제'를 확인할 수 있습니다. 또한 작업 및 기타 관련 속성의 상태도 알 수 있습니다. 이 서비스는 읽기(GET) 작업 또는 ARM(Azure Resource Manager) 모델을 사용하지 않는 리소스에 대한 작업을 기록하지 않습니다.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>필수 구성 요소

- Log Analytics 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- Azure Sentinel로의 로그 스트리밍을 시작 또는 중지하려는 구독에 대한 소유자 권한이 있어야 합니다.

- Azure Policy를 사용하여 Azure 활동 로그 구독에 로그 스트리밍 정책을 적용하려면 정책 할당 범위에 대한 소유자 역할이 있어야 합니다.

## <a name="data-structure-changes"></a>데이터 구조 변경

이 커넥터의 경우 백 엔드에서 활동 로그 이벤트를 수집하는 데 사용되는 메서드가 변경되고 있습니다. 이제 최신 커넥터에서 사용하는 **진단 설정** 파이프라인을 사용하고 있습니다(예: Azure Key Vault 및 Azure Kubernetes Service 커넥터). 이 커넥터에 기존 방식을 계속 사용하고 있다면 새 버전으로 *업그레이드* 하는 것이 좋습니다. 그러면 리소스 로그에 대한 일관성이 향상되고 더 나은 기능이 제공됩니다. 아래의 지침을 따르세요.

**AzureActivity** 테이블의 [구조가 일부 변경](../azure-monitor/essentials/activity-log.md#data-structure-changes)되었지만 **진단 설정** 메서드는 활동 로그 서비스에서 보낸 기존 메서드와 동일한 데이터를 보냅니다.

다음은 진단 설정 파이프라인으로의 이동으로 인한 몇 가지 주요 개선 사항입니다.
- 수집 대기 시간이 향상되었습니다(발생 후 15~20분이 아닌 2~3분 이내에 이벤트 수집).
- 향상된 안정성.
- 성능 개선.
- 활동 로그 서비스에 의해 로그된 이벤트의 모든 범주에 대한 지원(레거시 메커니즘은 하위 집합만 지원하며 예를 들어 Service Health 이벤트는 지원하지 않음).
- Azure Policy를 통한 대규모 관리.

[Azure 활동 로그](../azure-monitor/essentials/activity-log.md) 및 [진단 설정 파이프라인](../azure-monitor/essentials/diagnostic-settings.md)에 대한 자세한 내용은 [Azure Monitor 문서](../azure-monitor/logs/data-platform-logs.md)를 참조하세요.

## <a name="set-up-the-azure-activity-log-connector"></a>Azure 활동 로그 커넥터 설정

새 Azure 활동 로그 커넥터를 설정하려면 다음 두 단계를 수행합니다.
1. 레거시 메서드에서 기존 구독의 연결 끊기.

1. **Azure Policy** 를 사용하는 새 진단 설정 파이프라인에 대한 모든 관련 구독 연결

### <a name="disconnect-from-old-pipeline"></a>이전 파이프라인에서 연결 끊기

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다. 커넥터 목록에서 **Azure 활동** 을 선택한 다음, 오른쪽 아래에 있는 **커넥터 페이지 열기** 단추를 클릭합니다.

1. **지침** 탭 아래 **구성** 섹션의 1단계에서 레거시 메서드에 연결된 기존 구독 목록을 검토하여 새 메서드에 추가할 구독을 확인한 다음, 아래의 **모두 연결 끊기** 단추를 클릭하여 한 번에 모두 연결을 끊습니다.

### <a name="connect-to-new-pipeline"></a>새 파이프라인에 연결

1. 2단계 아래에서 **Azure Policy 할당 마법사 시작** 단추를 선택합니다. 정책 할당 마법사가 열리고, **지정된 Log Analytics 작업 영역으로 스트림할 Azure 활동 로그 구성** 이라는 새 정책을 만들 준비가 되었습니다.

1. **기본 사항** 탭에서 **범위** 아래에 3개 점이 있는 단추를 클릭하여 구독(및 선택적으로 리소스 그룹)을 선택하는 **범위** 패널을 엽니다. 설명을 추가할 수도 있습니다.

1. **매개 변수** 탭에서 **효과** 및 **로그 사용** 필드를 그대로 둡니다. **Log Analytics 작업 영역** 드롭다운 목록에서 Azure Sentinel 작업 영역을 선택합니다.

1. 정책은 나중에 추가되는 리소스에 적용됩니다. 기존 리소스에도 정책을 적용하려면 **수정** 탭을 선택하고 **수정 작업 생성** 확인란에 표시합니다.

1. **검토 + 만들기** 탭에서 **만들기** 를 클릭합니다. 이제 사용자가 선택한 범위에 정책이 할당됩니다.

> [!NOTE]
>
> 이 특정 데이터 커넥터를 사용하면 지난 14일 동안 특정 시점에 데이터를 수집한 경우에만 연결 상태 표시기(데이터 커넥터 갤러리의 색 줄무늬 및 데이터 형식 이름 옆에 연결 아이콘)가 *연결됨*(녹색)으로 표시됩니다. 데이터 수집 없이 14일이 경과되면 커넥터는 연결 끊김 상태로 표시됩니다. 더 많은 데이터가 수신되는 순간 *연결됨* 상태가 반환됩니다.

## <a name="find-your-data"></a>데이터 찾기

Azure 활동 경고의 Log Analytics에서 관련 스키마를 쿼리하려면 쿼리 창 맨 위에 `AzureActivity`를 입력합니다.

데이터를 분석하고 시각화하는 데 도움이 되는 몇 가지 유용한 샘플 쿼리 및 통합 문서 템플릿을 제공하는 커넥터 페이지의 **다음 단계** 탭을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 활동 로그를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [기본 제공](detect-threats-built-in.md) 또는 [사용자 지정](detect-threats-custom.md) 규칙을 사용하여 Azure Sentinel에서 위협 검색을 시작합니다.
