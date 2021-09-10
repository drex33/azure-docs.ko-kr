---
title: 사용자 지정 게스트 구성 패키지 아티팩트 게시 방법
description: 게스트 구성 패키지 파일을 Azure Blob Storage에 게시하고 보안 액세스용 SAS 토큰을 가져오는 방법에 대해 알아봅니다.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: aa20bc3d9c47258c6ebedd2419cf830ba47c1c1c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773108"
---
# <a name="how-to-publish-custom-guest-configuration-package-artifacts"></a>사용자 지정 게스트 구성 패키지 아티팩트 게시 방법

시작하기 전에 [게스트 구성](../concepts/guest-configuration.md)에 대한 개요 페이지를 읽어보는 것이 좋습니다.

게스트 구성 사용자 지정 .zip 패키지는 관리되는 컴퓨터에서 HTTPS를 통해 액세스할 수 있는 위치에 저장되어야 합니다. GitHub 리포지토리, Azure 리포지토리, Azure Storage 또는 프라이빗 데이터 센터 내의 웹 서버가 있습니다.

`Audit`및 `AuditandSet`를 지원하는 구성 패키지는 동일한 방식으로 게시됩니다. 패키지 모드를 기반으로 하는 게시 중에는 특별한 작업을 수행할 필요가 없습니다.

## <a name="publish-a-configuration-package"></a>구성 패키지 게시

구성 패키지를 저장하는 기본 위치는 Azure Blob Storage입니다.
스토리지 계정에 특별한 요구 사항은 없지만, 컴퓨터 근처 지역에서 파일을 호스트하는 것이 좋습니다. 패키지를 공개로 만들지 않으려는 경우 URL에 [SAS 토큰](../../../storage/common/storage-sas-overview.md)을 포함하거나 개인 네트워크의 컴퓨터에 대해 [서비스 엔드포인트](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)를 구현할 수 있습니다.

스토리지 계정이 없다면 다음 예제에 따라 계정을 만듭니다.

```powershell
# Creates a new resource group, storage account, and container
New-AzResourceGroup -name myResourceGroupName -Location WestUS
New-AzStorageAccount -ResourceGroupName myResourceGroupName -Name myStorageAccountName -SkuName 'Standard_LRS' -Location 'WestUs' | New-AzStorageContainer -Name guestconfiguration -Permission Blob
```

`Publish-GuestConfigurationPackage` 명령은 Azure Blob Storage에 구성 패키지를 업로드 하고 안전하게 액세스할 수 있도록 SAS 토큰을 검색합니다.

`Publish-GuestConfigurationPackage` cmdlet의 매개 변수는 다음과 같습니다.

- **Path**: 게시할 패키지의 위치
- **ResourceGroupName**: 스토리지 계정이 있는 리소스 그룹의 이름
- **StorageAccountName**: 패키지를 게시해야 하는 스토리지 계정의 이름
- **StorageContainerName**: (기본값: _guestconfiguration_) 스토리지 계정의 스토리지 컨테이너 이름
- **Force**: 스토리지 계정에서 이름이 같은 기존 패키지를 덮어씁니다.

다음 예제에서는 패키지를 스토리지 컨테이너 이름 'guestconfiguration'에 게시합니다.

```powershell
Publish-GuestConfigurationPackage -Path ./MyConfig.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName | % ContentUri
```

## <a name="next-steps"></a>다음 단계

- 사용자의 개발 환경에서 [패키지 아티팩트를 테스트](./guest-configuration-create-test.md)합니다.
- `GuestConfiguration` 모듈을 사용하여 사용자 환경을 대규모로 관리하기 위한 [Azure Policy 정의를 만듭니다](./guest-configuration-create-definition.md).
- Azure Portal을 사용하여 [사용자 지정 정책 정의를 할당합니다](../assign-policy-portal.md).
- [게스트 구성에 대한 규정 준수 세부 정보](./determine-non-compliance.md#compliance-details-for-guest-configuration) 정책 할당을 보는 방법을 알아봅니다.
