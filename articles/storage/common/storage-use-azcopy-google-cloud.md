---
title: AzCopy를 사용하여 Google Cloud Storage에서 Azure Storage로 복사 | Microsoft Docs
description: AzCopy를 사용하여 Google Cloud Storage에서 Azure Storage로 데이터를 복사합니다. AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8e592f8783bb0659a453c8d7d54cddf921f5b1e1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606934"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>AzCopy를 사용하여 Google Cloud Storage에서 Azure Storage로 데이터 복사(미리 보기)

AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서는 AzCopy를 사용하여 Google Cloud Storage에서 Azure Blob Storage로 객체, 디렉터리, 버킷을 복사하는 데 도움이 됩니다.

> [!IMPORTANT]
> Google Cloud Storage에서 Azure Storage로의 데이터 복사는 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="choose-how-youll-provide-authorization-credentials"></a>권한 부여 자격 증명을 제공하는 방법 선택

- Azure Storage로 권한을 부여하려면 Azure AD(Active Directory) 또는 SAS(공유 액세스 서명) 토큰을 사용합니다.

- Google Cloud Storage로 권한을 부여하려면 서비스 계정 키를 사용합니다.

### <a name="authorize-with-azure-storage"></a>Azure Storage를 사용하여 권한 부여

AzCopy를 다운로드하고 스토리지 서비스에 인증 자격 증명을 제공하는 방법에 관해 알아보려면 [AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조하세요.

> [!NOTE]
> 이 문서의 예제에서는 Azure AD(Azure Active Directory)를 사용하여 권한 부여 자격 증명을 제공했다고 가정합니다.
>
> SAS 토큰을 사용하여 blob 데이터에 대한 액세스 권한을 부여하려면 각 AzCopy 명령에서 해당 토큰을 리소스 URL에 추가할 수 있습니다. 예: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`

### <a name="authorize-with-google-cloud-storage"></a>Google Cloud Storage를 사용하여 권한 부여

Google Cloud Storage로 권한을 부여하려면 서비스 계정 키를 사용합니다. 서비스 계정 키를 만드는 방법에 관한 자세한 내용은 [서비스 계정 키 만들기 및 관리](https://cloud.google.com/iam/docs/creating-managing-service-account-keys)를 참조하세요.

서비스 키를 가져온 다음 `GOOGLE_APPLICATION_CREDENTIALS` 환경 변수를 서비스 계정 키 파일의 절대 경로로 설정합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>개체, 디렉터리 및 버킷 복사

AzCopy는 [URL에서 블록 배치](/rest/api/storageservices/put-block-from-url) API를 사용하므로 데이터가 Google Cloud Storage와 스토리지 서버 간에 직접 복사됩니다. 이 복사 작업은 컴퓨터의 네트워크 대역폭을 사용하지 않습니다.

> [!TIP]
> 이 섹션의 예제에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

 이 예제는 계층 구조 네임스페이스가 있는 계정에서도 작동합니다. [Data Lake Storage의 다중 프로토콜 액세스](../blobs/data-lake-storage-multi-protocol-access.md)를 사용하면 해당 계정에서 같은 URL 구문(`blob.core.windows.net`)을 사용할 수 있습니다.

### <a name="copy-an-object"></a>개체 복사

계층 구조 네임스페이스가 있는 계정에 같은 URL 구문(`blob.core.windows.net`)을 사용합니다.

**구문**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**예제**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```

### <a name="copy-a-directory"></a>디렉터리 복사

계층 구조 네임스페이스가 있는 계정에 같은 URL 구문(`blob.core.windows.net`)을 사용합니다.

**구문**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**예제**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> 이 예제에서는 `--recursive` 플래그를 추가하여 모든 하위 디렉터리의 파일을 복사합니다.

### <a name="copy-the-contents-of-a-directory"></a>디렉터리의 콘텐츠 복사

와일드카드 기호(*)를 사용하여 포함하는 디렉터리 자체를 복사하지 않고 디렉터리의 콘텐츠를 복사할 수 있습니다.

**구문**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**예제**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-cloud-storage-bucket"></a>클라우드 스토리지 버킷 복사

계층 구조 네임스페이스가 있는 계정에 같은 URL 구문(`blob.core.windows.net`)을 사용합니다.

**구문**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**예제**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Google Cloud 프로젝트의 모든 버킷 복사

먼저 `GOOGLE_CLOUD_PROJECT`를 Google Cloud 프로젝트의 프로젝트 ID로 설정합니다.

계층 구조 네임스페이스가 있는 계정에 같은 URL 구문(`blob.core.windows.net`)을 사용합니다.

**구문**

`azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**예제**

```azcopy
azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Google Cloud 프로젝트에서 버킷 하위 세트 복사

먼저 `GOOGLE_CLOUD_PROJECT`를 Google Cloud 프로젝트의 프로젝트 ID로 설정합니다.

버킷 이름에 와일드카드 기호(*)를 사용하여 버킷의 하위 세트를 복사합니다. 계층 구조 네임스페이스가 있는 계정에 같은 URL 구문(`blob.core.windows.net`)을 사용합니다.

**구문**

`azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**예제**

```azcopy
azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-bucket-naming-rules"></a>버킷 명명 규칙의 차이점 처리

Google Cloud Storage에는 Azure Blob 컨테이너와 다른 버킷 명명 규칙 집합이 있습니다. [여기](https://cloud.google.com/storage/docs/naming-buckets)에서 자세한 정보를 확인할 수 있습니다. 버킷 그룹을 Azure Storage 계정에 복사하도록 선택하면 이름 지정 차이로 인해 복사 작업이 실패할 수 있습니다.

AzCopy는 마침표를 포함하는 버킷, 연속 하이픈을 포함하는 버킷 및 밑줄을 포함하는 버킷의 가장 일반적인 세 가지 문제를 처리합니다. Google Cloud Storage 버킷 이름은 마침표와 연속 하이픈을 포함할 수 있지만 Azure의 컨테이너는 포함할 수 없습니다. AzCopy는 마침표를 하이픈으로, 연속 하이픈을 연속 하이픈 수를 나타내는 숫자로 바꿉니다(예: `my----bucket`이라는 버킷은 `my-4-bucket`이 됩니다. 버킷 이름에 밑줄()이 있는 경우 `_` AzCopy는 밑줄을 하이픈으로 대체합니다. 예를 들어 라는 버킷은 `my_bucket` 가 `my-bucket` 됩니다.

## <a name="handle-differences-in-object-naming-rules"></a>개체 명명 규칙의 차이점 처리

Google Cloud Storage에는 Azure Blob과 다른 개체 명명 규칙 집합이 있습니다. [여기](https://cloud.google.com/storage/docs/naming-objects)에서 자세한 정보를 확인할 수 있습니다.

Azure Storage에서는 개체 이름(또는 가상 디렉터리 경로의 모든 세그먼트)이 후행 점(예: `my-bucket...`)으로 끝날 수 없습니다. 복사 작업을 수행할 때 후행 점이 잘립니다.

## <a name="handle-differences-in-object-metadata"></a>개체 메타데이터의 차이점 처리

Google Cloud Storage 및 Azure에서는 개체 키 이름에 다른 문자 세트를 사용할 수 있습니다. [여기](https://cloud.google.com/storage/docs/metadata)에서 Google Cloud Storage의 메타데이터에 관한 정보를 확인할 수 있습니다. Azure 측에서 blob 개체 키는 [C# 식별자](/dotnet/csharp/language-reference/)의 명명 규칙을 따릅니다.

AzCopy `copy` 명령의 일부로 파일의 메타데이터에 호환되지 않는 키 이름이 포함된 파일을 처리하는 방법을 지정하는 `s2s-handle-invalid-metadata` 플래그 옵션의 값을 제공할 수 있습니다. 다음 표에서는 각 플래그 값을 설명합니다.

| 플래그 값 | Description  |
|--------|-----------|
| **ExcludeIfInvalid** | (기본 옵션) 메타데이터는 전송된 개체에 포함되지 않습니다. AzCopy에서 경고를 로그합니다. |
| **FailIfInvalid** | 개체는 복사되지 않습니다. AzCopy는 오류를 로그하고 전송 요약에 표시되는 실패 횟수에 해당 오류를 포함합니다.  |
| **RenameIfInvalid**  | AzCopy는 잘못된 메타데이터 키를 확인하고 확인된 메타데이터 키 값 쌍을 사용하여 개체를 Azure에 복사합니다. AzCopy가 개체 키의 이름을 바꾸는 데 사용하는 단계를 정확히 알아보려면 아래의 [AzCopy가 개체 키의 이름을 바꾸는 방법](#rename-logic) 섹션을 참조하세요. AzCopy가 키의 이름을 바꿀 수 없는 경우 개체는 복사되지 않습니다. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>AzCopy가 개체 키의 이름을 바꾸는 방법

AzCopy는 다음 단계를 수행합니다.

1. 잘못된 문자를 ‘_’로 바꿉니다.

2. 유효한 새 키의 시작 부분에 `rename_` 문자열을 추가합니다.

   이 키는 원본 메타데이터 **값** 을 저장하는 데 사용합니다.

3. 유효한 새 키의 시작 부분에 `rename_key_` 문자열을 추가합니다.
   이 키는 원본 메타데이터의 잘못된 **키** 를 저장하는 데 사용합니다.
   메타데이터 키는 Blob Storage 서비스의 값으로 유지되므로 이 키를 사용하여 Azure 쪽에서 메타데이터를 복구할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [예제: 업로드](storage-use-azcopy-blobs-upload.md)
- [예제: 다운로드](storage-use-azcopy-blobs-download.md)
- [예제: 계정 간 복사](storage-use-azcopy-blobs-copy.md)
- [예제: 동기화](storage-use-azcopy-blobs-synchronize.md)
- [예제: Amazon S3 버킷](storage-use-azcopy-s3.md)
- [예: Azure Files](storage-use-azcopy-files.md)
- [자습서: AzCopy를 사용하여 클라우드로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)

이러한 문서를 참조하여 설정을 구성하고, 성능을 최적화하고, 문제를 해결하세요.

- [AzCopy 구성 설정](storage-ref-azcopy-configuration-settings.md)
- [AzCopy의 성능 최적화](storage-use-azcopy-optimize.md)
- [로그 파일을 사용하여 Azure Storage의 AzCopy V10 문제 해결](storage-use-azcopy-configure.md)
