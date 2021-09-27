---
title: Azure Data Lake Storage Gen1용 재해 복구 지침 | Microsoft Docs
description: Azure Data Lake Storage Gen1의 로컬 중복 스토리지를 벗어난 지역 전체 가동 중단 또는 실수로 인한 삭제로부터 데이터를 추가로 보호하는 방법에 대해 알아봅니다.
author: normesta
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: normesta
ms.openlocfilehash: 59c2ff76f62e64e193b51ea479c04f8897c27717
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599566"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Data Lake Storage Gen1의 고가용성 및 재해 복구 지침

Data Lake Storage Gen1에서는 LRS(로컬 중복 스토리지)를 제공합니다. 그러므로 데이터 센터 내에서 일시적인 하드웨어 장애가 발생하는 경우 자동화된 복제본을 통해 Data Lake Storage Gen1 계정의 데이터를 복원할 수 있습니다. 이러한 방식을 통해 Data Lake Storage Gen1 SLA를 충족하는 고가용성과 내구성이 보장됩니다. 이 문서에서는 드물게 발생하는 전체 지역 가동 중단 또는 삭제 실수로부터 데이터를 보호하는 방법에 대한 지침을 제공합니다.

## <a name="disaster-recovery-guidance"></a>재해 복구 지침

재해 복구 계획을 준비하는 것이 중요합니다. 이 문서의 정보와 이러한 추가 리소스를 검토하여 자체 계획을 직접 만들 수 있습니다.

* [Azure 애플리케이션에 대한 재해 복구 및 고가용성](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Azure 복구력 기술 지침](/azure/architecture/framework/resiliency/app-design)

### <a name="best-practice-recommendations"></a>모범 사례 권장 사항

재해 복구 계획의 요구에 맞는 빈도로 다른 지역의 다른 Data Lake Storage Gen1 계정에 중요한 데이터를 복사하는 것이 좋습니다. [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) 또는 [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)를 포함하여 데이터를 복사하는 다양한 메서드가 있습니다. Azure Data Factory는 반복적으로 데이터 이동 파이프라인을 만들고 배포하기 위한 유용한 서비스입니다.

지역 가동 중단이 발생하는 경우 데이터가 복사된 지역에 있는 데이터에 액세스할 수 있습니다. [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/)를 모니터링하여 전세계의 Azure 서비스 상태를 확인할 수 있습니다.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>데이터 손상 또는 삭제 실수 복구 지침

Data Lake Storage Gen1에서 자동화된 복제본을 통해 데이터 복원 기능을 제공하기는 하지만, 애플리케이션이나 개발자/사용자가 데이터를 손상시키거나 실제로 삭제하는 행위를 방지할 수는 없습니다.

실수로 데이터를 삭제하지 않으려면 먼저 Data Lake Storage Gen1 계정에 대해 올바른 액세스 정책을 설정하는 것이 좋습니다. 이 과정에서는 [Azure 리소스 잠금](../azure-resource-manager/management/lock-resources.md)을 적용하여 중요한 리소스를 잠그고, 사용 가능한 [Data Lake Storage Gen1 보안 기능](data-lake-store-security-overview.md)을 통해 계정 및 파일 수준 액세스 제어를 적용합니다. 그리고 다른 Data Lake Storage Gen1 계정, 폴더 또는 Azure 구독에서 [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) 또는 [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)를 사용하여 중요한 데이터의 복사본을 정기적으로 만드는 것이 좋습니다. 데이터 손상 또는 삭제 인시던트로부터 복구하는 데 사용할 수 있습니다. Azure Data Factory는 반복적으로 데이터 이동 파이프라인을 만들고 배포하기 위한 유용한 서비스입니다.

또한 Data Lake Storage Gen1 계정에 [진단 로깅](data-lake-store-diagnostic-logs.md)을 사용하도록 설정하여 데이터 액세스 감사 내역을 수집할 수 있습니다. 감사 내역은 파일을 삭제하거나 업데이트한 사용자에 대한 정보를 제공합니다.

## <a name="next-steps"></a>다음 단계

* [Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)
* [Data Lake Storage Gen1의 데이터 보호](data-lake-store-secure-data.md)
