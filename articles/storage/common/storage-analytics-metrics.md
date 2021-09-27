---
title: Azure 스토리지 분석 메트릭(클래식)
description: Azure Storage에서 스토리지 분석 메트릭을 사용하는 방법에 대해 알아봅니다. 트랜잭션 및 용량 메트릭, 메트릭 저장 방법, 메트릭 활성화 등에 대해 알아봅니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: ebdac354cd5d490c30ac3eb65f75a71524051517
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553244"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure 스토리지 분석 메트릭(클래식)

**2023년 8월 31일** 에 *클래식 메트릭* 이라고도 하는 스토리지 분석 메트릭이 사용 중지됩니다. 자세한 내용은 [공식 공지](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/)를 참조하세요. 클래식 메트릭을 사용하는 경우 해당 날짜 이전의 Azure Monitor의 메트릭으로 전환해야 합니다. 이 문서는 전환을 수행하는 데 도움이 됩니다.

Azure Storage는 Azure 스토리지 분석 솔루션을 사용하여 스토리지 서비스에 대한 요청과 관련하여 집계된 트랜잭션 통계 및 용량 데이터를 포함하는 메트릭을 저장합니다. 트랜잭션은 API 작업 수준과 스토리지 서비스 수준에서 보고됩니다. 용량은 스토리지 서비스 수준에서 보고됩니다. 메트릭 데이터를 사용하여 다음 작업을 수행할 수 있습니다.
- 스토리지 서비스 사용량을 분석합니다.
- 스토리지 서비스에 대해 수행된 요청에 대한 문제를 진단합니다.
- 서비스를 사용하는 애플리케이션의 성능을 향상시킵니다.

 스토리지 분석 메트릭은 새 Storage 계정에서 기본적으로 활성화됩니다. PowerShell을 사용하거나 Azure CLI를 사용하여 [Azure Portal](https://portal.azure.com/)에서 메트릭을 구성할 수 있습니다. 단계별 안내는 [Azure 스토리지 분석 로그 메트릭(클래식) 설정 및 관리](./manage-storage-analytics-logs.md)를 참조하세요. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 스토리지 분석을 사용하도록 설정할 수도 있습니다. 서비스 속성 설정 작업을 사용하여 각 서비스에 대한 스토리지 분석을 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 스토리지 분석 메트릭은 Azure Blob Storage, Azure Queue Storage, Azure Table Storage 및 Azure Files에 대해 사용하도록 설정됩니다.
> 스토리지 분석 메트릭은 현재 클래식 메트릭입니다. 스토리지 분석 메트릭 대신 [Azure Monitor에서 스토리지 메트릭](../blobs/monitor-blob-storage.md)을 사용하는 것이 좋습니다.

## <a name="transaction-metrics"></a>트랜잭션 메트릭

 각 스토리지 서비스 및 요청한 API 작업에 대해 시간 또는 분 간격으로 유용한 데이터 세트가 기록됩니다. 해당 데이터에는 수신 및 송신, 가용성, 오류, 분류된 요청 비율 등이 포함됩니다. 트랜잭션 세부 정보의 전체 목록은 [스토리지 분석 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema)를 참조하세요.

 트랜잭션 데이터는 서비스 수준과 API 작업 수준에서 기록됩니다. 서비스 수준에서는 서비스에 대한 요청이 없더라도 요청한 모든 API 작업을 요약하는 통계가 1시간마다 테이블 엔터티에 기록됩니다. API 작업 수준에서는 해당 시간 동안 작업이 요청된 경우에만 통계가 엔터티에 기록됩니다.

 예를 들어 Blob service에 대해 **GetBlob** 작업을 수행하면 스토리지 분석 메트릭이 요청을 기록한 다음 이를 Blob service 및 **GetBlob** 작업에 대해 집계된 데이터에 포함시킵니다. 해당 시간 중에 **GetBlob** 작업이 요청되지 않았으면 엔터티가 해당 작업에 대한 *$MetricsTransactionsBlob* 에 기록되지 않습니다.

 스토리지 분석 자체에서 수행한 요청과 사용자 요청에 대해 트랜잭션 메트릭이 기록됩니다. 예를 들어 로그 및 테이블 엔터티 작성을 위한 스토리지 분석의 요청이 기록됩니다.

## <a name="capacity-metrics"></a>용량 메트릭

> [!NOTE]
>  현재는 Blob service에 대해서만 용량 메트릭이 제공됩니다.

 용량 데이터는 스토리지 계정의 Blob service에 대해 매일 기록되며 2개의 테이블 엔터티가 작성됩니다. 이러한 엔터티 중 하나는 사용자 데이터에 대한 통계를 제공하며 다른 하나는 스토리지 분석에서 사용하는 `$logs` Blob 컨테이너에 대한 통계를 제공합니다. *$MetricsCapacityBlob* 테이블에는 다음과 같은 통계가 포함됩니다.

- **Capacity**: 스토리지 계정의 Blob service가 사용하는 스토리지의 양(바이트)입니다.
- **ContainerCount**: 스토리지 계정 Blob service의 Blob 컨테이너 수입니다.
- **ObjectCount**: 스토리지 계정 Blob service에서 커밋된/커밋되지 않은 블록이나 페이지 Blob의 수입니다.

  용량 메트릭에 대한 자세한 내용은 [스토리지 분석 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema)를 참조하세요.

## <a name="how-metrics-are-stored"></a>메트릭 저장 방법

 각 스토리지 서비스에 대한 모든 메트릭 데이터는 해당 서비스에 대해 예약된 3개의 테이블인 트랜잭션 정보용 테이블, 세부 트랜잭션 정보용 테이블 및 용량 정보용 테이블에 저장됩니다. 트랜잭션 및 세부 트랜잭션 정보는 요청과 응답 데이터로 구성됩니다. 용량 정보는 스토리지 사용량 현황 데이터로 구성됩니다. 다음 표에서 설명하는 대로 이름이 지정된 테이블에서 스토리지 계정 Blob service의 시간 메트릭, 분 메트릭 및 용량 정보에 액세스합니다.

|메트릭 수준|테이블 이름|지원되는 버전|
|-------------------|-----------------|----------------------------|
|시간 메트릭, 기본 위치|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|2013년 8월 15일 이전 버전에만 해당됩니다. 해당 이름은 계속 지원되기는 하지만 다음 표를 사용하도록 전환하는 것이 좋습니다.|
|시간 메트릭, 기본 위치|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|모든 버전. 파일 서비스 메트릭에 대한 지원은 2015년 4월 5일 이후 버전에서만 사용할 수 있습니다.|
|분 메트릭, 기본 위치|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|모든 버전. 파일 서비스 메트릭에 대한 지원은 2015년 4월 5일 이후 버전에서만 사용할 수 있습니다.|
|시간 메트릭, 보조 위치|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|모든 버전. 읽기 권한의 지역 중복 복제를 사용하도록 설정해야 합니다.|
|분 메트릭, 보조 위치|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|모든 버전. 읽기 권한의 지역 중복 복제를 사용하도록 설정해야 합니다.|
|용량(Blob service만 해당)|$MetricsCapacityBlob|모든 버전.|

 해당 테이블은 스토리지 서비스 엔드포인트에 대해 스토리지 분석을 사용하도록 설정하면 자동으로 작성됩니다. 스토리지 계정의 네임스페이스를 통해 해당 테이블에 액세스할 수 있습니다. 예: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")` 메트릭 테이블은 목록 작업에 표시되지 않으며 테이블 이름을 통해 직접 액세스해야 합니다.

## <a name="metrics-alerts"></a>메트릭 알림

스토리지 서비스 동작의 중요한 변경 내용을 자동으로 알릴 수 있도록 [Azure Portal](https://portal.azure.com)에서 경고를 설정하는 것이 좋습니다. 단계별 지침은 [메트릭 경고 만들기](./manage-storage-analytics-logs.md)를 참조하세요.

Storage Explorer 도구를 사용하여 이 메트릭 데이터를 구분된 형식에서 다운로드하려면 Microsoft Excel을 사용하여 데이터를 분석할 수 있습니다. 사용 가능한 Storage Explorer 도구의 목록은 [Azure Storage 클라이언트 도구](./storage-explorers.md)를 참조하세요.

> [!IMPORTANT]
> 스토리지 이벤트와 해당 시간 또는 분 메트릭 데이터가 기록되는 시간 사이에 지연이 있을 수 있습니다. 분 메트릭의 경우 데이터의 몇 분을 한 번에 작성할 수 있습니다. 이 문제로 인해 현재 분의 트랜잭션에 집계되는 이전 분의 트랜잭션이 발생할 수 있습니다. 이 문제가 발생하는 경우 알림 서비스에는 구성된 알림 간격에 대한 사용 가능한 모든 메트릭 데이터가 없을 수 있어서 예기치 않게 알림이 발생할 수 있습니다.
>

## <a name="billing-on-storage-metrics"></a>스토리지 메트릭에 대한 청구

메트릭용 테이블 엔터티 작성을 위한 쓰기 요청에는 모든 Azure Storage 작업에 적용되는 표준 요금이 부과됩니다.

클라이언트의 메트릭 데이터 읽기 요청도 표준 요금이 청구됩니다.

메트릭 테이블에 사용되는 용량도 청구됩니다. 다음 정보를 통해 메트릭 데이터 저장에 사용되는 용량을 예상할 수 있습니다.

-   서비스가 1시간마다 모든 서비스의 모든 API를 사용하는 경우, 서비스 수준 및 API 수준 요약을 사용하도록 설정했다면 메트릭 트랜잭션 테이블에 시간당 약 148KB의 데이터가 저장됩니다.
-   서비스가 매 1시간 이내에 서비스의 모든 API를 사용하는 경우, 서비스 수준 요약만 사용하도록 설정했다면 메트릭 트랜잭션 테이블에 시간당 약 12KB의 데이터가 저장됩니다.
-   로그를 옵트인한 경우 Blob의 용량 테이블에는 매일 두 개의 행이 추가됩니다. 이 시나리오는 매일 이 테이블의 크기가 최대 약 300바이트 증가함을 의미합니다.

## <a name="next-steps"></a>다음 단계

- [스토리지 계정 모니터링](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)
- [스토리지 분석 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema)
- [스토리지 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
- [스토리지 분석 로깅](storage-analytics-logging.md)
