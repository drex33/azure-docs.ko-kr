---
title: Azure 독일에서 글로벌 Azure로 Azure Storage 리소스 마이그레이션
description: 이 문서에서는 Azure 독일에서 글로벌 Azure로 Azure Storage 리소스를 마이그레이션하는 방법에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurepowershell
ms.openlocfilehash: ce152dc6948e7d77ace2defede7f1cb0c0b744a8
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "122528068"
---
# <a name="migrate-storage-resources-to-global-azure"></a>스토리지 리소스를 글로벌 Azure로 마이그레이션

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

이 문서에는 Azure 독일에서 글로벌 Azure로 Azure Storage 리소스를 마이그레이션하는 데 도움이 되는 정보가 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="blobs"></a>Blob

AzCopy는 Blob, 파일 및 테이블을 복사하는 데 사용할 수 있는 무료 도구입니다. AzCopy는 Azure-Azure, 온-프레미스-Azure 및 Azure-온-프레미스 마이그레이션에 사용됩니다. 마이그레이션에 AzCopy를 사용하여 Blob을 Azure 독일에서 글로벌 Azure로 직접 복사합니다.

원본 VM에 관리 디스크를 사용하지 않는 경우 AzCopy를 사용하여 .vhd 파일을 대상 환경에 복사합니다. 그러지 않으면 몇 가지 단계를 미리 완료해야 합니다. 자세한 내용은 [관리 디스크에 대한 권장 사항](#managed-disks)을 참조하세요.

다음 예제에서는 AzCopy의 작동 방식을 보여 줍니다. 전체 참조는 [AzCopy 설명서](../storage/common/storage-use-azcopy-v10.md)를 참조하세요.

AzCopy는 URI로 표현된 용어 **원본** 및 **대상** 을 사용합니다. Azure 독일용 URI는 항상 다음 형식을 사용합니다.

```http
https://<storageaccountname>.blob.core.cloudapi.de/<containername>/<blobname>
```

글로벌 Azure의 URI는 항상 다음 형식을 사용합니다.

```http
https://<storageaccountname>.blob.core.windows.net/<containername>/<blobname>
```

Portal, PowerShell 또는 Azure CLI를 사용하여 URI의 세 부분(*storageaccountname*, *containername*, *blobname*)을 가져옵니다. Blob의 이름은 URI의 일부이거나 *vm121314.vhd* 같은 패턴으로 제공될 수 있습니다.

Azure Storage 계정에 액세스하려면 스토리지 계정 키도 필요합니다. Portal, PowerShell 또는 CLI를 사용하여 가져옵니다. 예를 들면 다음과 같습니다.

```powershell
Get-AzStorageAccountKey -Name <saname> -ResourceGroupName <rgname>
```

항상 그런 것처럼 각 스토리지 계정에 대해 두 키 중 하나만 필요합니다.

예:

URI 부분 | 예제 값
-------- | --------------
원본 storageAccount | `migratetest`
원본 컨테이너 | `vhds`
원본 Blob | `vm-121314.vhd`
대상 storageAccount | `migratetarget`
대상 컨테이너 | `targetcontainer`

이 명령은 가상 하드 디스크를 Azure 독일에서 글로벌 Azure로 복사합니다(가독성을 향상시키기 위해 키가 단축됨).

```cmd
azcopy -v /source:https://migratetest.blob.core.cloudapi.de/vhds /sourcekey:"0LN...w==" /dest:https://migratetarget.blob.core.windows.net/targetcontainer /DestKey:"o//ucDi5TN...w==" /Pattern:vm-121314.vhd
```

VHD의 일관된 복사본을 얻으려면 VHD를 복사하기 전에 VM을 종료합니다. 복사 활동에 대한 가동 중지 시간을 계획합니다. VHD가 복사되면 [대상 환경에서 VM을 다시 빌드합니다](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

추가 정보는 다음 항목을 참조하세요.

- [AZCopy 설명서](../storage/common/storage-use-azcopy-v10.md)를 검토합니다.
- [복원된 디스크에서 VM을 만드는 방법](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)을 알아봅니다.

## <a name="managed-disks"></a>Managed Disks

Azure Managed Disks는 VM 디스크와 연결된 스토리지 계정을 관리하여 Azure IaaS(Infrastructure as a Service) VM의 디스크 관리를 간소화합니다. 

.vhd 파일에 직접 액세스할 수 없으므로 AzCopy와 같은 도구를 직접 사용하여 파일을 복사할 수 없습니다([Blob](#blobs) 참조). 해결 방법은 먼저 임시 공유 액세스 서명 URI를 가져와 관리 디스크를 내보낸 다음, 이 정보를 사용하여 다운로드하거나 복사하는 것입니다. 다음 섹션에서는 공유 액세스 서명 URI를 가져오는 방법 및 이 URI를 사용하여 수행할 작업의 예를 보여 줍니다.

### <a name="step-1-get-the-shared-access-signature-uri"></a>1단계: 공유 액세스 서명 URI 가져오기

1. Portal에서 관리 디스크를 검색합니다. (VM과 동일한 리소스 그룹에 있습니다. 리소스 종류는 **디스크** 입니다.)
1. **개요** 페이지의 위쪽 메뉴에서 **내보내기** 단추를 선택합니다(먼저 VM을 종료 및 할당 취소하거나 VM을 분리해야 함).
1. URI가 만료되는 시간을 정의합니다(기본값은 3,600초).
1. URL을 생성합니다(이 단계는 몇 초밖에 걸리지 않음).
1. URL을 복사합니다(한 번만 표시).

### <a name="step-2-azcopy"></a>2단계: AzCopy

AzCopy를 사용하는 방법의 예제는 [Blob](#blobs)을 참조하세요. AzCopy(또는 유사한 도구)를 사용하여 원본 환경에서 대상 환경으로 디스크를 직접 복사합니다. AzCopy에서 URI를 기본 URI와 공유 액세스 서명 부분으로 분할해야 합니다. URI의 공유 액세스 서명 부분은 문자 " **?** "로 시작합니다. Portal에서는 공유 액세스 서명 URI에 대해 다음 URI를 제공합니다.

```http
https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D>
```

다음 명령은 AzCopy에 대한 원본 매개 변수를 보여 줍니다.

```cmd
/source:"https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd"
```

```cmd
/sourceSAS:" ?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D"
```

다음은 전체 명령입니다.

```cmd
azcopy -v /source:"https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd" /sourceSAS:"?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D" /dest:"https://migratetarget.blob.core.windows.net/targetcontainer/newdisk.vhd" /DestKey:"o//ucD... Kdpw=="
```

### <a name="step-3-create-a-new-managed-disk-in-the-target-environment"></a>3단계: 대상 환경에서 새 관리 디스크 만들기

새 관리 디스크를 만들기 위한 몇 가지 옵션이 있습니다. Azure Portal에서 이 작업을 수행하는 방법은 다음과 같습니다.

1. Portal에서 **새로 만들기** > **관리 디스크** > **만들기** 를 선택합니다.
1. 새 디스크의 이름을 입력합니다.
1. 리소스 그룹을 선택합니다.
1. **원본 유형** 에서 **스토리지 Blob** 을 선택합니다. 그런 다음, AzCopy 명령에서 대상 URI를 복사하거나 대상 URI를 찾아서 선택합니다.
1. OS 디스크를 복사한 경우 **OS** 유형을 선택합니다. 다른 디스크 유형에 대해 **만들기** 를 선택합니다.

### <a name="step-4-create-the-vm"></a>4단계: VM 만들기

앞서 언급했듯이 이 새 관리 디스크를 사용하여 VM을 만드는 방법에는 여러 가지가 있습니다. 두 가지 옵션은 다음과 같습니다.

- Portal에서 디스크를 선택한 다음, **VM 만들기** 를 선택합니다. VM의 다른 매개 변수를 평소처럼 정의합니다.
- PowerShell의 경우 [복원된 디스크에서 VM 만들기](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)를 참조하세요.

추가 정보는 다음 항목을 참조하세요.

- 공유 액세스 서명 URI를 가져와 [API를 통해](/rest/api/compute/disks/grantaccess) 디스크로 내보내는 방법을 알아봅니다. 
- 관리되지 않은 Blob에서 [API를 통해](/rest/api/compute/disks/createorupdate#create-a-managed-disk-by-importing-an-unmanaged-blob-from-a-different-subscription.) 관리 디스크를 만드는 방법을 알아봅니다.

## <a name="tables"></a>테이블

Storage Explorer를 사용하여 Azure에서 테이블을 마이그레이션할 수 있습니다. Storage Explorer는 Azure 클라우드 스토리지 리소스를 관리하기 위한 도구입니다. Storage Explorer를 사용하여 원본 독일 스토리지 계정에 연결하고 대상 Azure 글로벌 스토리지 계정에 테이블을 복사할 수 있습니다.

시작하려면 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 설치합니다.

### <a name="connect-to-source"></a>원본에 연결

Storage Explorer를 사용하여 원본 Azure Storage 계정에서 테이블을 복사합니다. 

Storage Explorer를 Microsoft Azure 독일의 원본 테이블 리소스에 연결합니다. [로그인하여 구독의 리소스에 액세스하거나](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) [특정 스토리지 리소스에 연결](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#attach-to-an-individual-resource)할 수 있습니다. 

### <a name="connect-to-target"></a>대상에 연결

Storage Explorer를 사용하여 대상 Azure Storage 계정에 테이블을 붙여넣습니다.

Storage Explorer를 Azure Storage의 대상 Microsoft Azure 구독에 연결합니다. [로그인하여 구독의 리소스에 액세스하거나](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) [특정 스토리지 리소스에 연결](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#attach-to-an-individual-resource)할 수 있습니다. 


### <a name="migrate-tables"></a>테이블 마이그레이션

Storage Explorer를 사용하여 Azure 독일에서 Azure 글로벌로 테이블을 복사합니다. 복사하려는 테이블을 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **테이블 복사** 를 선택하여 테이블을 복사할 수 있습니다. 다음 예제에서는 *Azure 독일 구독* 에서 *testmigrationtable* 을 복사하는 것을 보여 줍니다.

![Azure 독일 구독에서 선택한 테이블 복사 메뉴](./media/germany-migration-storage/copy-table.png)

Storage Explorer를 사용하여 대상 Azure Storage 계정에 테이블을 붙여넣습니다. 대상 Azure Storage 계정 내의 *테이블* 노드를 마우스 오른쪽 단추로 클릭하여 테이블을 붙여넣을 수 있습니다. 다음 예제에서는 *testmigrationtable* 을 연결된 Azure Storage 계정에 붙여넣는 방법을 보여 줍니다.
 
![대상 Azure Storage 선택한 테이블 붙여넣기 메뉴](./media/germany-migration-storage/paste-table.png)

마이그레이션하려는 각 테이블에 대해 복사 및 붙여넣기 단계를 반복합니다.

## <a name="file-shares"></a>파일 공유

마이그레이션에 AzCopy를 사용하여 파일 공유를 Azure 독일에서 글로벌 Azure로 직접 복사합니다. AzCopy는 Blob, 파일 및 테이블을 복사하는 데 사용할 수 있는 무료 도구입니다.

시작하려면 [AzCopy를 다운로드하고](https://aka.ms/downloadazcopy) 설치합니다.

AzCopy는 URI로 표현된 용어 **원본** 및 **대상** 을 사용합니다. Azure 독일용 URI는 항상 다음 형식을 사용합니다.

```http
https://<storageaccountname>.blob.core.cloudapi.de/<filesharename>
```

글로벌 Azure의 URI는 항상 다음 형식을 사용합니다.

```http
https://<storageaccountname>.blob.core.windows.net/<filesharename>
```
Azure Storage 계정에 액세스하려면 스토리지 계정 SAS 토큰이 필요합니다. 

다음 예제 명령은 Azure 독일 스토리지 계정의 모든 파일 공유, 디렉터리 및 파일을 글로벌 Azure 스토리지 계정에 복사합니다. 전체 참조는 [AzCopy 설명서](../storage/common/storage-use-azcopy-v10.md)를 참조하세요.

URI 부분 | 예제 값
-------- | --------------
원본 storageAccount | `migratetest`
원본 파일 공유 | `sourcefileshare`
대상 storageAccount | `migratetarget`
대상 파일 공유 | `targetfileshare`

```cmd
azcopy copy "https://migratetest.blob.core.cloudapi.de/sourcefileshare?<SAS-token>" "https://migratetarget.blob.core.windows.net/targetfileshare?<SAS-token>" --recursive=true
```

AzCopy에 대한 자세한 내용은 [AzCopy 설명서](../storage/common/storage-use-azcopy-v10.md) 및 [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](../storage/common/storage-use-azcopy-files.md#copy-files-between-storage-accounts)을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 서비스 범주에서 리소스 마이그레이션을 위한 도구, 기술, 권장 사항에 대해 알아봅니다.

- [컴퓨팅](./germany-migration-compute.md)
- [네트워킹](./germany-migration-networking.md)
- [Web](./germany-migration-web.md)
- [데이터베이스](./germany-migration-databases.md)
- [분석](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [통합](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [보안](./germany-migration-security.md)
- [관리 도구](./germany-migration-management-tools.md)
- [미디어](./germany-migration-media.md)
