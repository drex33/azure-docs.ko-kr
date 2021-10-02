---
title: Azure 파일 동기화 배포에 대한 계획 | Microsoft Docs
description: 온-프레미스 Windows Server 또는 클라우드 VM에서 여러 Azure 파일 공유를 캐시할 수 있는 서비스인 Azure 파일 동기화를 사용하여 배포를 계획합니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: c4429e0410fb9511d511ce5841876d5fbca173f5
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388099"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Azure 파일 동기화 배포에 대한 계획

:::row:::
    :::column:::
        [![Azure 파일 동기화를 소개하는 인터뷰 및 데모 - 재생하려면 클릭하세요!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure 파일 동기화는 온-프레미스 Windows Server 또는 클라우드 VM에서 여러 Azure 파일 공유를 캐시할 수 있는 서비스입니다. 
        
        이 문서에서는 Azure 파일 동기화 개념 및 기능을 소개합니다. Azure 파일 동기화에 익숙해지면 [Azure 파일 동기화 배포 가이드](file-sync-deployment-guide.md)에 따라 이 서비스를 사용해 보는 것이 좋습니다.        
    :::column-end:::
:::row-end:::

파일은 클라우드의 [Azure 파일 공유](../files/storage-files-introduction.md)에 저장됩니다. Azure 파일 공유는 이러한 서버리스 Azure 파일 공유(SMB)를 직접 탑재하거나 Azure 파일 동기화를 사용하여 온-프레미스에서 Azure 파일 공유를 캐시하는 두 가지 방법으로 사용할 수 있습니다. 선택한 배포 옵션에 따라 배포 계획에서 고려해야 할 측면이 달라집니다. 

- **Azure 파일 공유의 직접 탑재**: Azure Files는 SMB 액세스를 제공하므로 Windows, macOS 및 Linux에서 사용할 수 있는 표준 SMB 클라이언트를 사용하여 온-프레미스 또는 클라우드에서 Azure 파일 공유를 탑재할 수 있습니다. Azure 파일 공유는 서버리스이므로 프로덕션 시나리오를 위해 배포하는 경우 파일 서버 또는 NAS 디바이스를 관리할 필요가 없습니다. 즉 소프트웨어 패치를 적용하거나 실제 디스크를 교환할 필요가 없습니다. 

- **Azure 파일 동기화를 사용하여 온-프레미스에서 Azure 파일 공유 캐시**: Azure 파일 동기화를 사용하면 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 온-프레미스(또는 클라우드) Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. 

## <a name="management-concepts"></a>관리 개념
Azure 파일 동기화 배포에는 다음과 같은 세 가지 기본 관리 개체가 있습니다.

- **Azure 파일 공유**: Azure 파일 공유는 Azure 파일 동기화 관계의 *클라우드 엔드포인트* 를 제공하는 서버리스 클라우드 파일 공유입니다. Azure 파일 공유의 파일은 SMB 또는 FileREST 프로토콜을 통해 직접 액세스할 수 있지만, Azure 파일 공유가 Azure 파일 동기화에서 사용되는 경우 주로 Windows Server 캐시를 통해 파일에 액세스하는 것이 좋습니다. 이는 현재 Windows Server와 같은 효율적인 변경 검색 메커니즘이 Azure Files에 없기 때문입니다. 이에 따라 Azure 파일 공유를 직접 변경하면 서버 엔드포인트로 다시 전파되는 데 시간이 걸립니다.
- **서버 엔드포인트**: Azure 파일 공유에 동기화되는 Windows Server의 경로입니다. 볼륨 또는 볼륨의 루트에 있는 특정 폴더일 수 있습니다. 해당 네임스페이스가 겹치지 않으면 여러 서버 엔드포인트가 동일한 볼륨에 있을 수 있습니다.
- **동기화 그룹**: **클라우드 엔드포인트** 또는 Azure 파일 공유와 서버 엔드포인트 간의 동기화 관계를 정의하는 개체입니다. 동기화 그룹 내 엔드포인트는 서로 동기화된 상태를 유지합니다. 예를 들어 Azure 파일 동기화를 사용하여 관리하려는 두 개의 고유한 파일 세트가 있는 경우 두 개의 동기화 그룹을 만들고 서로 다른 엔드포인트를 각 동기화 그룹에 추가합니다.

### <a name="azure-file-share-management-concepts"></a>Azure 파일 공유 관리 개념
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure 파일 동기화 관리 개념
동기화 그룹은 **스토리지 동기화 서비스** 에 배포됩니다. 이 서비스는 Azure 파일 동기화에 사용할 서버를 등록하고 동기화 그룹 관계를 포함하는 최상위 개체입니다. Storage 동기화 서비스 리소스는 스토리지 계정 리소스의 피어로, Azure 리소스 그룹에 쉽게 배포할 수 있습니다. 스토리지 동기화 서비스는 여러 스토리지 계정과 여러 등록된 Windows Server에서 Azure 파일 공유를 포함하는 동기화 그룹을 만들 수 있습니다.

스토리지 동기화 서비스에서 동기화 그룹을 만들려면 먼저 Windows Server를 스토리지 동기화 서비스에 등록해야 합니다. 그러면 서버 또는 클러스터와 스토리지 동기화 서비스 간의 트러스트 관계를 나타내는 **등록된 서버** 개체가 만들어집니다. 스토리지 동기화 서비스를 등록하려면 먼저 Azure 파일 동기화 에이전트를 서버에 설치해야 합니다. 개별 서버 또는 클러스터를 한 번에 하나의 스토리지 동기화 서비스에만 등록할 수 있습니다.

동기화 그룹에는 하나의 클라우드 엔드포인트 또는 Azure 파일 공유와 하나 이상의 서버 엔드포인트가 포함됩니다. 서버 엔드포인트 개체에는 Azure 파일 동기화의 캐싱 기능을 제공하는 **클라우드 계층화** 기능을 구성하는 설정이 포함되어 있습니다. Azure 파일 공유와 동기화하려면 Azure 파일 공유가 포함된 스토리지 계정이 스토리지 동기화 서비스와 동일한 Azure 지역에 있어야 합니다.

> [!Important]  
> 동기화 그룹의 클라우드 엔드포인트 또는 서버 엔드포인트의 네임스페이스를 변경하고, 파일을 동기화 그룹의 다른 엔드포인트와 동기화할 수 있습니다. 클라우드 엔드포인트(Azure 파일 공유)를 직접 변경하는 경우 변경 사항은 먼저 Azure 파일 동기화 변경 내용 검색 작업으로 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩만 시작됩니다. 자세한 내용은 [Azure Files 질문과 대답](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-change-detection)을 참조하세요.

### <a name="consider-the-count-of-storage-sync-services-needed"></a>필요한 스토리지 동기화 서비스 수 고려
이전 섹션에서는 Azure 파일 동기화를 구성하기 위한 핵심 리소스인 *스토리지 동기화 서비스* 에 대해 설명합니다. Windows Server는 하나의 스토리지 동기화 서비스에만 등록할 수 있습니다. 따라서 하나의 스토리지 동기화 서비스만 배포하고 해당 서비스에 속한 모든 서버를 등록하는 것이 가장 좋습니다. 

다음과 같은 경우에만 여러 스토리지 동기화 서비스를 만듭니다.
* 서로 데이터를 교환해서는 안 되는 고유한 서버 집합이 있는 경우. 이 경우 다른 스토리지 동기화 서비스의 동기화 그룹에서 클라우드 엔드포인트로 이미 사용 중인 Azure 파일 공유와 동기화할 특정 서버 집합을 제외하도록 시스템을 디자인하려고 합니다. 다른 스토리지 동기화 서비스에 등록된 Windows Server는 동일한 Azure 파일 공유와 동기화할 수 없습니다.
* 단일 스토리지 동기화 서비스에서 지원할 수 있는 것보다 더 많은 서버 또는 동기화 그룹을 등록해야 하는 경우. 자세한 내용은 [Azure 파일 동기화 스케일링 목표](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-scale-targets)를 검토합니다.

## <a name="plan-for-balanced-sync-topologies"></a>균형 있는 동기화 토폴로지 계획
리소스를 배포하기 전에 Azure 파일을 공유하는 로컬 서버에서 동기화할 항목을 계획하는 것이 중요합니다. 계획을 세우면 필요한 스토리지 계정 수, Azure 파일 공유 수, 동기화 리소스 수를 결정하는 데 도움이 됩니다. 데이터가 현재 Windows Server 또는 장기간 사용하려는 서버에 있지 않더라도 이러한 고려 사항은 여전히 중요합니다. [마이그레이션 섹션](#migration)의 내용을 참조하면 상황에 적합한 마이그레이션 경로를 결정하는 데 도움이 될 수 있습니다.

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="windows-file-server-considerations"></a>Windows 파일 서버 고려 사항
Windows Server에서 동기화 기능을 사용하도록 설정하려면 다운로드 가능한 Azure 파일 동기화 에이전트를 설치해야 합니다. Azure 파일 동기화 에이전트는 서버 엔드포인트의 변경을 모니터링하고 동기화 세션을 시작하는 백그라운드 Windows 서비스인 `FileSyncSvc.exe` 및 클라우드 계층화와 빠른 재해 복구를 지원하는 파일 시스템 필터인 `StorageSync.sys`의 두 가지 주요 구성 요소를 제공합니다.  

### <a name="operating-system-requirements"></a>운영 체제 요구 사항
Azure 파일 동기화를 지원하는 Windows Server 버전은 다음과 같습니다.

| 버전 | 지원되는 SKU | 지원되는 배포 옵션 |
|---------|----------------|------------------------------|
| Windows Server 2019 | 데이터 센터, 표준 및 IoT | 전체 및 핵심 |
| Windows Server 2016 | 데이터 센터, 표준 및 스토리지 서버 | 전체 및 핵심 |
| Windows Server 2012 R2 | 데이터 센터, 표준 및 스토리지 서버 | 전체 및 핵심 |

이후 버전의 Windows Server는 출시되면 추가될 예정입니다.

> [!Important]  
> Windows 업데이트의 최신 업데이트를 사용하여 Azure 파일 동기화와 함께 사용되는 모든 서버를 최신 상태로 유지하는 것이 좋습니다. 

### <a name="minimum-system-resources"></a>최소 시스템 리소스
Azure 파일 동기화에는 하나 이상의 CPU 및 2GiB 이상의 메모리가 있는 물리적 또는 가상 서버가 필요합니다.

> [!Important]  
> 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM은 2,048MiB 이상의 메모리로 구성해야 합니다.

대부분의 프로덕션 워크로드의 경우 최소 요구 사항만으로 Azure 파일 동기화 서버를 구성하지 않는 것이 좋습니다. 자세한 내용은 [추천 시스템 리소스](#recommended-system-resources)를 참조하세요.

### <a name="recommended-system-resources"></a>추천 시스템 리소스
서버 기능 또는 애플리케이션과 마찬가지로 Azure 파일 동기화의 시스템 리소스 요구 사항은 배포 규모에 따라 결정됩니다. 서버에서 더 큰 규모로 배포하려면 더 많은 시스템 리소스가 필요합니다. Azure 파일 동기화의 경우 서버 엔드포인트의 개체 수와 데이터 세트의 변동에 따라 크기 조정이 결정됩니다. 단일 서버의 여러 동기화 그룹에 서버 엔드포인트가 있을 수 있으며, 다음 표에 나열된 개체 수는 서버가 연결된 전체 네임스페이스 크기를 나타냅니다. 

예를 들어, 서버 엔드포인트 A(1,000만 개 개체 포함) + 서버 엔드포인트 B(1,000만 개 개체 포함) = 2,000만 개 개체입니다. 이 배포 예에서는 안정 상태의 경우 8개의 CPU와 16GiB의 메모리, 초기 마이그레이션의 경우 48GiB의 메모리(가능한 경우)를 추천합니다.
 
네임스페이스 데이터는 성능상의 이유로 메모리에 저장됩니다. 따라서 네임스페이스가 클수록 효율적인 성능을 유지하기 위해 더 많은 메모리가 필요하며, 변동이 많을수록 이를 처리하기 위해 더 많은 CPU가 필요합니다. 
 
다음 표에는 네임스페이스의 크기와 일반적인 범용 파일 공유의 용량 변환이 모두 제공되었으며, 평균 파일 크기는 512KiB입니다. 파일 크기가 더 작은 경우 동일한 용량의 메모리를 추가하는 것이 좋습니다. 메모리 구성은 네임스페이스 크기에 맞게 설정합니다.

| 네임스페이스 크기 - 파일 및 디렉터리 수(100만)  | 일반 용량(TiB)  | CPU 코어 수  | 추천 메모리(GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8(초기 동기화)/2(일반적인 변동)      |
| 5        | 2.3     | 2        | 16(초기 동기화)/4(일반적인 변동)    |
| 10       | 4.7     | 4        | 32(초기 동기화)/8(일반적인 변동)   |
| 30       | 14.0    | 8        | 48(초기 동기화)/16(일반적인 변동)   |
| 50       | 23.3    | 16       | 64(초기 동기화)/32(일반적인 변동)  |
| 100*     | 46.6    | 32       | 128(초기 동기화)/32(일반적인 변동)  |

\*1억 개 이상의 파일과 디렉터리를 동기화하는 것은 현재 추천되지 않습니다. 이는 테스트된 임계값에 기반한 소프트 제한입니다. 자세한 내용은 [Azure 파일 동기화 크기 조정 목표](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-scale-targets)를 참조하세요.

> [!TIP]
> 네임스페이스의 초기 동기화는 집약적인 작업이므로 초기 동기화가 완료될 때까지 더 많은 메모리를 할당하는 것이 좋습니다. 이는 반드시 필요한 것이 아니지만 초기 동기화 속도를 높일 수 있습니다. 
> 
> 일반적인 변동은 매일 변경되는 네임스페이스의 0.5%입니다. 더 높은 수준의 변동에는 더 많은 CPU를 추가하는 것이 좋습니다. 

- NTFS 파일 시스템으로 포맷되어 로컬로 연결된 볼륨

### <a name="evaluation-cmdlet"></a>평가 cmdlet
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 cmdlet을 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 cmdlet은 지원되지 않는 문자 또는 운영 체제 버전과 같은 데이터 세트 및 파일 시스템과 관련된 잠재적인 문제를 확인합니다. 이 확인에는 아래에서 설명하는 기능 전부는 아니지만 대부분이 포함됩니다. 배포가 원활하게 진행되도록 하려면 이 섹션의 나머지 부분을 자세히 참조하는 것이 좋습니다. 

평가 cmdlet은 [Azure PowerShell 설치 및 구성](/powershell/azure/install-Az-ps)의 지침에 따라 설치할 수 있는 Az PowerShell 모듈을 설치하면 설치될 수 있습니다.

#### <a name="usage"></a>사용  
평가 도구는 몇 가지 다른 방법으로 호출할 수 있습니다. 즉 시스템 검사, 데이터 세트 검사 또는 둘 다를 수행할 수 있습니다. 시스템 검사 및 데이터 세트 검사를 모두 수행하려면 다음을 수행합니다. 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

데이터 세트만 테스트하려면 다음을 수행합니다.
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
시스템 요구 사항만 테스트하려면 다음을 수행합니다.
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
결과를 CSV 형식으로 표시하려면 다음을 수행합니다.
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>파일 시스템 호환성
Azure 파일 동기화는 직접 연결된 NTFS 볼륨에서만 지원됩니다. Windows Server의 DAS(직접 연결된 스토리지)는 Windows Server 운영 체제에서 파일 시스템을 소유하고 있음을 의미합니다. DAS는 디스크를 파일 서버에 물리적으로 연결하거나, 가상 디스크를 파일 서버 VM(예: Hyper-V에서 호스팅하는 VM)에 연결하거나, ISCSI를 통해 연결하여 제공할 수 있습니다.

NTFS 볼륨만 지원되며, ReFS, FAT, FAT32 및 기타 파일 시스템은 지원되지 않습니다.

다음 표에서는 NTFS 파일 시스템 기능의 interop 상태를 보여 줍니다. 

| 기능 | 상태 지원 | 메모 |
|---------|----------------|-------|
| ACL(액세스 제어 목록) | 완벽하게 지원 | Windows 스타일의 임의 액세스 제어 목록은 Azure 파일 동기화에서 유지되며, 서버 엔드포인트의 Windows Server에서 적용됩니다. 또한 Azure 파일 공유를 직접 탑재하는 경우 ACL을 적용할 수 있지만, 이 경우 추가 구성이 필요합니다. 자세한 내용은 [ID 섹션](#identity)을 참조하세요. |
| 하드 링크 | 건너뜀 | |
| 바로 가기 링크 | 건너뜀 | |
| 탑재 지점 | 부분적으로 지원됨 | 탑재 지점은 서버 엔드포인트의 루트일 수 있지만, 서버 엔드포인트의 네임스페이스에 포함된 경우 건너뜁니다. |
| 분기 동기화 | 건너뜀 | 분산 파일 시스템 DfrsrPrivate 및 DFSRoots 폴더를 예로 들 수 있습니다. |
| 재분석 지점 | 건너뜀 | |
| NTFS 압축 | 완벽하게 지원 | |
| 스파스 파일 | 완벽하게 지원 | 스파스 파일은 동기화되지만(차단되지 않음) 전체 파일로 클라우드와 동기화됩니다. 클라우드(또는 다른 서버)에서 파일 콘텐츠가 변경될 경우 변경 내용이 다운로드되면 파일은 더 이상 스파스 파일이 아닙니다. |
| ADS(대체 데이터 스트림) | 보존되지만 동기화되지 않음 | 예를 들어, 파일 분류 인프라에서 만들어진 분류 태그는 동기화되지 않습니다. 각 서버 엔드포인트의 파일에 대한 기존 분류 태그는 그대로 유지됩니다. |

<a id="files-skipped"></a>또한 Azure 파일 동기화는 다음과 같은 특정 임시 파일 및 시스템 폴더도 건너뜁니다.

| 파일/폴더 | 참고 |
|-|-|
| pagefile.sys | 시스템에 특정된 파일 |
| Desktop.ini | 시스템에 특정된 파일 |
| thumbs.db | 썸네일의 임시 파일 |
| ehthumbs.db | 미디어 썸네일의 임시 파일 |
| ~$\*.\* | Office 임시 파일 |
| \*.tmp | 임시 파일 |
| \*.laccdb | Access DB 잠금 파일|
| 635D02A9D91C401B97884B82B3BCDAEA.* | 내부 동기화 파일|
| \\시스템 볼륨 정보 | 볼륨에 특정된 폴더 |
| $RECYCLE.BIN| 폴더 |
| \\SyncShareState | 동기화할 폴더 |

### <a name="consider-how-much-free-space-you-need-on-your-local-disk"></a>로컬 디스크에 필요한 여유 공간의 양을 고려합니다.
Azure 파일 동기화 사용을 계획할 때 서버 엔드포인트를 사용하려는 로컬 디스크에 필요한 여유 공간의 양을 고려합니다.

Azure 파일 동기화를 사용하면 로컬 디스크에서 다음 공간을 차지해야 합니다.
- 클라우드 계층화 사용:
    - 계층화된 파일에 대한 재분석 지점
    - Azure 파일 동기화 메타데이터 데이터베이스
    - Azure 파일 동기화 열 저장소
    - 핫 캐시에서 완전히 다운로드한 파일(있는 경우)
    - 사용 가능한 볼륨 공간 정책 요구 사항

- 클라우드 계층화 사용하지 않음:  
    - 완전히 다운로드한 파일
    - Azure 파일 동기화 열 저장소
    - Azure 파일 동기화 메타데이터 데이터베이스

예제를 사용하여 로컬 디스크에서 사용 가능한 공간의 양을 예측하는 방법을 보여 줍니다. Azure Windows VM에 Azure 파일 동기화 에이전트를 설치하고 디스크 F에서 서버 엔드포인트를 만들 계획이라고 가정해 보겠습니다. 100만 개의 파일이 있고 모두 100,000개의 디렉터리 및 4KiB의 디스크 클러스터 크기로 계층화하려고 합니다. 디스크 크기는 1,000GiB입니다. 클라우드 계층화를 사용하고 사용 가능한 볼륨 공간 정책을 20%로 설정하려고 합니다. 

1. NTFS는 계층화된 각 파일에 대해 클러스터 크기를 할당합니다. 100만 개 파일 * 4KiB 클러스터 크기 = 4,000,000KiB(4GiB)
> [!Note]  
> 계층화된 파일이 차지하는 공간은 NTFS에서 할당합니다. 따라서 UI에 표시되지 않습니다.
3. 동기화 메타데이터는 항목당 클러스터 크기를 차지합니다. (100만 개 파일 + 100,000개 디렉터리) * 4KiB 클러스터 크기 = 4,400,000KiB(4.4GiB)
4. Azure 파일 동기화 열 저장소는 파일당 1.1KiB를 차지합니다. 100만 개 파일 * 1.1KiB = 1,100,000KiB(1.1GiB)
5. 사용 가능한 볼륨 공간 정책은 20%입니다. 1,000GiB * 0.2 = 200GiB

이 경우 Azure 파일 동기화에는 이 네임스페이스에 대한 약 209,500,000KiB(209.5GiB)의 공간이 필요합니다. 이 디스크에 필요한 여유 공간이 충분한지 파악하기 위해 원하는 추가 여유 공간에 이 크기를 추가합니다.

### <a name="failover-clustering"></a>장애 조치(Failover) 클러스터링
1. Windows Server 장애 조치(Failover) 클러스터링은 "범용 파일 서버" 배포 옵션의 Azure 파일 동기화에서 지원됩니다. 
2. Azure 파일 동기화에서 지 원하는 유일한 시나리오는 클러스터 된 디스크가 있는 서버 장애 조치 (Failover) 클러스터 Windows입니다.
3. 장애 조치 (Failover) 클러스터링은 "응용 프로그램 데이터에 대 한 스케일 아웃 파일 서버" (SOFS) 또는 Csv (클러스터 공유 볼륨) 또는 로컬 디스크에서 지원 되지 않습니다.

> [!Note]  
> 동기화가 제대로 작동하려면 장애 조치(Failover) 클러스터의 모든 노드에 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="data-deduplication"></a>데이터 중복 제거
**Windows Server 2016 및 Windows Server 2019**   
데이터 중복 제거는 Windows Server 2016 및 Windows Server 2019의 볼륨에 있는 하나 이상의 서버 엔드포인트에서 클라우드 계층화의 사용 여부에 관계없이 지원됩니다. 클라우드 계층화를 사용하도록 설정된 볼륨에서 데이터 중복 제거를 사용하도록 설정하면 더 많은 스토리지를 프로비저닝하지 않고도 온-프레미스에서 더 많은 파일을 캐시할 수 있습니다. 

클라우드 계층화를 사용하도록 설정된 볼륨에서 데이터 중복 제거를 사용하도록 설정하면 서버 엔드포인트 위치 내의 중복 제거 최적화 파일이 클라우드 계층화 정책 설정에 따라 일반 파일과 비슷하게 계층화됩니다. 중복 제거 최적화 파일이 계층화되면 볼륨의 다른 파일에서 더 이상 참조하지 않는 불필요한 청크를 제거하여 디스크 공간을 회수하기 위해 데이터 중복 제거 가비지 수집 작업이 자동으로 실행됩니다.

볼륨 절약은 서버에만 적용되며, Azure 파일 공유의 데이터는 중복 제거되지 않습니다.

> [!Note]  
> Windows Server 2019에서 클라우드 계층화를 사용하도록 설정된 볼륨에서 데이터 중복 제거를 지원하려면 Windows 업데이트 [KB4520062 - 2019년 10월](https://support.microsoft.com/help/4520062) 또는 이후 월별 롤업 업데이트를 설치해야 하고 Azure 파일 동기화 에이전트 버전 12.0.0.0 이상이 필요합니다.

**Windows Server 2012 R2**  
Azure 파일 동기화는 Windows Server 2012 R2에서 동일한 볼륨의 데이터 중복 제거 및 클라우드 계층화를 지원하지 않습니다. 볼륨에서 데이터 중복 제거를 사용하도록 설정하면 클라우드 계층화를 사용하지 않도록 설정해야 합니다. 

**참고 사항**
- Azure 파일 동기화 에이전트를 설치하기 전에 데이터 중복 제거가 설치되면, 동일한 볼륨에서 데이터 중복 제거 및 클라우드 계층화를 지원하기 위해 다시 시작해야 합니다.
- 클라우드 계층화를 사용하도록 설정한 후 볼륨에서 데이터 중복 제거를 사용하도록 설정하면, 초기 중복 제거 최적화 작업을 통해 아직 계층화되지 않은 볼륨에서 파일을 최적화하며 클라우드 계층화에 다음과 같은 영향을 미칩니다.
    - 사용 가능한 공간 정책은 열 지도를 사용하여 볼륨의 사용 가능한 공간에 따라 파일을 계속 계층화합니다.
    - 날짜 정책은 파일에 액세스하는 중복 제거 최적화 작업으로 인해 다른 방법으로 계층화될 수 있는 파일의 계층화를 건너뜁니다.
- 지속적인 중복 제거 최적화 작업의 경우 파일이 아직 계층화되지 않았으면 [MinimumFileAgeDays](/powershell/module/deduplication/set-dedupvolume) 데이터 중복 제거 설정에 따라 날짜 정책을 사용하는 클라우드 계층화가 지연됩니다. 
    - 예제: MinimumFileAgeDays 설정이 7일이고 클라우드 계층화 날짜 정책이 30일인 경우 날짜 정책은 37일 후에 파일을 계층화합니다.
    - 참고: 파일이 Azure 파일 동기화를 통해 계층화되면 중복 제거 최적화 작업에서 해당 파일을 건너뜁니다.
- Azure 파일 동기화 에이전트가 설치된 Windows Server 2012 R2를 실행하는 서버가 Windows Server 2016 또는 Windows Server 2019로 업그레이드되면, 동일한 볼륨에서 데이터 중복 제거 및 클라우드 계층화를 지원하기 위해 다음 단계를 수행해야 합니다.  
    - Windows Server 2012 R2용 Azure 파일 동기화 에이전트를 제거하고 서버를 다시 시작합니다.
    - 새 서버 운영 체제 버전(Windows Server 2016 또는 Windows Server 2019)용 Azure 파일 동기화 에이전트를 다운로드합니다.
    - Azure 파일 동기화 에이전트를 설치하고 서버를 다시 시작합니다.  
    
    참고: 에이전트를 제거하고 다시 설치하면 서버의 Azure 파일 동기화 구성 설정이 유지됩니다.

### <a name="distributed-file-system-dfs"></a>분산 파일 시스템(DFS)
Azure 파일 동기화에서는 DFS-N(DFS 네임스페이스) 및 DFS-R(DFS 복제)과의 상호 작용을 지원합니다.

**DFS 네임스페이스(DFS-N)** : DFS-N 서버에서 Azure 파일 동기화가 완전히 지원됩니다. 하나 이상의 DFS-N 멤버에 Azure 파일 동기화 에이전트를 설치하면 서버 엔드포인트 및 클라우드 엔드포인트 간에 데이터를 동기화할 수 있습니다. 자세한 내용은 [DFS 네임스페이스 개요](/windows-server/storage/dfs-namespaces/dfs-overview)를 참조하세요.
 
**DFS 복제(DFS-R)** : DFS-R 및 Azure 파일 동기화는 모두 복제 솔루션이므로 대부분의 경우, DFS-R을 Azure 파일 동기화로 대체하는 것이 좋습니다. 하지만 DFS-R과 Azure 파일 동기화를 함께 사용해야 하는 몇 가지 시나리오가 있습니다.

- DFS-R 배포에서 Azure 파일 동기화 배포로 마이그레이션하는 중입니다. 자세한 내용은 [DFS 복제(DFS-R) 배포를 Azure 파일 동기화로 마이그레이션](file-sync-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)을 참조하세요.
- 파일 데이터 복사본이 필요한 모든 온-프레미스 서버를 인터넷에 직접 연결할 수는 없습니다.
- 분기 서버는 Azure 파일 동기화를 사용할 단일 허브 서버에 데이터를 통합합니다.

Azure 파일 동기화 및 DFS-R을 나란히 작동하려면 다음을 수행합니다.

1. Azure 파일 동기화 클라우드 계층화는 DFS-R 복제 폴더를 포함하는 볼륨에서 사용하지 않도록 설정해야 합니다.
2. 서버 엔드포인트는 DFS-R 읽기 전용 복제 폴더에 대해 구성할 수 없습니다.

자세한 내용은 [DFS 복제 개요](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj127250(v=ws.11))를 참조하세요.

### <a name="sysprep"></a>Sysprep
sysprep은 Azure 파일 동기화 에이전트가 설치된 서버에서 사용할 수 없으며, 그렇지 않으면 예기치 않은 결과가 발생할 수 있습니다. 에이전트 설치 및 서버 등록은 서버 이미지 배포 및 sysprep 최소 설치 완료 후 발생해야 합니다.

### <a name="windows-search"></a>Windows 검색
클라우드 계층화가 서버 엔드포인트에서 활성화되면 계층화된 파일은 건너뛰고 Windows 검색을 통해 인덱싱되지 않습니다. 계층화되지 않은 파일은 제대로 인덱싱됩니다.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>다른 HSM(계층적 스토리지 관리) 솔루션
다른 HSM 솔루션은 Azure 파일 동기화와 함께 사용하면 안 됩니다.

## <a name="performance-and-scalability"></a>성능 및 확장성

Azure 파일 동기화 에이전트가 Azure 파일 공유에 연결된 Windows Server 컴퓨터에서 실행되므로 유효한 동기화 성능은 인프라에 포함된 많은 요소(Windows Server 및 기본 디스크 구성,서버와 Azure Storage 간의 네트워크 대역폭, 파일 크기, 데이터 세트의 총 크기 및 데이터 세트의 작업 등)에 따라 달라집니다. Azure 파일 동기화가 파일 수준에서 작동하므로 Azure 파일 동기화 기반 솔루션의 성능 특성은 초당 처리된 개체(예: 파일 및 디렉터리)의 수에서 정확하게 측정됩니다.

Azure Portal 또는 SMB를 사용하여 Azure 파일 공유에서 변경한 사항은 즉시 검색되지 않고 서버 엔드포인트의 변경 사항처럼 복제됩니다. Azure Files에는 아직 변경 알림/저널링이 없어서 파일이 변경될 때 동기화 세션을 자동으로 시작할 방법이 없습니다. Windows Server에서 Azure 파일 동기화는 [Windows USN 저널링](/windows/win32/fileio/change-journals)을 사용하여 파일이 변경될 때 자동으로 동기화 세션을 시작합니다.

Azure 파일 공유의 변경 사항을 검색하기 위해 Azure 파일 동기화에는 변경 검색 작업이라는 예약된 작업이 있습니다. 변경 검색 작업은 파일 공유의 모든 파일을 열거한 다음 해당 파일의 동기화 버전과 비교합니다. 변경 검색 작업에서 파일이 변경된 것으로 판단되면 Azure 파일 동기화는 동기화 세션을 시작합니다. 변경 검색 작업은 24시간마다 시작됩니다. 변경 검색 작업은 Azure 파일 공유의 모든 파일을 열거하여 작동하므로 변경 검색은 큰 네임스페이스가 작은 네임스페이스보다 오래 걸립니다. 큰 네임스페이스의 경우 변경된 파일을 확인하는 것이 24시간마다 한 번 이상이 걸릴 수 있습니다.

자세한 내용은 [Azure 파일 동기화 성능 메트릭](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-performance-metrics) 및 [Azure 파일 동기화 스케일링 목표](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-scale-targets)를 참조하세요.

## <a name="identity"></a>ID
Azure 파일 동기화는 동기화 설정 이외의 특별한 설정 없이 표준 AD 기반 ID를 사용하여 작동합니다. Azure 파일 동기화를 사용하는 경우 일반적으로 대부분의 액세스에서 Azure 파일 공유가 아니라 Azure 파일 동기화 캐싱 서버를 통과합니다. 서버 엔드포인트는 Windows Server에 있고 Windows Server는 오랫동안 AD 및 Windows 스타일 ACL을 지원했으므로 스토리지 동기화 서비스에 등록된 Windows 파일 서버가 도메인에 조인되어 있는지 확인하는 것 외에는 아무 작업도 필요하지 않습니다. Azure 파일 동기화는 ACL을 Azure 파일 공유의 파일에 저장하고 이를 모든 서버 엔드포인트에 복제합니다.

Azure 파일 공유를 직접 변경한 경우에도 동기화 그룹의 서버 엔드포인트와 동기화하는 데 시간이 더 오래 걸리므로 클라우드에서 파일 공유에 대한 AD 권한을 직접 적용할 수도 있습니다. 이렇게 하려면 Windows 파일 서버가 도메인에 조인되는 방법과 마찬가지로 스토리지 계정을 온-프레미스 AD의 도메인에 조인해야 합니다. 스토리지 계정을 고객 소유의 Active Directory의 도메인에 조인하는 방법에 대한 자세한 내용은 [Azure Files Active Directory 개요](../files/storage-files-active-directory-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)를 참조하세요.

> [!Important]  
> Azure 파일 동기화를 성공적으로 배포하기 위해 스토리지 계정을 Active Directory의 도메인에 조인할 필요가 없습니다. 이는 사용자가 Azure 파일 공유를 직접 탑재할 때 Azure 파일 공유에서 온-프레미스 ACL을 적용할 수 있도록 하는 엄격한 선택적 단계입니다.

## <a name="networking"></a>네트워킹
Azure 파일 동기화 에이전트는 항상 443 포트에서 HTTPS를 사용하는 Azure 파일 동기화 REST 프로토콜 및 FileREST 프로토콜을 사용하여 스토리지 동기화 서비스 및 Azure 파일 공유와 통신합니다. SMB는 Windows Server와 Azure 파일 공유 간에 데이터를 업로드하거나 다운로드하는 데 사용되지 않습니다. 대부분의 조직에서는 443 포트를 통한 HTTPS 트래픽을 허용하므로 대부분의 웹 사이트를 방문하기 위한 요구 사항에 따라 특별한 네트워킹 구성은 일반적으로 Azure 파일 동기화를 배포하는 데 필요하지 않습니다.

조직의 정책 또는 고유한 규정 요구 사항에 따라 Azure와의 더 제한적인 통신이 필요할 수 있으므로 Azure 파일 동기화에서 네트워킹을 구성할 수 있는 몇 가지 메커니즘을 제공합니다. 요구 사항에 따라 다음을 수행할 수 있습니다.

- ExpressRoute 또는 Azure VPN을 통해 동기화 및 파일 업로드/다운로드 트래픽을 터널링합니다. 
- Azure Files 및 Azure 네트워킹 기능(예: 서비스 엔드포인트 및 프라이빗 엔드포인트)을 활용합니다.
- 사용자 환경에서 프록시를 지원하도록 Azure 파일 동기화를 구성합니다.
- Azure 파일 동기화에서 네트워크 활동을 제한합니다.

> [!Important]  
> Azure 파일 동기화는 인터넷 라우팅을 지원 하지 않습니다. 기본 네트워크 라우팅 옵션인 Microsoft 라우팅은 Azure 파일 동기화에서 지원됩니다.

Azure 파일 동기화 및 네트워킹에 대한 자세한 내용은 [Azure 파일 동기화 네트워킹 고려 사항](file-sync-networking-overview.md)을 참조하세요.

## <a name="encryption"></a>암호화
Azure 파일 동기화를 사용하는 경우 고려해야 할 세 가지 암호화 계층이 있습니다. 즉 Windows Server 스토리지의 저장 데이터 암호화, Azure 파일 동기화 에이전트와 Azure 간의 전송 중 데이터 암호화 및 Azure 파일 공유의 저장 데이터 암호화입니다. 

### <a name="windows-server-encryption-at-rest"></a>Windows Server 저장 데이터 암호화 
일반적으로 Azure 파일 동기화에서 작동하는 Windows Server에서 데이터를 암호화하는 두 가지 전략이 있습니다. 즉 파일 시스템 아래의 암호화(파일 시스템 및 여기에 기록된 모든 데이터가 암호화됨) 및 파일 형식 자체 내의 암호화입니다. 이러한 방법은 상호 배타적이지 않습니다. 암호화 목적이 다르므로 원하는 경우 함께 사용할 수 있습니다.

암호화를 파일 시스템 아래에 제공하기 위해 Windows Server에서 BitLocker 받은 편지함을 제공합니다. BitLocker는 Azure 파일 동기화에 완전히 투명합니다. BitLocker와 같은 암호화 메커니즘을 사용하는 주된 이유는 누군가가 디스크를 도용하여 온-프레미스 데이터 센터에서 데이터가 물리적으로 반출되지 않도록 방지하고 데이터에 대한 무단 읽기/쓰기 작업을 수행하는 권한이 없는 OS를 사이드로드하지 않도록 방지할 수 있기 때문입니다. BitLocker에 대한 자세한 내용은 [BitLocker 개요](/windows/security/information-protection/bitlocker/bitlocker-overview)를 참조하세요.

BitLocker와 비슷하게 작동하는 타사 제품은 NTFS 볼륨 아래에 위치한다는 점에서 Azure 파일 동기화와 완전히 투명하게 작동해야 합니다. 

데이터를 암호화하는 다른 주요 방법은 애플리케이션에서 파일을 저장할 때 파일의 데이터 스트림을 암호화하는 것입니다. 일부 애플리케이션에서 기본적으로 이 작업을 수행할 수 있지만 일반적으로는 그렇지 않습니다. 파일 데이터 스트림을 암호화하는 방법의 예로 AIP(Azure Information Protection)/Azure RMS(Azure Rights Management Services)/Active Directory RMS가 있습니다. AIP/RMS와 같은 암호화 메커니즘을 사용하는 주된 이유는 사용자가 플래시 드라이브와 같은 대체 위치에 복사하거나 권한이 없는 사용자에게 이메일로 전송하여 파일 공유에서 데이터가 반출되지 않도록 방지할 수 있기 때문입니다. 파일의 데이터 스트림이 파일 형식의 일부로 암호화되는 경우 이 파일은 Azure 파일 공유에서 계속 암호화됩니다. 

Azure 파일 동기화는 파일 시스템 위에 있지만 파일의 데이터 스트림 아래에 있는 NTFS EFS(NTFS 암호화된 파일 시스템) 또는 타사 암호화 솔루션과 상호 운용되지 않습니다. 

### <a name="encryption-in-transit"></a>전송 중 암호화

> [!NOTE]
> Azure 파일 동기화 서비스는 2020년 8월 1일에 TLS 1.0 및 1.1 지원을 제거합니다. 지원되는 모든 Azure 파일 동기화 에이전트 버전은 기본적으로 TLS 1.2를 이미 사용하고 있습니다. 서버에서 TLS 1.2를 사용하지 않도록 설정했거나 프록시가 사용되는 경우 이전 버전의 TLS를 사용하고 있을 수 있습니다. 프록시를 사용하는 경우 프록시 구성을 확인하는 것이 좋습니다. 2020년 5월 1일 이후에 추가된 Azure 파일 동기화 서비스 지역은 TLS 1.2만 지원하며, TLS 1.0 및 1.1 지원은 2020년 8월 1일에 기존 지역에서 제거됩니다.  자세한 내용은 [문제 해결 가이드](file-sync-troubleshoot.md#tls-12-required-for-azure-file-sync)를 참조하세요.

Azure 파일 동기화 에이전트는 항상 443 포트에서 HTTPS를 사용하는 Azure 파일 동기화 REST 프로토콜 및 FileREST 프로토콜을 사용하여 스토리지 동기화 서비스 및 Azure 파일 공유와 통신합니다. Azure 파일 동기화는 암호화되지 않은 요청을 HTTP를 통해 보내지 않습니다. 

Azure 스토리지 계정에는 전송 중 암호화를 요구하는 스위치가 포함되어 있으며, 이는 기본적으로 사용하도록 설정되어 있습니다. 스토리지 계정 수준의 스위치가 사용하지 않도록 설정되어 있는 경우에도(Azure 파일 공유에 대한 암호화되지 않은 연결이 가능하다는 것을 의미함) Azure 파일 동기화는 여전히 암호화된 채널만 사용하여 파일 공유에 액세스합니다.

전송 중 암호화를 스토리지 계정에 사용하지 않도록 설정하는 주요 이유는 Windows Server 2008 R2 또는 이전 Linux 배포와 같은 이전 운영 체제에서 실행하여 Azure 파일 공유와 직접 통신해야 하는 레거시 애플리케이션을 지원하기 때문입니다. 레거시 애플리케이션에서 파일 공유의 Windows Server 캐시와 통신하는 경우 이 설정을 전환해도 아무런 영향을 주지 않습니다. 

전송 중 데이터 암호화를 사용하도록 설정하는 것이 좋습니다.

전송 중 암호화에 대한 자세한 내용은 [Azure Storage에서 보안 전송 필요](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

### <a name="azure-file-share-encryption-at-rest"></a>Azure 파일 공유 저장 데이터 암호화
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>스토리지 계층
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

#### <a name="regional-availability"></a>국가별 가용성
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Azure 파일 동기화 지역 가용성

지역별 가용성에 대해서는 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=storage)을 참조하세요.

다음 지역에서는 Azure 파일 동기화를 사용하기 전에 Azure Storage에 대한 액세스를 요청해야 합니다.

- 프랑스 남부
- 남아프리카 공화국 서부
- 아랍에미리트 중부

이러한 지역에 대한 액세스를 요청하려면 [이 문서](https://azure.microsoft.com/global-infrastructure/geographies/)의 프로세스를 따르세요.

## <a name="redundancy"></a>중복
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> 지역 중복 및 지리적 영역 중복 스토리지는 스토리지를 보조 지역에 수동으로 장애 조치할 수 있습니다. Azure 파일 동기화를 사용하는 경우 데이터를 손실할 가능성이 높으므로 재해 발생 시 이 작업을 수행하지 않는 것이 좋습니다. 스토리지의 수동 장애 조치를 시작하려는 재해가 발생하는 경우 Microsoft에서 지원 사례를 열어 Azure 파일 동기화에서 보조 엔드포인트와의 동기화를 다시 시작하도록 해야 합니다.

## <a name="migration"></a>마이그레이션
기존 Windows 파일 서버 2012R2 이상이 있는 경우 데이터를 새 서버로 이동할 필요 없이 Azure 파일 동기화를 직접 설치할 수 있습니다. Azure 파일 동기화 채택의 일부로서 새 Windows 파일 서버로 마이그레이션할 계획이거나 데이터가 현재 NAS(Network Attached Storage)에 있는 경우 이 데이터와 함께 Azure 파일 동기화를 사용하는 몇 가지 가능한 마이그레이션 방법이 있습니다. 선택해야 하는 마이그레이션 방법은 현재 데이터가 있는 위치에 따라 달라집니다. 

시나리오에 대한 자세한 지침은 [Azure 파일 동기화 및 Azure 파일 공유 마이그레이션 개요](../files/storage-files-migration-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) 문서를 참조하세요.

## <a name="antivirus"></a>바이러스 백신
바이러스 백신은 파일에서 알려진 악성 코드를 검색하는 방식으로 작동하므로 바이러스 백신 제품은 계층화된 파일을 회수하여 높은 송신 비용을 초래할 수 있습니다. Azure 파일 동기화 에이전트의 버전 4.0 이상에서 계층화된 파일에는 보안 Windows 특성 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS가 설정되어 있습니다. 이 특성이 설정된 파일 읽기를 건너뛰도록(대부분 자동으로 수행) 솔루션을 구성하는 방법을 소프트웨어 공급업체에 문의하세요. 

Microsoft의 사내 바이러스 백신 솔루션, Windows Defender 및 SCEP(System Center Endpoint Protection) 모두는 이 특성이 설정된 파일 읽기를 자동으로 건너뜁니다. 이러한 솔루션을 테스트하여 하나의 사소한 문제를 확인했습니다. 즉 기존 동기화 그룹에 서버를 추가하면 800바이트보다 작은 파일이 새 서버에서 회수(다운로드)됩니다. 이러한 파일은 새 서버에 남아 있지만 계층화 크기 요구 사항(64kb 초과)을 충족하지 않으므로 계층화되지 않습니다.

> [!Note]  
> 바이러스 백신 공급업체는 Microsoft 다운로드 센터에서 다운로드할 수 있는 [Azure 파일 동기화 바이러스 백신 호환성 테스트 도구 모음](https://www.microsoft.com/download/details.aspx?id=58322)을 사용하여 제품과 Azure 파일 동기화 간의 호환성을 확인할 수 있습니다.

## <a name="backup"></a>Backup 
클라우드 계층화를 사용하는 경우 서버 엔드포인트 또는 서버 엔드포인트가 있는 VM을 직접 백업하는 솔루션을 사용해서는 안 됩니다. 클라우드 계층화를 사용하면 전체 데이터 세트가 Azure 파일 공유에 상주하면서 데이터의 하위 집합만 서버 엔드포인트에 저장됩니다. 사용된 백업 솔루션에 따라 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS 특성 집합으로 인해 계층화된 파일을 건너뛰고 백업하지 않거나 이러한 파일이 디스크로 회수되어 높은 송신 요금이 발생합니다. 클라우드 백업 솔루션을 사용하여 Azure 파일 공유를 직접 백업하는 것이 좋습니다. 자세한 내용은 [Azure 파일 공유 백업 정보](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하거나 백업 공급자에게 문의하여 Azure 파일 공유 백업을 지원하는지 확인하세요.

온-프레미스 백업 솔루션을 사용하려는 경우 클라우드 계층화를 사용하지 않도록 설정된 동기화 그룹의 서버에서 백업을 수행해야 합니다. 복원을 수행할 때 볼륨 수준 또는 파일 수준 복원 옵션을 사용합니다. 파일 수준 복원 옵션을 사용하여 복원된 파일은 동기화 그룹의 모든 엔드포인트에 동기화되고 기존 파일은 백업에서 복원된 버전으로 대체됩니다.  볼륨 수준 복원은 Azure 파일 공유 또는 기타 서버 엔드포인트의 최신 파일 버전을 대체하지 않습니다.

> [!WARNING]
> 원본 또는 대상 서버에서 실행되는 Azure 파일 동기화 에이전트와 함께 Robocopy /B를 사용해야 하는 경우 Azure 파일 동기화 에이전트 버전 v12.0 이상으로 업그레이드하세요. v12.0 미만의 에이전트 버전에서 Robocopy /B를 사용하면 복사 중에 계층화된 파일이 손상될 수 있습니다.

> [!Note]  
> BMR(완전 복구) 복원은 예기치 않은 결과가 발생할 수 있으며 현재 지원되지 않습니다.

> [!Note]  
> Azure 파일 동기화 에이전트 버전 9를 사용하는 경우 VSS 스냅샷([이전 버전] 탭 포함)은 이제 클라우드 계층화를 사용하도록 설정된 볼륨에서 지원됩니다. 그러나 PowerShell을 통해 이전 버전과의 호환성을 사용하도록 설정해야 합니다. [방법을 알아보세요](file-sync-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="data-classification"></a>데이터 분류
데이터 분류 소프트웨어가 설치되어 있는 경우 클라우드 계층화를 사용하도록 설정하면 다음 두 가지 이유로 비용이 증가할 수 있습니다.

1. 클라우드 계층화를 사용하면 가장 많이 사용한 파일이 로컬로 캐시되고 가장 덜 사용한 파일이 클라우드의 Azure 파일 공유에 계층화됩니다. 데이터 분류가 파일 공유에 있는 모든 파일을 정기적으로 검색하는 경우 검색할 때마다 클라우드에 계층화된 파일을 회수해야 합니다. 

2. 데이터 분류 소프트웨어가 파일의 데이터 스트림에서 메타데이터를 사용하는 경우 소프트웨어가 분류를 볼 수 있도록 파일을 완전히 회수해야 합니다. 

회수 횟수와 회수되는 데이터 양이 모두 증가하면 비용이 증가할 수 있습니다.

## <a name="azure-file-sync-agent-update-policy"></a>Azure 파일 동기화 에이전트 업데이트 정책
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>다음 단계
* [방화벽 및 프록시 설정 고려](file-sync-firewall-and-proxy.md)
* [Azure Files 배포](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
* [Azure 파일 동기화 배포](file-sync-deployment-guide.md)
* [Azure 파일 동기화 모니터링](file-sync-monitoring.md)
