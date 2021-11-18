---
title: Microsoft Sentinel에 대한 모범 사례
description: Microsoft Sentinel 작업 영역을 관리할 때 사용하는 모범 사례에 대해 알아봅니다.
author: batamig
ms.author: bagol
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 894a92ce45207e8d1c99d8e97e3bff9f656ad16b
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757942"
---
# <a name="best-practices-for-microsoft-sentinel"></a>Microsoft Sentinel에 대한 모범 사례

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 모범 사례 컬렉션에서는 Microsoft Sentinel을 배포, 관리 및 사용할 때 사용할 수 있는 지침을 제공하며, 자세한 내용은 다른 문서에 대한 링크를 제공합니다.

> [!IMPORTANT]
> Microsoft Sentinel을 배포하기 전에 배포 전 활동 및 필수 조건 를 검토하고 [완료합니다.](prerequisites.md)
>


## <a name="best-practice-references"></a>모범 사례 참조

Microsoft Sentinel 설명서에는 문서 전체에 분산된 모범 사례 지침이 있습니다. 이 문서에 제공된 콘텐츠 외에도 자세한 내용은 다음을 참조하세요.

- **관리 사용자**:

    - [Microsoft Sentinel을 배포하기 위한 사전 배포 활동 및 필수 조건](prerequisites.md)
    - [Microsoft Sentinel 작업 영역 아키텍처 모범 사례](best-practices-workspace-architecture.md)
    - [Microsoft Sentinel 작업 영역 아키텍처 디자인](design-your-workspace-architecture.md)
    - [Microsoft Sentinel 샘플 작업 영역 디자인](sample-workspace-designs.md)
    - [데이터 수집 모범 사례](best-practices-data.md)
    - [Microsoft Sentinel 비용 및 청구](billing.md)
    - [Microsoft Sentinel의 권한](roles.md)
    - [Microsoft Sentinel에서 MSSP 지적 재산 보호](mssp-protect-intellectual-property.md)
    - [Microsoft Sentinel의 위협 인텔리전스 통합](threat-intelligence-integration.md)
    - [Microsoft Sentinel 쿼리 및 활동 감사](audit-sentinel-data.md)

- **분석가**:

    - [권장 플레이북](automate-responses-with-playbooks.md#recommended-playbooks)
    - [Microsoft Sentinel에서 가양성 처리](false-positives.md)
    - [Microsoft Sentinel을 사용하여 위협 헌츠](hunting.md)
    - [일반적으로 사용되는 Microsoft Sentinel 통합 문서](top-workbooks.md)
    - [처음부터 위협 탐지](detect-threats-built-in.md)
    - [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)
    - [Jupyter Notebook을 사용하여 보안 위협 헌팅](notebooks.md)

자세한 내용은 성공을 [위한 SecOps 설계: Microsoft Sentinel 배포 모범 사례](https://youtu.be/DyL9MEMhqmI) 비디오를 참조하세요.

## <a name="regular-soc-activities-to-perform"></a>수행할 정기적인 SOC 활동

지속적인 보안 모범 사례를 보장하기 위해 다음 Microsoft Sentinel 활동을 정기적으로 예약합니다.

### <a name="daily-tasks"></a>매일 수행하는 작업

- **인시던트를 심사하고 조사** 합니다.  Microsoft Sentinel **인시던트** 페이지를 검토하여 현재 구성된 분석 규칙에 의해 생성된 새 인시던트 및 새 인시던트 조사를 시작합니다. 자세한 내용은 [자습서: Microsoft Sentinel을 사용하여 인시던트 조사를 참조하세요.](investigate-cases.md)

- **헌팅 쿼리와 책갈피를 탐색** 합니다. 모든 기본 제공 쿼리에 대한 결과를 탐색하고 기존 헌팅 쿼리와 책갈피를 업데이트합니다. 해당하는 경우 새 인시던트를 수동으로 생성하거나 이전 인시던트를 업데이트합니다.  자세한 내용은 다음을 참조하세요.

    - [Microsoft 보안 경고에서 인시던트 자동 생성](create-incidents-from-alerts.md)
    - [Microsoft Sentinel을 사용하여 위협 헌츠](hunting.md)
    - [Microsoft Sentinel을 사용하여 헌팅하는 동안 데이터 추적](bookmarks.md)

- **분석 규칙**.  해당되는 경우 새 분석 규칙을 검토하고 사용하도록 설정합니다. 여기에는 최근에 연결된 데이터 커넥터의 새로 출시된 규칙과 새로 사용 가능한 규칙이 모두 포함됩니다.

- **데이터 커넥터**. 각 데이터 커넥터에서 마지막으로 받은 로그의 상태, 날짜 및 시간을 검토하여 데이터가 흐르는지 확인합니다. 새 커넥터를 확인하고 수집을 검토하여 세트 제한이 초과되지 않았는지 확인합니다. 자세한 내용은 [데이터 수집 모범 사례](best-practices-data.md) 및 [데이터 원본 연결](connect-data-sources.md)을 참조하세요.

- **Log Analytics 에이전트**. 서버와 워크스테이션이 작업 영역에 활발히 연결되어 있는지 확인하고 실패한 모든 연결 문제를 해결하고 수정합니다.   자세한 내용은 [Log Analytics 에이전트 개요](../azure-monitor/agents/log-analytics-agent.md)를 참조하세요.

- **플레이북 실패**. 플레이북 실행 상태를 확인하고 오류를 해결합니다.   자세한 내용은 [자습서: Microsoft Sentinel에서 자동화 규칙으로 플레이북 사용을 참조하세요.](tutorial-respond-threats-playbook.md)

### <a name="weekly-tasks"></a>주별 작업

- **통합 문서 업데이트**. 업데이트를 설치해야 하는 통합 문서가 있는지 확인합니다. 자세한 내용은 [일반적으로 사용되는 Microsoft Sentinel 통합 문서 를 참조하세요.](top-workbooks.md)

- **Microsoft Sentinel GitHub 리포지토리는 를 검토합니다.** Microsoft [Sentinel GitHub 리포지토리를](https://github.com/Azure/Azure-Sentinel) 검토하여 분석 규칙, 통합 문서, 헌팅 쿼리 또는 플레이북과 같은 환경에 대한 새로운 리소스 또는 업데이트된 가치 리소스가 있는지 살펴봅니다.

- **Microsoft Sentinel 감사.** Microsoft Sentinel 활동을 검토하여 분석 규칙, 책갈피 등과 같은 리소스를 업데이트하거나 삭제한 사람을 확인합니다. 자세한 내용은 [Microsoft Sentinel 쿼리 및 활동 감사를 참조하세요.](audit-sentinel-data.md)

### <a name="monthly-tasks"></a>월별 작업

- **사용자 액세스 검토**. 사용자에 대한 사용 권한을 검토하고 비활성 사용자를 확인합니다. 자세한 내용은 [Microsoft Sentinel의 사용 권한을 참조하세요.](roles.md)

- **Log Analytics 작업 영역 검토**. Log Analytics 작업 영역 데이터 보존 정책이 여전히 조직의 정책에 부합하는지 검토합니다.  자세한 내용은 [데이터 보존 정책](/workplace-analytics/privacy/license-expiration) 및 [장기 로그 보존을 위한 Azure Data Explorer 통합](store-logs-in-azure-data-explorer.md)을 참조하세요.


## <a name="integrate-with-microsoft-security-services"></a>Microsoft 보안 서비스와 통합

Microsoft Sentinel은 작업 영역으로 데이터를 전송하는 구성 요소의 역량을 강화하며 다른 Microsoft 서비스 통합을 통해 더욱 강력해졌습니다. Cloud Apps용 Microsoft Defender, 엔드포인트용 Microsoft Defender, Microsoft Defender for Identity 같은 제품으로 검색된 모든 로그를 통해 이러한 서비스에서 검색을 만들고, Microsoft Sentinel에 해당 검색을 제공할 수 있습니다. 로그를 Microsoft Sentinel에 직접 검색하여 이벤트 및 인시던트 전체 그림을 제공할 수도 있습니다.

예를 들어 다음 이미지는 Microsoft Sentinel이 다른 Microsoft 서비스 및 다중 클라우드 및 파트너 플랫폼의 데이터를 수집하여 환경에 대한 적용 범위를 제공하는 방법을 보여 줍니다.

:::image type="content" source="media/best-practices/azure-sentinel-and-other-services.png" alt-text="다른 Microsoft 및 파트너 서비스와 통합된 Microsoft Sentinel":::

다른 원본에서 경고 및 로그를 기록하는 것 외에도 Microsoft Sentinel은 다음을 수행합니다.

- **[기계 학습](bring-your-own-ml.md)을 통해 수집되는 정보를 사용** 하여 더 나은 이벤트 상관 관계, 경고 집계, 이상 탐지 등이 가능합니다.
- 관리 작업 및 조사에 사용되는 추세, 관련 정보 및 주요 데이터를 보여 주는 **[통합 문서](get-visibility.md)를 통해 대화형 시각적 개체를 빌드하고 제공** 합니다.
- 정보 수집, 항목에 대한 작업 수행 및 다양한 플랫폼에 알림 전송을 포함한 **경고에 수행할 [플레이북](tutorial-respond-threats-playbook.md)을 실행** 합니다.
- ServiceNow 및 Jira와 같은 **파트너 플랫폼과 통합** 하여 SOC 팀에 필수적인 서비스를 제공합니다.
- [위협 인텔리전스 플랫폼](threat-intelligence-integration.md)에서 **강화 피드를 수집 및 페치** 하여 수집에 유용한 데이터를 가져옵니다.

##  <a name="manage-and-respond-to-incidents"></a>인시던트 관리 및 응답

다음 이미지는 인시던트 관리 및 응답 프로세스의 권장 단계를 보여 줍니다.

:::image type="content" source="media/best-practices/incident-handling.png" alt-text="인시던트 관리 프로세스: 심사. 준비. 수정. 제거. 인시던트 후 작업.":::

다음 섹션에서는 프로세스 전체에서 인시던트 관리 및 대응을 위해 Microsoft Sentinel 기능을 사용하는 방법에 대한 높은 수준의 설명을 제공합니다. 자세한 내용은 [자습서: Microsoft Sentinel을 사용하여 인시던트 조사를 참조하세요.](investigate-cases.md)

### <a name="use-the-incidents-page-and-the-investigation-graph"></a>인시던트 페이지 및 조사 그래프 사용

Microsoft Sentinel의 Microsoft Sentinel 인시던트 페이지 및 **조사 그래프** 에서 새 **인시던트** 심사 프로세스를 시작합니다. 

계정, URL, IP 주소, 호스트 이름, 작업, 타임라인 등의 주요 엔터티를 검색합니다. 이 데이터를 사용하여 [가양성](false-positives.md)이 있는지 여부를 파악할 수 있습니다. 이 경우 인시던트를 직접 종료할 수 있습니다.

생성된 인시던트는 심사 및 조기 조사를 위한 중앙 위치로 사용되는 **인시던트** 페이지에 표시됩니다. **인시던트** 페이지에는 제목, 심각도 및 관련 경고, 로그 및 관심 엔터티가 나열됩니다. 인시던트는 수집된 로그 및 인시던트에 관련된 모든 도구로의 빠른 이동도 제공합니다.

**인시던트** 페이지는 **조사 그래프** 와 함께 작동하며 사용자는 경고에 대해 탐색하고 심층 조사를 통해 공격의 전체 범위를 표시할 수 있습니다. 그러면 사용자가 이벤트의 타임라인을 생성하고 위협 체인의 범위를 검색할 수 있습니다.

인시던트가 진양성임을 발견한 경우 **인시던트** 페이지에서 직접 작업을 수행하여 로그, 엔터티를 조사하고 위협 체인을 탐색합니다. 위협을 식별하고 작업 계획을 만든 후에는 Microsoft Sentinel 및 기타 Microsoft 보안 서비스의 다른 도구를 사용하여 조사를 [계속합니다.](best-practices.md#integrate-with-microsoft-security-services)


### <a name="handle-incidents-with-workbooks"></a>통합 문서를 사용하여 인시던트 처리

정보 [및 추세를 시각화하고 표시하는](get-visibility.md)것 외에도 Microsoft Sentinel 통합 문서가 중요한 조사 도구입니다.

예를 들어 [조사 인사이트](top-workbooks.md#investigation-insights) 통합 문서를 사용하여 관련된 엔터티 및 경고와 함께 특정 인시던트를 조사합니다. 이 통합 문서를 사용하면 관련 로그, 작업 및 경고를 표시하여 엔터티를 심층적으로 조사할 수 있습니다.

### <a name="handle-incidents-with-threat-hunting"></a>위협 헌팅으로 인시던트 처리

근본 원인을 조사하고 검색하는 동안 기본 제공 위협 헌팅 쿼리를 실행하고 손상 지표에 대한 결과를 확인합니다.

조사 도중 또는 위협을 제거하고 근절하기 위한 조치를 취한 후 [라이브 스트림](livestream.md)을 사용하여 지속적이고 악의적인 이벤트가 있는지 또는 여전히 악의적인 이벤트가 계속되고 있는지 여부를 실시간으로 모니터링합니다.

### <a name="handle-incidents-with-entity-behavior"></a>엔터티 동작을 사용하여 인시던트 처리

Microsoft 센티널의 엔터티 동작을 통해 사용자는 계정 및 호스트 이름 조사와 같은 특정 엔터티에 대 한 작업 및 경고를 검토 하 고 조사할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [Microsoft 센티널에서 사용자 및 엔터티 동작 분석 (UEBA) 사용](enable-entity-behavior-analytics.md)
- [UEBA 데이터를 사용하여 인시던트 조사](investigate-with-ueba.md)
- [Microsoft 센티널 UEBA 강화 참조](ueba-enrichments.md)

### <a name="handle-incidents-with-watchlists-and-threat-intelligence"></a>관심 목록 및 위협 인텔리전스를 사용하여 인시던트 처리

위협 인텔리전스 기반 검색을 최대화하려면 [위협 인텔리전스 데이터 커넥터](connect-threat-intelligence-tip.md)를 사용하여 손상 지표를 수집해야 합니다.

- [Fusion](fusion.md) 및 [TI 맵 경고](./understand-threat-intelligence.md)에 필요한 데이터 원본 연결
- [TAXII 및 TIP 플랫폼](./connect-threat-intelligence-tip.md)에서 지표 수집

위협 헌팅, 요소 수집 또는 더 많은 인시던트 생성 시 분석 규칙의 손상 지표를 사용합니다.

수집된 데이터와 보강 데이터 같은 외부 원본의 데이터를 결합하는 관심 목록을 사용합니다. 예를 들어 조직 또는 최근에 종료된 직원에서 사용되는 IP 주소 범위의 목록을 만듭니다. 플레이북을 통해 관심 목록을 사용하여 보강 데이터를 수집합니다. 예를 들어 탐지, 위협 헌팅 및 조사 시에 사용할 관심 목록에 악성 IP 주소를 추가합니다.

인시던트 도중 관심 목록을 사용하여 조사 데이터를 포함한 다음, 조사가 완료되면 중요한 데이터가 계속 표시되는 것을 막기 위해 삭제합니다.

## <a name="next-steps"></a>다음 단계

Microsoft 센티널를 시작 하려면 다음을 참조 하세요.

- [온-보드 Microsoft 센티널](quickstart-onboard.md)
- [경고 표시](get-visibility.md)
