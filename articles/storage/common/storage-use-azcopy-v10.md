---
title: AzCopy v10을 사용하여 Azure Storage로 데이터 복사 또는 이동 | Microsoft Docs
description: AzCopy는 스토리지 계정으로, 스토리지 계정에서 또는 스토리지 계정 간에 데이터를 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에서는 AzCopy를 다운로드하고, 스토리지 계정에 연결한 다음, 파일을 전송하는 방법을 설명합니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: ccf2f423137e4732b2785f23256577c92eee07ac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595658"
---
# <a name="get-started-with-azcopy"></a>AzCopy 시작

AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에서는 AzCopy를 다운로드하고, 스토리지 계정에 연결한 다음, 파일을 전송하는 방법을 설명합니다.

> [!NOTE]
> AzCopy **V10** 은 AzCopy의 현재 지원되는 버전입니다.
>
> 이전 버전의 AzCopy를 사용해야하는 경우 이 문서의 [이전 버전의 AzCopy 사용](#previous-version) 섹션을 참조하세요.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>AzCopy 다운로드

먼저 AzCopy V10 실행 파일을 컴퓨터의 아무 디렉터리에나 다운로드합니다. AzCopy V10은 실행 파일일 뿐 이므로 아무것도 설치할 것이 없습니다.

- [Windows 64비트](https://aka.ms/downloadazcopy-v10-windows)(zip)
- [Windows 32비트](https://aka.ms/downloadazcopy-v10-windows-32bit)(zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux)(tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac)(zip)

관련 파일은 zip 파일(Windows 및 Mac) 또는 tar 파일(Linux)로 압축되어 있습니다. Linux에서 tar 파일을 다운로드하고 압축을 풀려면 Linux 배포에 대한 설명서를 참조하세요.

> [!NOTE]
> [Azure Table Storage](../tables/table-storage-overview.md) 서비스로 또는 서비스에서 데이터를 복사하려면 [AzCopy 버전 7.3](https://aka.ms/downloadazcopynet)을 설치합니다.

## <a name="run-azcopy"></a>AzCopy 실행

편의상, AzCopy 실행 파일의 디렉터리 위치를 시스템 경로에 추가하여 사용하기 쉽도록 하는 것이 좋습니다. 이렇게 하면 시스템의 어느 디렉터리에서나 `azcopy`를 입력할 수 있습니다.

AzCopy 디렉터리를 경로에 추가하지 않도록 선택하는 경우 디렉터리를 AzCopy 실행 파일의 위치로 변경하고 Windows PowerShell 명령 프롬프트에서 `azcopy` 또는 `.\azcopy`를 입력해야 합니다.

Azure Storage 계정 소유자인 경우 자동으로 데이터에 액세스할 수 있는 권한이 할당되지 않습니다. AzCopy를 사용하여 의미 있는 작업을 수행할 수 있으려면 먼저 스토리지 서비스에 권한 부여 자격 증명을 제공하는 방법을 결정해야 합니다.

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>AzCopy 권한 부여

AD(Azure Active Directory) 또는 SAS(공유 액세스 서명) 토큰을 사용하여 권한 부여 자격 증명을 제공할 수 있습니다.

이 표를 가이드로 참조하세요.

| 스토리지 유형 | 현재 지원되는 권한 부여 방법 |
|--|--|
|**Blob Storage** | Azure AD 및 SAS |
|**Blob Storage(계층 구조 네임스페이스)** | Azure AD 및 SAS |
|**File Storage** | SAS 전용 |

#### <a name="option-1-use-azure-active-directory"></a>옵션 1: Azure Active Directory 사용

이 옵션은 Blob Storage에만 사용할 수 있습니다. Azure Active Directory를 사용하면 각 명령에 SAS 토큰을 추가할 필요 없이 자격 증명을 한 번 제공할 수 있습니다.

> [!NOTE]
> 현재 릴리스에서는 스토리지 계정 간에 Blob을 복사하려는 경우 각 원본 URL에 SAS 토큰을 추가해야 합니다. SAS 토큰은 대상 URL에서만 생략할 수 있습니다. 예제는 [스토리지 계정 간에 Blob 복사](#transfer-data)를 참조하세요.

Azure AD를 사용하여 액세스 권한을 부여하는 방법은 [AzCopy 및 Azure AD(Azure Active Directory)를 사용하여 Blob에 대한 액세스 권한 부여](storage-use-azcopy-authorize-azure-active-directory.md)를 참조하세요.

#### <a name="option-2-use-a-sas-token"></a>옵션 2: SAS 토큰 사용

AzCopy 명령에서 사용하는 각 원본 또는 대상 URL에 SAS 토큰을 추가할 수 있습니다.

이 예제 명령은 데이터를 로컬 디렉터리에서 Blob 컨테이너로 재귀적으로 복사합니다. 가상의 SAS 토큰이 컨테이너 URL의 끝에 추가됩니다.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS 토큰 및 SAS 토큰을 얻는 방법에 대한 자세한 내용은 [SAS(공유 액세스 서명) 사용](./storage-sas-overview.md)을 참조하세요.

> [!NOTE]
> 스토리지 계정의 [보안 전송 필요](storage-require-secure-transfer.md) 설정에 따라 스토리지 계정에 대한 연결이 TLS(전송 계층 보안)로 보호되는지 여부가 결정 됩니다. 이 설정은 기본적으로 사용하도록 설정되어 있습니다.

<a id="transfer-data"></a>

## <a name="transfer-data"></a>데이터 전송

자신의 ID에 권한을 부여하거나 SAS 토큰을 얻은 후에는 데이터 전송을 시작할 수 있습니다.

예제 명령을 찾으려면 다음 문서 중 하나를 참조하세요.

| 서비스 | 아티클 |
|--------|-----------|
|Azure Blob Storage|[Azure Blob Storage에 파일 업로드](storage-use-azcopy-blobs-upload.md) |
|Azure Blob Storage|[Azure Blob Storage에서 Blob 다운로드](storage-use-azcopy-blobs-download.md)|
|Azure Blob Storage|Azure 스토리지 계정 간에 [Blob 복사](storage-use-azcopy-blobs-copy.md)|
|Azure Blob Storage|[Azure Blob Storage 시작](storage-use-azcopy-blobs-synchronize.md)|
|Azure 파일 |[AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)|
|Amazon S3|[Amazon S3에서 Azure Storage로 데이터 복사](storage-use-azcopy-s3.md)|
|Google Cloud Storage|[Google Cloud Storage에서 Azure Storage로 데이터 복사(미리 보기)](storage-use-azcopy-google-cloud.md)|
|Azure Stack 스토리지|[AzCopy 및 Azure Stack 스토리지를 사용하여 데이터 전송](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="get-command-help"></a>명령 도움말 가져오기

명령 목록을 보려면 `azcopy -h`를 입력한 다음 Enter 키를 누릅니다.

특정 명령에 대한 자세한 내용을 보려면 명령의 이름만 포함하면 됩니다(예: `azcopy list -h`).

> [!div class="mx-imgBorder"]
> ![인라인 도움말](media/storage-use-azcopy-v10/azcopy-inline-help.png)

### <a name="list-of-commands"></a>명령 목록

다음 표에는 모든 AzCopy v10 명령이 나열되어 있습니다. 각 명령은 참조 문서에 연결됩니다.

|명령|Description|
|---|---|
|[azcopy bench](storage-ref-azcopy-bench.md?toc=/azure/storage/blobs/toc.json)|지정된 위치로 또는 지정된 위치에서 데이터를 업로드하거나 다운로드하여 성능 벤치마크를 실행합니다.|
|[azcopy copy](storage-ref-azcopy-copy.md?toc=/azure/storage/blobs/toc.json)|원본 데이터를 대상 위치에 복사|
|[azcopy doc](storage-ref-azcopy-doc.md?toc=/azure/storage/blobs/toc.json)|Markdown 형식으로 도구에 대한 문서를 생성합니다.|
|[azcopy env](storage-ref-azcopy-env.md?toc=/azure/storage/blobs/toc.json)|AzCopy의 동작을 구성할 수 있는 환경 변수를 표시합니다.|
|[azcopy jobs](storage-ref-azcopy-jobs.md?toc=/azure/storage/blobs/toc.json)|작업 관리와 관련된 하위 명령입니다.|
|[azcopy jobs clean](storage-ref-azcopy-jobs-clean.md?toc=/azure/storage/blobs/toc.json)|모든 작업의 모든 로그 및 계획 파일을 제거합니다.|
|[azcopy jobs list](storage-ref-azcopy-jobs-list.md?toc=/azure/storage/blobs/toc.json)|모든 작업에 대한 정보를 표시합니다.|
|[azcopy jobs remove](storage-ref-azcopy-jobs-remove.md?toc=/azure/storage/blobs/toc.json)|지정된 작업 ID와 연결된 파일을 모두 제거합니다.|
|[azcopy jobs resume](storage-ref-azcopy-jobs-resume.md?toc=/azure/storage/blobs/toc.json)|지정된 작업 ID를 사용하여 기존 작업을 다시 시작합니다.|
|[azcopy jobs show](storage-ref-azcopy-jobs-show.md?toc=/azure/storage/blobs/toc.json)|지정된 작업 ID에 대한 상세 정보를 표시합니다.|
|[azcopy load](storage-ref-azcopy-load.md)|특정 형식의 데이터 전송과 관련된 하위 명령입니다.|
|[azcopy load clfs](storage-ref-azcopy-load-avere-cloud-file-system.md?toc=/azure/storage/blobs/toc.json)|로컬 데이터를 컨테이너로 전송하고 Microsoft Avere CLFS(Cloud FileSystem) 형식으로 저장합니다.|
|[azcopy list](storage-ref-azcopy-list.md?toc=/azure/storage/blobs/toc.json)|지정된 리소스의 엔터티를 나열합니다.|
|[azcopy login](storage-ref-azcopy-login.md?toc=/azure/storage/blobs/toc.json)|Azure Active Directory에 로그인하여 Azure Storage 리소스에 액세스합니다.|
|[azcopy logout](storage-ref-azcopy-logout.md?toc=/azure/storage/blobs/toc.json)|사용자를 로그아웃시키고 Azure Storage 리소스에 대한 액세스를 종료합니다.|
|[azcopy make](storage-ref-azcopy-make.md?toc=/azure/storage/blobs/toc.json)|컨테이너 또는 파일 공유를 만듭니다.|
|[azcopy remove](storage-ref-azcopy-remove.md?toc=/azure/storage/blobs/toc.json)|Azure Storage 계정에서 Blob 또는 파일을 삭제합니다.|
|[azcopy sync](storage-ref-azcopy-sync.md?toc=/azure/storage/blobs/toc.json)|원본 위치를 대상 위치로 복제합니다.|

> [!NOTE]
> AzCopy에는 파일의 이름을 바꾸는 명령이 없습니다.

## <a name="use-in-a-script"></a>스크립트에서 사용

#### <a name="obtain-a-static-download-link"></a>정적 다운로드 링크 가져오기

시간이 지나면서 AzCopy [다운로드 링크](#download-and-install-azcopy)는 새 버전의 AzCopy를 가리킵니다. 스크립트를 통해 AzCopy를 다운로드하는 경우 스크립트가 종속하는 기능이 새 버전의 AzCopy로 인해 수정되면 스크립트가 작동하지 않을 수 있습니다.

문제를 방지하려면 현재 버전의 AzCopy로 연결되는 정적(변경되지 않는) 링크를 가져옵니다. 이렇게 하면 스크립트가 실행될 때마다 동일한 정확한 버전의 AzCopy가 다운로드됩니다.

링크를 가져오려면 다음 명령을 실행합니다.

| 운영 체제  | 명령 |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Linux의 경우 `tar` 명령의 `--strip-components=1`은 버전 이름이 포함된 최상위 폴더를 제거하고 대신 이진 파일을 현재 폴더로 직접 추출합니다. 이렇게 하면 `wget` URL만 업데이트해도 스크립트가 새 버전의 `azcopy`로 업데이트됩니다.

URL은 이 명령의 출력에 표시됩니다. 그러면 스크립트에서 해당 URL을 사용하여 AzCopy를 다운로드할 수 있습니다.

| 운영 체제  | 명령 |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>SAS 토큰의 이스케이프 특수 문자

`.cmd` 확장명이 있는 일괄 처리 파일에서는 SAS 토큰에 표시되는 `%` 문자를 이스케이프해야 합니다. SAS 토큰 문자열에서 기존 `%` 문자 옆에 추가 `%` 문자를 추가하여 이 작업을 수행할 수 있습니다.

#### <a name="run-scripts-by-using-jenkins"></a>Jenkins를 사용하여 스크립트 실행

[Jenkins](https://jenkins.io/)를 사용하여 스크립트를 실행하려는 경우 스크립트의 시작 부분에 다음 명령을 배치해야 합니다.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Azure Storage Explorer에서 사용

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)는 AzCopy를 사용하여 모든 데이터 전송 작업을 수행합니다. AzCopy의 성능상 이점을 활용하려는 경우 [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용할 수 있지만 명령줄이 아닌 그래픽 사용자 인터페이스를 사용하여 파일과 상호작용하는 것이 좋습니다.

Storage Explorer는 계정 키를 사용하여 작업을 수행하므로 Storage Explorer에 로그인 한 후에는 추가 권한 부여 자격 증명을 제공할 필요가 없습니다.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>구성, 최적화 및 수정

다음 리소스 중 하나를 참조하세요.

- [AzCopy 구성 설정](storage-ref-azcopy-configuration-settings.md)

- [AzCopy의 성능 최적화](storage-use-azcopy-optimize.md)

- [로그 파일을 사용하여 Azure Storage의 AzCopy V10 문제 해결](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>이전 버전 사용

이전 버전의 AzCopy를 사용해야 하는 경우 다음 링크 중 하나를 참조하세요.

- [Windows에서 AzCopy(v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [Linux에서 AzCopy(v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>다음 단계

질문, 문제 또는 일반 피드백은 [GitHub](https://github.com/Azure/azure-storage-azcopy)에서 제출하시면 됩니다.
