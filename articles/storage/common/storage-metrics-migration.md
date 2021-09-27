---
title: 스토리지 분석 메트릭에서 Azure Monitor 메트릭으로 이동 | Microsoft Docs
description: 스토리지 분석 메트릭(클래식 메트릭)에서 Azure Monitor의 메트릭으로 전환하는 방법을 알아봅니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 77097d0d59da65acd71b42eef58a3f2aa9b3c85b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587426"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>Azure Monitor의 메트릭으로 전환

**2023년 8월 31일** 에 클래식 메트릭이라고도 하는 스토리지 분석 메트릭이 사용 중지됩니다. 자세한 내용은 [공식 공지](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/)를 참조하세요. 클래식 메트릭을 사용하는 경우 해당 날짜 이전의 Azure Monitor의 메트릭으로 전환해야 합니다. 이 문서는 전환을 수행하는 데 도움이 됩니다.

## <a name="steps-to-complete-the-transition"></a>전환을 완료하기 위한 단계

Azure Monitor의 메트릭으로 전환하려면 다음 방법을 사용하는 것이 좋습니다.

1. 클래식 메트릭과 Azure Monitor 메트릭의 몇 가지 [주요 차이점](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor)에 대해 알아봅니다.

2. 현재 사용 중인 클래식 메트릭 목록을 컴파일합니다.

3. [Azure Monitor의 어느 메트릭](#metrics-mapping-between-old-metrics-and-new-metrics)이 현재 사용하는 메트릭과 동일한 데이터를 제공하는지 식별합니다.

4. [차트](/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal) 또는 [대시보드](/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal)를 만들어 메트릭 데이터를 봅니다.

   > [!NOTE]
   > Azure Monitor의 메트릭은 기본적으로 사용하도록 설정되어 있으므로 메트릭 캡처를 시작하기 위해 수행해야 하는 작업은 없습니다. 다만 이러한 메트릭을 보기 위해 차트 또는 대시보드를 만들어야 합니다.

5. 클래식 스토리지 메트릭을 기반으로 하는 경고 규칙을 만들었다면 Azure Monitor의 메트릭을 기반으로 하는 [경고 규칙을 만듭니다](../../azure-monitor/alerts/alerts-overview.md).

6. Azure Monitor의 모든 메트릭을 볼 수 있게 되면 클래식 로깅을 끌 수 있습니다.

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>클래식 메트릭과 Azure Monitor의 메트릭 비교

이 섹션에서는 이러한 두 메트릭 플랫폼 간의 몇 가지 주요 차이점을 설명합니다.

주요 차이점은 메트릭을 관리하는 방법에 있습니다. 클래식 메트릭은 Azure Storage에서 관리되지만 Azure Monitor의 메트릭은 Azure Monitor에서 관리됩니다. 클래식 메트릭의 경우 Azure Storage가 메트릭 값을 수집하고 집계한 후 스토리지 계정에 있는 테이블에 저장합니다. Azure Monitor 메트릭의 경우 Azure Storage가 메트릭 데이터를 Azure Monitor 백 엔드로 보냅니다. Azure Monitor는 Azure Portal의 데이터뿐 아니라 수집되는 데이터를 포함하는 통합 모니터링 환경을 제공합니다.

클래식 메트릭은 Azure 스토리지 계정에 전송되고 저장됩니다. Azure Monitor 메트릭은 여러 위치로 전송될 수 있습니다. 스토리지 계정은 이러한 위치 중 하나일 수 있지만 필수는 아닙니다.

메트릭이 지원하는 경우 클래식 메트릭은 Azure Blob 스토리지의 **용량** 메트릭만 제공합니다. Azure Monitor의 메트릭은 Blob, Table, File, Queue 및 Premium Storage의 용량 메트릭을 제공합니다. 클래식 메트릭은 Blob, Table, Azure File 및 Queue Storage에 대한 **트랜잭션** 메트릭을 제공합니다. Azure Monitor의 메트릭은 이 목록에 Premium Storage를 추가합니다.

계정의 활동이 메트릭을 트리거하지 않는 경우 클래식 메트릭은 해당 메트릭에 대해 0 값을 표시합니다. Azure Monitor의 메트릭은 데이터를 완전히 생략하므로 보고서가 더 깨끗해집니다. 예를 들어 클래식 메트릭의 경우 서버 시간 초과 오류가 보고되지 않으면 메트릭 테이블의 `ServerTimeoutError` 값이 0으로 설정됩니다. `ResponseType` 차원이 `ServerTimeoutError`와 동일한 `Transactions` 메트릭의 값을 쿼리하면 Azure Monitor는 아무 데이터도 반환하지 않습니다.

Azure Monitor의 메트릭에 대한 자세한 내용은 [Azure Monitor의 메트릭](../../azure-monitor/essentials/data-platform-metrics.md)을 참조하세요.

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>클래식 메트릭을 Azure Monitor의 메트릭에 매핑

 다음 표를 사용하여 Azure Monitor의 어느 메트릭이 현재 사용하는 메트릭과 동일한 데이터를 제공하는지 식별하세요.

**용량 메트릭**

| 클래식 메트릭 | Azure Monitor의 메트릭 |
| ------------------- | ----------------- |
| `Capacity`            | `BlobType` 차원이 `BlockBlob` 또는 `PageBlob`와 동일한 `BlobCapacity` |
| `ObjectCount`        | `BlobType` 차원이 `BlockBlob` 또는 `PageBlob`와 동일한 `BlobCount` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> 클래식 메트릭으로 사용할 수 없는 몇 가지 새로운 용량 메트릭도 있습니다. 전체 목록을 보려면 [메트릭](../blobs/monitor-blob-storage-reference.md#metrics)을 참조하세요.

**트랜잭션 메트릭**

| 클래식 메트릭 | Azure Monitor의 메트릭 |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | `ResponseType` 차원이 `AuthorizationError`와 동일하고 `Authentication` 차원이 `Anonymous`와 동일한 트랜잭션 |
| `AnonymousClientOtherError` | `ResponseType` 차원이 `ClientOtherError`와 동일하고 `Authentication` 차원이 `Anonymous`와 동일한 트랜잭션 |
| `AnonymousClientTimeoutError` | `ResponseType` 차원이 `ClientTimeoutError`와 동일하고 `Authentication` 차원이 `Anonymous`와 동일한 트랜잭션 |
| `AnonymousNetworkError` | `ResponseType` 차원이 `NetworkError`와 동일하고 `Authentication` 차원이 `Anonymous`와 동일한 트랜잭션 |
| `AnonymousServerOtherError` | `ResponseType` 차원이 `ServerOtherError`와 동일하고 `Authentication` 차원이 `Anonymous`와 동일한 트랜잭션 |
| `AnonymousServerTimeoutError` | `ResponseType` 차원이 `ServerTimeoutError`와 동일하고 `Authentication` 차원이 `Anonymous`와 동일한 트랜잭션 |
| `AnonymousSuccess` | `ResponseType` 차원이 `Success`와 동일하고 `Authentication` 차원이 `Anonymous`와 동일한 트랜잭션 |
| `AnonymousThrottlingError` | `ResponseType` 차원이 `ClientThrottlingError` 또는 `ServerBusyError`와 동일하고 `Authentication` 차원이 `Anonymous`와 동일한 트랜잭션 |
| `AuthorizationError` | `ResponseType` 차원이 `AuthorizationError`와 동일한 트랜잭션 |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | `ResponseType` 차원이 `ClientOtherError`와 동일한 트랜잭션 |
| `ClientTimeoutError` | `ResponseType` 차원이 `ClientTimeoutError`와 동일한 트랜잭션 |
| `NetworkError` | `ResponseType` 차원이 `NetworkError`와 동일한 트랜잭션 |
| `PercentAuthorizationError` | `ResponseType` 차원이 `AuthorizationError`와 동일한 트랜잭션 |
| `PercentClientOtherError` | `ResponseType` 차원이 `ClientOtherError`와 동일한 트랜잭션 |
| `PercentNetworkError` | `ResponseType` 차원이 `NetworkError`와 동일한 트랜잭션 |
| `PercentServerOtherError` | `ResponseType` 차원이 `ServerOtherError`와 동일한 트랜잭션 |
| `PercentSuccess` | `ResponseType` 차원이 `Success`와 동일한 트랜잭션 |
| `PercentThrottlingError` | `ResponseType` 차원이 `ClientThrottlingError` 또는 `ServerBusyError`와 동일한 트랜잭션 |
| `PercentTimeoutError` | `ResponseType` 차원이 `ServerTimeoutError`와 동일하거나 `ResponseType`이 `ClientTimeoutError`와 동일한 트랜잭션 |
| `SASAuthorizationError` | `ResponseType` 차원이 `AuthorizationError`와 동일하고 `Authentication` 차원이 `SAS`와 동일한 트랜잭션 |
| `SASClientOtherError` | `ResponseType` 차원이 `ClientOtherError`와 동일하고 `Authentication` 차원이 `SAS`와 동일한 트랜잭션 |
| `SASClientTimeoutError` | `ResponseType` 차원이 `ClientTimeoutError`와 동일하고 `Authentication` 차원이 `SAS`와 동일한 트랜잭션 |
| `SASNetworkError` | `ResponseType` 차원이 `NetworkError`와 동일하고 `Authentication` 차원이 `SAS`와 동일한 트랜잭션 |
| `SASServerOtherError` | `ResponseType` 차원이 `ServerOtherError`와 동일하고 `Authentication` 차원이 `SAS`와 동일한 트랜잭션 |
| `SASServerTimeoutError` | `ResponseType` 차원이 `ServerTimeoutError`와 동일하고 `Authentication` 차원이 `SAS`와 동일한 트랜잭션 |
| `SASSuccess` | `ResponseType` 차원이 `Success`와 동일하고 `Authentication` 차원이 `SAS`와 동일한 트랜잭션 |
| `SASThrottlingError` | `ResponseType` 차원이 `ClientThrottlingError` 또는 `ServerBusyError`와 동일하고 `Authentication` 차원이 `SAS`와 동일한 트랜잭션 |
| `ServerOtherError` | `ResponseType` 차원이 `ServerOtherError`와 동일한 트랜잭션 |
| `ServerTimeoutError` | `ResponseType` 차원이 `ServerTimeoutError`와 동일한 트랜잭션 |
| `Success` | `ResponseType` 차원이 `Success`와 동일한 트랜잭션 |
| `ThrottlingError` | `ResponseType` 차원이 `ClientThrottlingError` 또는 `ServerBusyError`와 동일한 `Transactions`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>다음 단계

- [Azure Monitor](../../azure-monitor/overview.md)
