---
title: Azure Storage Explorer의 네트워크 연결 | Microsoft Docs
description: Azure Storage Explorer의 네트워크 연결에 대한 설명서
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: marayerm
ms.openlocfilehash: 2731f4473b726e7433167fe15c9e1f29c775d9cb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627884"
---
# <a name="network-connections-in-storage-explorer"></a>저장소 탐색기의 네트워크 연결

로컬 에뮬레이터에 연결하지 않을 때 저장소 탐색기는 네트워크를 사용하여 스토리지 리소스와 기타 Azure 및 Microsoft 서비스에 요청합니다.

## <a name="hostnames-accessed-by-storage-explorer"></a>저장소 탐색기에서 액세스하는 호스트 이름

저장소 탐색기는 사용 중에 다양한 엔드포인트에 요청합니다. 다음 목록에서는 Storage Explorer 요청하는 일반적인 호스트 이름을 자세히 설명합니다.

- ARM 엔드포인트:
  - `management.azure.com`(글로벌 Azure)
  - `management.chinacloudapi.cn`(Azure 중국)
  - `management.microsoftazure.de`(Azure 독일)
  - `management.usgovcloudapi.net`(Azure 미국 정부)
- 로그인 엔드포인트:
  - `login.microsoftonline.com`(글로벌 Azure)
  - `login.chinacloudapi.cn`(Azure 중국)
  - `login.microsoftonline.de`(Azure 독일)
  - `login.microsoftonline.us`(Azure 미국 정부)
- 그래프 엔드포인트:
  - `graph.windows.net`(글로벌 Azure)
  - `graph.chinacloudapi.cn`(Azure 중국)
  - `graph.cloudapi.de`(Azure 독일)
  - `graph.windows.net`(Azure 미국 정부)
- Azure Storage 엔드포인트:
  - `(blob|file|queue|table|dfs).core.windows.net`(글로벌 Azure)
  - `(blob|file|queue|table|dfs).core.chinacloudapi.cn`(Azure 중국)
  - `(blob|file|queue|table|dfs).core.cloudapi.de`(Azure 독일)
  - `(blob|file|queue|table|dfs).core.usgovcloudapi.net`(Azure 미국 정부)
- 저장소 탐색기 업데이트: `storageexplorerpublish.blob.core.windows.net`
- Microsoft 링크 전달:
  - `aka.ms`
  - `go.microsoft.com`
- 리소스가 뒤에 있는 모든 사용자 지정 도메인, 개인 링크 또는 Azure Stack 인스턴스별 엔드포인트
- 원격 에뮬레이터 호스트 이름

## <a name="proxy-sources"></a>프록시 원본

저장소 탐색기에는 프록시에 연결하는 데 필요한 정보를 제공하는 방법/위치에 대한 몇 가지 옵션이 있습니다. 사용 중인 옵션을 변경하려면 **설정**(왼쪽 세로 도구 모음의 톱니바퀴 아이콘) > **애플리케이션** > **프록시** 로 이동합니다. 설정의 프록시 섹션에서 저장소 탐색기가 프록시 설정을 제공할 방법/위치를 선택할 수 있습니다.
- 프록시 사용 안 함
- 환경 변수 사용
- 앱 프록시 설정 사용
- 시스템 프록시 사용(미리 보기)

### <a name="do-not-use-proxy"></a>프록시 사용 안 함

이 옵션을 선택하면 저장소 탐색기가 프록시 연결을 시도하지 않습니다. 프록시 사용 안 함은 기본 옵션입니다.

### <a name="use-environment-variables"></a>환경 변수 사용

이 옵션을 선택하면 저장소 탐색기가 특정 환경 변수에서 프록시 정보를 찾습니다. 이러한 변수는 다음과 같습니다.
- `HTTP_PROXY`
- `HTTPS_PROXY`

두 변수가 모두 정의된 경우 저장소 탐색기가 `HTTPS_PROXY`에서 프록시 정보를 제공합니다.

이러한 환경 변수의 값은 다음 형식의 URL이어야 합니다.

`(http|https)://(username:password@)<hostname>:<port>`

프로토콜(`http|https`)과 호스트 이름만 필요합니다. 사용자 이름이 있는 경우에는 암호를 제공할 필요가 없습니다.

### <a name="use-app-proxy-settings"></a>앱 프록시 설정 사용

이 옵션을 선택하면 저장소 탐색기가 앱 내 프록시 설정을 사용합니다. 설정에는 다음이 포함됩니다.
- 프로토콜
- 호스트 이름
- 포트
- 자격 증명 사용/사용 안 함
- 자격 증명

자격 증명 이외의 모든 설정은 다음 중 하나에서 관리될 수 있습니다.
- **설정**(왼쪽 세로 도구 모음의 톱니바퀴 아이콘) > **애플리케이션** > **프록시** > **자격 증명 사용**
- 프록시 설정 대화 상자(**편집** > **프록시 구성**)

자격 증명을 설정하려면 프록시 설정 대화 상자(**편집** > **프록시 구성**)로 이동해야 합니다.

### <a name="use-system-proxy-preview"></a>시스템 프록시 사용(미리 보기)

이 옵션을 선택하면 저장소 탐색기가 OS 프록시 설정을 사용합니다. 더 구체적으로 말하면 Chromium 네트워킹 스택을 사용하여 네트워크 호출이 발생합니다. Chromium 네트워킹 스택은 일반적으로 저장소 탐색기에서 사용하는 NodeJS 네트워킹 스택보다 훨씬 강력합니다. 다음은 [Chromium 설명서](https://www.chromium.org/developers/design-documents/network-settings)의 모든 기능에 대한 조각입니다.

> Chromium 네트워크 스택은 시스템 네트워크 설정을 사용하므로 사용자와 관리자가 모든 애플리케이션의 네트워크 설정을 쉽게 제어할 수 있습니다. 네트워크 설정에는 다음이 포함됩니다.
> - 프록시 설정
> - SSL/TLS 설정
> - 인증서 해지 확인 설정
> - 인증서 및 프라이빗 키 저장소

프록시 서버에 자격 증명이 필요하고 해당 자격 증명이 OS 설정에서 구성되지 않은 경우 저장소 탐색기에서 자격 증명을 사용하도록 설정해야 합니다. 다음 중 하나에서 자격 증명 사용을 전환할 수 있습니다.
- **설정**(왼쪽 세로 도구 모음의 톱니바퀴 아이콘) > **애플리케이션** > **프록시** > **자격 증명 사용**
- 프록시 설정 대화 상자(**편집** > **프록시 구성**)

자격 증명을 설정하려면 프록시 설정 대화 상자(**편집** > **프록시 구성**)로 이동해야 합니다.

현재 모든 기능이 시스템 프록시를 지원하는 것은 아니기 때문에 이 옵션은 미리 보기로 제공됩니다. 전체 기능 목록은 [시스템 프록시를 지원하는 기능](#features-that-support-system-proxy)을 참조하세요. 시스템 프록시가 사용하도록 설정되면 시스템 프록시를 지원하지 않는 기능은 프록시에 연결을 시도하지 않습니다.

지원되는 기능이 있는 시스템 프록시를 사용하는 동안 문제가 발생하면 [GitHub에서 문제를 엽니다](https://github.com/Microsoft/AzureStorageExplorer/issues/new).

## <a name="proxy-server-authentication"></a>프록시 서버 인증

**환경 변수** 또는 **앱 프록시 설정** 에서 프록시 설정을 제공하도록 저장소 탐색기를 구성한 경우 기본 인증을 사용하는 프록시 서버만 지원됩니다.

**시스템 프록시** 를 사용하도록 저장소 탐색기를 구성한 경우 다음 인증 방법 중 하나를 사용하는 프록시 서버가 지원됩니다.
- 기본
- 다이제스트
- NTLM
- Negotiate

## <a name="which-proxy-source-should-i-choose"></a>어떤 프록시 원본을 선택해야 합니까?

[시스템 프록시를 지원하는 기능](#features-that-support-system-proxy)만 사용하는 경우 먼저 [**시스템 프록시**](#use-system-proxy-preview)를 사용해보세요. 지원되는 기능이 있는 시스템 프록시를 사용하는 동안 문제가 발생하면 [GitHub에서 문제를 엽니다](https://github.com/Microsoft/AzureStorageExplorer/issues/new).

시스템 프록시를 지원하지 않는 기능을 사용하는 경우에는 [**앱 설정**](#use-app-proxy-settings)이 차선책일 것입니다. 프록시 구성을 구성하기 위한 GUI 기반 환경은 프록시 정보를 올바르게 입력할 가능성을 줄입니다. 그러나 프록시 환경 변수가 이미 구성된 경우에는 [**환경 변수**](#use-environment-variables)를 사용하는 것이 더 나을 수 있습니다.

## <a name="azcopy-proxy-usage"></a>AzCopy 프록시 사용

저장소 탐색기는 대부분의 데이터 전송 작업에 AzCopy를 사용합니다. AzCopy는 저장소 탐색기와는 다른 기술 집합을 사용하여 작성되므로 프록시 기능 집합이 약간 다릅니다.

저장소 탐색기가 **프록시를 사용하지 않거나** **시스템 프록시** 를 사용하도록 구성된 경우, AzCopy는 자체 자동 감지 프록시 기능을 사용하여 프록시에 요청해야 하는지 여부와 방법을 결정합니다. **환경 변수** 또는 **앱 프록시 설정** 에서 프록시 설정을 제공하도록 저장소 탐색기를 구성한 경우 저장소 탐색기는 AzCopy에 동일한 프록시 설정을 사용하도록 지시합니다.

AzCopy에서 프록시를 사용하지 않으려면 **설정**(왼쪽 세로 도구 모음의 톱니바퀴 아이콘) > **전송** > **AzCopy** > **AzCopy 프록시 사용 안 함** 을 전환하여 프록시 사용을 사용하지 않도록 설정하면 됩니다.

현재 AzCopy는 **기본 인증** 을 사용하는 프록시 서버만 지원합니다.

## <a name="ssl-certificates"></a>SSL 인증서

기본적으로 저장소 탐색기는 NodeJS 네트워킹 스택을 사용합니다. NodeJS는 미리 정의된 신뢰할 수 있는 SSL 인증서 목록과 함께 제공됩니다. 프록시 서버 또는 바이러스 백신 소프트웨어와 같은 일부 네트워킹 기술은 자체 SSL 인증서를 네트워크 트래픽에 삽입합니다. 이러한 인증서는 NodeJS의 인증서 목록에 없는 경우가 많습니다. NodeJS는 이러한 인증서를 포함하는 응답을 신뢰하지 않습니다. NodeJS가 응답을 신뢰하지 않으면 저장소 탐색기에 오류가 수신됩니다.

이러한 오류를 해결하는 여러 옵션이 있습니다.
- [**시스템 프록시**](#use-system-proxy-preview)를 프록시 원본으로 사용합니다.
- 오류를 발생시키는 SSL 인증서의 복사본을 가져옵니다.
- SSL 인증서를 사용하지 않도록 설정합니다. (**권장하지 않음**)

## <a name="features-that-support-system-proxy"></a>시스템 프록시를 지원하는 기능

다음은 **시스템 프록시** 를 지원하는 기능 목록입니다.

- 업데이트 확인 및 다운로드
- 구독 나열
- 스토리지 계정 기능
  - 목록
- Blob 기능
  - 컨테이너
    - 만들기
    - 목록
    - 저장된 액세스 정책 관리
    - 공용 액세스 수준 변경
    - 임대
    - 속성
    - 삭제
  - Blob
    - 목록
    - 통계
    - 삭제 취소
  - ADLS Gen2 Blob
    - 목록
    - 통계
    - ACL 관리(기존 엔터티 보기 및 수정만 해당)
    - ACL 전파
    - 이동
    - 이름 바꾸기
    - 폴더 만들기
- 큐 기능
  - 큐
    - 만들기
    - 목록
    - 액세스 정책 관리
    - 속성
    - 삭제
    - 지우기
  - 큐 메시지
    - 목록
    - 이동
    - 추가
    - 큐에서 제거
- 파일 공유 기능
  - 파일 및 폴더
    - 새 폴더
    - 속성
- 디스크 기능
  - 리소스 그룹 나열
  - 디스크 나열
  - 디스크 업로드
  - 디스크 다운로드
  - 디스크 복사
  - 스냅숏 만들기
  - 디스크 삭제

## <a name="next-steps"></a>다음 단계

- [프록시 문제 해결](./storage-explorer-troubleshooting.md#proxy-issues)
- [인증서 문제 해결](./storage-explorer-troubleshooting.md#ssl-certificate-issues)
