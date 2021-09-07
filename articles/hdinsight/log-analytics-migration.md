---
title: Azure HDInsight로 Log Analytics 데이터 마이그레이션
description: Azure Monitor 통합 및 새 테이블 사용 관련 모범 사례에 대한 변경 내용을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.author: ali
author: AliciaLiMicrosoft
ms.date: 04/19/2021
ms.openlocfilehash: 823fb43afbd7ac51cafeff7e9e18472fe32c9aec
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528992"
---
# <a name="log-analytics-migration-guide-for-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터 Log Analytics 마이그레이션 가이드

 Azure HDInsight는 엔터프라이즈급 관리형 클러스터 서비스입니다. 이 서비스는 Azure에서 Apache Spark, Hadoop, HBase 및 Kafka과 같은 오픈 소스 분석 프레임 워크를 실행합니다. Azure HDInsight는 고객이 빅 데이터 분석 애플리케이션을 보다 효율적으로 관리할 수 있도록 다른 Azure 서비스와 통합되었습니다.

Log Analytics는 Azure Portal에서 로그 쿼리를 편집하고 실행하는 도구를 제공합니다. 쿼리는 Azure Monitor 로그에 의해 수집된 데이터에서 가져온 후 대화형으로 결과를 분석합니다. 고객은 Log Analytics 쿼리를 사용하여 특정 조건과 일치하는 레코드를 검색할 수 있습니다. 또한 쿼리를 사용하여 추세를 식별하고, 패턴을 분석하며, 데이터에 인사이트를 제공할 수 있습니다.

2017년에 Log Analytics와의 Azure HDInsight 통합이 이루어졌습니다. HDInsight 고객은 HDInsight 클러스터를 모니터링하고 클러스터의 로그를 쿼리하기 위해 이 기능을 신속하게 채택했습니다. 이 기능의 채택이 증가함에 따라, 팀은 통합에 따른 고객 피드백을 얻을 수 있었습니다. 몇 가지 피드백은 다음과 같았습니다.

- 고객이 저장할 로그를 결정할 수 없었고, 모든 로그를 저장하는 데 비용이 많이 들 수 있었습니다.
- 현재 HDInsight 스키마 로그는 일관된 명명 규칙을 따르지 않으며 일부 테이블이 반복됩니다.
- 고객은 HDInsight 클러스터의 KPI를 쉽게 모니터링할 수 있는 기본 대시보드를 원했습니다.
- 간단한 쿼리를 실행하려 해도 고객은 Log Analytics로 넘어가야만 합니다.

## <a name="solution-overview"></a>솔루션 개요

사용자 의견을 고려하여 Azure HDInsight 팀은 Azure Monitor와의 통합에 투자했습니다. 이 통합은 다음을 지원합니다.

- 고객의 Log Analytics 작업 영역에 있는 새 테이블 집합. 새 테이블은 새 Log Analytics 파이프라인을 통해 전달됩니다.
- 향상된 안정성
- 향상된 로그 전달 속도
- 리소스 기반 테이블 그룹화 및 기본 쿼리



> [!NOTE]  
> 새 Azure Monitor 통합은 퍼블릭 미리 보기로 제공됩니다. 미국 동부 및 서유럽 지역에서만 사용할 수 있습니다.


## <a name="benefits-of-the-new-azure-monitor-integration"></a>새 Azure Monitor 통합의 이점

이 문서에서는 Azure Monitor 통합의 변경 내용에 대해 간략하게 설명하고 새 테이블 사용 관련 모범 사례를 제공합니다.

**재설계된 스키마**: 새 Azure Monitor 통합을 위한 스키마 형식이 더 효과적으로 구성되고 이해하기 쉬워졌습니다. 레거시 스키마에서 가능한 한 많은 모호성을 제거하기 위한 스키마가 2/3 정도 적습니다.

**선택적 로깅(릴리스 예정)** : Log Analytics를 통해 로그 및 메트릭을 사용할 수 있습니다. 사용자가 모니터링 비용을 절감할 수 있도록 새로운 선택적 로깅 기능을 릴리스할 예정입니다. 이 기능을 사용하여 다양한 로그 및 메트릭 원본을 설정 및 해제할 수 있습니다. 이 기능을 사용 시 사용한 만큼만 요금을 지불하면 됩니다.

**로그 클러스터 포털 통합**: **로그** 창이 HDInsight 클러스터 포털에 새로 도입되었습니다. 클러스터에 액세스할 수 있는 모든 사용자는 이 창으로 이동하여 클러스터 리소스가 레코드를 전송하는 테이블을 쿼리할 수 있습니다. 사용자는 특정 클러스터 리소스에 대한 레코드를 보기 위해 더 이상 Log Analytics 작업 영역에 액세스할 필요가 없습니다.

**인사이트 클러스터 포털 통합**: **인사이트** 창도 HDInsight 클러스터 포털에 새로 도입되었습니다. 새 Azure Monitor 통합을 사용하도록 설정한 후에는 **인사이트** 창을 선택할 수 있으며, 클러스터의 유형과 관련된 기본 로그 및 메트릭 대시보드가 자동으로 채워집니다. 이러한 대시보드는 기존 Azure 솔루션에서 개선되었습니다. 클러스터의 성능 및 상태에 대한 깊이 있는 통찰력을 제공합니다.

**대규모 인사이트**: **Azure Monitor** 포털의 **대규모 인사이트** Workbook을 사용하여 다양한 구독에서 클러스터의 상태 및 성능을 모니터링할 수 있습니다.

## <a name="customer-scenarios"></a>고객 시나리오

다음 섹션에서는 고객이 다양한 시나리오에서 새로운 Azure Monitor 통합을 사용하는 방식을 설명합니다. [새 Azure Monitor 통합 활성화](#activate-a-new-azure-monitor-integration) 섹션에서는 새 Azure Monitor 통합을 활성화하고 사용하는 방법을 간략하게 설명합니다. [Azure Monitor 클래식에서 새 Azure Monitor 통합 섹션으로 마이그레이션](#migrate-to-the-new-azure-monitor-integration)에는 이전 Azure Monitor 통합에 의존하는 사용자를 위한 추가 정보가 포함되어 있습니다.

> [!NOTE]
> 2020년 9월 말 이후에 만들어진 클러스터만 새 Azure 모니터링 통합에 적합합니다.

## <a name="activate-a-new-azure-monitor-integration"></a>새 Azure Monitor 통합 활성화 

> [!NOTE]
> 새 통합을 사용하도록 설정하기 전에 액세스할 수 있는 구독에서 만든 Log Analytics 작업 영역이 있어야 합니다. Log Analytics 작업 영역을 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 참조하세요.

클러스터의 포털 페이지로 이동해서 **모니터링** 섹션에 도달할 때까지 왼쪽의 메뉴 아래로 스크롤하여 새 통합을 활성화합니다. **모니터링** 섹션에서 **Monitor 통합** 을 선택합니다. 그런 다음 **사용** 을 선택하면 로그를 보내려는 Log Analytics 작업 영역을 선택할 수 있습니다. 작업 영역을 선택한 후 **저장** 을 선택합니다. 

### <a name="access-the-new-tables"></a>새 테이블 액세스

새 테이블에 액세스하는 방법에는 두 가지가 있습니다. 

### <a name="approach-1"></a>접근 방식 1:
새 테이블에 액세스하는 첫 번째 방법은 Log Analytics 작업 영역을 사용하는 것입니다. 

1. 통합을 사용하도록 설정할 때 선택한 Log Analytics 작업 영역으로 이동합니다. 
2. 화면의 왼쪽 메뉴에서 아래로 스크롤한 다음 **로그** 을 선택합니다. 로그 쿼리 편집기에 작업 영역의 모든 테이블 목록이 팝업으로 표시됩니다. 
3. 테이블이 **솔루션** 별로 그룹화된 경우 새 HDI 테이블은 **로그 관리** 섹션 아래에 있습니다. 
4. **리소스 종류** 를 기준으로 테이블을 그룹화하면 테이블은 아래 이미지에 표시된 것처럼 **HDInsight 클러스터** 섹션 아래에 있습니다. 

> [!NOTE]
> 이 프로세스는 이전 통합에서 로그에 액세스했던 방식을 설명합니다. 이렇게 하려면 사용자에게 작업 영역에 대한 액세스 권한이 있어야 합니다.

### <a name="approach-2"></a>접근 방식 2:

새 테이블에 액세스하는 두 번째 방법은 클러스터 포털 액세스를 사용하는 것입니다.
 
1. 클러스터의 포털 페이지로 이동해서 **모니터링** 섹션이 표시될 때까지 왼쪽의 메뉴 아래로 스크롤합니다. 이 섹션에는 **로그** 창이 표시됩니다. 
2. **로그** 를 선택하면 로그 쿼리 편집기가 나타납니다. 편집기에는 클러스터 리소스와 연결된 모든 로그가 포함되어 있습니다. 통합을 사용하도록 설정할 때 Log Analytics 작업 영역으로 로그를 보냈습니다. 이러한 로그는 RBAC(리소스 기반 액세스)를 제공합니다. RBAC를 사용하는 경우, 클러스터에 대한 액세스 권한은 있지만 작업 영역에 대한 액세스 권한이 없는 사용자는 클러스터와 연결된 로그를 볼 수 있습니다.

비교를 위해 다음 스크린샷에서는 레거시 통합 작업 영역 보기와 새 통합 작업 영역 보기를 보여줍니다.

**레거시 통합 작업 영역 보기**

  :::image type="content" source="./media/log-analytics-migration/legacy-integration-workspace-view.png"  lightbox="./media/log-analytics-migration/legacy-integration-workspace-view.png" alt-text="레거시 통합 작업 영역 보기를 보여주는 스크린샷" border="false":::

**새로운 통합 작업 영역 보기**

  :::image type="content" source="./media/log-analytics-migration/new-integration-workspace-view.png" lightbox="./media/log-analytics-migration/new-integration-workspace-view.png" alt-text="새로운 통합 작업 영역 보기를 보여주는 스크린샷" border="false":::

### <a name="use-the-new-tables"></a>새로운 테이블 사용

이러한 통합은 다음을 통해 새 테이블을 사용하는 데 도움을 줄 수 있습니다.

#### <a name="default-queries-to-use-with-new-tables"></a>새 테이블에 사용할 기본 쿼리

로그 쿼리 편집기에서 테이블 목록 위의 **쿼리** 로 설정/해제를 설정합니다. **리소스 유형** 별로 쿼리를 그룹화하고 **HDInsight 클러스터** 이외의 리소스 유형에 대한 필터를 설정하지 않았는지 확인합니다. 다음 이미지는 **리소스 종류** 별로 그룹화하고 **HDInsight 클러스터** 에 대해 필터링할 경우 결과가 어떻게 표시되는지 보여줍니다. 하나를 선택하기만 하면 로그 쿼리 편집기에 표시됩니다. 쿼리를 성공적으로 실행하려면 클러스터 이름과 같은 일부 정보를 입력해야 하므로 쿼리에 포함된 설명을 꼭 확인하세요.

:::image type="content" source="./media/log-analytics-migration/default-query-results-grouped-resource-type.png" alt-text="기본 쿼리 결과를 그룹화한 리소스 종류를 보여주는 스크린샷" border="true":::


#### <a name="create-your-own-queries"></a>사용자 고유 쿼리 만들기

로그 쿼리 편집기에서 사용자 고유 쿼리를 입력할 수 있습니다. 새 테이블에는 구체화된 새로운 스키마가 있으므로 기존 테이블에 사용되는 쿼리는 새 테이블에 대해 유효하지 않습니다. 기본 쿼리는 새 테이블에 대한 쿼리 셰이핑에 유용한 참조입니다.

#### <a name="insights"></a>자세한 정보

인사이트는 [Azure 통합 문서](../azure-monitor/visualize/workbooks-overview.md)를 사용하여 만든 클러스터 관련 시각화 대시보드입니다. 이러한 대시보드는 클러스터가 실행되는 방법에 대해 자세한 그래프와 시각화를 제공합니다. 대시보드에는 각 클러스터 유형, YARN, 시스템 메트릭 및 구성 요소 로그에 대한 섹션이 있습니다. 포털에서 클러스터의 페이지를 방문하고 **모니터링** 섹션으로 스크롤한 다음 **인사이트** 창을 선택하여 클러스터의 대시보드에 액세스할 수 있습니다. 새 통합을 사용하도록 설정한 경우 대시보드가 자동으로 로드됩니다. 로그를 쿼리하면 그래프가 로드될 때까지 몇 초 정도 기다립니다.

:::image type="content" source="./media/log-analytics-migration/visualization-dashboard.png" lightbox="./media/log-analytics-migration/visualization-dashboard.png" alt-text="시각화 대시보드를 표시하는 스크린샷":::

#### <a name="custom-azure-workbooks"></a>사용자 지정 Azure Workbooks

사용자 지정 그래프 및 시각화를 사용하여 고유한 Azure Workbooks를 만들 수 있습니다. 클러스터의 포털 페이지에서 **모니터링** 섹션까지 아래로 스크롤하고 왼쪽의 메뉴에서 **Workbooks** 창을 선택합니다. 빈 템플릿을 사용하여 시작하거나 **HDInsight 클러스터** 섹션에서 템플릿 중 하나를 사용하여 시작할 수 있습니다. 각 클러스터 유형에 대한 템플릿이 있습니다. 기본 HDInsight Insights가 제공하지 않는 특정 사용자 지정 항목을 저장하려는 경우에 템플릿이 유용합니다. 부족하다고 생각할 경우 HDInsight 인사이트의 새로운 기능에 대한 요청을 언제든 보낼 수 있습니다.

#### <a name="at-scale-workbooks-for-new-azure-monitor-integrations"></a>새 Azure Monitor 통합을 위한 대규모 Workbooks

새로운 대규모 Workbooks를 사용하여 클러스터에 대한 다중 클러스터 모니터링 환경을 가져옵니다. 대규모 Workbooks는 모니터링 파이프라인을 사용하도록 설정한 클러스터를 보여줍니다. 또한 Workbooks를 통해 여러 클러스터의 상태를 한번에 간단하게 확인할 수 있습니다. 이 Workbooks를 보려면 다음을 수행합니다.

1. Azure Portal 홈페이지에서 **Azure Monitor** 페이지로 이동합니다.
2. **Azure Monitor** 페이지에서 **인사이트** 섹션 아래에 있는 **인사이트 허브** 를 선택합니다.
3. **분석** 섹션에서 **HDInsight 클러스터** 를 선택합니다.

   :::image type="content" source="./media/log-analytics-migration/at-scale-workbook.png" lightbox="./media/log-analytics-migration/at-scale-workbook.png" alt-text="를 보여주는 스크린샷" border="false":::

#### <a name="alerts"></a>경고

로그 쿼리 편집기에서 클러스터 및 작업 영역에 사용자 지정 경고를 추가할 수 있습니다. 클러스터 또는 작업 영역 포털에서 **로그** 창을 선택하여 로그 쿼리 편집기로 이동합니다. 다음 스크린샷에 표시된 것 처럼 쿼리를 실행하고 **새 경고 규칙** 을 선택합니다. 자세한 내용은 [경고 구성](../azure-monitor/alerts/alerts-log.md)을 참조하세요.

:::image type="content" source="./media/log-analytics-migration/new-rule-alert.png" alt-text="새 경고 규칙을 표시하는 스크린샷" border="false":::

## <a name="migrate-to-the-new-azure-monitor-integration"></a>새 Azure Monitor 통합으로 마이그레이션

클래식 Azure Monitor 통합을 사용하는 경우 새 Azure Monitor 통합으로 전환한 후 새 테이블 형식을 몇 가지 조정해야 합니다.

새 Azure Monitor 통합을 사용하도록 설정하려면 [새 Azure Monitor 통합 활성화](#activate-a-new-azure-monitor-integration) 섹션에 설명된 단계를 따르세요.

### <a name="run-queries-in-log-analytics"></a>Log Analytics의 쿼리 실행

새 테이블 형식이 이전 테이블 형식과 다르므로, 새 테이블을 사용할 수 있도록 쿼리를 수정해야 합니다. 새 Azure Monitor 통합을 사용하도록 설정하면 테이블과 스키마를 검색하여 이전 쿼리에 사용되는 필드를 식별할 수 있습니다.

대시보드 및 쿼리를 마이그레이션하는 데 사용해야 하는 새 필드를 신속하게 찾을 수 있도록 이전 테이블 간의 [매핑 테이블](#appendix-table-mapping)을 새 테이블에 제공합니다.

**기본 쿼리**: 일반적인 상황에서 새 테이블을 사용하는 방법을 보여주는 기본 쿼리를 만들었습니다. 기본 쿼리는 또한 각 테이블에 사용할 수 있는 정보도 표시합니다. 이 문서의 [새 테이블에 사용되는 기본 쿼리](#default-queries-to-use-with-new-tables) 섹션의 지침에 따라 기본 쿼리에 액세스할 수 있습니다.

### <a name="update-dashboards-for-hdinsight-clusters"></a>HDInsight 클러스터에 대한 대시보드 업데이트

여러 대시보드를 빌드하여 HDInsight 클러스터를 모니터링하는 경우, 새 Azure Monitor 통합을 사용하도록 설정하면 테이블 뒤에서 쿼리를 조정해야 합니다. 테이블 이름 또는 필드 이름은 새 통합에서 변경될 수 있지만 이전 통합에 있는 모든 정보가 포함됩니다.

대시보드 뒤의 쿼리를 업데이트하려면 이전 테이블/스키마 사이의 [매핑 테이블](#appendix-table-mapping)을 새 테이블/스키마에 대해 참조하세요.

#### <a name="out-of-box-dashboards"></a>기본 대시보드 

또한 클러스터 수준에서 기본 대시보드도 개선했습니다. 정보를 생성하는 기본 쿼리를 볼 수 있도록 모든 그래프의 오른쪽 위에 단추가 있습니다. 그래프는 새 테이블을 효과적으로 쿼리하는 방법을 이해하기에 좋은 방법입니다. [인사이트](#insights) 및 [새 Azure Monitor 통합을 위한 대규모 Workbooks](#at-scale-workbooks-for-new-azure-monitor-integrations) 섹션에서 찾을 수 있는 지침에 따라 기본 대시보드에 액세스할 수 있습니다.

### <a name="use-an-hdinsight-at-scale-monitoring-dashboard"></a>HDInsight 대규모 모니터링 대시보드 사용

Hdinsight Spark 모니터링 및 HDInsight 대화형 모니터링과 같은 HDInsight 클러스터의 기본 모니터링 대시보드를 사용하는 사용자를 위해, Microsoft는 Azure Monitor 포털에서 동일한 기능을 제공할 수 있도록 노력하고 있습니다.

Azure Monitor에 HDInsight 클러스터 옵션이 표시됩니다.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-azure-monitor.png" lightbox="./media/log-analytics-migration/hdinsight-azure-monitor.png" alt-text="Azure Monitor의 HDInsight 옵션을 보여주는 스크린샷" border="false":::

Azure Monitor 포털의 Insights 허브는 한곳에서 여러 HDInsight 클러스터를 모니터링하는 기능을 제공합니다. 작업 유형을 기반으로 클러스터를 구성하므로 Spark, HBase, Hive와 같은 유형이 표시됩니다. 이제 여러 대시보드로 이동하는 대신 이 보기에서 모든 HDInsight 클러스터를 모니터링할 수 있습니다.

> [!NOTE]
> 자세한 내용은 이 문서의 [인사이트](#insights) 및 [새 Azure Monitor 통합을 위한 대규모 Workbooks](#at-scale-workbooks-for-new-azure-monitor-integrations) 섹션을 참조하세요.

## <a name="enable-both-integrations-to-accelerate-the-migration"></a>마이그레이션을 가속화하기 위해 두 통합 모두 사용 설정

두 통합 모두에 적합한 클러스터에서 클래식 및 새 Azure Monitor 통합을 동시에 활성화하여 새 Azure Monitor 통합으로 신속하게 마이그레이션할 수 있습니다. 새 통합은 2020년 9월 중순 이후에 생성된 모든 클러스터에 사용할 수 있습니다.

이러한 방식으로 사용 중인 쿼리를 손쉽게 나란히 비교할 수 있습니다.

### <a name="enabling-the-classic-integration"></a>클래식 통합 사용 설정

2020 년 9월 중순에 생성된 클러스터를 사용할 경우 클러스터의 포털에 새 포털 환경이 표시됩니다. 새 파이프라인을 사용 설정하려면 [새 Azure Monitor 통합 활성화](#activate-a-new-azure-monitor-integration) 섹션에 설명된 단계를 따르면 됩니다. 이 클러스터에서 클래식 통합을 활성화하려면 클러스터의 포털 페이지로 이동합니다. 클러스터 포털 페이지 왼쪽 메뉴의 **모니터링** 섹션에서 **모니터 통합** 창을 선택합니다. **HDInsight 클러스터 통합 (클래식)에 대한 Azure Monitor 구성** 을 선택합니다. 클래식 Azure 모니터링 통합을 사용하거나 사용하지 않도록 설정하는 데 사용할 수 있는 토글과 함께 측면 컨텍스트가 나타납니다. 

> [!NOTE]
> 클러스터 포털의 로그 및 인사이트 페이지를 통해서는 클래식 통합의 로그 또는 메트릭이 표시되지 않습니다. 이러한 위치에는 새 통합 로그 및 메트릭만 표시됩니다.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-classic-integration.png" alt-text="클래식 통합의 액세스 링크를 보여주는 스크린샷" border="false":::

2021년 7월 1일 이후부터는 클래식 Azure Monitor 통합을 사용하여 새 클러스터를 만들 수 없습니다.

## <a name="release-and-support-timeline"></a>릴리스 및 지원 타임라인

- 2021년 10월 15일 이후부터는 클래식 Azure 모니터링 통합을 사용할 수 없습니다. 이 날짜 이후에는 클래식 Azure 모니터링 통합을 사용 설정할 수 없습니다.
- 기존 클래식 Azure 모니터링 통합은 계속 작동합니다. 클래식 Azure 모니터링 통합에 대한 지원은 제한됩니다. 
  - 고객이 지원 티켓을 제출하면 문제가 조사됩니다.
  - 솔루션에서 이미지를 변경해야 하는 경우 고객은 새 통합으로 이동해야 합니다.
  - 중요한 보안 문제를 제외하고는 클래식 Azure 모니터링 통합 클러스터를 패치하지 않습니다.

## <a name="appendix-table-mapping"></a>부록: 테이블 매핑

다음 차트는 클래식 Azure 모니터링 통합에서 새 테이블로의 테이블 매핑을 보여줍니다. **워크로드** 열은 각 테이블이 연결된 워크로드를 설명합니다. **새 테이블** 행은 새 테이블의 이름을 표시합니다. **설명** 행은 이 테이블에서 사용할 수 있는 로그/메트릭의 유형을 설명합니다. **이전 테이블** 행은 현재 데이터가 **새 테이블** 행에 나열된 테이블에 표시되는 클래식 Azure Monitor 통합의 모든 테이블 목록입니다.

> [!NOTE]
> 일부 테이블은 새 테이블이며 기존 테이블을 기반으로 하지 않습니다.

## <a name="general-workload-tables"></a>일반 워크로드 테이블

| 새 테이블 | 세부 정보 |
| --- | --- |
| HDInsightAmbariSystemMetrics | <ul><li>**설명**: 이 테이블에는 Ambari에서 수집된 시스템 메트릭이 포함되어 있습니다. 이제 메트릭은 두 헤드 노드 대신 클러스터의 각 노드(에지 노드 제외)에서 가져오게 됩니다. 각 메트릭은 열이며, 메트릭은 레코드별로 한 번만 보고됩니다.</li><li>**이전 테이블**: metrics\_cpu\_nice\_cl, metrics\_cpu\_system\_cl, metrics\_cpu\_user\_cl, metrics\_memory\_cache\_CL, metrics\_memory\_swap\_CL, metrics\_memory\_total\_CLmetrics\_memory\_buffer\_CL, metrics\_load\_1min\_CL, metrics\_load\_cpu\_CL, metrics\_load\_nodes\_CL, metrics\_load\_procs\_CL, metrics\_network\_in\_CL, metrics\_network\_out\_CL</li></ul>|
| HDInsightAmbariClusterAlerts | <ul><li>**설명**: 이 테이블에는 클러스터의 각 노드(에지 노드 제외)에 대한 Ambari 클러스터 경고가 포함됩니다. 각 경고는 이 테이블의 레코드입니다.</li><li>**이전 테이블**: metrics\_cluster\_alerts\_CL</li></ul>|
| HDInsightSecurityLogs | <ul><li>**설명**: 이 테이블에는 Ambari 감사 및 인증 로그의 레코드가 포함되어 있습니다.</li><li>**이전 테이블**: log\_ambari\_audit\_CL, log\_auth\_CL</li></ul>|
| HDInsightRangerAuditLogs | <ul><li>**설명**: 이 테이블에는 ESP 클러스터에 대한 레인저 감사 로그의 모든 레코드가 포함되어 있습니다.</li><li>**이전 테이블**: ranger\_audit\_logs\_CL</li></ul>|
| HDInsightGatewayAuditLogs\_CL | <ul><li>**설명**: 이 테이블에는 게이트웨이 노드 감사 정보가 포함되어 있습니다. 이전 테이블의 테이블 열과 동일한 형식입니다. **이 파일은 여전히 사용자 지정 로그 섹션에 있습니다.**</li><li>**이전 테이블**: log\_gateway\_Audit\_CL</li></ul>|

## <a name="spark-workload"></a>Spark 워크로드

> [!NOTE]
> Spark 애플리케이션 관련 테이블은 Spark 워크로드에 대해 깊이 있는 정보를 제공하는 11개의 새 Spark 테이블로 대체되었습니다(HDInsightSpark*부터).


| 새 테이블 | 세부 정보 |
| --- | --- |
| HDInsightSparkLogs | <ul><li>**설명**: 이 테이블에는 Spark 및 관련 구성 요소인 Livy 및 Jupyter와 관련된 모든 로그가 포함되어 있습니다.</li><li>**이전 테이블**: log\_livy,\_CL, log\_jupyter\_CL, log\_spark\_CL, log\_sparkappsexecutors\_CL, log\_sparkappsdrivers\_CL</li></ul>|
| HDInsightSparkApplicationEvents | <ul><li>**설명**: 이 테이블에는 전송 및 완료 시간, 앱 ID, 앱 이름 등 Spark 애플리케이션의 이벤트 정보가 포함되어 있습니다. 애플리케이션이 시작되고 완료되는 시기를 추적하는 데 유용합니다. </li></ul>|
| HDInsightSparkBlockManagerEvents | <ul><li>**설명**: 이 테이블에는 Spark의 블록 관리자와 관련된 이벤트 정보가 포함되어 있습니다. 실행자 메모리 사용량과 같은 정보를 포함합니다.</li></ul>|
| HDInsightSparkEnvironmentEvents | <ul><li>**설명**: 이 테이블에는 애플리케이션이 실행되는 환경에 대한 이벤트 정보가 포함되어 있습니다. 여기에는 Spark 배포 모드, Master 및 실행자에 대한 정보가 포함됩니다.</li></ul>|
| HDInsightSparkExecutorEvents | <ul><li>**설명**: 이 테이블에는 애플리케이션의 Spark Executor 사용에 대한 이벤트 정보가 포함되어 있습니다.</li></ul>|
| HDInsightSparkExtraEvents | <ul><li>**설명**: 이 테이블에는 다른 Spark 테이블에 맞지 않는 이벤트 정보가 포함되어 있습니다. </li></ul>|
| HDInsightSparkJobEvents | <ul><li>**설명**: 이 테이블에는 시작 및 종료 시간, 결과, 연결된 단계 등 Spark 작업에 대한 정보가 포함되어 있습니다.</li></ul>|
| HDInsightSparkSQLExecutionEvents | <ul><li>**설명**: 이 테이블에는 계획 정보, 설명, 시작 및 종료 시간 등 Spark SQL 쿼리에 대한 이벤트 정보가 포함되어 있습니다.</li></ul>|
| HDInsightSparkStageEvents | <ul><li>**설명**: 이 테이블에는 시작 및 완료 시간, 오류 상태 및 자세한 실행 정보 등 Spark 스테이에 대한 이벤트 정보가 포함되어 있습니다.</li></ul>|
| HDInsightSparkStageTaskAccumulables | <ul><li>**설명**: 이 테이블에는 스테이지 및 태스크에 대한 성능 메트릭이 포함되어 있습니다.</li></ul>|
| HDInsightTaskEvents | <ul><li>**설명**: 이 테이블에는 시작 및 완료 시간, 연결된 스테이지, 실행 상태, 태스크 유형 등 Spark 태스크에 대한 이벤트 정보가 포함되어 있습니다.</li></ul>|
| HDInsightJupyterNotebookEvents | <ul><li>**설명**: 이 테이블에는 Jupyter 노트북에 대한 이벤트 정보가 포함되어 있습니다.</li></ul>|

## <a name="hadoopyarn-workload"></a>Hadoop/YARN 워크로드

| 새 테이블 | 세부 정보 |
| --- | --- |
| HDInsightHadoopAndYarnMetrics | <ul><li>**설명**: 이 테이블에는 Hadoop 및 YARN 프레임워크의 JMX 메트릭이 포함되어 있습니다. 이전 사용자 지정 로그 테이블과 동일한 JMX 메트릭을 모두 포함하고 중요한 것으로 간주된 메트릭을 포함합니다. 타임라인 서버, 노드 관리자 및 작업 기록 서버 메트릭을 추가했습니다. 레코드마다 메트릭이 하나씩 포함되어 있습니다.</li><li>**이전 테이블**: metrics\_resourcemanager\_clustermetrics\_CL, metrics\_resourcemanager\_jvm\_CL, metrics\_resourcemanager\_queue\_root\_CL, metrics\_resourcemanager\_queue\_root\_joblauncher\_CL, metrics\_resourcemanager\_queue\_root\_default\_CL, metrics\_resourcemanager\_queue\_root\_thriftsvr\_CL</li></ul>|
| HDInsightHadoopAndYarnLogs | <ul><li>**설명**: 이 테이블에는 Hadoop 및 YARN 프레임워크에서 생성된 모든 로그가 포함되어 있습니다.</li><li>**이전 테이블**: log\_mrjobsummary\_CL, log\_resourcemanager\_CL, log\_timelineserver\_CL, log\_nodemanager\_CL</li></ul>|

 
## <a name="hivellap-workload"></a>Hive/LLAP 워크로드 

| 새 테이블 | 세부 정보 |
| --- | --- |
| HDInsightHiveAndLLAPMetrics | <ul><li>**설명**: 이 테이블에는 Hive 및 LLAP 프레임워크의 JMX 메트릭이 포함되어 있습니다. 이전 사용자 지정 로그 테이블과 동일한 JMX 메트릭을 모두 포함합니다. 레코드마다 메트릭이 하나씩 포함되어 있습니다.</li><li>**이전 테이블**: llap\_metrics\_hiveserver2\_CL, llap\_metrics\_hs2\_metrics\_subsystemllap\_metrics\_jvm\_CL, llap\_metrics\_llap\_daemon\_info\_CL, llap\_metrics\_buddy\_allocator\_info\_CL, llap\_metrics\_deamon\_jvm\_CL, llap\_metrics\_io\_CL, llap\_metrics\_executor\_metrics\_CL, llap\_metrics\_metricssystem\_stats\_CL, llap\_metrics\_cache\_CL</li></ul>|
| HDInsightHiveAndLLAPLogs | <ul><li>**설명**: 이 테이블에는 Hive, LLAP, 관련 구성 요소인 WebHCat 및 Zeppelin에서 생성된 로그가 포함되어 있습니다.</li><li>**이전 테이블**: log\_hivemetastore\_CL log\_hiveserver2\_CL, log\_hiveserve2interactive\_CL, log\_webhcat\_CL, log\_zeppelin\_zeppelin\_CL</li></ul>|


## <a name="kafka-workload"></a>Kafka 워크로드

| 새 테이블 | 세부 정보 |
| --- | --- |
| HDInsightKafkaMetrics | <ul><li>**설명**: 이 테이블에는 Kafka의 JMX 메트릭이 포함되어 있습니다. 이전 사용자 지정 로그 테이블과 동일한 JMX 메트릭을 모두 포함하고 중요한 것으로 간주된 메트릭을 포함합니다. 레코드마다 메트릭이 하나씩 포함되어 있습니다.</li><li>**이전 테이블**: metrics\_kafka\_CL</li></ul>|
| HDInsightKafkaLogs | <ul><li>**설명**: 이 테이블에는 Kafka broker에서 생성된 모든 로그가 포함되어 있습니다.</li><li>**이전 테이블**: log\_kafkaserver\_CL, log\_kafkacontroller\_CL</li></ul>|

## <a name="hbase-workload"></a>HBase 워크로드

| 새 테이블 | 세부 정보 |
| --- | --- |
| HDInsightHBaseMetrics | <ul><li>**설명**: 이 테이블에는 HBase의 JMX 메트릭이 포함되어 있습니다. 이전 스키마 열에 나열된 테이블의 동일한 JMX 메트릭이 모두 포함되어 있습니다. 이전 테이블과 달리 각 행에는 하나의 메트릭이 포함됩니다.</li><li>**이전 테이블**: metrics\_regionserver\_CL, metrics\_regionserver\_wal\_CL, metrics\_regionserver\_ipc\_CL, metrics\_regionserver\_os\_CL, metrics\_regionserver\_replication\_CL, metrics\_restserver\_CL, metrics\_restserver\_jvm\_CL, metrics\_hmaster\_assignmentmanager\_CL, metrics\_hmaster\_ipc\_CL, metrics\_hmaser\_os\_CL, metrics\_hmaster\_balancer\_CL, metrics\_hmaster\_jvm\_CL, metrics\_hmaster\_CL,metrics\_hmaster\_fs\_CL</li></ul>|
| HDInsightHBaseLogs | <ul><li>**설명**: 이 테이블에는 HBase와 관련 구성 요소인 Phoenix 및 HDFS의 로그가 포함되어 있습니다.</li><li>**이전 테이블**: log\_regionserver\_CL, log\_restserver\_CL, log\_phoenixserver\_CL, log\_hmaster\_CL, log\_hdfsnamenode\_CL, log\_garbage\_collector\_CL</li></ul>|

## <a name="storm-workload"></a>Storm 워크로드

| 새 테이블 | 세부 정보 |
| --- | --- |
| HDInsightStormMetrics | <ul><li>**설명**: 이 테이블에는 이전 테이블 섹션의 테이블과 동일한 JMX 메트릭이 포함되어 있습니다. 해당 행에는 레코드마다 하나의 메트릭이 포함됩니다.</li><li>**이전 테이블**: metrics\_stormnimbus\_CL, metrics\_stormsupervisor\_CL</li></ul>|
| HDInsightStormTopologyMetrics | <ul><li>**설명**: 이 테이블에는 Storm의 토폴로지 수준 메트릭이 포함되어 있습니다. 이전 테이블 섹션에 나열된 테이블과 같은 모양입니다.</li><li>**이전 테이블**: metrics\_stormrest\_CL</li></ul>|
| HDInsightStormLogs | <ul><li>**설명**: 이 테이블에는 Storm에서 생성된 모든 로그가 포함되어 있습니다.</li><li>**이전 테이블**: log\_supervisor\_CL, log\_nimbus\_CL</li></ul>|

## <a name="oozie-workload"></a>Oozie 워크로드

| 새 테이블 | 세부 정보 |
| --- | --- |
| HDInsightOozieLogs | <ul><li>**설명**: 이 테이블에는 Oozie 프레임워크에서 생성된 모든 로그가 포함되어 있습니다.</li><li>**이전 테이블**: Log\_oozie\_CL</li></ul>|

## <a name="next-steps"></a>다음 단계
[Azure Monitor Logs를 쿼리하여 HDInsight 클러스터 모니터링](hdinsight-hadoop-oms-log-analytics-use-queries.md)
