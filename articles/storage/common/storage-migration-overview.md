---
title: Azure Storage 마이그레이션 가이드
description: 스토리지 마이그레이션 개요 가이드에서는 스토리지 마이그레이션에 대한 기본 지침을 설명합니다.
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: 2d02399daf6a7ea56c41f936544697f9df91a62c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601276"
---
# <a name="azure-storage-migration-overview"></a>Azure Storage 마이그레이션 개요

이 문서에서는 Azure로 스토리지를 마이그레이션하는 방법을 중점적으로 설명하고 다음과 같은 스토리지 마이그레이션 시나리오에 대한 지침을 제공합니다.

- 비정형 데이터(예: 파일 및 개체) 마이그레이션
- 디스크 및 SAN(저장 영역 네트워크) 같은 블록 기반 디바이스 마이그레이션

## <a name="migration-of-unstructured-data"></a>비정형 데이터 마이그레이션

비정형 데이터의 마이그레이션은 다음과 같은 시나리오를 포함합니다.

- NAS(네트워크 연결 스토리지)에서 Azure 파일 제품 중 하나로 파일 마이그레이션:
  - [Azure 파일](https://azure.microsoft.com/services/storage/files/)
  - [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)
  - [ISV(Independent Software Vendor) 솔루션](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md)
- 개체 스토리지 솔루션에서 Azure 개체 스토리지 플랫폼으로 개체 마이그레이션:
  - [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
  - [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

### <a name="migration-phases"></a>마이그레이션 단계

전체 마이그레이션은 검색, 평가 및 마이그레이션이라는 여러 단계로 구성됩니다.

| 검색 | 평가 | 마이그레이션 |
| --------- | ---------- | --------- |
| - 마이그레이션할 원본 검색 | - 적용 가능한 대상 서비스 평가 <br> - 기술 및 비용 고려 사항 | - 초기 마이그레이션 <br> - 다시 동기화 <br> - 최종 전환 |

#### <a name="discovery-phase"></a>검색 단계

검색 단계에서는 SMB 공유, NFS 내보내기 또는 개체 네임스페이스와 같이 마이그레이션해야 하는 모든 원본을 결정합니다. 이 단계를 수동으로 수행하거나 자동화된 도구를 사용할 수 있습니다.

#### <a name="assessment-phase"></a>평가 단계

평가 단계는 마이그레이션에 사용할 수 있는 옵션을 이해하는 데 중요합니다. 마이그레이션 중에 발생하는 위험을 줄이고 일반적인 문제를 방지하려면 다음 세 단계를 수행합니다.

| 평가 프로세스 단계                     | 옵션                                                                          |
|--------------------------------------------|----------------------------------------------------------------------------------|
| **대상 스토리지 서비스 선택**            | - Azure Blob Storage 및 Data Lake Storage<br>- Azure Files<br>- Azure NetApp Files<br>- ISV 솔루션 |
| **마이그레이션 방법 선택**                  | - 온라인<br>- 오프라인<br> - 둘의 조합                                  |
| **작업에 가장 적합한 마이그레이션 도구 선택** | - 상용 도구(Azure 및 ISV)<br> - 오픈 소스

평가 단계에 도움이 될 수 있는 여러 가지 ISV(상용) 도구가 있습니다. [비교 행렬](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)을 참조하세요.

##### <a name="choose-a-target-storage-service"></a>대상 스토리지 서비스 선택

대상 스토리지 서비스 선택은 데이터에 액세스하는 애플리케이션 또는 사용자에 따라 달라집니다. 올바른 선택은 기술적 측면과 재무적 측면 모두에 영향을 받습니다. 먼저 기술 평가를 수행하여 가능한 대상을 평가하고 요구 사항을 충족하는 서비스를 확인합니다. 다음으로, 재무 평가를 수행하여 최상의 선택을 결정합니다.

마이그레이션 대상 스토리지 서비스를 선택하는 데 도움이 되도록 각 서비스의 다음 측면을 평가합니다.

- 프로토콜 지원
- 성능 특징
- 대상 스토리지 서비스의 제한 사항

다음 다이어그램은 권장되는 Azure 파일 서비스를 안내해 주는 간소화된 의사 결정 트리입니다. 기본 Azure 서비스가 요구 사항을 충족하지 않는 경우, 요구 사항을 충족해 줄 다양한 [ISV(Independent Software Vendor) 솔루션](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md)이 있습니다.

기술 평가를 완료하고 적절한 대상을 선택한 후 비용 평가를 수행하여 가장 비용 효율적인 옵션을 결정합니다.

![올바른 파일 서비스 선택을 위한 기본 의사 결정 트리](./media/storage-migration-overview/files-decision-tree.png)

의사 결정 트리를 단순하게 유지하기 위해 대상 스토리지 서비스의 제한 사항은 다이어그램에 통합되어 있지 않습니다. 현재 제한 사항에 대한 자세한 내용을 확인하고 이에 따라 선택을 수정해야 하는지 여부를 확인하려면 다음을 참조하세요.

- [Storage 계정 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)
- [Blob Storage 제한 사항](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-blob-storage-limits)
- [Azure Files 확장성 및 성능 목표](../files/storage-files-scale-targets.md)
- [Azure NetApp Files 리소스 제한 사항](../../azure-netapp-files/azure-netapp-files-resource-limits.md)

서비스 사용 차단을 유발하는 제한 사항이 하나라도 있을 경우 Azure는 Azure Marketplace에 관련 솔루션을 제공하는 여러 스토리지 공급업체를 지원합니다. 파일 서비스를 제공하는 검증된 ISV 파트너에 대한 자세한 내용은 [기본 및 보조 스토리지에 대한 Azure Storage 파트너](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md)를 참조하세요.

##### <a name="select-the-migration-method"></a>마이그레이션 방법 선택

스토리지 마이그레이션을 위한 기본 마이그레이션 방법에는 두 가지가 있습니다.

- **온라인**. 온라인 방법은 데이터 마이그레이션에 네트워크를 사용합니다. 공용 인터넷 또는 [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) 중 하나를 사용할 수 있습니다. 서비스에 공용 엔드포인트가 없으면 공용 인터넷에서 VPN을 사용해야 합니다.
- **오프라인.** 오프라인 방법은 [Azure Data Box](https://azure.microsoft.com/services/databox/) 디바이스 중 하나를 사용합니다.

온라인 방법과 오프라인 방법 중 어느 방법을 사용할지는 사용 가능한 네트워크 대역폭에 따라 달라집니다. 필요한 타임라인 내에서 마이그레이션을 수행하는 데 충분한 네트워크 대역폭이 있는 경우 온라인 방법이 권장됩니다.

초기 대량 마이그레이션에는 오프라인 방법을, 변경 내용의 증분 마이그레이션에는 온라인 방법을 사용하는 등, 두 방법을 조합하여 사용할 수 있습니다. 두 방법을 동시에 사용하려면 높은 수준의 조정이 필요하므로 권장되지 않습니다. 두 방법을 모두 사용하도록 선택하는 경우 오프라인으로 마이그레이션된 데이터 세트에서 온라인으로 마이그레이션된 데이터 세트를 격리합니다.

여러 마이그레이션 방법 및 지침에 대한 자세한 내용은 [데이터 전송을 위한 Azure 솔루션 선택](./storage-choose-data-transfer-solution.md) 및 [Azure 파일 공유로 마이그레이션](../files/storage-files-migration-overview.md)을 참조하세요.

##### <a name="choose-the-best-migration-tool-for-the-job"></a>작업에 가장 적합한 마이그레이션 도구 선택

마이그레이션을 수행하는 데 사용할 수 있는 다양한 마이그레이션 도구가 있습니다. AzCopy, robocopy, xcopy 및 rsync와 같은 오픈 소스 도구도 있고, 상용 도구도 있습니다. 사용 가능한 상용 도구를 비교한 목록은 [비교 행렬](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)에서 볼 수 있습니다.

오픈 소스 도구는 소규모 마이그레이션에 적합합니다. Windows 파일 서버에서 Azure Files로 마이그레이션하는 경우 Azure Files 기본 기능으로 시작하고 [Azure 파일 동기화](/windows-server/manage/windows-admin-center/azure/azure-file-sync)를 사용하는 것이 좋습니다. 서로 다른 원본, 대용량 또는 대역폭 제한이나 감사 기능을 사용한 상세 보고와 같은 특별한 요구 사항으로 구성된 더 복잡한 마이그레이션은 상용 도구를 선택하는 것이 가장 좋습니다. 이러한 도구를 사용하면 마이그레이션을 더 쉽게 수행하고 위험을 크게 줄일 수 있습니다. 대부분의 상용 도구는 평가에 중요한 입력을 제공하는 검색을 수행할 수도 있습니다.

#### <a name="migration-phase"></a>마이그레이션 단계

마이그레이션 단계는 데이터 이동 및 마이그레이션을 수행하는 최종 마이그레이션 단계입니다. 일반적으로 보다 쉽게 전환하기 위해 마이그레이션 단계를 여러 번 실행합니다. 마이그레이션 프로세스는 다음 단계로 구성됩니다.

1. **초기 마이그레이션.** 초기 마이그레이션 단계는 원본에서 대상으로 모든 데이터를 마이그레이션합니다. 이 단계에서는 마이그레이션해야 하는 대량의 데이터를 마이그레이션합니다.
2. **다시 동기화.** 다시 동기화 작업은 초기 마이그레이션 단계 이후 변경된 모든 데이터를 마이그레이션합니다. 변경 내용이 많을 경우 이 단계를 여러 번 반복할 수 있습니다. 다시 동기화 작업을 여러 번 실행하는 이유는 최종 단계에 소요되는 시간을 줄이기 위해서입니다. 비활성 데이터와 변경 사항이 없는 데이터(예: 백업 또는 보관 데이터)의 경우 이 단계를 건너뛸 수 있습니다.
3. **최종 전환**. 최종 전환 단계에서는 활성 사용 데이터를 원본에서 대상으로 전환하고 원본을 사용 중지합니다.

비정형 데이터의 마이그레이션 기간은 여러 요소에 따라 달라집니다. 선택한 방법 외에 가장 중요한 요소는 데이터의 전체 크기 및 파일 크기 분포입니다. 총 데이터 세트가 커질수록 마이그레이션 시간이 길어집니다. 평균 파일 크기가 작아질수록 마이그레이션 시간이 길어집니다. 작은 파일이 많이 있는 경우, 전체 마이그레이션 시간을 줄이기 위해 더 큰 파일(예: .tar 또는 .zip 파일)에 보관하는 것이 좋습니다(해당하는 경우).

## <a name="migration-of-block-based-devices"></a>블록 기반 디바이스 마이그레이션

블록 기반 디바이스의 마이그레이션은 일반적으로 가상 머신 또는 물리적 호스트 마이그레이션의 일부로 수행됩니다. 마이그레이션 후까지 블록 스토리지 결정을 미루어야 한다는 것은 일반적인 오해입니다. 워크로드 요구 사항을 적절히 고려하여 이러한 결정을 미리 내리면 클라우드로 더욱 원활하게 마이그레이션할 수 있습니다.

마이그레이션할 워크로드 및 사용할 접근법에 대한 자세한 내용은 [Azure Disk Storage 설명서](../../virtual-machines/disks-types.md) 및 [Disk Storage 제품 페이지](https://azure.microsoft.com/services/storage/disks/#resources)의 리소스를 참조하세요. 요구 사항에 적합한 디스크 및 [디스크 버스팅](../../virtual-machines/disk-bursting.md)과 같은 최신 기능에 대해 알아볼 수 있습니다. 블록 기반 디바이스의 마이그레이션은 다음 두 가지 방법으로 수행할 수 있습니다.
- 기본 블록 기반 디바이스와 함께 전체 가상 머신을 마이그레이션하는 경우 [Azure Migrate](../../migrate/index.yml) 설명서를 참조하세요.
- 블록 기반 디바이스만 마이그레이션하고 더 복잡한 사용 사례의 경우 [Cirrus Migrate Cloud 를](../solution-integration/validated-partners/data-management/cirrus-data-migration-guide.md)사용합니다.

## <a name="see-also"></a>참조

- [데이터 전송에 사용할 Azure 솔루션 선택](./storage-choose-data-transfer-solution.md)
- [상용 마이그레이션 도구 비교](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)
- [Azure 파일 공유로 마이그레이션](../files/storage-files-migration-overview.md)
- [Azure용 WANdisco LiveData 플랫폼을 사용하여 Data Lake Storage로 마이그레이션](../blobs/migrate-gen2-wandisco-live-data-platform.md)
- [AzCopy를 사용하여 Azure Storage로 데이터 복사 또는 이동](./storage-use-azcopy-v10.md)
- [AzReplicate를 사용하여 Azure Blob Storage로 대량 데이터 세트 마이그레이션](/samples/azure/azreplicate/azreplicate/)
