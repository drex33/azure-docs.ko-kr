---
title: Azure Sentinel 모범 사례
description: Azure Sentinel 작업 영역을 관리할 때 사용할 모범 사례에 대해 알아봅니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: 8454a75936c84f012d05c8917cfffeec4a08dcdc
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278427"
---
# <a name="best-practices-for-azure-sentinel"></a>Azure Sentinel 모범 사례

이 모범 사례 모음에서는 다른 문서에 대한 링크를 비롯하여 Azure Sentinel을 배포, 관리 및 사용할 때 적용할 지침을 제공합니다.

> [!IMPORTANT]
> Azure Sentinel을 배포하기 전에 [사전 배포 활동 및 필수 구성 요소](prerequisites.md)를 검토하고 완료합니다.
>


## <a name="best-practice-references"></a>모범 사례 참조

Azure 센티널 설명서는 문서 전체에 분산 된 모범 사례 지침을 제공 합니다. 이 문서에서 제공 하는 내용 외에 자세한 내용은 다음을 참조 하세요.

- **관리 사용자**:

    - [Azure Sentinel을 배포하기 위한 사전 배포 활동 및 필수 구성 요소](prerequisites.md)
    - [Azure Sentinel 작업 영역 아키텍처 모범 사례](best-practices-workspace-architecture.md)
    - [Azure Sentinel 작업 영역 아키텍처 디자인](design-your-workspace-architecture.md)
    - [Azure Sentinel 샘플 작업 영역 디자인](sample-workspace-designs.md)
    - [데이터 수집 모범 사례](best-practices-data.md)
    - [Azure Sentinel 비용 및 청구](azure-sentinel-billing.md)
    - [Azure Sentinel의 권한](roles.md)
    - [Azure Sentinel에서 MSSP 지적 재산 보호](mssp-protect-intellectual-property.md)
    - [Azure Sentinel에서 위협 인텔리전스 통합](threat-intelligence-integration.md)
    - [Azure Sentinel 쿼리 및 활동 감사](audit-sentinel-data.md)

- **분석가**:

    - [권장 플레이북](automate-responses-with-playbooks.md#recommended-playbooks)
    - [Azure Sentinel의 가양성 처리](false-positives.md)
    - [Azure Sentinel을 사용하여 위협 헌팅](hunting.md)
    - [일반적으로 사용되는 Azure Sentinel 통합 문서](top-workbooks.md)
    - [처음부터 위협 탐지](detect-threats-built-in.md)
    - [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)
    - [Jupyter Notebook을 사용하여 보안 위협 헌팅](notebooks.md)

자세한 내용은 [성공을 위한 SecOps 설계: Azure Sentinel 배포 모범 사례](https://youtu.be/DyL9MEMhqmI) 비디오를 참조하세요.

## <a name="regular-soc-activities-to-perform"></a>수행할 정기적인 SOC 활동

다음 Azure Sentinel 활동을 정기적으로 예약하여 지속적인 보안 모범 사례를 확인합니다.

### <a name="daily-tasks"></a>매일 수행하는 작업

- **인시던트를 심사하고 조사** 합니다.  Azure Sentinel **인시던트** 페이지를 검토하여 현재 구성된 분석 규칙에 의해 생성된 새 인시던트를 확인하고 새 인시던트 조사를 시작합니다. 자세한 내용은 [자습서: Azure Sentinel을 사용하여 인시던트 조사](investigate-cases.md)를 참조하세요.

- **헌팅 쿼리와 책갈피를 탐색** 합니다. 모든 기본 제공 쿼리에 대한 결과를 탐색하고 기존 헌팅 쿼리와 책갈피를 업데이트합니다. 해당하는 경우 새 인시던트를 수동으로 생성하거나 이전 인시던트를 업데이트합니다.  자세한 내용은 다음을 참조하세요.

    - [Microsoft 보안 경고에서 인시던트 자동 생성](create-incidents-from-alerts.md)
    - [Azure Sentinel을 사용하여 위협 헌팅](hunting.md)
    - [Azure Sentinel을 사용하여 헌팅하는 동안 데이터 추적 유지](bookmarks.md)

- **분석 규칙**.  해당되는 경우 새 분석 규칙을 검토하고 사용하도록 설정합니다. 여기에는 최근에 연결된 데이터 커넥터의 새로 출시된 규칙과 새로 사용 가능한 규칙이 모두 포함됩니다.

- **데이터 커넥터**. 각 데이터 커넥터에서 마지막으로 받은 로그의 상태, 날짜 및 시간을 검토하여 데이터가 흐르는지 확인합니다. 새 커넥터를 확인하고 수집을 검토하여 세트 제한이 초과되지 않았는지 확인합니다. 자세한 내용은 [데이터 수집 모범 사례](best-practices-data.md) 및 [데이터 원본 연결](connect-data-sources.md)을 참조하세요.

- **Log Analytics 에이전트**. 서버와 워크스테이션이 작업 영역에 활발히 연결되어 있는지 확인하고 실패한 모든 연결 문제를 해결하고 수정합니다.   자세한 내용은 [Log Analytics 에이전트 개요](../azure-monitor/agents/log-analytics-agent.md)를 참조하세요.

- **플레이북 실패**. 플레이북 실행 상태를 확인하고 오류를 해결합니다.   자세한 내용은 [자습서: Azure Sentinel에서 자동화 규칙으로 플레이북 사용](tutorial-respond-threats-playbook.md)을 참조하세요.


### <a name="weekly-tasks"></a>주별 작업

- **통합 문서 업데이트**. 업데이트를 설치해야 하는 통합 문서가 있는지 확인합니다. 자세한 내용은 [일반적으로 사용되는 Azure Sentinel 통합 문서](top-workbooks.md)를 참조하세요.

- **Azure Sentinel GitHub 리포지토리 검토**. [Azure Sentinel GitHub](https://github.com/Azure/Azure-Sentinel) 리포지토리를 검토하여 사용자 환경에 대한 새로운 리소스 또는 업데이트된 리소스 값(예: 분석 규칙, 통합 문서, 헌팅 쿼리 또는 플레이북)이 있는지 여부를 살펴봅니다.

- **Azure Sentinel 감사**. Azure Sentinel 활동을 검토하여 분석 규칙, 책갈피 등의 리소스를 업데이트하거나 삭제한 사용자를 확인합니다. 자세한 내용은 [Azure Sentinel 쿼리 및 활동 감사](audit-sentinel-data.md)를 참조하세요.

### <a name="monthly-tasks"></a>월별 작업

- **사용자 액세스 검토**. 사용자에 대한 사용 권한을 검토하고 비활성 사용자를 확인합니다. 자세한 내용은 [Azure Sentinel의 권한](roles.md)을 참조하세요.

- **Log Analytics 작업 영역 검토**. Log Analytics 작업 영역 데이터 보존 정책이 여전히 조직의 정책에 부합하는지 검토합니다.  자세한 내용은 [데이터 보존 정책](/workplace-analytics/privacy/license-expiration) 및 [장기 로그 보존을 위한 Azure Data Explorer 통합](store-logs-in-azure-data-explorer.md)을 참조하세요.


## <a name="integrate-with-microsoft-security-services"></a>Microsoft 보안 서비스와 통합

Azure Sentinel은 작업 영역에 데이터를 전송하는 구성 요소에 의해 사용되며 다른 Microsoft 서비스와의 통합을 통해 더 강력하게 이루어집니다. Microsoft Cloud App Security, Microsoft Defender for Endpoint 및 Microsoft Defender for Identity와 같은 제품으로 수집된 모든 로그는 이러한 서비스에서 탐지를 생성하는 것을 허용하여 결국 Azure Sentinel에 해당 탐지를 제공합니다. 로그를 Azure Sentinel로 직접 수집하여 이벤트 및 인시던트에 대한 더 자세한 그림을 제공할 수도 있습니다.

예를 들어 다음 이미지는 Azure Sentinel에서 다른 Microsoft 서비스 및 다중 클라우드 및 파트너 플랫폼의 데이터를 수집하여 사용자 환경에 대한 범위를 제공하는 방법을 보여 줍니다.

:::image type="content" source="media/best-practices/azure-sentinel-and-other-services.png" alt-text="다른 Microsoft 및 파트너 서비스와 Azure Sentinel 통합":::

다른 원본의 경고와 로그를 수집하는 것 외에도 Azure Sentinel은 다음을 수행합니다.

- **[기계 학습](bring-your-own-ml.md)을 통해 수집되는 정보를 사용** 하여 더 나은 이벤트 상관 관계, 경고 집계, 이상 탐지 등이 가능합니다.
- 관리 작업 및 조사에 사용되는 추세, 관련 정보 및 주요 데이터를 보여 주는 **[통합 문서](get-visibility.md)를 통해 대화형 시각적 개체를 빌드하고 제공** 합니다.
- 정보 수집, 항목에 대한 작업 수행 및 다양한 플랫폼에 알림 전송을 포함한 **경고에 수행할 [플레이북](tutorial-respond-threats-playbook.md)을 실행** 합니다.
- ServiceNow 및 Jira와 같은 **파트너 플랫폼과 통합** 하여 SOC 팀에 필수적인 서비스를 제공합니다.
- [위협 인텔리전스 플랫폼](threat-intelligence-integration.md)에서 **강화 피드를 수집 및 페치** 하여 수집에 유용한 데이터를 가져옵니다.

##  <a name="manage-and-respond-to-incidents"></a>인시던트 관리 및 응답

다음 이미지는 인시던트 관리 및 응답 프로세스의 권장 단계를 보여 줍니다.

:::image type="content" source="media/best-practices/incident-handling.png" alt-text="인시던트 관리 프로세스: 심사. 준비. 수정. 제거. 인시던트 후 작업.":::

다음 섹션에서는 프로세스 전체에서 인시던트 관리 및 응답에 Azure Sentinel 기능을 사용하는 방법에 대한 개략적인 설명을 제공합니다. 자세한 내용은 [자습서: Azure Sentinel을 사용하여 인시던트 조사](investigate-cases.md)를 참조하세요.

### <a name="use-the-incidents-page-and-the-investigation-graph"></a>인시던트 페이지 및 조사 그래프 사용

Azure Sentinel에서 Azure Sentinel **인시던트** 페이지 및 **조사 그래프** 에서 새 인시던트에 대한 심사 프로세스를 시작합니다. 

계정, URL, IP 주소, 호스트 이름, 작업, 타임라인 등의 주요 엔터티를 검색합니다. 이 데이터를 사용하여 [가양성](false-positives.md)이 있는지 여부를 파악할 수 있습니다. 이 경우 인시던트를 직접 종료할 수 있습니다.

생성된 인시던트는 심사 및 조기 조사를 위한 중앙 위치로 사용되는 **인시던트** 페이지에 표시됩니다. **인시던트** 페이지에는 제목, 심각도 및 관련 경고, 로그 및 관심 엔터티가 나열됩니다. 인시던트는 수집된 로그 및 인시던트에 관련된 모든 도구로의 빠른 이동도 제공합니다.

**인시던트** 페이지는 **조사 그래프** 와 함께 작동하며 사용자는 경고에 대해 탐색하고 심층 조사를 통해 공격의 전체 범위를 표시할 수 있습니다. 그러면 사용자가 이벤트의 타임라인을 생성하고 위협 체인의 범위를 검색할 수 있습니다.

인시던트가 진양성임을 발견한 경우 **인시던트** 페이지에서 직접 작업을 수행하여 로그, 엔터티를 조사하고 위협 체인을 탐색합니다. 위협을 식별하고 작업 계획을 만든 후에는 Azure Sentinel의 다른 도구 및 [기타 Microsoft 보안 서비스](best-practices.md#integrate-with-microsoft-security-services)를 사용하여 조사를 계속합니다.


### <a name="handle-incidents-with-workbooks"></a>통합 문서를 사용하여 인시던트 처리

[정보 및 추세를 시각화하고 표시](get-visibility.md)하는 것 외에도 Azure Sentinel 통합 문서는 중요한 조사 도구입니다.

예를 들어 [조사 인사이트](top-workbooks.md#investigation-insights) 통합 문서를 사용하여 관련된 엔터티 및 경고와 함께 특정 인시던트를 조사합니다. 이 통합 문서를 사용하면 관련 로그, 작업 및 경고를 표시하여 엔터티를 심층적으로 조사할 수 있습니다.

### <a name="handle-incidents-with-threat-hunting"></a>위협 헌팅으로 인시던트 처리

근본 원인을 조사하고 검색하는 동안 기본 제공 위협 헌팅 쿼리를 실행하고 손상 지표에 대한 결과를 확인합니다.

조사 도중 또는 위협을 제거하고 근절하기 위한 조치를 취한 후 [라이브 스트림](livestream.md)을 사용하여 지속적이고 악의적인 이벤트가 있는지 또는 여전히 악의적인 이벤트가 계속되고 있는지 여부를 실시간으로 모니터링합니다.

### <a name="handle-incidents-with-entity-behavior"></a>엔터티 동작을 사용하여 인시던트 처리

Azure Sentinel의 엔터티 동작을 통해 사용자는 계정 및 호스트 이름 조사와 같은 특정 엔터티에 대한 작업 및 경고를 검토하고 조사할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [Azure Sentinel의 UEBA(사용자 및 엔터티 동작 분석) 사용 설정](enable-entity-behavior-analytics.md)
- [UEBA 데이터를 사용하여 인시던트 조사](investigate-with-ueba.md)
- [Azure Sentinel UEBA 보강 참조](ueba-enrichments.md)

### <a name="handle-incidents-with-watchlists-and-threat-intelligence"></a>관심 목록 및 위협 인텔리전스를 사용하여 인시던트 처리

위협 인텔리전스 기반 검색을 최대화하려면 [위협 인텔리전스 데이터 커넥터](connect-threat-intelligence-tip.md)를 사용하여 손상 지표를 수집해야 합니다.

- [Fusion](fusion.md) 및 [TI 맵 경고](./understand-threat-intelligence.md)에 필요한 데이터 원본 연결
- [TAXII 및 TIP 플랫폼](./connect-threat-intelligence-tip.md)에서 지표 수집

위협 헌팅, 요소 수집 또는 더 많은 인시던트 생성 시 분석 규칙의 손상 지표를 사용합니다.

수집된 데이터와 보강 데이터 같은 외부 원본의 데이터를 결합하는 관심 목록을 사용합니다. 예를 들어 조직 또는 최근에 종료된 직원에서 사용되는 IP 주소 범위의 목록을 만듭니다. 플레이북을 통해 관심 목록을 사용하여 보강 데이터를 수집합니다. 예를 들어 탐지, 위협 헌팅 및 조사 시에 사용할 관심 목록에 악성 IP 주소를 추가합니다.

인시던트 도중 관심 목록을 사용하여 조사 데이터를 포함한 다음, 조사가 완료되면 중요한 데이터가 계속 표시되는 것을 막기 위해 삭제합니다.

## <a name="next-steps"></a>다음 단계

Azure Sentinel을 시작하려면 다음을 참조하세요.

- [Azure Sentinel 온보딩](quickstart-onboard.md)
- [경고 표시](get-visibility.md)
