---
title: 블록 Blob Storage 성능 계층
titleSuffix: Azure Storage
description: Azure 블록 Blob Storage에 대한 프리미엄 및 표준 성능 계층 간의 차이를 설명합니다.
author: normesta
ms.author: normesta
ms.date: 09/23/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 1e0ac8784a8a4e5077d600b2582bc7ce3e7d981b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129279187"
---
# <a name="performance-tiers-for-block-blob-storage"></a>블록 Blob Storage에 대한 성능 계층

기업이 성능이 중요한 클라우드 네이티브 애플리케이션을 배포할 때는 다양한 성능 수준에서 비용 효율적인 데이터 스토리지에 대한 옵션이 마련되어야 합니다.

Azure 블록 Blob 스토리지는 다음과 같이 두 가지 성능 계층을 제공합니다.

- **프리미엄**: 높은 트랜잭션 속도에 최적화되어 있으며 스토리지 대기 시간이 한 자릿수로 일관됨
- **표준**: 고용량 및 고처리량에 최적화됨

다음 고려 사항은 다른 성능 계층에 적용됩니다.

| Area | 표준 성능 | 프리미엄 성능 |
|--|--|--|
| 지역 가용성 | 모든 지역 | [선택 지역](https://azure.microsoft.com/global-infrastructure/services/?products=storage) |
| 지원되는 [스토리지 계정 형식](../common/storage-account-overview.md#types-of-storage-accounts) | 범용 v2, 범용 v1, 레거시 Blob | 프리미엄 블록 Blob |
| [고 처리량 블록 Blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) 지원 | 예, 4MiB PutBlock 또는 Putblock 크기를 초과합니다. | 예, 256KiB PutBlock 또는 Putblock 크기보다 큼 |
| 중복 | [스토리지 계정 유형](../common/storage-account-overview.md#types-of-storage-accounts)을 참조하세요. | 현재 LRS(로컬 중복 스토리지) 및 ZRS(영역 중복 스토리지)만 지원합니다.<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div> |

<div id="zone-redundant-storage"><sup>1</sup> ZRS(영역 중복 스토리지)는 프리미엄 블록 Blob Storage 계정의 일부 지역에서 사용할 수 있습니다.</div>

비용과 관련하여 프리미엄 성능은 트랜잭션 비율이 높은 애플리케이션에 최적화된 가격을 제공하여 이러한 워크로드에 대한 [총 스토리지 비용을 절감](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/)할 수 있도록 합니다.

## <a name="premium-performance"></a>프리미엄 성능

프리미엄 성능 블록 Blob 스토리지는 고성능 하드웨어를 통해 데이터를 사용할 수 있도록 합니다. 데이터는 짧은 대기 시간에 최적화된 SSD(반도체 드라이브)에 저장됩니다. SSD는 기존 하드 드라이브에 비해 더 높은 처리량을 제공합니다.

프리미엄 성능 스토리지는 빠르고 일관성 있는 응답 시간을 필요로 하는 워크로드에 적합합니다. 소규모의 많은 트랜잭션을 수행하는 작업에 가장 적합합니다. 워크로드의 예는 다음과 같습니다.

- **대화형 워크로드**. 해당 워크로드는 전자상거래 및 매핑 애플리케이션과 같이 즉각적인 업데이트 및 사용자 피드백을 필요로 합니다. 예를 들어 전자상거래 애플리케이션에서 자주 표시되지 않는 항목은 캐시되지 않을 가능성이 높습니다. 그러나 고객이 요청 시 즉시 표시되어야 합니다.

- **분석**. IoT 시나리오에서는 매초 소량의 다양한 쓰기 작업을 클라우드에 푸시할 수 있습니다. 대량 데이터를 수집하고 분석 목적으로 집계한 다음 거의 즉각적으로 삭제할 수 있습니다. 프리미엄 블록 Blob 스토리지의 고성능 수집 기능을 사용하면 해당 유형의 워크로드에 대한 효율성을 개선할 수 있습니다.

- **AI/ML(인공 지능/기계 학습)** AI/ML은 시각적 개체, 음성 및 텍스트와 같은 다양한 데이터 형식의 소비와 처리를 다룹니다. 워크로드의 고성능 컴퓨팅 형식은 데이터 분석에 대한 신속한 응답과 효율적인 수집 시간을 필요로 하는 대량의 데이터를 처리합니다.

- **데이터 변환**. 데이터를 일관성 있게 편집, 수정 및 변환해야 하는 프로세스에는 즉각적인 업데이트가 필요합니다. 데이터를 정확하게 표현하려면 해당 데이터의 소비자가 변경 내용이 즉각적으로 반영되는지 여부를 확인해야 합니다.

## <a name="standard-performance"></a>표준 성능

표준 성능은 가장 비용이 효율적인 방식으로 데이터를 저장하기 위해 다양한 [액세스 계층](access-tiers-overview.md)을 지원합니다. 대용량 데이터 집합에 대한 높은 용량과 처리량에 최적화되어 있습니다.

- **백업 및 재해 복구 데이터 세트** 표준 성능 스토리지는 비용 효율적인 계층을 제공하므로 단기 및 장기 재해 복구 데이터 집합, 보조 백업 및 규정 준수 데이터 보관에 대한 완벽한 사용 사례를 제공합니다.

- **미디어 콘텐츠**. 이미지 및 비디오는 처음 생성될 때와 저장될 때 자주 액세스되지만 이 콘텐츠 형식은 오래된 경우 자주 사용됩니다. 표준 성능 스토리지는 미디어 콘텐츠 요구 사항에 적합한 계층을 제공합니다.

- **대량 데이터 처리**. 이러한 종류의 작업은 일관된 짧은 대기 시간보다 비용이 효율적인 처리량이 높은 스토리지를 필요로 하기 때문에 표준 스토리지에 적합합니다. 대량 원시 데이터 집합은 처리를 위해 준비되고 궁극적으로는 궁극적 계층으로 마이그레이션됩니다.

## <a name="migrate-from-standard-to-premium"></a>표준에서 프리미엄으로 마이그레이션

프리미엄 성능에 맞춰 기존 표준 성능 스토리지 계정을 블록 Blob Storage 계정으로 변환할 수 없습니다. 프리미엄 성능 스토리지 계정으로 마이그레이션하려면 프리미엄 블록 Blob 계정을 만들고 데이터를 새 계정으로 마이그레이션해야 합니다. 자세한 내용은 [블록 Blob Storage 계정 만들기](../common/storage-account-create.md)를 참조하세요.

스토리지 계정 간에 Blob을 복사하려면 최신 버전의 [AzCopy](../common/storage-use-azcopy-v10.md#transfer-data) 명령줄 도구를 사용할 수 있습니다. Azure Data Factory와 같은 기타 도구는 데이터 이동 및 변환에도 사용할 수 있습니다.

## <a name="blob-lifecycle-management"></a>Blob 수명 주기 관리

Blob Storage 수명 주기 관리는 다음과 같은 다양한 규칙 기반 정책을 제공합니다.

- **프리미엄**: 수명 주기가 끝나면 데이터가 만료됩니다.
- **표준**: 데이터를 최상의 액세스 계층으로 전환하고 수명 주기가 끝나면 데이터가 만료됩니다.

자세한 내용은 [Azure Blob Storage 수명 주기 관리](./lifecycle-management-overview.md)를 참조하세요.

프리미엄 블록 Blob Storage 계정에 저장된 데이터는 핫, 쿨 및 보관 계층 간에 이동할 수 없습니다. 그러나 블록 Blob Storage 계정에서 *다른* 계정의 핫 액세스 계층으로 Blob을 복사할 수 있습니다. 데이터를 다른 계정에 복사하려면 [URL에서 블록 넣기](/rest/api/storageservices/put-block-from-url) API 또는 [AzCopy v10](../common/storage-use-azcopy-v10.md)을 사용합니다. **URL에서 블록 넣기** API는 서버의 데이터를 동기적으로 복사합니다. 모든 데이터가 원본 서버 위치에서 대상 위치로 이동된 후에만 호출이 완료됩니다.

## <a name="next-steps"></a>다음 단계

GPv2 및 Blob Storage 계정에서 핫, 쿨 및 보관을 평가합니다.

- [보관 계층에서 Blob 데이터 리하이드레이션에 대해 자세히 알아보기](archive-rehydrate-overview.md)
- [Azure Storage 메트릭을 활성화하여 현재 Storage 계정의 사용 현황 평가](./monitor-blob-storage.md)
- [지역별 Blob Storage 및 GPv2 계정에서 핫, 쿨 및 보관 가격 책정 확인](https://azure.microsoft.com/pricing/details/storage/)
- [데이터 전송 가격 확인](https://azure.microsoft.com/pricing/details/data-transfers/)
