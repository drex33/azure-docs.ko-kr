---
title: Azure 파일 동기화로 StorSimple 8000 시리즈 마이그레이션
description: StorSimple 8100 또는 8600 어플라이언스를 Azure 파일 동기화로 마이그레이션하는 방법을 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/22/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 9bb33a10314460462cc32838227cadd3480cf362
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022701"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Azure 파일 동기화로 StorSimple 8100 및 8600 마이그레이션

StorSimple 8000 시리즈는 8100 또는 8600 물리적 온-프레미스 어플라이언스와 해당 클라우드 서비스 구성 요소로 표시됩니다. StorSimple 8010 및 8020 가상 어플라이언스도 이 마이그레이션 가이드에서 다룹니다. 선택적 Azure 파일 동기화 사용하여 이러한 어플라이언스 중 하나에서 Azure 파일 공유로 데이터를 마이그레이션할 수 있습니다. Azure 파일 동기화 StorSimple 온-프레미스 기능을 대체하는 기본 및 전략적 장기 Azure 서비스입니다.

StorSimple 8000 시리즈는 2022년 12월에 [수명 종료](/lifecycle/products/azure-storsimple-8000-series)됩니다. 따라서 최대한 신속하게 마이그레이션 계획을 시작해야 합니다. 이 문서에서는 Azure 파일 동기화로 성공적으로 마이그레이션하기 위해 필요한 배경 지식과 마이그레이션 단계를 제공합니다.

## <a name="phase-1-prepare-for-migration"></a>1단계: 마이그레이션 준비

이 섹션에는 StorSimple 볼륨에서 Azure 파일 공유로 마이그레이션을 시작할 때 수행해야 하는 단계가 포함되어 있습니다.

### <a name="inventory"></a>재고

마이그레이션 계획을 시작할 때 가장 먼저 마이그레이션할 모든 StorSimple 어플라이언스와 볼륨을 파악합니다. 파악이 끝나면 최적의 마이그레이션 경로를 결정할 수 있습니다.

* StorSimple 물리적 어플라이언스(8000 시리즈)는 이 마이그레이션 가이드를 사용합니다.
* 가상 어플라이언스 [StorSimple 1200 시리즈는 다른 마이그레이션 가이드를 사용](storage-files-migration-storsimple-1200.md)합니다.

### <a name="migration-cost-summary"></a>마이그레이션 비용 요약

StorSimple Data Manager 리소스의 마이그레이션 작업을 통해 StorSimple 볼륨에서 Azure 파일 공유로 마이그레이션하는 작업은 무료입니다. 마이그레이션 도중 및 마이그레이션 후에 다른 비용이 발생할 수 있습니다.

* **네트워크 송신:** StorSimple 파일은 특정 Azure 지역 내의 스토리지에 있습니다. Azure 파일 공유를 프로비저닝하고 동일한 Azure 지역에 있는 스토리지 계정으로 마이그레이션하는 경우 송신 비용이 발생하지 않습니다. 이 마이그레이션의 일부로 다른 지역의 스토리지 계정으로 파일을 이동할 수 있습니다. 이 경우에는 송신 비용이 적용됩니다.
* **Azure 파일 공유 트랜잭션:** 파일이 Azure 파일 공유에 복사되는 경우(마이그레이션 과정에서 또는 마이그레이션 이외의 과정에서) 파일 및 메타데이터가 작성될 때 트랜잭션 비용이 적용됩니다. 마이그레이션 중에 트랜잭션 최적화 계층에서 Azure 파일 공유를 시작하고, 마이그레이션이 완료된 후 원하는 계층으로 전환하는 것이 가장 좋은 방법입니다. 자세한 내용은 나중에 적절한 시점에서 설명하겠습니다.
* **Azure 파일 공유 계층 변경:** Azure 파일 공유 계층을 변경하면 트랜잭션 비용이 발생합니다. 대부분의 경우 위의 조언을 따르는 것이 비용 측면에서 보다 효율적입니다.
* **스토리지 비용:** 마이그레이션 과정에서 Azure 파일 공유에 파일이 복사되기 시작하면 Azure Files 스토리지가 사용되고 비용이 청구됩니다. 마이그레이션된 백업은 [Azure 파일 공유 스냅샷](storage-snapshots-files.md)이 됩니다. 파일 공유 스냅샷은 포함된 내용 중 다른 부분에만 스토리지 용량을 사용합니다.
* **StorSimple:** StorSimple 디바이스 및 스토리지 계정의 프로비전을 해제할 때까지 스토리지, 백업 및 어플라이언스에 대한 StorSimple 비용이 계속 발생합니다.

### <a name="direct-share-access-vs-azure-file-sync"></a>직접 공유 액세스와 Azure 파일 동기화의 차이점

Azure 파일 공유는 파일 서비스 배포를 구성할 수 있는 완전히 새로운 기회를 제공합니다. Azure 파일 공유는 친숙한 Kerberos 인증 및 기존 NTFS 권한(파일 및 폴더 ACL)이 기본적으로 작동하는 SMB 프로토콜을 통해 사용자가 직접 액세스하도록 설정할 수 있는 클라우드의 SMB 공유일 뿐입니다. [Azure 파일 공유에 대한 ID 기반 액세스](storage-files-active-directory-overview.md)에 대해 자세히 알아보세요.

직접 액세스를 대체하는 방법은 [Azure 파일 동기화](../file-sync/file-sync-planning.md)입니다. Azure 파일 동기화는 온-프레미스에서 자주 사용되는 파일을 캐시하는 StorSimple의 직접적인 아날로그 기능입니다.

Azure 파일 동기화는 다음과 같은 두 가지 주요 구성 요소를 기반으로 하는 Microsoft 클라우드 서비스입니다.

* 모든 Windows Server에서 성능 액세스 캐시를 만드는 파일 동기화 및 클라우드 계층화
* SMB, 파일 REST 등의 여러 프로토콜을 통해 액세스할 수 있는 Azure의 기본 스토리지인 파일 공유

Azure 파일 공유는 특성, 권한, 타임스탬프 등과 같이 저장된 파일의 중요한 파일 충실도 특징을 유지합니다. Azure 파일 공유를 사용하면 애플리케이션 또는 서비스에서 더 이상 클라우드에 저장된 파일 및 폴더를 해석할 필요가 없습니다. Windows 파일 탐색기와 같은 친숙한 프로토콜 및 클라이언트를 통해 기본적으로 액세스할 수 있습니다. Azure 파일 공유를 사용하면 범용 파일 서버 데이터 및 애플리케이션 데이터를 클라우드에 저장할 수 있습니다. Azure 파일 공유의 백업은 기본 제공되는 기능이며 Azure Backup을 사용하여 더욱 향상시킬 수 있습니다.

이 문서에서는 마이그레이션 단계를 중점적으로 설명합니다. 마이그레이션하기 전에 Azure 파일 동기화에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Azure 파일 동기화 개요](../file-sync/file-sync-planning.md "개요")
* [Azure 파일 동기화 배포 가이드](../file-sync/file-sync-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>StorSimple 서비스 데이터 암호화 키

StorSimple 어플라이언스를 처음으로 설정할 때 "서비스 데이터 암호화 키"가 생성되고 키를 안전하게 저장하라는 지침이 표시됩니다. 이 키는 StorSimple 어플라이언스가 파일을 저장하는 연결된 Azure 스토리지 계정의 모든 데이터를 암호화하는 데 사용됩니다.

마이그레이션이 성공하려면 "서비스 데이터 암호화 키"가 필요합니다. 이제 레코드에서 이 키를 검색하겠습니다. 이 키는 인벤토리의 각 어플라이언스마다 있습니다.

레코드에서 키를 찾을 수 없는 경우 어플라이언스에서 새 키를 생성할 수 있습니다. 각 어플라이언스에는 고유한 암호화 키가 있습니다.

#### <a name="change-the-service-data-encryption-key"></a>서비스 데이터 암호화 키 변경

[!INCLUDE [storage-files-migration-generate-key](../../../includes/storage-files-migration-generate-key.md)]

> [!CAUTION]
> StorSimple 어플라이언스에 연결하는 방법을 결정할 때 다음 사항을 고려해야 합니다.
>
> * HTTPS 세션을 통해 연결하는 것이 가장 안전하고 권장되는 옵션입니다.
> * 디바이스 직렬 콘솔에 직접 연결하는 것은 안전하지만 네트워크 스위치를 통해 직렬 콘솔에 연결하는 것은 안전하지 않습니다.
> * HTTP 세션 연결은 하나의 옵션이지만 *암호화되지* 않습니다. 폐쇄된 신뢰할 수 있는 네트워크 내에서 사용하지 않는 한 추천하지 않습니다.

### <a name="known-limitations"></a>알려진 제한 사항

StorSimple Data Manager 및 Azure 파일 공유에는 마이그레이션을 방지할 수 있기 때문에 마이그레이션을 시작하기 전에 고려해야 할 몇 가지 제한 사항은 다음과 같습니다.
* StorSimple 어플라이언스의 NTFS 볼륨만 지원됩니다.
* 서비스가 BitLocker로 암호화된 볼륨에서 작동하지 않습니다.
* 손상된 StorSimple 백업은 마이그레이션할 수 없습니다.
* StorSimple 백업이 저장된 원본 스토리지 계정이나 Azure 파일 공유를 보유하는 대상 스토리지 계정에서 방화벽 또는 프라이빗 엔드포인트 전용 통신과 같은 특수 네트워킹 옵션을 사용할 수 없습니다.


### <a name="file-fidelity"></a>파일 충실도

[알려진 제한](#known-limitations) 사항의 제한 사항 중 어느 것도 마이그레이션을 방해하지 않는 경우 알고 있어야 하는 Azure 파일 공유에 저장할 수 있는 것에 대한 제한은 여전히 있습니다.
_파일 충실도는_ 파일을 구성하는 다양한 특성, 타임스탬프 및 데이터를 나타냅니다. 마이그레이션에서 파일 충실도는 원본(StorSimple 볼륨)의 정보를 대상(Azure 파일 공유)으로 변환(마이그레이션)할 수 있는 정도의 측정값입니다.
Azure Files [NTFS 파일 속성의](/windows/win32/fileio/file-attribute-constants) [하위 집합을 지원합니다.](/rest/api/storageservices/set-file-properties) ACL, 일반 메타데이터 및 일부 타임스탬프가 마이그레이션됩니다. 다음 항목은 마이그레이션을 방지하지 않지만 마이그레이션 중에 항목별 문제를 발생합니다.

* 타임스탬프: 파일 변경 시간이 설정되지 않습니다. 현재 REST 프로토콜을 통해 읽기 전용입니다. 파일의 마지막 액세스 타임스탬프는 이동되지 않으며 현재 Azure 파일 공유에 저장된 파일에서 지원되는 특성이 아닙니다.
* [대체 데이터 스트림](/openspecs/windows_protocols/ms-fscc/b134f29a-6278-4f3f-904f-5e58a713d2c5) Azure 파일 공유에 저장할 수 없습니다. 대체 데이터 스트림 있는 파일은 복사되지만 대체 데이터 스트림 프로세스의 파일에서 제거됩니다.
* 심볼 링크, 하드 링크, 접합 및 재분석 지점은 마이그레이션 중에 건너뜁니다. 마이그레이션 복사 로그에는 건너뛴 각 항목과 이유가 나열됩니다.
* EFS 암호화된 파일을 복사하지 못합니다. 복사 로그에 "액세스가 거부되었습니다."라는 항목이 복사되지 않은 것으로 표시됩니다.
* 손상된 파일은 생략됩니다. 복사 로그는 StorSimple 디스크에서 손상된 각 항목에 대해 서로 다른 오류를 나열할 수 있습니다. "심각한 디바이스 하드웨어 오류로 인해 요청이 실패했습니다." 또는 "파일 또는 디렉터리를 손상했거나 읽을 수 없습니다." 또는 "ACL(액세스 제어 목록) 구조가 잘못되었습니다."
* 4 TiB보다 큰 개별 파일은 건너뜁 있습니다.
* 파일 경로 길이는 2048자 미만이어야 합니다. 경로가 긴 파일 및 폴더는 건너뜁니다.

### <a name="storsimple-volume-backups"></a>StorSimple 볼륨 백업

StorSimple은 볼륨 수준에서 차등 백업을 제공합니다. Azure 파일 공유에도 공유 스냅샷이라고 하는 이 기능이 있습니다.
마이그레이션 작업에서는 라이브 볼륨의 데이터가 아닌 백업만 이동할 수 있습니다. 따라서 최근 백업은 항상 마이그레이션에서 이동된 백업 목록에 있어야 합니다.

마이그레이션에서 이전 백업을 이동해야 하는지 여부를 결정합니다.
이 목록을 되도록이면 작게 유지하는 것이 가장 좋습니다. 그러면 마이그레이션 작업이 더 빨리 완료됩니다.

반드시 마이그레이션해야 하는 중요한 백업을 확인하려면 백업 정책의 검사 목록을 만듭니다. 예:
* 최근 백업. (참고: 최근 백업은 항상 이 목록에 있어야 합니다.)
* 12개월 동안 한 달에 한 번 백업
* 3년 동안 1년에 한 번 백업 

나중에 마이그레이션 작업을 만들 때 이 목록을 사용하여 요구 사항을 충족하기 위해 마이그레이션해야 하는 정확한 StorSimple 볼륨 백업을 이 목록에서 확인할 수 있습니다.

> [!CAUTION]
> StorSimple 볼륨 백업을 **50** 개까지만 선택할 수 있습니다.
> 마이그레이션 작업에서는 라이브 볼륨의 데이터가 아닌 백업만 이동할 수 있습니다. 최근 백업은 라이브 데이터와 가장 가까우며, 따라서 항상 마이그레이션에서 이동할 백업 목록에 포함되어야 합니다.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>기존 StorSimple 볼륨을 Azure 파일 공유에 매핑

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>스토리지 계정 수

마이그레이션에서 각각 소수의 Azure 파일 공유를 포함하는 여러 스토리지 계정을 배포하는 것이 좋습니다.

파일 공유가 매우 활발한 경우(여러 사용자 또는 애플리케이션에서 파일 공유를 사용하는 경우) 두 개의 Azure 파일 공유가 스토리지 계정의 성능 제한에 도달할 수 있습니다. 이로 인해 각각 자체적인 개별 파일 공유를 포함하는 여러 스토리지 계정에 마이그레이션하는 것이 가장 좋은 방법이며, 스토리지 계정당 공유 2~3개를 두는 것이 일반적입니다.

가장 좋은 것은 각각 파일 공유가 하나씩 있는 스토리지 계정을 배포하는 것입니다. Azure 파일 공유에 보관 공유가 있다면 여러 Azure 파일 공유를 동일한 스토리지 계정으로 풀링할 수 있습니다.

이러한 고려 사항은 Azure 파일 동기화보다는 Azure VM 또는 서비스를 통한 [직접 클라우드 액세스](#direct-share-access-vs-azure-file-sync)에 더 많이 적용됩니다. 이러한 공유에서 배타적으로 Azure 파일 동기화를 사용하려면 여러 공유를 Azure 스토리지 계정으로 그룹화하는 것이 좋습니다. 나중에 앱을 클라우드로 리프트 앤 시프트하여 파일 공유에 직접 액세스할 수 있습니다. 이 시나리오에서는 IOPS 및 처리량을 높이면 도움이 됩니다. 또는 IOPS 및 처리량이 높으면 좋은 Azure 서비스로 시작할 수도 있습니다.

공유 목록을 만들었으면 각 공유를 상주할 스토리지 계정에 매핑합니다.

> [!IMPORTANT]
> Azure 지역을 결정하고, 각 스토리지 계정과 Azure 파일 동기화 리소스가 선택한 지역과 일치하는지 확인합니다.
> 지금은 스토리지 계정의 네트워크 및 방화벽 설정을 구성하지 마세요. 지금 구성하면 마이그레이션이 불가능하게 됩니다. 마이그레이션이 완료된 후 이러한 Azure 스토리지 설정을 구성합니다.

### <a name="storage-account-settings"></a>스토리지 계정 설정

스토리지 계정에는 다양한 구성을 수행할 수 있습니다. 스토리지 계정 구성을 확인하기 위해 다음 체크리스트를 고려해야 합니다. 마이그레이션이 완료된 후 네트워킹 구성의 인스턴스를 변경할 수 있습니다. 

> [!div class="checklist"]
> * 대량 파일 공유: 사용 - 대량 파일 공유를 사용하면 성능을 높이고 한 공유에 최대 100TiB를 저장할 수 있습니다. 이 설정은 Azure 파일 공유가 포함된 대상 스토리지 계정에 적용됩니다.
> * 방화벽 및 가상 네트워크: 사용 안 함 - IP 제한이나 특정 VNET에 대한 스토리지 계정 액세스 제한을 구성하지 않습니다. 스토리지 계정의 퍼블릭 엔드포인트를 마이그레이션 중에 사용합니다. Azure VM의 모든 IP 주소가 허용되어야 합니다. 마이그레이션 후 스토리지 계정에 대한 방화벽 규칙을 구성하는 것이 좋습니다. 이러한 방식으로 원본 및 대상 스토리지 계정을 둘 다 구성하세요.
> * 프라이빗 엔드포인트 지원 - 프라이빗 엔드포인트를 사용할 수는 있으나 퍼블릭 엔드포인트를 마이그레이션에 사용하고, 사용 가능한 상태로 유지해야 합니다. 이러한 고려 사항은 원본 및 대상 스토리지 계정 둘 다에 적용됩니다.

### <a name="phase-1-summary"></a>1단계 요약

1 단계를 마치면 다음과 같이 됩니다.

* StorSimple 디바이스 및 볼륨의 개요를 이해했습니다.
* 각 StorSimple 디바이스의 "서비스 데이터 암호화 키"를 검색했으므로 Data Manager 서비스에서 클라우드의 StorSimple 볼륨에 액세스할 수 있습니다.
* 어떤 볼륨과 백업(최근 백업이 아닌 백업이 있는 경우)을 마이그레이션해야 하는지 계획이 있습니다.
* 볼륨을 적절한 수의 Azure 파일 공유 및 스토리지 계정에 매핑하는 방법을 알고 있습니다.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>2단계: Azure 스토리지 및 마이그레이션 리소스 배포

이 섹션에서는 Azure에 필요한 여러 리소스 종류의 배포에 대한 고려 사항을 설명합니다. 일부는 마이그레이션 후 데이터를 포함하고 있고 일부는 마이그레이션에만 필요합니다. 배포 계획을 완료하기 전에는 리소스 배포를 시작하지 마세요. Azure 리소스를 배포한 후에는 리소스의 특정 양상을 변경하기가 어렵거나 불가능할 수 있습니다.

### <a name="deploy-storage-accounts"></a>스토리지 계정 배포

여러 Azure 스토리지 계정을 배포해야 하는 경우가 많습니다. 각 계정에는 이 문서의 이전 섹션에서 완료한 배포 계획에 따라 소수의 Azure 파일 공유가 포함됩니다. Azure Portal로 이동하여 [계획된 스토리지 계정을 배포](../common/storage-account-create.md#create-a-storage-account)합니다. 새 스토리지 계정에 대해 다음과 같은 기본 설정을 준수하는 것이 좋습니다.

> [!IMPORTANT]
> 지금은 스토리지 계정의 네트워크 및 방화벽 설정을 구성하지 마세요. 지금 구성하면 마이그레이션이 불가능하게 됩니다. 마이그레이션이 완료된 후 이러한 Azure 스토리지 설정을 구성합니다.

#### <a name="subscription"></a>Subscription

StorSimple 배포에 사용한 구독을 사용해도 되고 다른 구독을 사용해도 됩니다. 유일한 제한 사항은 구독이 StorSimple 구독과 동일한 Azure Active Directory 테넌트에 있어야 한다는 것입니다. 마이그레이션을 시작하기 전에 StorSimple 구독을 적절한 테넌트로 이동하는 것이 좋습니다. 전체 구독을 이동하는 것만 가능하며, 개별 StorSimple 리소스를 다른 테넌트 또는 구독으로 이동할 수는 없습니다.

#### <a name="resource-group"></a>Resource group

리소스 그룹은 리소스 구성과 관리자 관리 권한에 도움이 됩니다. 자세한 내용은 [Azure의 리소스 그룹](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)을 참조하세요.

#### <a name="storage-account-name"></a>스토리지 계정 이름

스토리지 계정의 이름은 URL의 일부가 되고 문자 제한이 있습니다. 스토리지 계정 이름은 전 세계에서 고유해야 하고, 소문자와 숫자만 사용할 수 있고, 길이는 3~24자여야 하고, 하이픈이나 밑줄 같은 특수 문자를 사용할 수 없다는 명명 규칙을 고려해야 합니다. 자세한 내용은 [Azure 스토리지 리소스 명명 규칙](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage)을 참조하세요.

#### <a name="location"></a>위치

스토리지 계정의 위치 또는 Azure 지역은 매우 중요합니다. Azure 파일 동기화를 사용하는 경우 모든 스토리지 계정은 스토리지 동기화 서비스 리소스와 동일한 지역에 있어야 합니다. 사용자가 선택하는 Azure 지역은 로컬 서버 및 사용자와 가까이 있거나 중앙에 있어야 합니다. 리소스를 배포한 후에는 해당 지역을 변경할 수 없습니다.

StorSimple 데이터(스토리지 계정)가 현재 있는 지역과 다른 지역을 선택할 수도 있습니다.

> [!IMPORTANT]
> 현재 StorSimple 스토리지 계정 위치와 다른 지역을 선택하는 경우 마이그레이션 중에 [송신 요금이 적용됩니다](https://azure.microsoft.com/pricing/details/bandwidth). 데이터는 StorSimple 지역을 나가서 새로운 스토리지 계정 영역으로 들어갑니다. 동일한 Azure 지역 내에 머물면 대역폭 요금이 적용되지 않습니다.

#### <a name="performance"></a>성능

Azure 파일 공유 또는 표준 스토리지에 프리미엄 스토리지(SSD)를 선택하는 옵션이 있습니다. 표준 스토리지에는 [파일 공유에 대한 여러 계층](storage-how-to-create-file-share.md#change-the-tier-of-an-azure-file-share)이 포함되어 있습니다. 표준 스토리지는 StorSimple에서 마이그레이션하는 대부분의 고객에게 적합한 옵션입니다.

아직 확실하지 않나요?

* [프리미엄 Azure 파일 공유의 성능](understanding-billing.md#provisioned-model)이 필요하면 프리미엄 스토리지를 선택합니다.
* 핫 데이터 및 보관 데이터를 포함하는 범용 파일 서버 워크로드에는 표준 스토리지를 선택합니다. 클라우드의 공유에 있는 유일한 워크로드가 Azure 파일 동기화인 경우 표준 스토리지를 선택합니다.
* 프리미엄 파일 공유의 경우 저장소 계정 만들기 마법사에서 *파일 공유* 를 선택 합니다.

#### <a name="replication"></a>복제

여러 가지 복제 설정이 있습니다. 다양한 복제 유형에 대해 자세히 알아보세요.

다음 두 가지 옵션 중 하나를 선택합니다.

* *LRS(로컬 중복 스토리지)* .
* 일부 Azure 지역에서 사용할 수 있는 *ZRS(영역 중복 스토리지)* .

> [!NOTE]
> LRS 및 ZRS 중복 유형만 대용량 100TiB Azure 파일 공유와 호환됩니다.

현재는 어떠한 GRS(지역 중복 스토리지) 변형도 지원되지 않습니다. 나중에 중복 유형을 전환하고, Azure에서 GRS가 지원될 때 GRS로 전환할 수 있습니다.

#### <a name="enable-100-tib-capacity-file-shares"></a>100TiB 용량 파일 공유 사용

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="스토리지 계정을 만들 수 있는 Azure Portal의 [고급] 탭을 보여주는 이미지":::
    :::column-end:::
    :::column:::
        Azure Portal에 있는 새 스토리지 계정 마법사의 **고급** 섹션에서 이 스토리지 계정에 **대량 파일 공유** 지원을 사용하도록 설정할 수 있습니다. 이 옵션을 사용할 수 없는 경우 잘못된 중복 유형을 선택했을 가능성이 높습니다. 이 옵션을 사용하려면 LRS 또는 ZRS 중에 선택해야 합니다.
    :::column-end:::
:::row-end:::

대용량 100TiB 파일 공유를 옵트인하면 다음과 같은 여러 이점이 있습니다.

* 더 작은 5TiB 파일 공유에 비해 성능이 대폭 향상됩니다(예: IOPS 10배).
* 마이그레이션이 훨씬 빨리 완료됩니다.
* 차등 백업에 필요한 스토리지 용량을 포함하여 파일 공유에 마이그레이션할 데이터를 모두 저장할 수 있는 충분한 용량이 확보됩니다.
* 향후 확장 여유가 있습니다.

> [!IMPORTANT]
> 마이그레이션 이전 또는 중간에 스토리지 계정에 특별한 네트워킹을 적용하지 마세요. 퍼블릭 엔드포인트를 원본 및 대상 스토리지 계정에서 액세스할 수 있어야 합니다. 특정 IP 주소 범위 또는 VNET로의 제한은 지원되지 않습니다. 마이그레이션 후 스토리지 계정 네트워킹 구성을 변경할 수 있습니다.

### <a name="azure-file-shares"></a>Azure 파일 공유

스토리지 계정을 만든 후에는 스토리지 계정의 **파일 공유** 섹션으로 이동하여 1단계의 마이그레이션 계획에 따라 적절한 수의 Azure 파일 공유를 배포합니다. Azure의 새 파일 공유에 대한 다음 기본 설정을 준수하는 것이 좋습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="새 파일 공유 UI를 보여주는 Azure Portal 스크린샷":::
    :::column-end:::
    :::column:::
        </br>**이름**</br>소문자, 숫자 및 하이픈이 지원됩니다.</br></br>**할당량**</br>이 할당량은 Windows Server 인스턴스의 SMB 하드 할당량과 비슷합니다. 할당량에 도달하면 마이그레이션 및 기타 서비스가 실패하므로 여기서는 할당량을 설정하지 않는 것이 좋습니다.</br></br>**계층**</br>새 파일 공유에 대해 **트랜잭션 최적화** 를 선택합니다. 마이그레이션 중에는 많은 트랜잭션이 발생합니다. 나중에 워크로드에 가장 적합한 계층으로 변경하는 것이 보다 비용 효율적입니다.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple 데이터 관리자

마이그레이션 작업을 보관할 Azure 리소스를 **StorSimple Data Manager** 라고 합니다. **새 리소스** 를 선택하고 새 리소스를 검색합니다. 그런 다음 **생성** 를 선택합니다.

이 임시 리소스는 오케스트레이션에 사용됩니다. 마이그레이션이 완료된 후 이 리소스의 프로비전을 해제합니다. StorSimple 스토리지 계정과 동일한 구독, 리소스 그룹 및 지역에 배포해야 합니다.

### <a name="azure-file-sync"></a>Azure 파일 동기화

Azure 파일 동기화를 사용하면 자주 액세스하는 파일의 온-프레미스 캐싱을 추가할 수 있습니다. StorSimple의 캐싱 기능과 마찬가지로, Azure 파일 동기화 클라우드 계층화 기능은 Windows Server 인스턴스와 다중 사이트 동기화에서 사용 가능한 캐시 용량에 대한 향상된 제어 기능과 함께 로컬 액세스 대기 시간을 제공합니다. 온-프레미스 캐시가 목표인 경우 로컬 네트워크에서 충분한 직접 연결 스토리지 용량이 있는 Windows Server VM(물리적 서버 및 장애 조치(failover) 클러스터도 지원됨)을 준비합니다.

> [!IMPORTANT]
> 아직은 Azure 파일 동기화를 설정하지 마세요. 공유 마이그레이션이 완료된 후 Azure 파일 동기화를 설정하는 것이 가장 좋습니다. Azure 파일 동기화 배포는 마이그레이션의 4단계 전에 시작하면 안 됩니다.

### <a name="phase-2-summary"></a>2단계 요약

2단계를 완료했으므로 스토리지 계정이 배포되고 모든 Azure 파일 공유가 스토리지 계정에 배포되었습니다. StorSimple Data Manager 리소스도 있습니다. 후자는 마이그레이션 작업을 구성할 때 3단계에서 사용됩니다.

## <a name="phase-3-create-and-run-a-migration-job"></a>3단계: 마이그레이션 작업 만들기 및 실행

이 섹션에서는 마이그레이션 작업을 설정하고, 선택한 대상 Azure 파일 공유에 복사해야 하는 StorSimple 볼륨의 디렉터리를 신중하게 매핑하는 방법을 설명합니다. 시작하려면 StorSimple Data Manager로 이동하고, 메뉴에서 **작업 정의** 를 찾고, **+ 작업 정의** 를 선택합니다. 올바른 대상 스토리지 유형은 기본값인 **Azure 파일 공유** 입니다.

![StorSimple 8000 시리즈 마이그레이션 작업 유형](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "작업 유형(파일 공유에 복사 또는 Blob 컨테이너에 복사)을 묻는 새 작업 정의 대화 상자가 열려 있는 작업 정의 Azure Portal의 스크린샷")

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png" alt-text="마이그레이션 작업에 대 한 새 작업 생성 양식의 스크린샷":::
    :::column-end:::
    :::column:::
        **작업 정의 이름**</br>이 이름은 이동하려는 파일 세트를 나타내야 합니다. Azure 파일 공유와 비슷한 이름을 지정하는 것이 좋습니다. </br></br>**작업이 실행되는 위치**</br>지역을 선택할 때 StorSimple 스토리지 계정과 동일한 지역을 선택해야 합니다. 동일한 지역을 선택할 수 없으면 가까운 지역을 선택합니다. </br></br><h3>원본</h3>**원본 구독**:</br>StorSimple 디바이스 관리자 리소스를 저장하는 구독을 선택합니다. </br></br>**StorSimple 리소스**</br>어플라이언스가 등록된 StorSimple 디바이스 관리자를 선택합니다. </br></br>**서비스 데이터 암호화 키**</br>레코드에서 키를 찾을 수 없는 경우 [이 문서의 이전 섹션](#storsimple-service-data-encryption-key)을 확인하세요. </br></br>**디바이스**</br>마이그레이션하려는 볼륨이 들어 있는 StorSimple 디바이스를 선택합니다. </br></br>**볼륨**</br>원본 볼륨을 선택합니다. 대상 Azure 파일 공유로 전체 볼륨을 마이그레이션할 것인지 아니면 하위 디렉터리를 마이그레이션할 것인지는 나중에 결정합니다.</br></br> **볼륨 백업**</br>*볼륨 백업 선택* 을 선택하여 이 작업의 일부로 이동할 백업을 선택할 수 있습니다. 자세한 프로세스는 곧 추가될 [이 문서의 해당 섹션](#selecting-volume-backups-to-migrate)에서 설명합니다.</br></br><h3>대상</h3>이 마이그레이션 작업의 대상으로 구독, 스토리지 계정 및 Azure 파일 공유를 선택합니다.</br></br><h3>디렉터리 매핑</h3>관련 내용은 [이 문서의 해당 섹션](#directory-mapping)에서 자세히 설명합니다.
    :::column-end:::
:::row-end:::

### <a name="selecting-volume-backups-to-migrate"></a>마이그레이션할 볼륨 백업 선택

마이그레이션할 백업 선택과 관련하여 중요한 사항이 있습니다.

- 마이그레이션 작업은 라이브 볼륨 데이터가 아닌 백업만 이동할 수 있습니다. 따라서 최근 백업은 라이브 데이터와 가장 가까이 있으며 항상 마이그레이션에서 이동할 백업 목록에 포함되어야 합니다. 백업 선택 대화 상자를 열면 기본적으로 선택 됩니다.
- 최근 백업이 가장 최신인지 확인하여 라이브 공유의 델타를 최대한 작게 유지합니다. 마이그레이션 작업을 만들기 전에 다른 볼륨 백업을 수동으로 트리거하고 완료해 보는 것도 좋습니다. 라이브 공유의 델타가 작으면 마이그레이션 환경이 개선됩니다. 이 델타를 0으로 만들 수 있다면 목록의 최신 백업을 수행한 후에도 StorSimple 볼륨의 변경 내용이 없으므로 5단계: 사용자 중단이 대폭 간소화되고 빨라집니다.
- 백업은 Azure 파일 공유 **오름차순(오래된 항목순)** 으로 다시 재생되어야 합니다. 이전 백업은 마이그레이션 작업이 실행된 후 Azure 파일 공유의 백업 목록에 "정렬"할 수 없습니다. 따라서 작업을 만들기 *전에* 백업 목록이 완료되었는지 확인해야 합니다. 
- 작업을 만든 후에는 작업을 실행하지 않아도 작업의 이 백업 목록을 수정할 수 없습니다. 

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups.png" alt-text="마이그레이션할 StorSimple 백업이 선택되는 부분을 자세히 설명하는 새 작업 만들기 양식의 스크린샷" lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-expanded.png":::
    :::column-end:::
    :::column:::
        마이그레이션 작업에 사용할 StorSimple 볼륨 백업을 선택하려면 작업 만들기 양식에서 *볼륨 백업 선택* 을 선택합니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png" alt-text="백업을 선택하는 블레이드의 위쪽 절반에 사용 가능한 모든 백업이 나열된 것을 보여주는 이미지. 선택한 백업은 이 목록에서 회색으로 표시되고 블레이드 아래쪽 절반에 있는 두 번째 목록에 추가됩니다. 여기서 백업을 다시 삭제할 수도 있습니다." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png":::
    :::column-end:::
    :::column:::
        백업 선택 블레이드가 열리면 두 개의 목록으로 분리됩니다. 첫 번째 목록에는 사용 가능한 모든 백업이 표시됩니다. 시간 범위를 필터링하여 결과 세트를 확장하고 좁힐 수 있습니다. (다음 섹션 참조) </br></br>선택한 백업은 회색으로 표시되고 블레이드 아래쪽 절반에 있는 두 번째 목록에 추가됩니다. 두 번째 목록에는 마이그레이션을 위해 선택한 모든 백업이 표시됩니다. 잘못 선택한 백업을 다시 제거할 수도 있습니다.
        > [!CAUTION]
        > 마이그레이션하려는 **모든** 백업을 선택해야 합니다. 이전 백업은 나중에 추가할 수 없습니다. 작업을 만든 후에는 작업을 수정하여 선택 항목을 변경할 수 없습니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time.png" alt-text="백업 선택 블레이드에서 시간 범위를 선택하는 것을 보여주는 스크린샷" lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time-expanded.png":::
    :::column-end:::
    :::column:::
        기본적으로이 목록은 지난 7 일 이내에 StorSimple 볼륨 백업을 표시 하도록 필터링 됩니다. 지난 7 일 동안 발생 하지 않은 경우에도 가장 최근의 백업은 기본적으로 선택 됩니다. 이전 백업의 경우 블레이드 위쪽에 있는 시간 범위 필터를 사용 합니다. 기존 필터 중에 선택할 수도 있고 사용자 지정 시간 범위를 설정하여 해당 기간 중에 수행된 백업만 필터링할 수도 있습니다.
    :::column-end:::
:::row-end:::

> [!CAUTION]
> StorSimple 볼륨 백업을 50개까지만 선택할 수 있습니다. 백업 수가 많은 작업은 실패할 수 있습니다.

### <a name="directory-mapping"></a>디렉터리 매핑

디렉터리 매핑은 마이그레이션 작업의 선택 사항입니다. 이 섹션을 비워 두면 StorSimple 볼륨의 루트에 있는 *모든* 파일과 폴더가 대상 Azure 파일 공유의 루트로 이동됩니다. 대부분의 경우 전체 볼륨의 콘텐츠를 Azure 파일 공유에 저장하는 것은 가장 좋은 방법이 아닙니다. 볼륨 콘텐츠를 Azure의 여러 파일 공유에 분할하는 것이 좋은 경우가 많습니다. 아직 계획을 작성하지 않은 경우 [Azure 파일 공유에 StorSimple 볼륨 매핑](#map-your-existing-storsimple-volumes-to-azure-file-shares)을 먼저 살펴보세요.

마이그레이션 계획을 작성할 때 StorSimple 볼륨의 폴더를 여러 Azure 파일 공유에 분할해야겠다고 결정했을 수 있습니다. 이 경우 다음과 같은 방법으로 분할할 수 있습니다.

1. 볼륨의 폴더를 마이그레이션하는 여러 작업을 정의합니다. 각 폴더에는 동일한 StorSimple 볼륨 원본이 포함되지만 Azure 파일 공유는 대상과 다릅니다.
1. 작업 만들기 양식의 **디렉터리 매핑** 섹션을 사용하고 특정 [매핑 의미 체계](#semantic-elements)에 따라 StorSimple 볼륨의 어떤 폴더를 지정된 파일 공유로 마이그레이션해야 하는지 정확하게 지정합니다.

> [!IMPORTANT]
> 양식을 제출한 후에는 양식의 경로 및 매핑 식의 유효성을 검사할 수 없습니다. 매핑이 잘못 지정되면 작업이 완전히 실패하거나 올바르지 않은 결과가 발생할 수 있습니다. 이 경우 일반적으로 Azure 파일 공유를 삭제하고 다시 만든 다음, 공유에 대한 새 마이그레이션 작업에서 매핑 문을 수정하는 것이 가장 좋습니다. 매핑 문이 수정된 새 작업을 실행하면 생략된 폴더를 수정하여 기존 공유에 가져올 수 있습니다. 그러나 경로 철자 오류로 인해 생략된 폴더만 이 방식으로 해결할 수 있습니다.

#### <a name="semantic-elements"></a>의미 체계 요소

매핑은 왼쪽에서 오른쪽으로, 즉, [\원본 경로] \> [\대상 경로]로 표시됩니다.

|의미 체계 문자          | 의미  |
|:---------------------------|:---------|
| **\\**                     | 루트 수준 표시기입니다.       |
| **\>**                     | [원본] 및 [대상-매핑] 연산자입니다.     |
|**\|** 또는 RETURN(새 줄) | 두 폴더 매핑 명령의 구분 기호입니다. </br>또는 이 문자를 생략하고 **Enter** 를 선택하여 자체 줄에 다음 매핑 식을 가져올 수 있습니다.        |

### <a name="examples"></a>예
*User data* 폴더의 콘텐츠를 대상 파일 공유의 루트로 이동합니다.
``` console
\User data > \
```
전체 볼륨 콘텐츠를 대상 파일 공유의 새 경로로 이동합니다.
``` console
\ > \Apps\HR tracker
```
원본 폴더 콘텐츠를 대상 파일 공유의 새 경로로 이동합니다.
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
여러 원본 위치를 새 디렉터리 구조로 정렬합니다.
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>의미 체계 규칙

* 항상 루트 수준을 기준으로 폴더 경로를 지정합니다.
* 루트 수준 표시기 "\\"로 각 폴더 경로를 시작합니다.
* 드라이브 문자를 포함하지 않습니다.
* 여러 경로를 지정할 때 원본 또는 대상 경로가 겹치면 안 됩니다.</br>
   원본 경로가 겹치는 잘못된 예:</br>
    *\\folder\1 > \\folder*</br>
    *\\folder\\1\\2 > \\folder2*</br>
   대상 경로가 겹치는 잘못된 예:</br>
   *\\folder > \\*</br>
   *\\folder2 > \\*</br>
* 존재하지 않는 원본 폴더는 무시됩니다.
* 대상에 없는 폴더 구조가 생성됩니다.
* Windows와 마찬가지로 폴더 이름은 대/소문자를 구분하지 않지만 대/소문자를 유지합니다.

> [!NOTE]
> *\System Volume Information* 폴더의 콘텐츠와 StorSimple 볼륨의 *$Recycle.Bin* 은 마이그레이션 작업에서 복사되지 않습니다.

### <a name="run-a-migration-job"></a>마이그레이션 작업 실행

마이그레이션 작업은 리소스 그룹에 배포한 Data Manager 리소스의 *작업 정의* 아래에 나열됩니다.
작업 정의 목록에서 실행하려는 작업을 선택합니다.

열리는 작업 블레이드에서 작업의 현재 상태와 선택한 백업 목록을 볼 수 있습니다. 백업 목록은 가장 오래 된 순서로 정렬 되며이 순서로 Azure 파일 공유로 마이그레이션됩니다.  

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-never-ran-focused.png" alt-text="작업을 시작 하는 명령 주위에 강조 표시 된 마이그레이션 작업 블레이드의 스크린샷 또한 마이그레이션에 예약 된 선택한 백업만 표시 합니다." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-never-ran.png":::
    :::column-end:::
    :::column:::
        처음에는 마이그레이션 작업의 상태가 **실행 되지 않음** 으로 나타납니다. </br>준비가 되 면이 마이그레이션 작업을 시작할 수 있습니다. (해상도가 더 높은 버전의 이미지를 선택 합니다.) </br> 백업이 성공적으로 마이그레이션되면 자동 Azure 파일 공유 스냅숏이 생성 됩니다. StorSimple 백업의 원래 백업 날짜는 Azure 파일 공유 스냅숏의 *설명* 섹션에 배치 됩니다. 이 필드를 사용 하면 파일 공유 스냅숏이 만들어진 시간에 비해 데이터가 원래 백업 된 시간을 확인할 수 있습니다.
        </br></br>
        > [!CAUTION]
        > 백업은 가장 오래 된에서 최신으로 처리 되어야 합니다. 마이그레이션 작업을 만든 후에는 선택한 StorSimple 볼륨 백업의 목록을 변경할 수 없습니다. 백업 목록이 잘못 되었거나 불완전 한 경우 작업을 시작 하지 마세요. 작업을 삭제 하 고 올바른 백업을 선택 하 여 새 작업을 만듭니다.
    :::column-end:::
:::row-end:::

### <a name="per-item-errors"></a>항목당 오류

마이그레이션 작업에는 복사 중 발생 했을 수 있는 모든 문제를 나열 하는 백업 목록에 두 개의 열이 있습니다.

* 복사 오류 </br>이 열에는 복사한 파일이 나 폴더가 나열 되지 않습니다. 이러한 오류는 복구할 수 있는 경우가 많습니다. 백업이이 열에 항목 문제를 나열 하는 경우 복사 로그를 검토 합니다. 이러한 파일을 마이그레이션해야 하는 경우 **백업 다시 시도** 를 선택 합니다. 백업 처리가 완료 되 면이 옵션을 사용할 수 있게 됩니다. [마이그레이션 작업 관리](#manage-a-migration-job) 섹션에서 옵션에 대해 자세히 설명 합니다.
* 지원 되지 않는 파일 </br>이 열에는 마이그레이션할 수 없는 파일이 나 폴더가 나열 됩니다. Azure Storage에는 현재 또는 논리적으로 Azure 파일 공유에 저장 될 수 없는 파일 이름, 경로 길이 및 파일 형식에 대 한 제한이 있습니다. 마이그레이션 작업은 이러한 종류의 오류에 대해 일시 중지 되지 않습니다. 백업 마이그레이션을 다시 시도 해도 결과가 변경 되지 않습니다. 백업에서이 열에 항목 문제를 나열 하는 경우 복사 로그를 검토 하 고 메모 합니다. 이러한 문제가 마지막 백업에서 발생 하 고 복사 로그에서 파일 이름, 경로 길이 또는 영향을 미치는 다른 문제로 인해 실패 한 것으로 확인 되는 경우 라이브 StorSImple 볼륨의 문제를 해결 하 고, StorSimple 볼륨 백업을 수행 하 고, 해당 백업만 사용 하 여 새 마이그레이션 작업을 만들 수 있습니다. 그런 다음이 해결 되지 않은 네임 스페이스를 마이그레이션하고 Azure 파일 공유의 최신/라이브 버전이 됩니다. 이는 수동 및 시간이 오래 걸리는 프로세스입니다. 복사 로그를 신중 하 게 검토 하 고 가치가 있는지 평가 합니다.

이러한 복사 로그는 *\*.csv* 네임 스페이스 항목을 나열 하 고 복사 하지 못한 항목을 나열 하는 파일입니다. 오류는 앞에서 설명한 범주로 분할 됩니다.
로그 파일 위치에서 "failed"를 검색 하 여 실패 한 파일에 대 한 로그를 찾을 수 있습니다. 결과는 복사 하지 못한 파일에 대 한 로그 집합 이어야 합니다. 이러한 로그는 크기를 기준으로 정렬 합니다. 17 바이트 크기의 추가 로그가 생성 될 수 있습니다. 이러한 로그는 비어 있으며 무시해도 됩니다. 정렬을 사용 하면 콘텐츠를 사용 하 여 로그에 집중할 수 있습니다.

복사 성공이 기록된 로그 파일에도 동일한 프로세스가 적용됩니다.

### <a name="manage-a-migration-job"></a>마이그레이션 작업 관리

마이그레이션 작업의 상태는 다음과 같습니다.
* **실행 되지 않음** </br>정의 되었지만 이전에 실행 되지 않은 새 작업입니다.
* **대기 중** </br>이 상태의 작업은 마이그레이션이 서비스에서 리소스를 프로 비전 할 때까지 대기 하 고 있습니다. 준비가 되 면 다른 상태로 자동 전환 됩니다.
* **실패** </br>실패 한 작업에 심각한 오류가 발생 하 여 더 이상 백업을 처리할 수 없습니다. 작업은이 상태로 전환 될 수 없습니다. 지원 요청은 가장 좋은 조치입니다.
* **취소되었습니다.**  /  **취소 중**</br>및 전체 마이그레이션 작업 또는 작업 내의 개별 백업을 취소할 수 있습니다. 취소된 백업은 처리되지 않습니다. 취소된 마이그레이션 작업은 더 많은 백업 처리를 중지합니다. 작업을 취소하는 데 시간이 오래 걸릴 것으로 예상합니다. 이렇게 해서 새 작업을 만들 수 있는 것은 아닙니다. 가장 좋은 작업 과정은 작업이 **취소된** 상태로 완전히 도착할 수 있도록 하는 것입니다. 실패/취소된 작업을 무시하거나 나중에 삭제할 수 있습니다. StorSimple 마이그레이션이 끝날 때 Data Manager 리소스를 삭제하기 전에 작업을 삭제할 필요가 없습니다.


:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-running-focused.png" alt-text="실행 중 상태 맨 위에 큰 상태 아이콘이 있는 마이그레이션 작업 블레이드의 스크린샷." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-running.png":::
    :::column-end:::
    :::column:::
        **실행 중** </br></br>실행 중인 작업이 현재 백업을 처리하고 있습니다. 현재 처리 중인 백업과 이미 마이그레이션되었을 수 있는 백업을 보려면 블레이드의 아래쪽 절반에 있는 테이블을 참조하세요. </br>이미 마이그레이션된 백업에는 복사 로그에 대한 링크가 있는 열이 있습니다. 백업에 대해 보고된 오류가 있는 경우 해당 복사 로그를 검토해야 합니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-paused-focused.png" alt-text="일시 중지된 상태의 맨 위에 큰 상태 아이콘이 있는 마이그레이션 작업 블레이드의 스크린샷." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-paused.png":::
    :::column-end:::
    :::column:::
        **일시 중지됨** </br></br>결정해야 하는 경우 마이그레이션 작업이 일시 중지됩니다. 이 조건은 블레이드 맨 위에 있는 두 개의 명령 단추를 사용하도록 설정합니다. </br>백업에 이동해야 하지만 이동하지 않은 파일이 표시되면 **백업 다시 시도(복사** *오류* 열)를 선택합니다. </br>백업이 누락되거나(마이그레이션 작업을 만든 이후 정책에 의해 삭제된 경우) 또는 백업이 손상된 경우 백업 **건너뛰기** 를 선택합니다. 실패한 백업을 클릭하면 열리는 블레이드에서 자세한 오류 정보를 찾을 수 있습니다. </br></br>현재 백업을 *건너뛰거나* *다시 시도하면* 마이그레이션 서비스는 대상 Azure 파일 공유에 새 스냅샷을 만듭니다. 나중에 이전 항목을 삭제할 수 있으며 불완전할 수 있습니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-success-focused.png" alt-text="전체 상태의 맨 위에 큰 상태 아이콘이 있는 마이그레이션 작업 블레이드를 보여 주는 이미지입니다." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-success.png":::
    :::column-end:::
    :::column:::
        **경고가** **있는 완료** 및 완료</br></br>작업의 모든 백업이 성공적으로 처리되면 마이그레이션 작업이 **완료로** 나열됩니다. </br>**경고 완료는** 다음과 같은 경우에 발생하는 상태입니다. <ul><li>백업에서 복구 가능한 문제가 발생했습니다. 이 백업은 *부분 성공* 또는 *실패로* 표시됩니다.</li><li>문제가 있는 백업을 건너뛰어 일시 중지된 작업을 계속하기로 결정했습니다. (백업 *다시 시도* 대신 백업 *건너뛰기* 선택)</li></ul> 경고와 함께 마이그레이션 작업이 완료되면 항상 관련 백업에 대한 복사 로그를 검토해야 합니다.
    :::column-end:::
:::row-end:::

#### <a name="run-jobs-in-parallel"></a>병렬로 작업 실행

Azure 파일 공유로 마이그레이션해야 하는 자체 공유가 있는 여러 StorSimple 볼륨이 있을 수 있습니다. 병렬로 수행할 수 있는 작업을 이해하는 것이 중요합니다. 사용자 경험에 적용되지 않는 제한 사항도 있으며, 작업이 동시에 실행되는 경우 전체 마이그레이션의 성능이 저하되거나 금지됩니다.

마이그레이션 작업을 정의하는 데는 제한이 없습니다. 동일하거나 다른 StorSimple 어플라이언스에 동일한 StorSimple 원본 볼륨, 동일한 Azure 파일 공유를 정의할 수 있습니다. 그러나 실행은 다음과 같은 제한 사항입니다.

* 동일한 StorSimple 원본 볼륨을 가진 하나의 마이그레이션 작업만 동시에 실행할 수 있습니다.
* 동일한 대상 Azure 파일 공유를 가진 하나의 마이그레이션 작업만 동시에 실행할 수 있습니다.
* 이전 규칙을 준수하는 한 StorSimple 디바이스 관리자당 최대 4개의 마이그레이션 작업을 병렬로 실행할 수 있습니다.

마이그레이션 작업을 시작하려고 하면 이전 규칙이 확인됩니다. 실행 중인 작업이 있는 경우 현재 작업을 시작하지 못할 수 있습니다. 새 작업을 시작하기 전에 완료해야 하는 현재 실행 중인 작업의 이름을 나열하는 경고가 표시됩니다.

> [!TIP]
> *Data Manager* 리소스의 *작업 정의* 탭에서 마이그레이션 작업을 정기적으로 확인하여 해당 작업이 일시 중지되어 입력을 완료해야 하는지 확인하는 것이 좋습니다.

### <a name="phase-3-summary"></a>3단계 요약

3단계를 마치면 StorSimple 볼륨에서 Azure 파일 공유로 마이그레이션하는 작업을 한 번 이상 실행하게 됩니다. 실행하면 지정된 백업을 Azure 파일 공유 스냅샷으로 마이그레이션하게 됩니다. 이제 공유에 대한 Azure 파일 동기화 설정(공유에 대한 마이그레이션 작업이 완료되면) 또는 정보 근로자 및 앱에 대한 직접 공유 액세스를 Azure 파일 공유로 설정하는 데 집중할 수 있습니다.

## <a name="phase-4-access-your-azure-file-shares"></a>4단계: Azure 파일 공유에 액세스

Azure 파일 공유에 액세스하는 두 가지 주요 전략은 다음과 같습니다.

* **Azure 파일 동기화**: 온-프레미스 Windows Server 인스턴스에 [Azure 파일 동기화를 배포](#deploy-azure-file-sync)합니다. Azure 파일 동기화는 StorSimple과 마찬가지로 로컬 캐시의 모든 장점을 갖고 있습니다.
* **직접 공유 액세스**: [직접 공유 액세스를 배포](#deploy-direct-share-access)합니다. 지정된 Azure 파일 공유에 대한 액세스 시나리오에서 로컬 캐싱이 도움이 되지 않거나 더 이상 온-프레미스 Windows Server 인스턴스를 호스트할 수 없는 경우 이 전략을 사용합니다. 여기서 사용자와 앱은 SMB 프로토콜을 통해 SMB 공유에 계속 액세스할 수 있습니다. 이러한 공유는 더 이상 온-프레미스 서버에 없고 클라우드에서 직접 사용할 수 있습니다.

이 가이드의 [1단계](#phase-1-prepare-for-migration)에서 가장 적합한 옵션을 이미 결정했습니다.

이 섹션의 나머지 부분에서는 배포 지침을 중점적으로 설명합니다.

### <a name="deploy-azure-file-sync"></a>Azure 파일 동기화 배포

Azure 파일 동기화의 일부를 배포할 시간입니다.

1. Azure 파일 동기화 클라우드 리소스를 만듭니다.
1. 온-프레미스 서버에 Azure 파일 동기화 에이전트를 배포합니다.
1. 서버를 클라우드 리소스에 등록합니다.

아직 동기화 그룹을 만들지 마세요. Azure 파일 공유와의 동기화는 Azure 파일 공유로 마이그레이션하는 작업이 완료된 후에 설정해야 합니다. 마이그레이션을 완료하기 전에 Azure 파일 동기화를 시작하면 언제 사용자를 중단해야 하는지 알기 어렵기 때문에 마이그레이션이 불필요하게 어려워집니다.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Azure 파일 동기화 클라우드 리소스 배포

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> 마이그레이션이 완료된 후 데이터가 상주하는 Azure 지역을 변경하려면 이 마이그레이션의 대상 스토리지 계정과 동일한 지역에 스토리지 동기화 서비스를 배포합니다.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>온-프레미스 Windows Server 인스턴스 배포

* Windows Server 2019(2012R2 이상)를 가상 머신 또는 물리적 서버로 만듭니다. Windows Server 장애 조치(failover) 클러스터도 지원됩니다. StorSimple 8100 또는 8600 앞의 서버를 다시 사용하지 마세요.
* DAS(직접 연결된 스토리지)를 프로비저닝하거나 추가합니다. Network Attached Storage는 지원되지 않습니다.

새 Windows Server 인스턴스에 StorSimple 8100 또는 8600 어플라이언스가 로컬로 캐싱에 사용할 수 있는 양보다 많거나 같은 스토리지를 제공하는 것이 가장 좋습니다. StorSimple 어플라이언스를 사용한 것과 같은 방식으로 Windows Server 인스턴스를 사용할 것입니다. 어플라이언스와 같은 양의 스토리지가 있으면 캐싱 환경이 완전히 같지는 않아도 비슷합니다. Windows Server 인스턴스의 스토리지를 마음대로 추가하거나 제거할 수 있습니다. 이 기능을 통해 로컬 볼륨 크기와 캐싱에 사용할 수 있는 로컬 스토리지의 양을 스케일링할 수 있습니다.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>파일 동기화에 사용할 Windows Server 인스턴스 준비

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Windows Server 인스턴스에서 Azure 파일 동기화 구성

이 프로세스를 진행하려면 등록된 온-프레미스 Windows Server 인스턴스가 준비되어 있고 인터넷에 연결되어 있어야 합니다.

> [!IMPORTANT]
> 계속하려면 파일 및 폴더를 Azure 파일 공유로 이동하는 StorSimple 마이그레이션을 먼저 완료해야 합니다. 파일 공유를 더 이상 변경하면 안 됩니다.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 클라우드 계층화를 켭니다. 클라우드 계층화는 로컬 서버가 클라우드에 저장되는 것보다 적은 스토리지 용량을 가지면서도 전체 네임스페이스를 사용할 수 있도록 하는 Azure 파일 동기화 기능입니다. 또한 로컬에서 필요한 데이터가 로컬로 캐시되어 빠른 로컬 액세스 성능을 제공합니다. 이 단계에서 클라우드 계층화를 켜는 또 다른 이유는 이 단계에서 파일 콘텐츠를 동기화하지 않을 것이기 때문입니다. 지금은 네임스페이스만 이동해야 합니다.

### <a name="deploy-direct-share-access"></a>직접 공유 액세스 배포

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        이 비디오는 간단한 다섯 가지 단계를 통해 Azure 파일 공유를 정보 근로자 및 앱에 직접 안전하게 노출하는 방법에 대한 가이드 및 데모입니다.</br>
        이 비디오는 다음 항목에 대한 전용 설명서를 참조합니다.

* [ID 개요](storage-files-active-directory-overview.md)
* [스토리지 계정을 도메인에 조인하는 방법](storage-files-identity-auth-active-directory-enable.md)
* [Azure 파일 공유에 대한 네트워킹 개요](storage-files-networking-overview.md)
* [퍼블릭 및 프라이빗 엔드포인트를 구성하는 방법](storage-files-networking-endpoints.md)
* [S2S VPN을 구성하는 방법](storage-files-configure-s2s-vpn.md)
* [Windows P2S VPN을 구성하는 방법](storage-files-configure-p2s-vpn-windows.md)
* [Linux P2S VPN을 구성하는 방법](storage-files-configure-p2s-vpn-linux.md)
* [DNS 전달을 구성하는 방법](storage-files-networking-dns.md)
* [DFS-N 구성](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>4단계 요약

이 단계가 끝나면 StorSimple Data Manager에서 여러 마이그레이션 작업을 만들고 실행 했습니다. 이러한 작업은 파일 및 폴더와 해당 백업을 Azure 파일 공유로 마이그레이션 했습니다. 또한 Azure 파일 동기화를 배포했거나 직접 공유 액세스를 위한 네트워크 및 스토리지 계정을 준비했습니다.

## <a name="phase-5-user-cut-over"></a>5단계: 사용자 중단

이 단계에서는 마이그레이션을 래핑합니다.

* 가동 중지 시간을 계획합니다.
* 3단계에서 마이그레이션 작업을 실행하는 동안 StorSimple 쪽에서 사용자와 앱이 생성한 변경 내용을 업데이트합니다.
* 사용자를 Azure 파일 동기화를 사용 하 여 새 Windows 서버 인스턴스로 장애 조치 (failover) 하거나 직접 공유 액세스를 통해 Azure 파일 공유로 장애 조치 (failover) 합니다.

### <a name="plan-your-downtime"></a>가동 중지 시간 계획

이 마이그레이션 방법에서는 사용자 및 앱에 대한 약간의 가동 중지 시간이 필요합니다. 목표는 가동 중지 시간을 최소화하는 것입니다. 다음 사항을 고려하면 도움이 됩니다.

* 마이그레이션 작업을 실행하는 동안 StorSimple 볼륨을 계속 사용할 수 있도록 유지합니다.
* 공유에 대한 데이터 마이그레이션 작업 실행이 완료된 후에는 StorSimple 볼륨 또는 공유에서 사용자 액세스 권한(최소한 쓰기 권한)을 제거해야 합니다. 최종 RoboCopy는 Azure 파일 공유를 업데이트합니다. 그 후 사용자를 중단할 수 있습니다. RoboCopy를 실행하는 위치는 Azure 파일 동기화를 사용하도록 선택했는지 아니면 직접 공유 액세스를 사용하도록 선택했는지에 따라 달라집니다. 이 주제는 RoboCopy에 대해 예정된 섹션에서 다룹니다.
* RoboCopy 업데이트가 완료되면 Azure 파일 공유를 사용하여 직접 또는 Azure 파일 동기화를 사용하는 Windows Server 인스턴스의 SMB 공유를 통해 새 위치를 사용자에게 표시할 수 있습니다. DFS-N 배포를 사용하면 중단을 신속하고 효율적으로 처리하는 데 도움이 되는 경우가 자주 있습니다. 이 배포는 기존 공유 주소를 일관적으로 유지하고 마이그레이션된 파일 및 폴더가 포함된 새 위치를 다시 가리킵니다.

보관 데이터의 경우 StorSimple 볼륨 (또는 하위 폴더)에 대 한 가동 중지 시간을 발생 시킬 수 있는 완벽 한 접근 방식으로, 하나 이상의 StorSimple 볼륨 백업을 사용 하 고 마이그레이션한 후 사용자 및 앱에서 액세스 하기 위한 마이그레이션 대상을 열어야 합니다. 이렇게 하면이 섹션에 설명 된 대로 보완 RoboCopy에 대 한 필요성을 확보할 수 있습니다. 그러나이 방법은 마이그레이션해야 하는 파일 및 백업 수에 따라 며칠 이상 확장 될 수 있는 긴 가동 중지 시간으로 인해 발생 합니다. 이는 오랜 시간 동안 쓰기 권한 없이 수행할 수 있는 보관 워크 로드에 대 한 옵션만 될 수 있습니다.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>네임스페이스가 서버와 완전히 동기화된 시간 확인

Azure 파일 공유에 Azure 파일 동기화를 사용하는 경우 로컬 RoboCopy를 시작하기 *전에* 전체 네임스페이스가 서버에 완전히 다운로드되었는지 확인해야 합니다. 네임스페이스를 다운로드하는 데 걸리는 시간은 Azure 파일 공유의 항목 수에 따라 달라집니다. 네임스페이스가 서버에 완전히 도착했는지 확인하는 두 가지 방법이 있습니다.

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 네임스페이스가 완전히 도착한 시간을 확인할 수 있습니다.

* Azure Portal에 로그인하고 동기화 그룹으로 이동합니다. 동기화 그룹과 서버 엔드포인트의 동기화 상태를 확인합니다.
* 알고 싶은 방향은 다운로드입니다. 서버 엔드포인트가 새로 프로비저닝되면 네임스페이스를 다운로드 중이라는 의미의 **초기 동기화** 가 표시됩니다.
**초기 동기화** 를 제외한 상태 작업 후, 서버에서 네임스페이스가 완전히 채워집니다. 이제 로컬 RoboCopy를 진행할 수 있습니다.

#### <a name="windows-server-event-viewer"></a>Windows Server 이벤트 뷰어

또한 Windows Server 인스턴스에서 이벤트 뷰어를 사용하여 네임스페이스가 완전히 도착한 시간을 확인할 수 있습니다.

1. **이벤트 뷰어** 를 열고 **애플리케이션 및 서비스** 로 이동합니다.
1. **Microsoft\FileSync\Agent\Telemetry** 로 이동하여 엽니다.
1. 완료된 동기화 세션에 해당하는 최신 **9102 이벤트** 를 찾습니다.
1. **세부 정보** 를 선택하고, **SyncDirection** 값이 **Download** 인 이벤트가 표시되는지 확인합니다.
1. 네임스페이스가 서버에 완전히 다운로드된 시간을 확인할 수 있도록 **Scenario**, **FullGhostedSync** 값, **HResult** = **0** 인 이벤트가 하나 있습니다.
1. 해당 이벤트를 놓친 경우 **SyncDirection** = **Download** 이고 **Scenario** =  **"RegularSync"** 인 다른 **9102 이벤트** 를 찾아볼 수도 있습니다. 이러한 이벤트 중 하나를 찾았다는 것은 현재 동기화할 항목이 있는지 여부에 관계 없이 네임스페이스 다운로드가 완료되었으며 동기화가 일반 동기화 세션으로 진행되었다는 뜻입니다.

### <a name="a-final-robocopy"></a>최종 RoboCopy

지금은 온-프레미스 Windows Server 인스턴스와 StorSimple 8100 또는 8600 어플라이언스 간에 차이가 있습니다.

1. 마이그레이션이 진행되는 동안 StorSimple 쪽에서 사용자 또는 앱이 생성한 변경 내용을 업데이트해야 합니다.
1. Azure 파일 동기화를 사용하는 경우: StorSimple 어플라이언스에는 채워진 캐시가 있거나 현재 로컬로 저장된 파일 콘텐츠 없이 네임스페이스만 있는 Windows Server 인스턴스가 있습니다. 최종 RoboCopy는 로컬로 캐시된 파일 콘텐츠를 가능한 만큼 풀링하여 로컬 Azure 파일 동기화 캐시를 신속하게 시작하고 Azure 파일 동기화 서버에 맞게 조정할 수 있습니다.
1. 일부 파일은 잘못된 문자로 인해 마이그레이션 작업에서 남겨질 수 있습니다. 이 경우 Azure 파일 동기화를 사용하는 Windows Server 인스턴스로 이러한 파일을 복사합니다. 나중에 이러한 파일을 조정하여 동기화할 수 있습니다. 특정 공유에 대해 Azure 파일 동기화를 사용하지 않는 경우 StorSimple 볼륨에서 잘못된 문자가 포함된 파일의 이름을 바꾸는 것이 좋습니다. 그런 다음, Azure 파일 공유에 대해 직접 RoboCopy를 실행합니다.

> [!WARNING]
> Windows Server 2019에서 Robocopy는 robocopy의 /MIR 함수를 사용하는 경우 대상 서버에서 Azure 파일 동기화에 의해 계층화된 파일이 원본에서 다시 복사되어 Azure에 다시 업로드되는 문제가 있습니다. 따라서 2019가 아닌 Windows 서버에서 Robocopy를 사용해야 합니다. Windows Server 2016을 선택하는 것이 좋습니다. Windows 업데이트를 통해 문제가 해결되면 이 정보도 업데이트됩니다.

> [!WARNING]
> Azure 파일 공유의 네임스페이스가 서버에 완전히 다운로드되기 전에는 RoboCopy를 시작하면 *안 됩니다*. 자세한 내용은 [네임스페이스가 서버에 완전히 다운로드된 시간 확인](#determine-when-your-namespace-has-fully-synced-to-your-server)을 참조하세요.

 마이그레이션 작업을 마지막으로 실행한 이후에 변경된 파일과 이전에 이러한 작업을 통해 이동하지 않은 파일만 복사하려고 합니다. 마이그레이션이 완료된 후 나중에 서버에서 파일이 이동하지 않은 이유와 관련된 문제를 해결할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](../file-sync/file-sync-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)을 참조하세요.

RoboCopy에는 여러 매개 변수가 있습니다. 다음 예제에서는 완성된 명령과 이러한 매개 변수를 선택하는 이유 목록을 보여줍니다.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

RoboCopy 명령의 원본 및 대상 위치를 구성할 때 원본과 대상의 구조를 검토하여 일치하는지 확인해야 합니다. 마이그레이션 작업의 디렉터리 매핑 기능을 사용하는 경우 루트 디렉터리 구조가 StorSimple 볼륨의 구조와 다를 수 있습니다. 이 경우 하위 디렉터리마다 하나씩 여러 개의 RoboCopy 작업이 필요할 수 있습니다. 명령이 예상대로 수행되는지 확실하지 않은 경우에는 */L* 매개 변수를 사용할 수 있습니다. 이 매개 변수는 실제로 변경하지 않고 명령을 시뮬레이션합니다.

이 RoboCopy 명령은 /MIR을 사용하므로 동일한 파일(예: 계층화된 파일)을 이동하지 않습니다. 하지만 원본 및 대상 경로가 잘못된 경우 /MIR은 StorSimple 원본 경로에 없는 Windows Server 인스턴스 또는 Azure 파일 공유의 디렉터리 구조까지 제거합니다. 마이그레이션된 콘텐츠를 마이그레이션이 진행되는 동안 수행된 최신 변경 내용으로 업데이트하는 목표를 달성하려면 RoboCopy 작업과 정확히 일치해야 합니다.

RoboCopy 로그 파일을 검토하여 남겨진 파일이 있는지 확인합니다. 문제가 있으면 수정하고 RoboCopy 명령을 다시 실행합니다. 관심 있는 파일 또는 폴더와 관련된 미해결 문제를 수정하기 전에는 StorSimple 리소스의 프로비전을 해제하지 마세요.

Azure 파일 동기화를 사용하여 원하는 Azure 파일 공유를 캐시하지 않고 직접 공유 액세스를 선택한 경우:

1. [Azure 파일 공유](storage-how-to-use-files-windows.md#mount-the-azure-file-share)를 로컬 Windows 머신의 네트워크 드라이브로 탑재합니다.
1. StorSimple과 탑재된 Azure 파일 공유 간에 RoboCopy를 수행합니다. 파일이 복사되지 않으면 StorSimple 쪽에서 이름을 수정하여 잘못된 문자를 제거합니다. 그런 다음, RoboCopy를 다시 시도합니다. StorSimple을 불필요하게 회수하는 일 없이, 이전에 나열된 RoboCopy 명령을 여러 번 실행할 수 있습니다.

### <a name="troubleshoot-and-optimize"></a>문제 해결 및 최적화

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

### <a name="user-cut-over"></a>사용자 전환

Azure 파일 동기화를 사용하는 경우 StorSimple 볼륨에 있는 공유와 일치하는 Azure 파일 동기화 사용 Windows Server 인스턴스에서 SMB 공유를 만들어야 합니다. 이 단계를 앞부분에 배치하고 일찍 수행하면 여기서 시간을 낭비하지 않을 수 있습니다. 그러나 이 시점 전에는 누구에게도 Windows Server 인스턴스를 변경하는 액세스 권한이 없어야 합니다.

DFS-N을 배포한 경우 DFN-네임스페이스가 새 서버 폴더 위치를 가리키게 할 수 있습니다. DFS-N을 배포하지 않았으며 Windows Server 인스턴스를 사용하여 8100 또는 8600 어플라이언스를 로컬로 프론팅한 경우 해당 서버를 도메인에서 분리할 수 있습니다. 그런 다음, 새 Azure 파일 동기화 사용 Windows Server 인스턴스를 도메인 가입합니다. 중단이 사용자, 그룹 정책 및 스크립트에 대해 투명하게 유지되도록 이 프로세스 중에 이전 서버와 동일한 서버 이름과 공유 이름을 서버에 지정합니다.

[DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)에 대해 자세히 알아보세요.

## <a name="phase-6-deprovision"></a>6 단계: 프로 비전 해제

리소스의 프로비전을 해제하면 해당 리소스와 해당 데이터의 구성에 대한 액세스 권한이 사라집니다. 프로비전 해제는 실행 취소할 수 없습니다. 다음 사항을 확인하기 전에는 진행하지 마세요.

* 마이그레이션이 완료되었습니다.
* 프로비전을 해제할 StorSimple 파일, 폴더 또는 볼륨 백업에 대한 종속성이 없습니다.

시작하기 전에, 잠시 프로덕션 환경에서 새 Azure 파일 동기화 배포를 관찰하는 것이 좋습니다. 이 시간을 통해 발생 가능성이 있는 문제를 해결할 수 있습니다. Azure 파일 동기화 배포를 적어도 며칠 정도 관찰한 후, 다음 순서로 리소스의 프로비전을 해제하면 됩니다.

1. Azure Portal을 통해 StorSimple Data Manager 리소스의 프로비전을 해제합니다. 모든 DTS 작업이 함께 삭제됩니다. 복사 로그는 쉽게 검색할 수 없습니다. 레코드에 중요한 복사 로그인 경우 프로비전을 해제하기 전에 검색합니다.
1. StorSimple 물리적 어플라이언스가 마이그레이션되었는지 확인하고 등록을 취소합니다. 마이그레이션되었는지 확실하지 않은 경우 진행하지 마세요. 여전히 필요한 리소스를 프로비전 해제하는 경우 데이터 또는 해당 구성을 복구할 수 없습니다.<br>필요에 따라 우선 StorSimple 볼륨 리소스의 프로비전을 해제할 수 있습니다. 그러면 어플라이언스의 데이터가 정리됩니다 이 프로세스는 며칠이 걸릴 수 있으며 어플라이언스의 데이터를 포렌식으로 0으로 처리하지 않습니다. 이것이 중요한 경우 프로비전 해제와 별도로 정책에 따라 디스크 제로화를 처리합니다.
1. StorSimple 디바이스 관리자에 더 이상 등록된 디바이스가 남아 있지 않으면 디바이스 관리자 리소스 자체를 제거할 수 있습니다.
1. 이제 Azure에서 StorSimple 스토리지 계정을 삭제할 시간입니다. 마찬가지로 마이그레이션이 완료되었으며 이 데이터에 종속된 사람 또는 항목이 하나도 없는 것을 확인한 후 계속 진행합니다.
1. 데이터 센터에서 StorSimple 물리적 어플라이언스를 분리합니다.
1. StorSimple 어플라이언스가 본인 소유인 경우에는 PC 재활용이 가능합니다. 임대 디바이스인 경우에는 임대인에게 알리고 디바이스를 적절하게 반환합니다.

마이그레이션이 완료되었습니다.

---

> [!NOTE]
> 여전히 궁금한 점이 있거나 문제가 있나요?</br>
> Microsoft는 microsoft dot :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-migration-email.png" alt-text="com에서 마이그레이션을 Azure Files"::: 도움을 드립니다. 한 단어로 메일 주소 보내기

## <a name="next-steps"></a>다음 단계

* [Azure 파일 동기화: aka.ms/AFS](../file-sync/file-sync-planning.md)에 대해 더 알아봅니다.
* [클라우드 계층화](../file-sync/file-sync-cloud-tiering-overview.md) 정책의 유연성을 알아봅니다.
* Azure 파일 공유에서 [Azure Backup을 사용](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane)하여 스냅샷을 예약하고 백업 보존 일정을 정의합니다.
* Azure Portal에서 일부 파일이 영구적으로 동기화되지 않는 것이 발견되면 [문제 해결 가이드](../file-sync/file-sync-troubleshoot.md)에서 문제 해결 단계를 검토합니다.
