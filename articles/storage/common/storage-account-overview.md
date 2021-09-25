---
title: Storage 계정 개요
titleSuffix: Azure Storage
description: Azure Storage의 다양한 스토리지 계정 유형에 대해 알아봅니다. 계정 이름, 성능 계층, 액세스 계층, 중복, 암호화, 엔드포인트 등을 살펴봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: cb19372e5b97a9f508367366faa9ae49d0d11261
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596964"
---
# <a name="storage-account-overview"></a>Storage 계정 개요

Azure Storage 계정에는 Blob, 파일 공유, 큐, 테이블, 디스크 등, 모든 Azure Storage 데이터 개체가 포함됩니다. 스토리지 계정은 HTTP 또는 HTTPS를 통해 전 세계 어디에서나 액세스할 수 있는 Azure Storage 데이터에 고유한 네임스페이스를 제공합니다. 스토리지 계정의 데이터는 지속적이고 가용성이 높으며 안전하고 대규모로 확장 가능합니다.

Azure Storage 계정을 만드는 방법은 [스토리지 계정 만들기](storage-account-create.md)를 참조하세요.

## <a name="types-of-storage-accounts"></a>스토리지 계정 유형

Azure Storage는 여러 유형의 스토리지 계정을 제공합니다. 각각의 유형은 서로 다른 기능을 지원하며 고유의 가격 책정 모델이 있습니다. 스토리지 계정을 만들기 전에 애플리케이션에 적합한 계정 유형을 결정하는 데 이러한 차이점을 고려합니다.

다음 표에서는 대부분의 시나리오에서 Microsoft에서 권장하는 스토리지 계정 유형에 대해 설명합니다. 이들 모두는 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 배포 모델을 사용합니다.

| 스토리지 계정의 유형 | 지원되는 스토리지 서비스 | 중복 옵션 | 사용량 |
|--|--|--|--|
| 표준 범용 v2 | Blob(Data Lake Storage <sup>1</sup> 포함), 큐 및 Table Storage, Azure Files  | LRS/GRS/RA-GRS<br /><br />ZRS/GZRS/RA-GZRS<sup>2</sup> | Blob, 파일 공유, 큐 및 테이블에 대한 Standard Storage 계정 유형 대부분의 시나리오에 대해 Azure Storage를 사용하는 것이 좋습니다. Azure Files NFS 파일 공유에 대한 지원을 원하는 경우 프리미엄 파일 공유 계정 유형을 사용합니다. |
| 프리미엄 블록 Blob <sup>3</sup> | Blob 스토리지(Data Lake Storage <sup>1</sup> 포함) | LRS<br /><br />ZRS<sup>2</sup> | 블록 Blob 및 추가 Blob에 대한 프리미엄 스토리지 계정 유형입니다. 트랜잭션 속도가 높은 시나리오 또는 더 작은 개체를 사용하거나 지속적으로 짧은 스토리지 대기 시간이 필요한 시나리오에 추천됩니다. [예시 워크로드에 대해 자세히 알아봅니다.](../blobs/storage-blob-performance-tiers.md#premium-performance) |
| 프리미엄 파일 공유<sup>3</sup> | Azure Files | LRS<br /><br />ZRS<sup>2</sup> | 파일 공유 전용 프리미엄 스토리지 계정 유형입니다. 엔터프라이즈 또는 고성능 규모의 애플리케이션에 추천됩니다. SMB 및 NFS 파일 공유를 모두 지원하는 스토리지 계정을 원하는 경우 이 계정 유형을 사용합니다. |
| 프리미엄 페이지 Blob<sup>3</sup> | 페이지 Blob만 해당 | LRS | 페이지 Blob에 대한 프리미엄 스토리지 계정 유형입니다. [페이지 Blob 및 샘플 사용 사례에 대해 자세히 알아봅니다.](../blobs/storage-blob-pageblob-overview.md) |

<sup>1</sup> Data Lake Storage는 Azure Blob 스토리지를 기반으로 하는 빅 데이터 분석 전용 기능 세트입니다. 자세한 내용은 [Data Lake Storage Gen2 소개](../blobs/data-lake-storage-introduction.md) 및 [Data Lake Storage Gen2와 함께 사용할 스토리지 계정 만들기](../blobs/create-data-lake-storage-account.md)를 참조하세요.

<sup>2</sup> ZRS(영역 중복 스토리지) 및 GZRS/RA-GZRS(지역 중복 스토리지)는 특정 지역의 표준 범용 v2, 프리미엄 블록 Blob 및 프리미엄 파일 공유 계정에서만 사용할 수 있습니다. 자세한 내용은 [Azure Storage 중복성](storage-redundancy.md)을 참조하세요.

프리미엄 성능 계층의 <sup>3</sup> 스토리지 계정은 짧은 대기 시간과 높은 처리량을 위해 SSD(SolID-State Drive)를 사용합니다.

레거시 스토리지 계정도 지원됩니다. 자세한 내용은 [레거시 스토리지 계정 유형](#legacy-storage-account-types)을 참조하세요.

## <a name="storage-account-endpoints"></a>Storage 계정 엔드포인트

스토리지 계정은 데이터에 대해 Azure에서 고유의 네임스페이스를 제공합니다. Azure Storage 계정에 저장한 모든 개체는 고유 계정 이름을 포함하는 주소를 갖습니다. 계정 이름과 Azure Storage 서비스 엔드포인트의 조합이 스토리지 계정의 엔드포인트가 됩니다.

스토리지 계정의 이름을 지정할 때는 다음 규칙에 유의하세요.

- Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.
- 스토리지 계정 이름은 Azure 내에서 고유해야 합니다. 두 개의 스토리지 계정이 같은 이름을 사용할 수 없습니다.

다음 표에서는 각 Azure Storage 서비스에 대한 엔드포인트의 형식을 나열합니다.

| 스토리지 서비스 | 엔드포인트 |
|--|--|
| Blob 스토리지 | `https://<storage-account>.blob.core.windows.net` |
| Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure 파일 | `https://<storage-account>.file.core.windows.net` |
| Queue Storage | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

스토리지 계정의 개체 위치를 엔드포인트에 추가하여 스토리지 계정의 개체에 액세스하기 위한 URL을 작성합니다. 예를 들어 Blob의 URL은 다음과 유사합니다.

`http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*`

Blob에 사용자 지정 도메인 이름을 사용하도록 스토리지 계정을 구성할 수도 있습니다. 자세한 내용은 [Azure Storage 계정에 대한 사용자 지정 도메인 이름 구성](../blobs/storage-custom-domain-name.md)을 참조하세요.

## <a name="migrate-a-storage-account"></a>스토리지 계정 마이그레이션

다음 표에는 스토리지 계정을 이동, 업그레이드 또는 마이그레이션하는 방법에 대한 참고 자료가 요약되어 있습니다.

| 마이그레이션 시나리오 | 세부 정보 |
|--|--|
| 스토리지 계정을 다른 구독으로 이동 | Azure Resource Manager는 리소스를 다른 구독으로 이동하는 옵션을 제공합니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요. |
| 스토리지 계정을 다른 리소스 그룹으로 이동 | Azure Resource Manager는 리소스를 다른 리소스 그룹으로 이동하는 옵션을 제공합니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요. |
| 스토리지 계정을 다른 지역으로 이동 | 스토리지 계정을 이동하려면 다른 지역에 스토리지 계정의 복사본을 만듭니다. 그런 다음, AzCopy 또는 원하는 다른 도구를 사용하여 해당 계정으로 데이터를 이동합니다. 자세한 내용은 [Azure Storage 계정을 다른 지역으로 이동](storage-account-move.md)을 참조하세요. |
| 범용 v2 스토리지 계정으로 업그레이드 | 범용 v1 스토리지 계정 또는 Blob Storage 계정을 범용 v2 계정으로 업그레이드할 수 있습니다. 이 동작은 실행 취소할 수 없습니다. 자세한 내용은 [범용 v2 스토리지 계정으로 업그레이드](storage-account-upgrade.md)를 참조하세요. |
| 클래식 스토리지 계정을 Azure Resource Manager로 마이그레이션 | Azure Resource Manager 배포 모델은 기능, 확장성 및 보안 측면에서 클래식 배포 모델보다 우수합니다. 클래식 스토리지 계정을 Azure Resource Manager로 마이그레이션하는 방법에 대한 자세한 내용은 [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts)에서 "스토리지 계정 마이그레이션" 섹션을 참조하세요. |

## <a name="transfer-data-into-a-storage-account"></a>스토리지 계정으로 데이터 전송

Microsoft는 온-프레미스 스토리지 디바이스나 타사 클라우드 스토리지 공급자에서 데이터를 가져오기 위한 서비스와 유틸리티를 제공합니다. 사용하는 솔루션은 전송 데이터의 크기에 따라 다릅니다. 자세한 내용은 [Azure Storage 마이그레이션 개요](storage-migration-overview.md)를 참조하세요.

## <a name="storage-account-encryption"></a>스토리지 계정 암호화

스토리지 계정의 모든 데이터는 서비스 쪽에서 자동으로 암호화됩니다. 암호화 및 키 관리에 대한 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](storage-service-encryption.md)를 참조하세요.

## <a name="storage-account-billing"></a>Storage 계정 사용 비용

스토리지 계정 사용량에 따라 Azure Storage 요금이 청구됩니다. 스토리지 계정의 모든 개체는 그룹으로 합산 청구됩니다. 스토리지 비용은 다음 요인에 따라 계산됩니다.

- **지역** 이란 계정이 기초하는 지리적 위치를 의미합니다.
- **계정 유형** 은 사용 중인 스토리지 계정 유형을 나타냅니다.
- **액세스 계층** 은 범용 v2 또는 Blob 스토리지 계정에 지정한 데이터 사용 패턴을 나타냅니다.
- **용량** 은 데이터를 저장하는 데 사용하는 스토리지 계정 할당량이 어느 정도인지를 나타냅니다.
- **중복도** 에 따라 한 번에 유지 관리되는 데이터의 복사본 수 및 위치가 결정됩니다.
- **트랜잭션** 은 Azure Storage에 대한 모든 읽기 및 쓰기 작업을 나타냅니다.
- **데이터 송신** 은 Azure 지역 외부에서 전송되는 데이터를 나타냅니다. 스토리지 계정의 데이터에 동일한 지역에서 실행되지 않는 애플리케이션이 액세스하는 경우 데이터 송신 요금이 부과됩니다. 리소스 그룹을 기반으로 동일한 지역의 데이터 및 서비스를 그룹화하여 송신 요금을 제한하는 방법에 대한 자세한 내용은 [Azure 리소스 그룹이란?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)을 참조하세요.

[Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/) 페이지는 계정 유형, 스토리지 용량, 복제 및 트랜잭션에 대한 자세한 가격 정보를 제공합니다. [데이터 전송 가격 정보](https://azure.microsoft.com/pricing/details/data-transfers/) 에서는 데이터 송신에 대한 자세한 가격 정보를 제공합니다. [Azure Storage 가격 계산기](https://azure.microsoft.com/pricing/calculator/?scenario=data-management)를 사용하여 비용을 예측할 수 있습니다.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="legacy-storage-account-types"></a>레거시 스토리지 계정 유형

다음 표에서는 레거시 스토리지 계정 유형에 대해 설명합니다. 이러한 계정 유형은 Microsoft에서 권장되지 않지만 특정 시나리오에서 사용될 수 있습니다.

| 레거시 스토리지 계정 유형 | 지원되는 스토리지 서비스 | 중복 옵션 | 배포 모델 | 사용량 |
|--|--|--|--|--|
| 표준 범용 v1 | Blob, 큐 및 Table Storage, Azure Files | LRS/GRS/RA-GRS | Resource Manager, 클래식 | 범용 v1 계정에는 최신 기능이나 최저 기가바이트당 가격이 없을 수 있습니다. 다음 시나리오에 사용을 고려합니다.<br /><ul><li>애플리케이션에 Azure [클래식 배포 모델](../../azure-portal/supportability/classic-deployment-model-quota-increase-requests.md)이 필요합니다.</li><li>애플리케이션은 트랜잭션 집약적이거나 상당한 지역 복제 대역폭을 사용하지만 대용량이 필요하지는 않습니다. 이 경우 범용 v1이 가장 경제적인 선택이 될 수 있습니다.</li><li>2014-02-14 이전 버전인 Azure Storage REST API 또는 4.x보다 낮은 버전의 클라이언트 라이브러리를 사용하며 애플리케이션을 업그레이드할 수 없습니다.</li></ul> |
| 표준 Blob Storage | Blob Storage(블록 Blob 및 추가 Blob만 해당) | LRS/GRS/RA-GRS | 리소스 관리자 | 가능하면 대신 표준 범용 v2 계정을 사용하는 것이 좋습니다. |

## <a name="next-steps"></a>다음 단계

- [스토리지 계정을 만드는](storage-account-create.md)
- [범용 v2 스토리지 계정으로 업그레이드](storage-account-upgrade.md)
- [삭제된 스토리지 계정 복구](storage-account-recover.md)
