---
title: AzCopy v10 구성 설정(Azure Storage) | Microsoft Docs
description: 이 문서에서는 AzCopy V10 구성 설정에 관한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7c3ad407b0c412e3cf4dbb5cc1635376c718a747
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605813"
---
# <a name="azcopy-v10-configuration-settings-azure-storage"></a>AzCopy v10 구성 설정(Azure Storage)

AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에는 AzCopy v10을 구성하는 데 사용할 수 있는 환경 변수 목록이 포함됩니다.

> [!NOTE]
> AzCopy를 시작하는 데 도움이 되는 콘텐츠를 찾고 있다면 [AzCopy 시작](storage-use-azcopy-v10.md)을 참조하세요.

## <a name="azcopy-v10-environment-variables"></a>AzCopy v10 환경 변수

다음 표에서는 각 환경 변수를 설명하고 변수를 사용하는 데 도움이 될 수 있는 콘텐츠의 링크를 제공합니다.

| 환경 변수 | 설명 |
|--|--|
| AWS_ACCESS_KEY_ID | Amazon Web Services 액세스 키입니다. Amazon Web Services를 사용하여 권한을 부여할 키를 제공합니다. [AzCopy를 사용하여 Amazon S3에서 Azure Storage로 데이터 복사](storage-use-azcopy-s3.md) |
| AWS_SECRET_ACCESS_KEY | Amazon Web Services 비밀 액세스 키는 Amazon Web Services를 사용하여 권한을 부여할 비밀 키를 제공합니다. [AzCopy를 사용하여 Amazon S3에서 Azure Storage로 데이터 복사](storage-use-azcopy-s3.md) |
| AZCOPY_ACTIVE_DIRECTORY_ENDPOINT | 사용할 Azure Active Directory 엔드포인트입니다. 이 변수는 자동 로그인에만 사용됩니다. login 명령을 호출할 때는 명령줄 플래그를 대신 사용하세요. |
| AZCOPY_AUTO_LOGIN_TYPE | 이 변수를 `DEVICE`, `MSI` 또는 `SPN`으로 설정합니다. 이 변수는 `azcopy login` 명령을 사용하지 않고 권한을 부여하는 기능을 제공합니다. 이 메커니즘은 운영 체제에 Linux ‘인증 키’와 같은 비밀 저장소가 없는 경우에 유용합니다. [비밀 저장소 없이 권한 부여](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)를 참조하세요. |
| AZCOPY_BUFFER_GB | 파일을 다운로드하고 업로드할 때 AzCopy를 사용할 시스템 메모리의 최대 크기를 지정합니다. 이 값을 기가바이트(GB) 단위로 표시합니다. [메모리 사용 최적화](storage-use-azcopy-optimize.md#optimize-memory-use)를 참조하세요. |
| AZCOPY_CACHE_PROXY_LOOKUP | 기본적으로 AzCopy on Windows는 호스트 이름 수준에서 프록시 서버 조회를 캐시합니다(URL 경로를 고려하지 않음). 캐시를 사용하지 않으려면 ‘true’ 이외의 다른 값으로 설정합니다. |
| AZCOPY_CONCURRENCY_VALUE | 발생할 수 있는 동시 요청 수를 지정합니다. 이 변수를 사용하여 처리량을 늘릴 수 있습니다. 컴퓨터에 5개 미만의 CPU가 있는 경우 이 변수의 값은 `32`로 설정됩니다. 그렇지 않으면 기본값은 16에 CPU 수를 곱한 값과 같습니다. 이 변수의 최대 기본값은 `3000`이지만 수동으로 이 값을 더 높거나 낮게 설정할 수 있습니다. [동시성 향상](storage-use-azcopy-optimize.md#increase-concurrency)을 참조하세요. |
| AZCOPY_CONCURRENT_FILES | 동시에 전송을 시작하는 파일 수를 제어하여 한 번에 진행 중인 대략적인 파일 수를 재정의합니다. |
| AZCOPY_CONCURRENT_SCAN | 검색 중에 사용되는 최대 병렬 처리 수준을 제어합니다. Azure Files/Blob 및 로컬 파일 시스템을 포함하는 병렬 처리된 열거자에만 영향을 줍니다. |
| AZCOPY_CONTENT_TYPE_MAP  | 운영 체제에서 정의한 하나 이상의 기본 MIME 형식 매핑을 재정의합니다. 매핑을 정의하는 JSON 파일의 경로로 이 변수를 설정합니다.  예제 JSON 파일의 내용은 다음과 같습니다. <br><br> {<br>&nbsp;&nbsp;"MIMETypeMapping": { <br>&nbsp;&nbsp;&nbsp;&nbsp;".323": "text/h323",<br>&nbsp;&nbsp;&nbsp;&nbsp;".aaf": "application/octet-stream",<br>&nbsp;&nbsp;&nbsp; ".aca": "application/octet-stream",<br>&nbsp;&nbsp;&nbsp;&nbsp;".accdb": "application/msaccess",<br>&nbsp;&nbsp;&nbsp;&nbsp;  }<br>}
|
| AZCOPY_DEFAULT_SERVICE_API_VERSION | AzCopy가 Azure Stack과 같은 사용자 지정 환경을 수용할 수 있도록 서비스 API 버전을 재정의합니다. |
| AZCOPY_DISABLE_HIERARCHICAL_SCAN | Azure Blob이 원본인 경우에만 적용됩니다. 동시 검사는 더 빠르지만 계층 목록 API를 사용하므로 더 많은 IO/비용이 발생할 수 있습니다. 성능이 저하되지만 비용을 절감하려면 ‘true’를 지정합니다. |
| AZCOPY_JOB_PLAN_LOCATION | 디스크를 채우지 않도록 작업 계획 파일(진행률 추적 및 다시 시작에 사용됨)이 저장되는 위치를 재정의합니다. |
| AZCOPY_LOG_LOCATION | 디스크를 채우지 않도록 로그 파일이 저장되는 위치를 재정의합니다. |
| AZCOPY_MSI_CLIENT_ID | 사용자가 할당한 관리 ID의 클라이언트 ID입니다. `AZCOPY_AUTO_LOGIN_TYPE`이 `MSI`로 설정된 경우 사용합니다. [비밀 저장소 없이 권한 부여](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)를 참조하세요. |
| AZCOPY_MSI_OBJECT_ID | 사용자가 할당한 관리 ID의 개체 ID입니다. `AZCOPY_AUTO_LOGIN_TYPE`이 `MSI`로 설정된 경우 사용합니다. [비밀 저장소 없이 권한 부여](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)를 참조하세요. |
| AZCOPY_MSI_RESOURCE_STRING | 사용자가 할당한 관리 ID의 리소스 ID입니다. [비밀 저장소 없이 권한 부여](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)를 참조하세요. |
| AZCOPY_PACE_PAGE_BLOBS | 페이지 Blob의 처리량을 서비스 한도에 맞게 자동으로 조정해야 하나요? 기본값은 true입니다. 사용하지 않으려면 ‘false’로 설정합니다. |
| AZCOPY_PARALLEL_STAT_FILES | 로컬 파일 시스템을 검사할 때 AzCopy가 병렬 ‘스레드’에서 파일 속성을 조회하도록 합니다.  스레드는 AZCOPY_CONCURRENT_SCAN에 의해 정의된 풀에서 가져옵니다.  이를 true로 설정하면 Linux에서 검사 성능이 향상될 수 있습니다.  Windows에서는 필요하지 않거나 권장되지 않습니다. |
| AZCOPY_SHOW_PERF_STATES | 어떤 것으로든 설정하면 화면 출력에 상태별 청크 수가 포함됩니다. |
| AZCOPY_SPA_APPLICATION_ID | 서비스 주체 앱 등록의 애플리케이션 ID입니다. `AZCOPY_AUTO_LOGIN_TYPE`이 `SPN`으로 설정된 경우 사용합니다. [비밀 저장소 없이 권한 부여](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)를 참조하세요. |
| AZCOPY_SPA_CERT_PASSWORD | 인증서의 암호입니다. `AZCOPY_AUTO_LOGIN_TYPE`이 `SPN`으로 설정된 경우 사용합니다. [비밀 저장소 없이 권한 부여](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)를 참조하세요. |
| AZCOPY_SPA_CERT_PATH | 인증서 파일의 상대 경로 또는 정규화된 경로입니다. `AZCOPY_AUTO_LOGIN_TYPE`이 `SPN`으로 설정된 경우 사용합니다. [비밀 저장소 없이 권한 부여](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)를 참조하세요. |
| AZCOPY_SPA_CLIENT_SECRET | 클라이언트 암호입니다. `AZCOPY_AUTO_LOGIN_TYPE`이 `SPN`으로 설정된 경우 사용합니다. [비밀 저장소 없이 권한 부여](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)를 참조하세요. |
| AZCOPY_TENANT_ID | OAuth 디바이스 대화형 로그인에 사용할 Azure Active Directory 테넌트 ID입니다. 이 변수는 자동 로그인에만 사용됩니다. login 명령을 호출할 때는 명령줄 플래그를 대신 사용하세요. |
| AZCOPY_TUNE_TO_CPU | 해당 동시성 수준을 자동 조정하는 경우(예: 벤치마크 명령에서) AzCopy에서 CPU 사용량을 고려하지 않으려면 false로 설정합니다. |
| AZCOPY_USER_AGENT_PREFIX | 원격 분석 용도로 사용되는 기본 AzCopy 사용자 에이전트에 접두사를 추가합니다. 공백이 자동으로 삽입됩니다. |
| GOOGLE_APPLICATION_CREDENTIALS | 서비스 계정 키 파일의 절대 경로는 Google Cloud Storage를 사용하여 권한을 부여할 키를 제공합니다. [AzCopy를 사용하여 Google Cloud Storage에서 Azure Storage로 데이터 복사(미리 보기)](storage-ref-azcopy-configuration-settings.md) |
| HTTPS_PROXY | AzCopy의 프록시 설정을 구성합니다. 이 변수를 프록시 IP 주소 및 프록시 포트 번호로 설정합니다. 예: `xx.xxx.xx.xxx:xx`. Windows에서 AzCopy를 실행하는 경우 AzCopy가 프록시 설정을 자동으로 검색하므로 Windows에서 이 설정을 사용할 필요가 없습니다. Windows에서 이 설정을 사용하도록 선택하면 자동 검색이 재정의됩니다. [프록시 설정 구성](#configure-proxy-settings)을 참조하세요. |

## <a name="configure-proxy-settings"></a>프록시 설정 구성

AzCopy에 대한 프록시 설정을 구성하려면 `HTTPS_PROXY` 환경 변수를 설정합니다. Windows에서 AzCopy를 실행하는 경우 AzCopy가 프록시 설정을 자동으로 검색하므로 Windows에서 이 설정을 사용할 필요가 없습니다. Windows에서 이 설정을 사용하도록 선택하면 자동 검색이 재정의됩니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | 명령 프롬프트에서 `set HTTPS_PROXY=<proxy IP>:<proxy port>`를 사용합니다.<br> PowerShell에서 `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`를 사용합니다.|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

현재 AzCopy는 NTLM 또는 Kerberos 인증을 요구하는 프록시를 지원하지 않습니다.

### <a name="bypassing-a-proxy"></a>프록시 우회

Windows에서 AzCopy를 실행 중이고 설정을 자동 검색하는 대신 프록시를 전혀 사용하지 *않도록* 지시하려면 다음 명령을 사용합니다. 이 설정을 사용하면 AzCopy에서 프록시를 조회하거나 사용하지 않습니다.

| 운영 체제 | 환경 | 명령  |
|--------|-----------|----------|
| **Windows** | CMD(명령 프롬프트) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

다른 운영 체제에서는 프록시를 사용하지 않으려면 HTTPS_PROXY 변수를 설정하지 않은 상태로 두면 됩니다.

## <a name="see-also"></a>참조

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [Azure Storage를 사용하여 AzCopy v10의 성능 최적화](storage-use-azcopy-optimize.md)
- [로그 파일을 사용하여 Azure Storage의 AzCopy V10 문제 해결](storage-use-azcopy-configure.md)
