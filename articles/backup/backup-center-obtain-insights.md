---
title: 백업 센터를 사용하여 인사이트 얻기
description: 백업 센터를 사용하여 기록 추세를 분석하고 백업에 대한 심층적인 인사이트를 얻는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: 5244ba6edaac3b58550107c2519b90447ff197e0
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177843"
---
# <a name="obtain-insights-using-backup-center"></a>백업 센터를 사용하여 인사이트 얻기

기록 추세를 분석하고 백업에 대한 심층적인 인사이트를 얻기 위해 백업 센터는 [Azure Monitor 로그](../azure-monitor/logs/data-platform-logs.md) 및 [Azure 통합 문서](../azure-monitor/visualize/workbooks-overview.md)를 사용하는 [Backup 보고서](configure-reports.md)에 대한 인터페이스를 제공합니다. Backup 보고서는 다음과 같은 기능을 제공합니다.

- 사용된 클라우드 스토리지를 할당하고 예측합니다.

- 백업 및 복원을 감사합니다.

- 여러 세분성 수준에서 주요 추세를 확인합니다.

- 백업의 비용 최적화 기회에 대한 가시성과 인사이트 확보

## <a name="supported-scenarios"></a>지원되는 시나리오

- Backup 보고서 현재 Backup 자격 증명 모음을 사용하여 백업되는 워크로드에 대해 지원되지 않습니다.

- 지원되는 시나리오와 지원되지 않는 시나리오 상세 목록은 [지원 매트릭스](backup-center-support-matrix.md)를 참조하세요.

## <a name="get-started"></a>시작

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>Log Analytics 작업 영역에 데이터를 보내도록 자격 증명 모음 구성

[자격 증명 모음에 대해 대규모로 진단 설정을 구성하는 방법 알아보기](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>백업 센터 포털에서 Backup 보고서 보기

백업 센터에서  **보고서** 메뉴 항목을 선택하면 보고서가 열립니다. 하나 이상의 Log Analytics 작업 영역을 선택하여 백업에 대한 주요 정보를 보고 분석합니다.

![백업 센터의 Backup 보고서](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

사용할 수 있는 보기는 다음과 같습니다.

1. **요약** - 이 탭에서는 백업 자산의 간단한 개요를 볼 수 있습니다. [자세한 정보](./configure-reports.md#summary)

2. **백업 항목** - 이 탭에서는 백업 항목 수준에서 사용되는 클라우드 저장소에 대한 정보와 추세를 볼 수 있습니다. [자세한 정보](./configure-reports.md#backup-items)

3. **사용 현황** - 이 탭에서는 백업에 대한 주요 청구 매개 변수를 볼 수 있습니다. [자세한 정보](./configure-reports.md#usage)

4. **작업** - 이 탭에서는 일별 실패한 작업 수, 작업 실패의 주요 원인과 같은 장기 실행 추세를 볼 수 있습니다. [자세한 정보](./configure-reports.md#jobs)

5. **정책** - 이 탭에서는 연결된 항목 수, 특정 정책 하에서 백업된 항목이 사용한 총 클라우드 저장소 등과 같은 모든 활성 정책에 대한 정보를 볼 수 있습니다. [자세한 정보](./configure-reports.md#policies)

6. **최적화** - 이 탭을 사용하여 백업의 잠재적 비용 최적화 기회를 파악할 수 있습니다. [자세한 정보](./configure-reports.md#optimize)

7. **정책 준수** - 이 탭에서는 모든 백업 인스턴스에 하루에 하나 이상의 성공적인 백업이 있는지 여부를 확인할 수 있습니다. [자세한 정보](./configure-reports.md#policy-adherence)

전자 메일 보고서 기능을 사용하여 이러한 보고서에 대한 [메일을](backup-reports-email.md) 구성할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [백업 모니터링 및 운영](backup-center-monitor-operate.md)
- [백업 공간 제어](backup-center-govern-environment.md)
- [Backup 센터를 사용하여 작업 수행](backup-center-actions.md)