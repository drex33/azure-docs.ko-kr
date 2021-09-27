---
title: 스토리지 계정 장애 조치(failover) 시작
titleSuffix: Azure Storage
description: 스토리지 계정의 기본 엔드포인트를 사용할 수 없는 경우 계정 장애 조치(failover)를 시작하는 방법을 알아봅니다. 장애 조치(failover)는 스토리지 계정의 주 지역이 되도록 보조 지역을 업데이트합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/07/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ab764fd95168fef768efd0687adb5e2841bfb140
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609292"
---
# <a name="initiate-a-storage-account-failover"></a>스토리지 계정 장애 조치(failover) 시작

어떤 이유로든 지역 중복 스토리지 계정의 기본 엔드포인트를 사용할 수 없게 되면 계정 장애 조치(failover)를 시작할 수 있습니다. 계정 장애 조치(failover)는 스토리지 계정의 기본 엔드포인트가 되도록 보조 엔드포인트를 업데이트합니다. 장애 조치(failover)가 완료되면 클라이언트가 새로운 주 지역에 쓰기 시작할 수 있습니다. 강제 장애 조치(failover)를 사용하면 애플리케이션에 대해 고가용성을 유지할 수 있습니다.

이 문서에서는 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 스토리지 계정에 대해 계정 장애 조치(failover)를 시작하는 방법을 보여 줍니다. 계정 장애 조치(failover)에 대한 자세한 내용은 [재해 복구 및 스토리지 계정 장애 조치(failover)](storage-disaster-recovery-guidance.md)를 참조하세요.

> [!WARNING]
> 일반적으로 계정 장애 조치(failover)를 수행하면 일부 데이터가 손실됩니다. 계정 장애 조치(failover)의 의미를 파악하고 데이터 손실에 대비하려면 [계정 장애 조치(failover) 프로세스 이해](storage-disaster-recovery-guidance.md#understand-the-account-failover-process)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

스토리지 계정에서 계정 장애 조치(failover)를 수행하려면 먼저 스토리지 계정이 지역 복제를 사용하도록 구성되어 있는지 확인합니다. 스토리지 계정은 다음과 같은 중복성 옵션 중 하나를 사용할 수 있습니다.

- GRS(지역 중복 스토리지) 또는 RA-GRS(읽기 액세스 지역 중복 스토리지)
- GZRS(지역 영역 중복 스토리지) 또는 RA-GZRS(읽기 액세스 지역 영역 중복 스토리지)

Azure Storage 중복에 대한 자세한 내용은 [Azure Storage 중복](storage-redundancy.md)을 참조하세요.

다음 기능 및 서비스는 계정 장애 조치(failover)에서 지원되지 않습니다.

- Azure 파일 동기화는 스토리지 계정 장애 조치(Failover)를 지원하지 않습니다. Azure 파일 동기화에서 클라우드 엔드포인트로 사용되는 Azure 파일 공유를 포함하는 스토리지 계정은 장애 조치(failover)하지 않아야 합니다. 이러한 계정을 장애 조치(failover)하면 동기화가 더 이상 진행되지 않고, 새로 계층화된 파일의 경우 예기치 않은 데이터 손실이 발생할 수도 있습니다.
- 계층 구조 네임스페이스를 사용하는 스토리지 계정(예: Data Lake Storage Gen2)은 현재 지원되지 않습니다.
- 프리미엄 블록 blob을 포함하는 스토리지 계정은 장애 조치(failover)할 수 없습니다. 프리미엄 블록 blob를 지원하는 스토리지 계정은 현재 지리적 중복을 지원하지 않습니다.
- [WORM 불변성 정책](../blobs/immutable-storage-overview.md)이 활성화된 컨테이너의 스토리지 계정은 장애 조치(failover)할 수 없습니다. 잠금 해제/잠금 시간 기반 보존 또는 법적 보류 정책은 규정 준수를 유지하기 위해 장애 조치(failover)를 방지합니다.

## <a name="initiate-the-failover"></a>장애 조치(failover) 시작

## <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 계정 장애 조치(failover)를 시작하려면 다음 단계를 수행합니다.

1. 스토리지 계정으로 이동합니다.
1. **설정** 에서 **지역 복제** 를 선택합니다. 다음 이미지는 스토리지 계정의 지역 복제 및 장애 조치(failover) 상태를 보여 줍니다.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-prepare.png" alt-text="지역 복제 및 장애 조치(failover) 상태가 표시된 스크린샷":::

1. 스토리지 계정이 GRS(지역 중복 스토리지) 또는 RA-GRS(읽기 액세스 지역 중복 스토리지)에 대해 구성되었는지 확인합니다. 구성되지 않은 경우 **설정** 에서 **구성** 을 선택하여 계정을 지역 중복으로 업데이트합니다.
1. **마지막 동기화 시간** 속성은 보조 지역이 주 지역보다 지연된 정도를 나타냅니다. **마지막 동기화 시간** 을 통해 장애 조치(failover)가 완료된 후의 데이터 손실 범위를 예측할 수 있습니다. **마지막 동기화 시간** 속성을 확인하는 방법에 대한 자세한 내용은 [스토리지 계정에 대한 마지막 동기화 시간 속성 확인](last-sync-time-get.md)을 참조하세요.
1. **장애 조치(failover) 준비** 를 선택합니다.
1. 확인 대화 상자를 검토합니다. 준비되면 **예** 를 입력하여 장애 조치(failover)를 확인하고 시작합니다.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-confirm.png" alt-text="계정 장애 조치(failover)에 대한 확인 대화 상자가 표시된 스크린샷":::

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

계정 장애 조치(failover) 기능은 일반 공급되지만 PowerShell용 미리 보기 모듈을 계속 사용합니다. PowerShell을 사용하여 계정 장애 조치(failover)를 시작하려면 먼저 Az.Storage [1.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) 모듈을 설치해야 합니다. 다음 단계에 따라 모듈을 설치합니다.

1. 모든 이전 Azure PowerShell 설치를 제거합니다.

    - **설정** 에 있는 **앱 및 기능** 설정을 사용하여 Windows에서 이전에 설치한 Azure PowerShell을 제거합니다.
    - `%Program Files%\WindowsPowerShell\Modules`에서 **Azure** 모듈을 모두 제거합니다.

1. 최신 버전의 PowerShellGet이 설치되어 있는지 확인합니다. Windows PowerShell 창을 열고 다음 명령을 실행하여 최신 버전을 설치합니다.

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. PowerShellGet을 설치한 후 PowerShell 창을 닫았다가 다시 엽니다.

1. Azure PowerShell의 최신 버전을 설치합니다.

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 계정 장애 조치(failover)를 지원하는 Azure Storage 미리 보기 모듈을 설치합니다.

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
    ```

PowerShell에서 계정 장애 조치(failover)를 시작하려면 다음 명령을 실행합니다.

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 계정 장애 조치(failover)를 시작하려면 다음 명령을 실행합니다.

```azurecli-interactive
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>계정 장애 조치(failover)의 중요한 의미

스토리지 계정에 대해 계정 장애 조치(failover)를 시작하면 보조 엔드포인트가 기본 엔드포인트가 되도록 보조 엔드포인트에 대한 DNS 레코드가 업데이트됩니다. 장애 조치(failover)를 시작하기 전에 스토리지 계정에 대한 잠재적 영향을 파악해야 합니다.

장애 조치(failover)를 시작하기 전에 가능한 데이터 손실 범위를 예측하려면 **마지막 동기화 시간** 속성을 확인합니다. **마지막 동기화 시간** 속성을 확인하는 방법에 대한 자세한 내용은 [스토리지 계정에 대한 마지막 동기화 시간 속성 확인](last-sync-time-get.md)을 참조하세요.

시작 후 장애 조치(failover)에 소요되는 시간은 일반적으로 1시간 미만으로 달라질 수 있습니다.

장애 조치(failover) 후에는 새로운 주 지역에서 스토리지 계정 유형이 LRS(로컬 중복 스토리지)로 자동 변환됩니다. 계정에 대해 GRS(지역 중복 스토리지) 또는 RA-GRS(읽기 액세스 지역 중복 스토리지)를 다시 사용할 수 있습니다. LRS에서 GRS 또는 RA-GRS로 변환하는 경우 추가 비용이 발생합니다. 데이터를 새 보조 지역으로 다시 복제하기 위해 네트워크 송신 요금이 발생합니다. 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요.

스토리지 계정에 대해 GRS를 다시 사용하면 계정의 데이터가 새 보조 지역으로 복제되기 시작합니다. 복제 시간은 다음을 포함하는 다양한 요인에 따라 다릅니다.

- 스토리지 계정의 개체 수 및 크기 많은 작은 개체는 더 적고 큰 개체보다 더 오래 걸릴 수 있습니다.
- CPU, 메모리, 디스크 및 WAN 용량과 같은 백그라운드 복제에 사용할 수 있는 리소스입니다. 라이브 트래픽은 지역 복제보다 우선합니다.
- Blob 스토리지를 사용하는 경우 Blob당 스냅샷 수입니다.
- Table 스토리지를 사용하는 경우 [데이터 분할 전략](/rest/api/storageservices/designing-a-scalable-partitioning-strategy-for-azure-table-storage)입니다. 복제 프로세스는 사용하는 파티션 키 수를 초과하여 확장할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [재해 복구 및 저장소 계정 장애 조치(failover)](storage-disaster-recovery-guidance.md)
- [저장소 계정에 대한 마지막 동기화 시간 속성 확인](last-sync-time-get.md)
- [지리적 중복성을 사용하여 고가용성 애플리케이션 설계](geo-redundant-design.md)
- [자습서: Blob Storage에서 고가용성 애플리케이션 빌드](../blobs/storage-create-geo-redundant-storage.md)
