---
title: AzCopy를 사용하여 Amazon S3에서 Azure Storage로 데이터 복사 | Microsoft Docs
description: AzCopy를 사용하여 Amazon S3에서 Azure Storage로 데이터를 복사합니다. AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 68aaa447aef65a109105f870b805dd485f322643
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639986"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>AzCopy를 사용하여 Amazon S3에서 Azure Storage로 데이터 복사

AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서는 AzCopy를 사용하여 AWS(Amazon Web Services) S3에서 Azure Blob Storage로 객체, 디렉터리, 버킷을 복사하는 데 도움이 됩니다.

## <a name="choose-how-youll-provide-authorization-credentials"></a>권한 부여 자격 증명을 제공하는 방법 선택

- Azure Storage로 권한을 부여하려면 Azure AD(Active Directory) 또는 SAS(공유 액세스 서명) 토큰을 사용합니다.

- AWS S3로 권한을 부여하려면 AWS 액세스 키와 비밀 액세스 키를 사용합니다.

### <a name="authorize-with-azure-storage"></a>Azure Storage를 사용하여 권한 부여

[AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조하여 AzCopy를 다운로드하고 스토리지 서비스에 권한 부여 자격 증명을 제공하는 방법을 선택합니다.

> [!NOTE]
> 이 문서의 예제에서는 `AzCopy login` 명령을 사용하여 ID를 인증했다고 가정합니다. 그러면 AzCopy에서 Azure AD 계정을 사용하여 Blob Storage의 데이터에 대한 액세스 권한을 부여합니다.
>
> SAS 토큰을 사용하여 blob 데이터에 대한 액세스 권한을 부여하려면 각 AzCopy 명령에서 해당 토큰을 리소스 URL에 추가할 수 있습니다.
>
> 예: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`

### <a name="authorize-with-aws-s3"></a>AWS S3로 권한 부여

AWS 액세스 키 및 비밀 액세스 키를 수집하고 다음 환경 변수를 설정합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **macOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>개체, 디렉터리 및 버킷 복사

AzCopy는 [URL에서 블록 배치](/rest/api/storageservices/put-block-from-url) API를 사용하므로 데이터가 AWS S3와 스토리지 서버 간에 직접 복사됩니다. 이 복사 작업은 컴퓨터의 네트워크 대역폭을 사용하지 않습니다.

> [!TIP]
> 이 섹션의 예제에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

 이 예제는 계층 구조 네임스페이스가 있는 계정에서도 작동합니다. [Data Lake Storage의 다중 프로토콜 액세스](../blobs/data-lake-storage-multi-protocol-access.md)를 사용하면 해당 계정에서 같은 URL 구문(`blob.core.windows.net`)을 사용할 수 있습니다.

### <a name="copy-an-object"></a>개체 복사

계층 구조 네임스페이스가 있는 계정에 같은 URL 구문(`blob.core.windows.net`)을 사용합니다.

**구문**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**예제**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```

> [!NOTE]
> 이 문서의 예제에서는 AWS S3 버킷의 경로-스타일 URL(예: `http://s3.amazonaws.com/<bucket-name>`)을 사용합니다. 
>
> 가상 호스트-스타일 URL도 사용할 수 있습니다(예: `http://bucket.s3.amazonaws.com`). 
>
> 버킷의 가상 호스팅에 관해 자세히 알아보려면 [버킷의 가상 호스팅](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)을 참조하세요.

### <a name="copy-a-directory"></a>디렉터리 복사

계층 구조 네임스페이스가 있는 계정에 같은 URL 구문(`blob.core.windows.net`)을 사용합니다.

**구문**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**예제**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> 이 예제에서는 `--recursive` 플래그를 추가하여 모든 하위 디렉터리의 파일을 복사합니다.

### <a name="copy-the-contents-of-a-directory"></a>디렉터리의 콘텐츠 복사

와일드카드 기호(*)를 사용하여 포함하는 디렉터리 자체를 복사하지 않고 디렉터리의 콘텐츠를 복사할 수 있습니다.

**구문**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**예제**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-bucket"></a>버킷 복사

계층 구조 네임스페이스가 있는 계정에 같은 URL 구문(`blob.core.windows.net`)을 사용합니다.

**구문**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true`

**예제**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true
```

### <a name="copy-all-buckets-in-all-regions"></a>모든 지역에서 모든 버킷 복사

계층 구조 네임스페이스가 있는 계정에 같은 URL 구문(`blob.core.windows.net`)을 사용합니다.

**구문**

`azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**예제**

```azcopy
azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>특정 S3 지역의 모든 버킷 복사

계층 구조 네임스페이스가 있는 계정에 같은 URL 구문(`blob.core.windows.net`)을 사용합니다.

**구문**

`azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**예제**

```azcopy
azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-object-naming-rules"></a>개체 명명 규칙의 차이점 처리

AWS S3에는 Azure Blob 컨테이너와 다른 버킷 명명 규칙 집합이 있습니다. [여기](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)에서 자세한 정보를 확인할 수 있습니다. 버킷 그룹을 Azure Storage 계정에 복사하도록 선택하면 이름 지정 차이로 인해 복사 작업이 실패할 수 있습니다.

AzCopy는 마침표를 포함하는 버킷과 연속 하이픈을 포함하는 버킷의 가장 일반적인 두 가지 문제를 처리합니다. AWS S3 버킷 이름은 마침표와 연속 하이픈을 포함할 수 있지만 Azure의 컨테이너는 포함할 수 없습니다. AzCopy는 마침표를 하이픈으로, 연속 하이픈을 연속 하이픈 수를 나타내는 숫자로 바꿉니다(예: `my----bucket`이라는 버킷은 `my-4-bucket`이 됩니다.

또한 AzCopy는 파일을 복사할 때 이름 충돌을 확인하고 해결하려 합니다. 예를 들어 이름이 `bucket-name` 및 `bucket.name`인 버킷이 있으면 AzCopy는 이름이 `bucket.name`인 버킷을 먼저 `bucket-name`으로 확인한 다음 `bucket-name-2`로 확인합니다.

## <a name="handle-differences-in-object-metadata"></a>개체 메타데이터의 차이점 처리

AWS S3 및 Azure에서는 개체 키 이름에 다른 문자 세트를 사용할 수 있습니다. AWS S3에서 사용하는 문자에 관한 내용은 [여기](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)에서 확인할 수 있습니다. Azure 측에서 blob 개체 키는 [C# 식별자](/dotnet/csharp/language-reference/)의 명명 규칙을 따릅니다.

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
- [예: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [예: Azure Files](storage-use-azcopy-files.md)
- [자습서: AzCopy를 사용하여 클라우드로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)

이러한 문서를 참조하여 설정을 구성하고, 성능을 최적화하고, 문제를 해결하세요.

- [AzCopy 구성 설정](storage-ref-azcopy-configuration-settings.md)
- [AzCopy의 성능 최적화](storage-use-azcopy-optimize.md)
- [로그 파일을 사용하여 Azure Storage의 AzCopy V10 문제 해결](storage-use-azcopy-configure.md)
