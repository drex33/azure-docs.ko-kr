---
title: 장기 로그 보존 시 Azure Data Explorer 통합 | Microsoft Docs
description: 데이터 저장 비용을 줄이기 위해 장기 보존을 위해 Microsoft 센티널 로그를 Azure 데이터 탐색기로 보냅니다.
author: batamig
ms.topic: how-to
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 42022ade761e1019a3c24d61d7624bf9ea7aaa75
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132762123"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>장기 로그 보존 시 Azure Data Explorer 통합

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

기본적으로 Microsoft 센티널에 수집 로그는 Azure Monitor Log Analytics에 저장 됩니다. 이 문서에서는 장기 보존을 위해 Azure 데이터 탐색기에 전송 하 여 Microsoft 센티널에서 보존 비용을 줄이는 방법을 설명 합니다.

Azure Data Explorer에 로그를 저장하면 데이터를 쿼리할 수 있는 기능을 유지하면서 비용을 절감할 수 있습니다. 이는 데이터가 증가할 때 특히 유용합니다. 예를 들어 보안 데이터는 시간이 흐름에 따라 손실될 수 있지만, 규정 요구 사항을 준수하기 위해 로그를 보존해야 하거나 기존 데이터를 정기적으로 조사해야 할 수도 있습니다.

## <a name="about-azure-data-explorer"></a>Azure Data Explorer 정보

Azure Data Explorer는 로그 및 데이터 분석에 매우 최적화된 빅 데이터 분석 플랫폼입니다. Azure 데이터 탐색기는 쿼리 언어로 KQL (Kusto Query Language)를 사용 하므로 Microsoft 센티널 데이터 저장소에 대 한 좋은 대안입니다. 데이터 저장소에 Azure 데이터 탐색기를 사용 하면 플랫폼 간 쿼리를 실행 하 고 Azure 데이터 탐색기 및 Microsoft 센티널에서 데이터를 시각화할 수 있습니다.

자세한 내용은 Azure Data Explorer [설명서](/azure/data-explorer/) 및 [블로그](https://azure.microsoft.com/blog/tag/azure-data-explorer/)를 참조하세요.

### <a name="when-to-integrate-with-azure-data-explorer"></a>Azure Data Explorer와 통합하는 경우

Microsoft 센티널은 전체 SIEM 및 대화 충성도 기능, 신속한 배포 및 구성 뿐만 아니라 SOC 팀을 위한 고급 기본 제공 보안 기능을 제공 합니다. 그러나 Microsoft 센티널에 보안 데이터를 저장 하는 값은 몇 개월 후에 삭제 될 수 있습니다. SOC 사용자가 최신 데이터에 액세스 하는 경우에는 자주 액세스할 필요가 없습니다.

정기적 조사 나 감사와 같이 특정 테이블에만 액세스 해야 하는 경우에는 Microsoft 센티널에 데이터를 유지 하는 것이 더 이상 비용 효율적이 지 않을 수 있습니다. 이 시점에서 비용은 더 저렴 하지만 Microsoft 센티널에서 실행 하는 것과 동일한 KQL 쿼리를 사용 하 여 탐색할 수 있는 Azure 데이터 탐색기에 데이터를 저장 하는 것이 좋습니다.

[Log Analytics azure 데이터 탐색기 프록시 기능](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md)을 사용 하 여 Microsoft 센티널에서 직접 azure 데이터 탐색기의 데이터에 액세스할 수 있습니다. 이렇게 하려면 로그 검색 또는 통합 문서에서 클러스터 간 쿼리를 사용합니다.

> [!IMPORTANT]
> 분석 규칙, UEBA 및 조사 그래프 등의 핵심 SIEM 기능은 Azure Data Explorer에 저장된 데이터를 지원하지 않습니다.
>

> [!NOTE]
> Azure Data Explorer와 통합하면 데이터에 제어 및 세분성도 사용하도록 설정할 수 있습니다. 자세한 내용은 [디자인 고려 사항](#design-considerations)을 참조하세요.
>
## <a name="send-data-directly-to-microsoft-sentinel-and-azure-data-explorer-in-parallel"></a>Microsoft 센티널 및 Azure 데이터 탐색기에 직접 데이터를 동시에 전송

검색, 인시던트 조사, 위협 검색, UEBA 등에 사용할 Microsoft 센티널의 *보안 값을 포함* 하는 모든 데이터를 유지할 수 있습니다. Microsoft 센티널에이 데이터를 유지 하면 일반적으로 3-12 개월 분량의 저장소와 같은 SOC (Security Operations Center) 사용자에 게 유용 합니다.

또한 *보안 값에 상관없이* 모든 데이터를 동시에 Azure Data Explorer로 전송하여 더 오래 저장할 수 있도록 구성할 수 있습니다. Microsoft 센티널와 Azure 데이터 탐색기 모두 동시에 데이터를 전송 하는 동안 일부 중복이 발생 하는 경우 Microsoft 센티널의 보존 비용을 줄일 수 있으므로 비용 절감은 중요할 수 있습니다.

> [!TIP]
> 또한이 옵션을 사용 하면 데이터 저장소에 분산 된 데이터의 상관 관계를 지정할 수 있습니다. 예를 들어, Azure 데이터 탐색기에 저장 된 운영 또는 장기 데이터를 사용 하 여 Microsoft 센티널에 저장 된 보안 데이터를 보강 합니다. 자세한 내용은 [Azure Monitor를 사용하여 Azure Data Explorer 리소스 간 쿼리](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md)를 참조하세요.
>

다음 이미지는 매일 사용 하기 위해 Microsoft 센티널에 보안 데이터만 보내는 한편 Azure 데이터 탐색기에 모든 데이터를 유지 하는 방법을 보여 줍니다.

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="Azure 데이터 탐색기 및 Microsoft 센티널에 데이터를 병렬로 저장 합니다.":::

이 아키텍처 옵션을 구현하는 방법에 대한 자세한 내용은 [Azure Data Explorer 모니터링](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer)을 참조하세요.

## <a name="export-data-from-log-analytics-into-azure-data-explorer"></a>Log Analytics의 데이터를 Azure Data Explorer로 내보내기

Azure Data Explorer에 직접 데이터를 전송하는 대신, Azure Event Hub 또는 Azure Data Factory를 통해 Log Analytics에서 Azure Data Explorer로 데이터를 내보내도록 선택할 수 있습니다.

### <a name="data-export-architecture"></a>데이터 내보내기 아키텍처

다음 이미지는 Azure Monitor 수집 파이프라인을 통해 내보낸 데이터의 샘플 흐름을 보여 줍니다. 데이터는 기본적으로 Log Analytics로 이동되지만, Azure Storage 계정 또는 Event Hub로 내보내도록 구성할 수도 있습니다.

:::image type="content" source="media/store-logs-in-adx/export-data-from-azure-monitor.png" alt-text="Azure Monitor - 아키텍처에서 데이터를 내보냅니다.":::

데이터 내보내기 규칙을 구성할 때 내보내려는 로그 유형을 선택합니다. 구성한 후에는 Log Analytics 수집 엔드포인트에 도착하고 선택한 테이블의 작업 영역을 대상으로 하는 새 데이터가 스토리지 계정 또는 Event Hub로 내보내집니다.

내보낼 데이터를 구성할 때 다음 고려 사항에 유의하세요.

|고려 사항  | 세부 정보 |
|---------|---------|
|**내보낸 데이터의 범위**     |  특정 테이블에 대한 내보내기를 구성하면 예외 없이 해당 테이블로 전송된 모든 데이터가 내보내집니다. 필터링된 데이터 하위 집합을 내보내거나, 특정 이벤트로 내보내기를 제한하는 기능은 지원되지 않습니다.       |
|**위치 요구 사항**     |   Azure Monitor/Microsoft 센티널 작업 영역 및 대상 위치 (Azure Storage 계정 또는 이벤트 허브)는 모두 동일한 지역에 위치 해야 합니다.      |
|**지원되는 테이블**     | 지원되지 않는 사용자 지정 테이블과 같이 모든 테이블이 내보내기에 지원되는 것은 아닙니다. <br><br>자세한 내용은 [Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기](../azure-monitor/logs/logs-data-export.md) 및 [지원되는 테이블 목록](../azure-monitor/logs/logs-data-export.md#supported-tables)을 참조하세요.         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>데이터 내보내기 방법 및 프로시저

Microsoft 센티널에서 Azure 데이터 탐색기로 데이터를 내보내려면 다음 절차 중 하나를 사용 합니다.

- **Azure Event Hub 사용**. Log Analytics의 데이터를 Azure Data Explorer로 수집할 수 있는 Event Hub로 내보냅니다. 이 방법은 Microsoft 센티널 및 Azure 데이터 탐색기 모두에 일부 데이터 (첫 번째 X 개월)를 저장 합니다.

- **Azure Storage 및 Azure Data Factory 사용**. Log Analytics의 데이터를 Azure Blob Storage로 내보낸 다음, Azure Data Factory를 사용해 주기적으로 복사 작업을 실행하여 데이터를 Azure Data Explorer에 추가로 내보냅니다. 이 방법을 사용 하면 Microsoft 센티널/Log Analytics의 보존 제한에 도달한 경우에만 Azure Data Factory에서 데이터를 복사 하 여 중복을 방지할 수 있습니다.

### <a name="azure-event-hub"></a>[Azure 이벤트 허브](#tab/adx-event-hub)

이 섹션에서는 Log Analytics에서 Microsoft 센티널 데이터를 Azure 데이터 탐색기에 수집할 수 있는 이벤트 허브로 내보내는 방법을 설명 합니다. [Microsoft 센티널 및 azure 데이터 탐색기에 직접 데이터를 전송](#send-data-directly-to-microsoft-sentinel-and-azure-data-explorer-in-parallel)하는 것과 마찬가지로,이 방법에는 데이터가 Log Analytics에 도착 하면 azure 데이터 탐색기으로 스트리밍되는 데이터 중복이 포함 됩니다.

다음 이미지는 Azure Data Explorer로 수집되는 위치에서 Event Hub로 내보낸 데이터의 샘플 흐름을 보여 줍니다.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="Azure Event Hub를 통해 Azure Data Explorer로 데이터를 내보냅니다.":::

이전 이미지에 표시 된 아키텍처는 모든 *X* 개월 마다 자주 액세스 해야 하는 데이터에 대 한 인시던트 관리, 시각적 조사, 위협 구하기, 고급 시각화, UEBA 등 전체 Microsoft 센티널 siem 환경을 제공 합니다. 이와 동시에이 아키텍처를 사용 하면 azure 데이터 탐색기에서 직접 액세스 하거나 Azure 데이터 탐색기 프록시 기능 덕분에 Microsoft 센티널을 통해 장기 데이터를 쿼리할 수도 있습니다. Microsoft 센티널에서 Azure 데이터 탐색기로 변경 하지 않고 Azure 데이터 탐색기의 장기 데이터 저장소에 대 한 쿼리를 이식할 수 있습니다.

> [!NOTE]
> Event Hub를 통해 Azure Data Explorer로 여러 데이터 테이블을 내보낼 때 Log Analytics 데이터 내보내기에는 네임 스페이스당 최대 Event Hubs 수에 대한 제한이 있습니다. 데이터 내보내기에 대한 자세한 내용은 [Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기](../azure-monitor/logs/logs-data-export.md?tabs=portal)를 참조하세요.
>
> 대부분의 고객은 Event Hub 표준 계층을 사용하는 것이 좋습니다. 내보내야 하는 테이블의 양과 해당 테이블에 대한 트래픽의 양에 따라 Event Hub 전용 계층을 사용해야 할 수도 있습니다. 자세한 내용은 [Event Hub 설명서](../event-hubs/event-hubs-quotas.md)를 참조하세요.
>

> [!TIP]
> 이 프로시저에 대한 자세한 내용은 [자습서: Azure Data Explorer에서 모니터링 데이터 수집 및 쿼리](/azure/data-explorer/ingest-data-no-code)를 참조하세요.
>

**Event Hub를 통해 Azure Data Explorer로 데이터를 내보내려면** 다음을 수행 합니다.

1. **Event Hub로 Log Analytics 데이터 내보내기를 구성** 합니다. 자세한 내용은 [Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기](../azure-monitor/logs/logs-data-export.md)를 참조하세요.

1. **Azure Data Explorer 클러스터 및 데이터베이스 만들기**. 자세한 내용은 다음을 참조하세요.

    - [Azure Data Explorer 클러스터 및 데이터베이스 만들기](/azure/data-explorer/create-cluster-database-portal)
    - [Azure Data Explorer 클러스터에 적합한 컴퓨팅 SKU 선택](/azure/data-explorer/manage-cluster-choose-sku)

1. **대상 테이블을 만듭니다**. 원시 데이터는 먼저 중간 테이블에 수집됩니다. 여기에서 원시 데이터가 저장, 조작되고 확장됩니다.

    모든 새 데이터에 적용 되는 함수와 비슷한 업데이트 정책은 확장 된 데이터를 Microsoft 센티널의 원본 테이블과 동일한 스키마를 가진 최종 테이블에 수집 하는 데 사용 됩니다.

    원시 테이블의 보존 기간을 **0** 일로 설정합니다. 데이터는 올바르게 형식이 지정된 테이블에만 저장되고, 변환되는 즉시 원시 테이블에서 삭제됩니다.

    자세한 내용은 [Azure Data Explorer에서 모니터링 데이터 수집 및 쿼리](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)를 참조하세요.

1. <a name="mapping"></a>**테이블 매핑을 만듭니다**. JSON 테이블을 매핑하여 레코드가 Event Hub에서 들어얼 때 원시 이벤트 테이블에 배치되는 방식을 정의합니다. 자세한 내용은 [메트릭 및 로그 데이터에 대한 업데이트 정책 만들기](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)를 참조하세요.

1. **업데이트 정책을 만들어 원시 레코드 테이블에 연결합니다**. 이 단계에서 업데이트 정책이라는 함수를 만들고 대상 테이블에 연결하여 수집 시 데이터를 변환합니다.

    > [!NOTE]
    > Microsoft 센티널과 동일한 스키마와 형식을 사용 하 여 Azure 데이터 탐색기에 데이터 테이블을 포함 하려는 경우에만이 단계가 필요 합니다.
    >

    자세한 내용은 [Azure Data Explorer에 Event Hub 연결](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)을 참조하세요.

1. **Azure Data Explorer에서 Event Hub와 원시 데이터 테이블 간에 데이터를 연결합니다**. 데이터를 Event Hub로 내보내는 방법에 대한 세부 정보를 사용하여 Azure Data Explorer를 구성합니다.

    [Azure Data Explorer 설명서](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)의 지침을 사용하고 다음 세부 정보를 지정합니다.

    - **대상** 에서 원시 데이터로 특정 테이블을 지정합니다.
    - **형식**. 테이블 형식을 `.json`으로 지정합니다.
    - **적용할 매핑**. 위의 [4단계](#mapping)에서 만든 매핑 테이블을 지정합니다.

1. **대상 테이블의 보존 기간을 수정** 합니다. [기본 Azure Data Explorer 보존 정책](/azure/data-explorer/kusto/management/retentionpolicy)은 필요한 것보다 훨씬 더 길어질 수 있습니다.

    다음 명령을 사용하여 보존 정책을 1년으로 업데이트합니다.

    ```kusto
    .alter-merge table <tableName> policy retention softdelete = 365d recoverability = disabled
    ```

### <a name="azure-storage--azure-data-factory"></a>[Azure Storage/Azure Data Factory](#tab/azure-storage-azure-data-factory)

이 섹션에서는 Log Analytics의 Microsoft Sentinel 데이터를 Azure Storage 내보내는 방법을 설명합니다. 여기서 Azure Data Factory 일반 작업을 실행하여 데이터를 Azure Data Explorer 내보낼 수 있습니다.

Azure Storage 및 Azure Data Factory 사용하면 Microsoft Sentinel/Log Analytics의 보존 한도에 가까운 경우에만 Azure Storage 데이터를 복사할 수 있습니다. 데이터 중복은 없으며 Azure Data Explorer Microsoft Sentinel의 보존 제한보다 오래된 데이터에 액세스하는 *데만* 사용됩니다.

> [!TIP]
> 레거시 데이터에서 Azure Storage 및 Azure Data Factory를 사용하기 위한 아키텍처는 더 복잡하지만, 이 방법은 전반적으로 더 큰 비용 절감 효과를 제공할 수 있습니다.
>
다음 이미지는 Azure Storage로 내보낸 데이터의 샘플 흐름을 보여 줍니다. 여기서 Azure Data Factory는 정규 작업을 실행하여 Azure Data Explorer에 추가로 내보냅니다.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-azure-storage-azure-data-factory.png" alt-text="Azure Storage 및 Azure Data Factory를 통해 Azure Data Explorer로 데이터를 내보냅니다.":::

**Azure Storage 및 Azure Data Factory를 통해 Azure Data Explorer로 데이터를 내보내려면** 다음을 수행합니다.

1. **Event Hub로 Log Analytics 데이터 내보내기를 구성** 합니다. 자세한 내용은 [Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기](../azure-monitor/logs/logs-data-export.md?tabs=portal#enable-data-export)를 참조하세요.

1. **Azure Data Explorer 클러스터 및 데이터베이스 만들기**. 자세한 내용은 다음을 참조하세요.

    - [Azure Data Explorer 클러스터 및 데이터베이스 만들기](/azure/data-explorer/create-cluster-database-portal)
    - [Azure Data Explorer 클러스터에 적합한 컴퓨팅 SKU 선택](/azure/data-explorer/manage-cluster-choose-sku)

1. **대상 테이블을 만듭니다**. 원시 데이터는 먼저 중간 테이블에 수집됩니다. 여기에서 원시 데이터가 저장, 조작되고 확장됩니다.

    모든 새 데이터에 적용된 함수와 유사한 업데이트 정책은 확장된 데이터를 Microsoft Sentinel의 원래 테이블과 동일한 스키마가 있는 최종 테이블로 수집하는 데 사용됩니다.

    원시 테이블의 보존 기간을 **0** 일로 설정합니다. 데이터는 올바르게 형식이 지정된 테이블에만 저장되고, 변환되는 즉시 원시 테이블에서 삭제됩니다.

    자세한 내용은 [Azure Data Explorer에서 모니터링 데이터 수집 및 쿼리](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)를 참조하세요.

1. <a name="mapping"></a>**테이블 매핑을 만듭니다**. JSON 테이블을 매핑하여 레코드가 Event Hub에서 들어얼 때 원시 이벤트 테이블에 배치되는 방식을 정의합니다. 자세한 내용은 [메트릭 및 로그 데이터에 대한 업데이트 정책 만들기](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)를 참조하세요.

1. **업데이트 정책을 만들어 원시 레코드 테이블에 연결합니다**. 이 단계에서 업데이트 정책이라는 함수를 만들고 대상 테이블에 연결하여 수집 시 데이터를 변환합니다.

    > [!NOTE]
    > 이 단계는 Microsoft Sentinel과 동일한 스키마 및 형식의 Azure Data Explorer 데이터 테이블을 사용하려는 경우에만 필요합니다.
    >

    자세한 내용은 [Azure Data Explorer에 Event Hub 연결](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)을 참조하세요.

1. **Azure Data Explorer에서 Event Hub와 원시 데이터 테이블 간에 데이터를 연결합니다**. 데이터를 Event Hub로 내보내는 방법에 대한 세부 정보를 사용하여 Azure Data Explorer를 구성합니다.

    [Azure Data Explorer 설명서](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)의 지침을 사용하고 다음 세부 정보를 지정합니다.

    - **대상** 에서 원시 데이터로 특정 테이블을 지정합니다.
    - **형식**. 테이블 형식을 `.json`으로 지정합니다.
    - **적용할 매핑**. 위의 [4단계](#mapping)에서 만든 매핑 테이블을 지정합니다.

1. **Azure Data Factory 파이프라인 설정**:

    - Azure Storage 및 Azure Data Explorer에 연결된 서비스를 만듭니다. 자세한 내용은 다음을 참조하세요.

        - [Azure Data Factory를 사용하여 Azure Blob Storage에서 데이터 복사 및 변환](../data-factory/connector-azure-blob-storage.md)
        - [Azure Data Factory를 사용하여 Azure Data Explorer로(부터) 데이터를 복사합니다](../data-factory/connector-azure-data-explorer.md).

    - Azure Storage에서 데이터 세트를 만듭니다. 자세한 내용은 [Azure Data Factory의 데이터 세트](../data-factory/concepts-datasets-linked-services.md)를 참조하세요.

    - **LastModifiedDate** 속성에 따라 복사 작업을 사용하여 데이터 파이프라인을 만듭니다.

        자세한 내용은 [Azure Data Factory에서 **LastModifiedDate** 까지 새 파일 및 변경된 파일 복사](../data-factory/solution-template-copy-new-files-lastmodifieddate.md)를 참조하세요.

---

## <a name="design-considerations"></a>디자인 고려 사항

Microsoft Sentinel 데이터를 Azure Data Explorer 저장할 때 다음 요소를 고려합니다.

|고려 사항  |설명  |
|---------|---------|
|**클러스터 크기 및 SKU**     | 클러스터의 노드 수와 VM SKU를 신중하게 계획하세요. 이러한 요소가 처리 능력의 양과 핫 캐시의 크기(SSD 및 메모리)를 결정합니다. 캐시가 클수록 더 많은 데이터를 더 높은 성능으로 쿼리할 수 있습니다. <br><br>다양한 구성을 사용하고 결과 비용을 확인할 수 있는 [Azure Data Explorer 크기 조정 계산기](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)를 사용해 보는 것이 좋습니다. <br><br>Azure Data Explorer에는 클러스터 로드를 기준으로 필요에 따라 노드를 추가/제거하도록 지능적인 결정을 내리는 자동 크기 조정 기능도 있습니다. 자세한 내용은 [수요 변화를 수용하도록 Azure Data Explorer의 클러스터 수평 확장 관리(규모 확장)](/azure/data-explorer/manage-cluster-horizontal-scaling)를 참조하세요.      |
|**핫/콜드 캐시**     | Azure Data Explorer는 핫 캐시에 있는 데이터 테이블에 대한 제어 성능을 제공하고 결과를 더 빠르게 반환합니다. Azure Data Explorer 클러스터에 많은 양의 데이터가 있는 경우 테이블을 월별로 분류하여 핫 캐시에 있는 데이터를 더욱 세분화할 수 있습니다. <br><br>자세한 내용은 [캐시 정책(핫 캐시 및 콜드 캐시)](/azure/data-explorer/kusto/management/cachepolicy)을 참조하세요.       |
|**보존**     |   Azure Data Explorer에서는 데이터베이스 또는 개별 테이블에서 데이터가 제거되는 시기를 구성할 수 있으며, 이는 스토리지 비용을 제한하는 데 중요한 부분이기도 합니다. <br><br> 자세한 내용은 [보존 정책](/azure/data-explorer/kusto/management/retentionpolicy)을 참조하세요.       |
|**보안**     |  여러 Azure Data Explorer 설정은 ID 관리, 암호화 등과 같이 데이터를 보호하는 데 도움이 될 수 있습니다. 특히 RBAC(역할 기반 액세스 제어)의 경우 Azure Data Explorer에서 데이터베이스, 테이블 또는 테이블 내 행에 대한 액세스를 제한하도록 구성할 수 있습니다. 자세한 내용은 [Azure Data Explorer의 보안](/azure/data-explorer/security) 및 [행 수준 보안](/azure/data-explorer/kusto/management/rowlevelsecuritypolicy)을 참조하세요.|
|**데이터 공유**     |   Azure Data Explorer를 사용하면 파트너나 공급업체 같은 다른 당사자에게 데이터를 제공할 수 있고 다른 당사자로부터 데이터를 구입할 수도 있습니다. 자세한 내용은 [Azure Data Share를 사용하여 Azure Data Explorer와 데이터 공유](/azure/data-explorer/data-share)를 참조하세요.      |
| **기타 비용 구성 요소** | 다음과 같은 방법에 대한 기타 비용 구성 요소를 고려합니다. <br><br>**Azure Event Hub를 통해 데이터 내보내기**: <br>- Log Analytics 데이터 내보내기 비용, 내보낸 GB별로 청구됩니다. <br>- Event Hub 비용, 처리량 단위별로 청구됩니다.  <br><br>**Azure Storage 및 Azure Data Factory를 통해 데이터 내보내기**: <br>- Log Analytics 데이터 내보내기, 내보낸 GB별로 청구됩니다. <br>- Azure Storage, 저장된 GB별로 청구됩니다. <br>- Azure Data Factory, 활동 실행의 복사본별로 청구됩니다.
|     |         |

## <a name="next-steps"></a>다음 단계

데이터를 저장하는 위치에 관계없이 Microsoft Sentinel을 사용하여 헌팅 및 조사를 계속합니다.

자세한 내용은 다음을 참조하세요.

- [자습서: Microsoft Sentinel을 사용하여 인시던트 조사](investigate-cases.md)
- [Microsoft Sentinel을 사용하여 위협 헌츠](hunting.md)
