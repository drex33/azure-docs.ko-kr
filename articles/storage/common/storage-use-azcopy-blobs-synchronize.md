---
title: AzCopy v10을 사용하여 Azure Blob Storage와 동기화 | Microsoft Docs
description: 이 문서에는 Azure Blob Storage와 동기화하는 데 도움이 되는 AzCopy 예제 명령 컬렉션이 포함되어 있습니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 84c8c1e0eeb402185b7451ce953581d23b39c860
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624826"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy"></a>AzCopy를 사용하여 Azure Blob 스토리지와 동기화

AzCopy v10 명령줄 유틸리티를 사용하여 로컬 스토리지를 Azure Blob Storage와 동기화할 수 있습니다.

로컬 파일 시스템의 콘텐츠를 Blob 컨테이너와 동기화할 수 있습니다. 컨테이너와 가상 디렉터리를 서로 동기화할 수도 있습니다. 한 가지 방법으로 동기화가 있습니다. 즉, 이 두 엔드포인트 중에서 소스와 대상을 선택합니다. 동기화에서도 서버 간 API를 사용합니다. 이 섹션에 제시된 예제는 계층 구조 네임스페이스가 있는 계정에서도 작동합니다.

> [!NOTE]
> 현재 AzCopy 릴리스는 다른 소스와 대상(예: 파일 스토리지 또는 AWS(Amazon Web Services) S3 버킷) 간에 동기화되지 않습니다.

파일 업로드, blob 다운로드 또는 계정 간 blob 복사와 같은 다른 작업 형식의 예를 보려면 이 문서의 [다음 단계](#next-steps) 섹션에 있는 링크를 참조하세요.

## <a name="get-started"></a>시작

AzCopy를 다운로드하고 스토리지 서비스에 인증 자격 증명을 제공하는 방법에 관해 알아보려면 [AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조하세요.

> [!NOTE]
> 이 문서의 예제에서는 Azure AD(Azure Active Directory)를 사용하여 권한 부여 자격 증명을 제공했다고 가정합니다.
>
> SAS 토큰을 사용하여 blob 데이터에 대한 액세스 권한을 부여하려면 각 AzCopy 명령에서 해당 토큰을 리소스 URL에 추가할 수 있습니다. 예: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`

## <a name="guidelines"></a>지침

- [sync](storage-ref-azcopy-sync.md) 명령은 파일 이름과 마지막으로 수정된 타임스탬프를 비교합니다. `--delete-destination` 선택적 플래그를 `true` 또는 `prompt` 값으로 설정하여 해당 파일이 소스 디렉터리에 더는 존재하지 않는 경우 대상 디렉터리의 파일을 삭제합니다.

- `--delete-destination` 플래그를 `true`로 설정하면 AzCopy에서 프롬프트를 제공하지 않고 파일을 삭제합니다. AzCopy가 파일을 삭제하기 전에 프롬프트를 표시하려면 `--delete-destination` 플래그를 `prompt`로 설정합니다.

- `--delete-destination` 플래그를 `prompt` 또는 `false`로 설정하려는 경우 [sync](storage-ref-azcopy-sync.md) 명령 대신 [copy](storage-ref-azcopy-copy.md) 명령을 사용하고 `--overwrite` 매개 변수를 `ifSourceNewer`로 설정하는 것이 좋습니다. 복사 작업은 파일을 이동하기 전에 원본 또는 대상을 인덱싱하지 않아도 되기 때문에 [copy](storage-ref-azcopy-copy.md) 명령을 사용하면 메모리를 적게 사용하고 청구 비용을 줄일 수 있습니다.

- 실수로 삭제되는 것을 방지하려면 `--delete-destination=prompt|true` 플래그를 사용하기 전에 [일시 삭제 ](../blobs/soft-delete-blob-overview.md) 기능을 사용하도록 설정해야 합니다.

- 마지막으로 수정된 시간은 파일이 전송되어야 하는지 여부를 결정하는 데 중요하므로 sync 명령을 실행하는 머신에 정확한 시스템 클록이 있어야 합니다. 시스템의 클록 오차가 심각한 경우 sync 명령을 실행하려는 시간에 너무 가까운 대상의 파일을 수정하지 마십시오.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>변경이 포함된 컨테이너를 로컬 파일 시스템으로 업데이트

이 경우 컨테이너는 대상이며 로컬 파일 시스템은 원본입니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**예제**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>변경이 포함된 로컬 파일 시스템을 컨테이너로 업데이트

이 경우 로컬 파일 시스템은 대상이며 컨테이너는 원본입니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive`

**예제**

```azcopy
azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive
```

## <a name="update-a-container-with-changes-in-another-container"></a>다른 컨테이너의 변경 내용으로 컨테이너 업데이트

이 명령에 표시되는 첫 번째 컨테이너는 원본입니다. 두 번째는 대상입니다. 각 원본 URL에 SAS 토큰을 추가해야 합니다.

Azure AD(Azure Active Directory)를 사용하여 권한 부여 자격 증명을 제공하는 경우 대상 URL에서만 SAS 토큰을 생략할 수 있습니다. 대상 계정에 적절한 역할을 설정했는지 확인합니다. [옵션 1: Azure Active Directory 사용](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory)을 참조하세요.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**예제**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>다른 컨테이너에 있는 디렉터리의 변경 내용으로 디렉터리 업데이트

이 명령에 표시되는 첫 번째 디렉터리는 원본입니다. 두 번째는 대상입니다. 각 원본 URL에 SAS 토큰을 추가해야 합니다.

Azure AD(Azure Active Directory)를 사용하여 권한 부여 자격 증명을 제공하는 경우 대상 URL에서만 SAS 토큰을 생략할 수 있습니다. 대상 계정에 적절한 역할을 설정했는지 확인합니다. [옵션 1: Azure Active Directory 사용](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory)을 참조하세요.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive`

**예제**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive
```

## <a name="synchronize-with-optional-flags"></a>선택적 플래그를 사용하여 동기화

선택적 플래그를 사용하여 동기화 작업을 조정할 수 있습니다. 다음은 몇 가지 예제입니다.

|시나리오|플래그|
|---|---|
|다운로드할 때 얼마나 엄격하게 MD5 해시의 유효성을 검사할지를 지정합니다.|**--check-md5**=\[NoCheck\|LogOnly\|FailIfDifferent\|FailIfDifferentOrMissing\]|
|패턴을 기반으로 파일을 제외합니다.|**--exclude-path**|
|동기화 관련 로그 항목의 세부 정보를 지정합니다.|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|

플래그의 전체 목록을 보려면 [옵션](storage-ref-azcopy-sync.md#options)을 참조하세요.

> [!NOTE]
> `--recursive` 플래그는 기본적으로 `true`로 설정됩니다. `--exclude-pattern` 및 `--include-pattern` 플래그는 파일 이름에만 적용되고 파일 경로의 다른 부분에는 적용되지 않습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [예제: 업로드](storage-use-azcopy-blobs-upload.md)
- [예제: 다운로드](storage-use-azcopy-blobs-download.md)
- [예제: 계정 간 복사](storage-use-azcopy-blobs-copy.md)
- [예제: Amazon S3 버킷](storage-use-azcopy-s3.md)
- [예: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [예: Azure Files](storage-use-azcopy-files.md)
- [자습서: AzCopy를 사용하여 클라우드로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)

이러한 문서를 참조하여 설정을 구성하고, 성능을 최적화하고, 문제를 해결하세요.

- [AzCopy 구성 설정](storage-ref-azcopy-configuration-settings.md)
- [AzCopy의 성능 최적화](storage-use-azcopy-optimize.md)
- [로그 파일을 사용하여 Azure Storage의 AzCopy V10 문제 해결](storage-use-azcopy-configure.md)
