---
title: Azure Storage Explorer 문제 해결 가이드 | Microsoft Docs
description: Azure Storage Explorer의 디버깅 기술 개요
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: 3feae35a4b7c98ea317bb9cec5dd54ca3f5db63c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562517"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage Explorer 문제 해결 가이드

Microsoft Azure Storage Explorer는 Windows, macOS 및 Linux에서 Azure Storage 데이터를 쉽게 사용할 수 있게 하는 독립 실행형 앱입니다. 앱은 Azure, National Clouds 및 Azure Stack에서 호스트되는 스토리지 계정에 연결할 수 있습니다.

이 가이드에는 Storage Explorer에 일반적으로 표시되는 문제에 대한 솔루션이 요약되어 있습니다.

## <a name="azure-rbac-permissions-issues"></a>Azure RBAC 사용 권한 문제

[Azure RBAC](../../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어)를 사용하면 권한 집합을 *역할* 로 결합하여 Azure 리소스에 대한 액세스를 매우 세부적으로 관리할 수 있습니다. 다음은 Storage Explorer에서 Azure RBAC가 최적으로 작동하도록 하기 위한 몇 가지 전략입니다.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Storage Explorer에서 내 리소스에 액세스하려면 어떻게 해야 하나요?

Azure RBAC를 통해 스토리지 리소스에 액세스하는 데 문제가 있는 경우 적절한 역할이 할당되지 않았을 수 있습니다. 다음 섹션에서는 Storage Explorer가 현재 스토리지 리소스에 액세스하는 데 필요한 권한을 설명합니다. 적절한 역할 또는 권한이 있는지 확실하지 않은 경우 Azure 계정 관리자에게 문의하세요.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"읽기: 스토리지 계정 나열/가져오기" 권한 문제

스토리지 계정 나열에 대한 권한이 있어야 합니다. 이 권한을 얻으려면 *읽기 권한자* 역할이 할당되어야 합니다.

#### <a name="list-storage-account-keys"></a>스토리지 계정 키 나열

Storage Explorer는 계정 키를 사용하여 요청을 인증할 수도 있습니다. *기여자* 역할과 같은 보다 강력한 역할을 통해 계정 키에 액세스할 수 있습니다.

> [!NOTE]
> 액세스 키는 이를 보유한 모든 사용자에게 무제한 권한을 부여합니다. 따라서 이러한 키를 계정 사용자에게 제공하지 않는 것이 좋습니다. 액세스 키를 철회해야 하는 경우 [Azure Portal](https://portal.azure.com/)에서 다시 생성할 수 있습니다.

#### <a name="data-roles"></a>데이터 역할

리소스에서 데이터를 읽을 수 있는 액세스 권한을 부여하는 역할이 하나 이상 할당되어야 합니다. 예를 들어 Blob을 나열하거나 다운로드하려면 최소한 *Storage Blob 데이터 읽기 권한자* 역할이 필요합니다.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Storage Explorer에서 내 리소스를 볼 때 관리 계층 역할이 필요한 이유는 무엇인가요?

Azure Storage에는 *관리* 및 *데이터* 라는 두 가지 액세스 계층이 있습니다. 구독 및 스토리지 계정은 관리 계층을 통해 액세스됩니다. 컨테이너, Blob 및 기타 데이터 리소스는 데이터 계층을 통해 액세스됩니다. 예를 들어 Azure에서 스토리지 계정 목록을 가져오려면 관리 엔드포인트에 요청을 보냅니다. 계정의 Blob 컨테이너 목록이 필요한 경우 적절한 서비스 엔드포인트에 요청을 보냅니다.

Azure 역할은 관리 또는 데이터 계층 액세스 권한을 부여할 수 있습니다. 예를 들어 읽기 권한자 역할은 관리 계층 리소스에 대한 읽기 전용 액세스 권한을 부여합니다.

엄밀히 말하면 읽기 권한자 역할은 데이터 계층 권한을 제공하지 않으며 데이터 계층에 액세스하는 데 필요하지 않습니다.

Storage Explorer를 사용하면 Azure 리소스에 연결하는 데 필요한 정보를 수집하여 리소스에 쉽게 액세스할 수 있습니다. 예를 들어 Blob 컨테이너를 표시하기 위해 Storage Explorer는 Blob 서비스 엔드포인트에 "컨테이너 나열" 요청을 보냅니다. Storage Explorer는 해당 엔드포인트를 가져오기 위해 액세스 권한이 있는 구독 및 스토리지 계정 목록을 검색합니다. 또한 구독 및 스토리지 계정을 찾으려면 Storage Explorer에 관리 계층에 대한 액세스 권한이 있어야 합니다.

관리 계층 권한을 부여하는 역할이 없는 경우 Storage Explorer 데이터 계층에 연결하는 데 필요한 정보를 얻을 수 없습니다.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>관리자로부터 필요한 관리 계층 권한을 얻을 수 없는 경우 어떻게 해야 하나요?

Blob 컨테이너, ADLS Gen2 컨테이너나 디렉터리, 큐에 액세스하려는 경우 Azure 자격 증명을 사용하여 해당 리소스에 연결할 수 있습니다.

1. 연결 대화 상자를 엽니다.
1. 연결할 리소스 유형을 선택합니다.
1. **Azure AD(Azure Active Directory)를 사용하여 로그인** 을 선택합니다. **다음** 을 선택합니다.
1. 연결하려는 리소스와 연결된 사용자 계정 및 테넌트를 선택합니다. **다음** 을 선택합니다.
1. 리소스에 대한 URL을 입력하고 연결에 대한 고유한 표시 이름을 입력합니다. **다음** 을 선택하고 **연결** 을 선택합니다.

다른 리소스 유형의 경우 현재 Azure RBAC 관련 솔루션이 없습니다. 이 문제를 해결하려면 다음 단계에 따라 SAS URL을 요청한 후 리소스에 연결합니다.

1. 연결 대화 상자를 엽니다.
1. 연결할 리소스 유형을 선택합니다.
1. **SAS(공유 액세스 서명)** 를 선택합니다. **다음** 을 선택합니다.
1. 받은 SAS URL을 입력하고 연결에 대한 고유한 표시 이름을 입력합니다. **다음** 을 선택하고 **연결** 을 선택합니다.

리소스에 연결하는 방법에 대한 자세한 내용은 [개별 리소스 연결](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource)을 참조하세요.

### <a name="recommended-azure-built-in-roles"></a>권장되는 Azure 기본 제공 역할

Storage Explorer를 사용하는 데 필요한 권한을 제공할 수 있는 몇 가지 Azure 기본 제공 역할이 있습니다. 이러한 역할 중 일부는 다음과 같습니다.
- [소유자](../../role-based-access-control/built-in-roles.md#owner): 리소스에 대한 액세스를 포함한 모든 것을 관리합니다.
- [기여자](../../role-based-access-control/built-in-roles.md#contributor): 리소스에 대한 액세스를 제외한 모든 것을 관리합니다.
- [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader): 리소스를 읽고 나열합니다.
- [스토리지 계정 기여자](../../role-based-access-control/built-in-roles.md#storage-account-contributor): 스토리지 계정에 대한 전체 관리 권한을 갖습니다.
- [Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Azure Storage Blob 컨테이너 및 데이터에 대한 전체 액세스 권한을 갖습니다.
- [Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Azure Storage 컨테이너 및 Blob을 읽고, 쓰고, 삭제합니다.
- [Storage Blob 데이터 읽기 권한자](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Azure Storage 컨테이너 및 Blob을 읽고 나열합니다.

> [!NOTE]
> 소유자, 기여자 및 스토리지 계정 기여자 역할은 계정 키 액세스 권한을 부여합니다.

## <a name="ssl-certificate-issues"></a>SSL 인증서 문제

### <a name="understanding-ssl-certificate-issues"></a>SSL 인증서 문제 이해하기

계속하기 전에 Storage Explorer 네트워킹 문서의 [SSL 인증서 섹션](./storage-explorer-network.md#ssl-certificates)을 읽었는지 확인하십시오.

### <a name="use-system-proxy"></a>시스템 프록시 사용

**시스템 프록시 사용** 설정을 지원하는 기능만 사용하는 경우 해당 설정을 사용해 보십시오. **시스템 프록시** 설정에 대한 자세한 내용은 [여기](./storage-explorer-network.md#use-system-proxy-preview)를 참조하세요.

### <a name="importing-ssl-certificates"></a>SSL 인증서 내보내기

자체 서명된 인증서의 복사본이 있으면 다음 단계에 따라 Storage Explorer가 이를 신뢰하게 할 수 있습니다.

1. 인증서의 Base-64 인코딩 X.509(.cer) 사본 가져오기
2. **편집** > **SSL 인증서** > **인증서 가져오기** 로 이동한 다음, 파일 선택기를 사용하여 해당 .cer 파일을 찾아 선택하고 엽니다.

이 문제는 여러 인증서(루트 및 중간)가 있는 경우에도 발생할 수 있습니다. 이 오류를 해결하려면 두 인증서를 모두 추가해야 합니다.

### <a name="finding-ssl-certificates"></a>SSL 인증서 찾기

자체 서명된 인증서 사본이 없는 경우 IT 관리자에게 도움을 요청하십시오.

이러한 단계를 수행 하 여 찾을 수 있습니다.

1. OpenSSL을 설치합니다.
    - [Windows](https://slproweb.com/products/Win32OpenSSL.html): 라이트 버전이면 충분합니다.
    - Mac 및 Linux: 운영 체제에 포함되어야 합니다.
2. OpenSSL을 실행합니다.
    - Windows: 설치 디렉터리를 열고 **/bin/** 을 선택한 다음 **openssl.exe** 를 두 번 클릭합니다.
    - Mac 및 Linux: 터미널에서 `openssl`을 실행합니다.
3. 스토리지 리소스가 뒤에 있는 Microsoft 또는 Azure 호스트 이름에 대해 `s_client -showcerts -connect <hostname>:443` 명령을 실행합니다. 여기에서 Storage Explorer에서 자주 액세스하는 호스트 이름 목록을 찾을 수 있습니다.
4. 자체 서명된 인증서를 찾습니다. 제목 `("s:")` 및 발급자 `("i:")`가 동일한 경우 인증서가 자체 서명되었을 가능성이 큽니다.
5. 자체 서명된 인증서를 찾으면 각 인증서에 대해 `-----BEGIN CERTIFICATE-----`에서 `-----END CERTIFICATE-----`까지(및 포함) 모든 항목을 복사하여 새 .cer 파일에 붙여넣습니다.
6. Storage Explorer를 열고 **편집** > **SSL 인증서** > **인증서 가져오기** 로 이동합니다. 그런 다음 파일 선택기를 사용하여 생성한 .cer 파일을 찾아 선택하고 엽니다.

### <a name="disabling-ssl-certificate-validation"></a>SSL 인증서 유효성 검사 비활성화

다음 단계에 따라 자체 서명된 인증서를 찾을 수 없는 경우 피드백 도구를 통해 문의하세요. `--ignore-certificate-errors` 플래그를 사용하여 명령줄에서 Storage Explorer를 열 수도 있습니다. 이 플래그를 사용하여 열면 Storage Explorer에서 인증서 오류를 무시합니다. **이 플래그는 권장되지 않습니다.**

## <a name="sign-in-issues"></a>로그인 문제

### <a name="understanding-sign-in"></a>로그인 이해

계속하기 전에 [Storage Explorer에 로그인](./storage-explorer-sign-in.md) 문서를 읽었는지 확인하십시오.

### <a name="frequently-having-to-reenter-credentials"></a>자격 증명을 자주 다시 입력해야 함

자격 증명을 다시 입력해야 한다면 AAD 관리자가 조건부 액세스 정책을 설정했기 때문일 수 있습니다. Storage Explorer가 계정 패널에서 자격 증명을 다시 입력하라는 메시지를 표시하면 **오류 세부 정보...** 링크가 표시됩니다. 이를 클릭하여 Storage Explorer가 자격 증명을 다시 입력하도록 요청하는 이유를 확인합니다. 자격 증명을 다시 입력해야 하는 조건부 액세스 정책 오류는 다음과 같습니다.
- 새로 고침 토큰이 만료되었습니다...
- 액세스할 때 다단계 인증을 사용해야 합니다...
- 관리자의 구성 변경으로 인해...

위와 같은 오류로 인해 자격 증명을 다시 입력해야 하는 빈도를 줄이려면 AAD 관리자에게 문의해야 합니다.

### <a name="conditional-access-policies"></a>조건부 액세스 정책

계정에 대해 충족해야 하는 조건부 액세스 정책이 있는 경우 **로그인에 사용할 계정** 설정에 **기본 웹 브라우저** 값을 사용하고 있는지 확인합니다. 해당 설정에 대한 자세한 내용은 [로그인이 발생하는 위치 변경](./storage-explorer-sign-in.md#changing-where-sign-in-happens)을 참조하세요.

### <a name="browser-complains-about-http-redirect-during-sign-in"></a>로그인하는 동안 브라우저에서 HTTP 리디렉션에 대해 불만을 제기함

Storage Explorer가 웹 브라우저에서 로그인을 수행하면 로그인 프로세스가 끝날 때 `localhost`로 리디렉션됩니다. 브라우저에서 리디렉션이 HTTPS 대신 HTTP를 통해 수행되고 있다는 경고 또는 오류를 표시하는 경우가 있습니다. 일부 브라우저에서는 HTTPS를 사용하여 리디렉션을 강제로 수행하려고 할 수도 있습니다. 이 중 하나가 발생하면 브라우저에 따라 다양한 옵션이 있습니다.
- 경고를 무시합니다.
- `localhost`에 대한 예외를 추가합니다.
- 전역적으로 또는 `localhost`에 대해서만 HTTPS 강제 적용을 사용하지 않도록 설정합니다.

이러한 옵션을 수행할 수 없는 경우 [로그인이 발생하는 위치를 변경](./storage-explorer-sign-in.md#changing-where-sign-in-happens)할 수도 있습니다.

### <a name="unable-to-acquire-token-tenant-is-filtered-out"></a>테넌트가 필터링되어 토큰을 가져올 수 없음

테넌트가 필터링되어 토큰을 얻을 수 없다는 오류 메시지가 표시되면 필터링한 테넌트에 있는 리소스에 액세스하려고 시도하고 있음을 의미합니다. 테넌트를 필터링 해제하려면 **계정 패널** 로 이동하여 오류에 지정된 테넌트의 확인란이 선택되어 있는지 확인합니다. Storage Explorer에서의 테넌트 필터링에 대한 자세한 내용은 [계정 관리](./storage-explorer-sign-in.md#managing-accounts)를 참조하세요.

### <a name="authentication-library-failed-to-start-properly"></a>인증 라이브러리가 제대로 시작되지 않음

시작 시 Storage Explorer의 인증 라이브러리가 제대로 시작되지 않았다는 오류 메시지가 표시되면 설치 환경이 모든 [필수 조건](../../vs-azure-tools-storage-manage-with-storage-explorer.md#prerequisites)을 충족하는지 확인합니다. 필수 조건을 충족하지 못하는 것이 이 오류 메시지의 가장 큰 원인입니다.

설치 환경이 모든 필수 조건을 충족한다고 생각되면 [GitHub에서 문제를 엽니다](https://github.com/Microsoft/AzureStorageExplorer/issues/new). 문제를 열 때는 다음을 포함해야 합니다.
- 운영 체제
- 사용하려는 Storage Explorer의 버전
- 필수 조건을 충족했는지 여부
- Storage Explorer의 실행 실패에 대한 [인증 로그](#authentication-logs). 이러한 유형의 오류가 발생하면 자세한 인증 로깅이 자동으로 활성화됩니다.

### <a name="blank-window-when-using-integrated-sign-in"></a>통합 로그인 사용 시 빈 창 표시

**통합 로그인** 을 사용하도록 선택했는데 빈 로그인 창이 표시되는 경우 다른 로그인 방법으로 전환해야 할 수 있습니다. 빈 로그인 대화 상자는 ADFS(Active Directory Federation Services) 서버가 Electron에서 지원하지 않는 리디렉션을 Storage Explorer에서 수행하라는 메시지를 표시할 때 가장 자주 발생합니다.

다른 로그인 방법으로 변경하려면 **설정** > **애플리케이션** > **로그인** 에서 **로그인에 사용할 계정** 설정을 변경합니다. 다양한 유형의 로그인 방법에 대한 자세한 내용은 [로그인이 발생하는 위치 변경](./storage-explorer-sign-in.md#changing-where-sign-in-happens)을 참조하세요.

### <a name="reauthentication-loop-or-upn-change"></a>재인증 루프 또는 UPN 변경

재인증 루프에 있거나 계정 중 한 계정의 UPN을 변경한 경우 다음 단계를 시도하세요.

1. Storage Explorer를 엽니다.
2. 도움말 > 초기화로 이동합니다.
3. 최소한 인증이 선택되어 있는지 확인합니다. 초기화를 원하지 않는 다른 항목은 선택 취소할 수 있습니다.
4. 초기화 단추를 클릭합니다.
5. Storage Explorer를 다시 시작하고 다시 로그인합니다.

재설정 후에도 문제가 계속되면 다음 단계를 수행합니다.

1. Storage Explorer를 엽니다.
2. 모든 계정을 제거한 다음, Storage Explorer를 닫습니다.
3. 시스템에서 `.IdentityService` 폴더를 삭제합니다. Windows에서 폴더는 `C:\users\<username>\AppData\Local`에 있습니다. Mac 및 Linux의 경우 사용자 디렉토리의 루트에서 폴더를 찾을 수 있습니다.
4. Mac 또는 Linux를 실행하는 경우 운영 체제의 키 저장소에서 Microsoft.Developer.IdentityService 항목도 삭제해야 합니다. Mac에서 키 저장소는 *Gnome Keychain* 애플리케이션입니다. Linux에서 애플리케이션의 이름은 일반적으로 *Keyring* 이지만 배포에 따라 다를 수 있습니다.
6. Storage Explorer를 다시 시작하고 다시 로그인합니다.

### <a name="macos-keychain-errors-or-no-sign-in-window"></a>macOS: 키 집합 오류가 발생하거나 로그인 창이 표시되지 않음

macOS 키 집합은 Storage Explorer 인증 라이브러리에서 문제를 유발하는 상태가 될 수 있습니다. 이 상태에서 키 집합 문제를 해결하려면 다음 단계를 수행합니다.

1. Storage Explorer를 닫습니다.
2. 키 집합을 엽니다(Command+스페이스바를 누르고 **keychain** 을 입력한 다음 Enter 키 누름).
3. "로그인" 키 집합을 선택합니다.
4. 자물쇠 아이콘을 선택하여 키 집합을 잠급니다. (프로세스가 완료되면 자물쇠가 잠긴 상태로 표시됩니다. 열려 있는 앱에 따라 몇 초 정도 걸릴 수 있습니다.)

    ![자물쇠 아이콘](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Storage Explorer를 엽니다.
6. "서비스 허브에서 키 집합에 액세스하려고 합니다."와 같은 메시지가 표시됩니다. Mac 관리자 계정 암호를 입력하고 **항상 허용**(또는 **항상 허용** 을 사용할 수 없는 경우 **허용**)을 선택합니다.
7. 로그인을 시도합니다.

### <a name="default-browser-doesnt-open"></a>기본 브라우저가 열리지 않음

로그인을 시도할 때 기본 브라우저가 열리지 않으면 다음 기술을 모두 시도해 보세요.
- Storage Explorer 다시 시작
- 로그인을 시작하기 전에 브라우저를 수동으로 열기
- **통합 로그인** 사용. 방법에 대한 지침은 [로그인이 발생하는 위치 변경](./storage-explorer-sign-in.md#changing-where-sign-in-happens)을 참조하세요.

### <a name="other-sign-in-issues"></a>기타 로그인 문제

위의 설명이 해당 로그인 문제에 적용되지 않거나 로그인 문제를 해결할 수 없는 경우 [GitHub에서 문제를 엽니다](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>누락된 구독 및 손상된 테넌트

성공적으로 로그인한 후 구독을 검색할 수 없는 경우 다음 단계에 따라 문제를 해결합니다.

- 해당 계정이 원하는 구독에 액세스할 수 있는지 확인합니다. 사용하려는 Azure 환경의 포털에 로그인하여 액세스 권한이 있는지 확인할 수 있습니다.
- 올바른 Azure 환경(Azure, Azure 중국 21Vianet, Azure 독일, Azure 미국 정부 또는 사용자 지정 환경)을 통해 로그인했는지 확인합니다.
- 프록시 서버 뒤에 있는 경우 Storage Explorer 프록시를 올바르게 구성했는지 확인합니다.
- 계정을 제거하고 다시 추가해 봅니다.
- "추가 정보" 또는 "오류 세부 정보" 링크가 있는 경우 실패한 테넌트에 대해 보고되는 오류 메시지를 확인합니다. 오류 메시지에 어떻게 응답해야 할지 잘 모르는 경우 [GitHub에서 문제를 열어](https://github.com/Microsoft/AzureStorageExplorer/issues) 볼 수 있습니다.

## <a name="cant-remove-an-attached-storage-account-or-resource"></a>연결된 스토리지 계정 또는 리소스를 제거할 수 없음

연결된 계정이나 스토리지 리소스를 UI를 통해 제거할 수 없는 경우 다음 폴더를 삭제하여 연결된 리소스를 모두 수동으로 삭제할 수 있습니다.

- Windows: `%AppData%/StorageExplorer`
- macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
- Linux: `~/.config/StorageExplorer`

> [!NOTE]
> 이러한 폴더를 삭제하기 전에 먼저 Storage Explorer를 닫습니다.

> [!NOTE]
> SSL 인증서를 가져온 적이 있는 경우 `certs` 디렉터리의 콘텐츠를 백업합니다. 다음에는 백업을 사용하여 SSL 인증서를 다시 가져올 수 있습니다.

## <a name="proxy-issues"></a>프록시 문제

Storage Explorer는 프록시 서버를 통한 Azure Storage 리소스 연결을 지원합니다. 프록시를 통해 Azure에 연결하는 데 문제가 있는 경우 몇 가지 제안 사항이 있습니다.

> [!NOTE]
> Storage Explorer는 프록시 서버를 사용한 기본 인증만 지원합니다. NTLM과 같은 다른 인증 방법은 지원되지 않습니다.

> [!NOTE]
> Storage Explorer는 프록시 설정 구성에 대한 프록시 자동 구성 파일을 지원하지 않습니다.

### <a name="verify-storage-explorer-proxy-settings"></a>Storage Explorer 프록시 설정 확인

**애플리케이션 → 프록시 → 프록시 구성** 설정은 프록시 구성을 가져오는 원본 Storage Explorer를 결정합니다.

"환경 변수 사용"을 선택한 경우 `HTTPS_PROXY` 또는 `HTTP_PROXY` 환경 변수를 설정해야 합니다(환경 변수는 대/소문자를 구분하므로 정확하게 설정해야 함). 이러한 변수가 정의되지 않았거나 유효하지 않은 경우 Storage Explorer는 프록시를 사용하지 않습니다. 환경 변수를 수정한 후 Storage Explorer를 다시 시작합니다.

"앱 프록시 설정 사용"을 선택한 경우 앱 내 프록시 설정이 올바른지 확인합니다.

### <a name="steps-for-diagnosing-issues"></a>문제를 진단하기 위한 단계

여전히 문제가 발생하는 경우 다음 문제 해결 방법을 시도합니다.

1. 프록시를 사용하지 않고 인터넷에 연결할 수 있으면 프록시 설정을 사용하지 않고 Storage Explorer가 작동하는지 확인합니다. Storage Explorer가 성공적으로 연결되면 프록시 서버에 문제가 있는 것일 수 있습니다. 관리자와 함께 문제를 식별합니다.
2. 프록시 서버를 사용하는 다른 애플리케이션이 예상대로 작동하는지 확인합니다.
3. 사용할 Azure 환경에 대한 포털에 로그인하여 확인합니다.
4. 서비스 엔드포인트에서 응답을 수신할 수 있는지 확인합니다. 엔드포인트 URL 중 하나를 브라우저에 입력합니다. 연결할 수 있으면 `InvalidQueryParameterValue` 또는 유사한 XML 응답을 수신해야 합니다.
5. 동일한 프록시 서버로 Storage Explorer를 사용하는 다른 사람이 연결할 수 있는지 확인합니다. 연결할 수 있다면 프록시 서버 관리자에게 문의해야 할 수도 있습니다.

### <a name="tools-for-diagnosing-issues"></a>문제를 진단하기 위한 도구

Fiddler와 같은 네트워킹 도구가 문제를 진단하는 데 도움이 될 수 있습니다.

1. 네트워킹 도구를 로컬 호스트에서 실행되는 프록시 서버로 구성합니다. 실제 프록시 뒤에서 계속 작업해야 하는 경우 프록시를 통해 연결하도록 네트워킹 도구를 구성해야 할 수 있습니다.
2. 네트워킹 도구에서 사용하는 포트 번호를 확인합니다.
3. 로컬 호스트 및 네트워킹 도구의 포트 번호(예: "localhost:8888")를 사용하도록 Storage Explorer 프록시 설정을 구성합니다.

올바르게 설정되면 네트워킹 도구가 Storage Explorer에서 만든 네트워크 요청을 관리 및 서비스 엔드포인트에 기록합니다.

네트워킹 도구가 Storage Explorer 트래픽을 로깅하지 않는 경우 다른 애플리케이션을 사용하여 도구를 테스트해 보세요. 예를 들어 웹 브라우저에서 스토리지 리소스(예: `https://contoso.blob.core.windows.net/`)에 대한 엔드포인트 URL을 입력하면 다음과 유사한 응답을 받게 됩니다.

  ![코드 샘플](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  응답은 리소스에 액세스할 수 없더라도 리소스가 있음을 나타냅니다.

네트워킹 도구에 다른 애플리케이션의 트래픽이 표시되는 경우 Storage Explorer에서 프록시 설정을 조정해야 할 수 있습니다. 그러지 않으면 도구 설정을 조정해야 합니다.

### <a name="contact-proxy-server-admin"></a>프록시 서버 관리자 문의

프록시 설정이 올바른 경우 다음을 위해 프록시 서버 관리자에게 문의해야 할 수 있습니다.

- 프록시가 Azure 관리 또는 리소스 엔드포인트에 대한 트래픽을 차단하지 않는지 확인합니다.
- 프록시 서버에서 사용하는 인증 프로토콜을 확인합니다. Storage Explorer는 기본 인증 프로토콜만 지원합니다. Storage Explorer는 NTLM 프록시를 지원하지 않습니다.

## <a name="unable-to-retrieve-children-error-message"></a>"하위 항목을 검색할 수 없음" 오류 메시지

프록시를 통해 Azure에 연결된 경우 프록시 설정이 올바른지 확인합니다.

구독 또는 계정 소유자가 리소스에 대한 액세스를 부여한 경우 해당 리소스에 대한 읽기 또는 목록 권한이 있는지 확인합니다.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>연결 문자열에 전체 구성 설정이 없음

이 오류 메시지를 수신하는 경우 스토리지 계정의 키를 가져올 필요한 사용 권한이 없을 수 있습니다. 이를 확인하려면 포털로 이동하여 스토리지 계정을 찾습니다. 스토리지 계정의 노드를 마우스 오른쪽 단추로 클릭하고 **포털에서 열기** 를 선택합니다. 그런 다음 **액세스 키** 블레이드로 이동합니다. 키를 볼 수 있는 권한이 없는 경우 "액세스 권한이 없음"이라는 메시지가 표시됩니다. 이 문제를 해결하려면 다른 사용자의 계정 키를 확보하고 이름 및 키를 통해 연결하거나 스토리지 계정에 대한 SAS를 요청하고 이를 사용하여 해당 스토리지 계정에 연결할 수 있습니다.

계정 키가 표시되는 경우 GitHub에 문제를 제출하면 문제 해결에 도움을 드릴 수 있습니다.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>새 연결을 추가하는 동안 오류 발생: TypeError: 정의되지 않은 '버전' 속성을 읽을 수 없음

사용자 지정 연결을 추가하려고 할 때 이 오류 메시지가 표시되면 로컬 자격 증명 관리자에 저장된 연결 데이터가 손상되었을 수 있습니다. 이 문제를 해결하려면 손상된 로컬 연결을 삭제한 다음 다시 추가합니다.

1. Storage Explorer를 시작합니다. 메뉴에서 **도움말** > **개발자 도구 설정/해제** 로 이동합니다.
2. 열린 창의 **애플리케이션** 탭에서 **로컬 스토리지**(왼쪽) > **file://** 로 이동합니다.
3. 문제가 있는 연결 유형에 따라 키를 찾은 다음 해당 값을 텍스트 편집기에 복사합니다. 이 값은 다음과 같은 사용자 지정 연결 이름의 배열입니다.
    - Storage 계정
        - `StorageExplorer_CustomConnections_Accounts_v1`
    - Blob 컨테이너
        - `StorageExplorer_CustomConnections_Blobs_v1`
        - `StorageExplorer_CustomConnections_Blobs_v2`
    - 파일 공유
        - `StorageExplorer_CustomConnections_Files_v1`
    - 큐
        - `StorageExplorer_CustomConnections_Queues_v1`
    - 테이블
        - `StorageExplorer_CustomConnections_Tables_v1`
4. 현재 연결 이름을 저장한 후 개발자 도구의 값을 `[]`로 설정합니다.

손상되지 않은 연결을 유지하려면 다음 단계를 사용하여 손상된 연결을 찾을 수 있습니다. 기존 연결이 모두 끊어져도 괜찮다면 이 단계를 건너뛰고 플랫폼별 지침에 따라 연결 데이터를 지울 수 있습니다.

1. 텍스트 편집기에서 각 연결 이름을 개발자 도구에 다시 추가한 다음 연결이 계속 작동하는지 확인합니다.
2. 연결이 올바르게 작동하는 경우 손상되지 않은 것이므로 그대로 두어도 좋습니다. 연결이 작동하지 않는 경우 개발자 도구에서 해당 값을 제거하고 나중에 다시 추가할 수 있도록 기록합니다.
3. 모든 연결을 검사할 때까지 반복합니다.

모든 연결에 대해 수행한 후에는 다시 추가되지 않은 모든 연결 이름에 대해 손상된 데이터(있는 경우)를 지우고 Storage Explorer의 표준 단계를 사용하여 다시 추가해야 합니다.

### <a name="windows"></a>[Windows](#tab/Windows)

1. **시작** 메뉴에서 **자격 증명 관리자** 를 검색하여 엽니다.
2. **Windows 자격 증명** 으로 이동합니다.
3. **일반 자격 증명** 에서 `<connection_type_key>/<corrupted_connection_name>`키가 있는 항목(예: `StorageExplorer_CustomConnections_Accounts_v1/account1`)을 찾습니다.
4. 이러한 항목을 삭제하고 연결을 다시 추가합니다.

### <a name="macos"></a>[macOS](#tab/macOS)

1. Spotlight(Command+스페이스바)를 열고 **키 집합 액세스** 를 검색합니다.
2. `<connection_type_key>/<corrupted_connection_name>` 키가 있는 항목(예: `StorageExplorer_CustomConnections_Accounts_v1/account1`)을 찾습니다.
3. 이러한 항목을 삭제하고 연결을 다시 추가합니다.

### <a name="linux"></a>[Linux](#tab/Linux)

로컬 자격 증명 관리는 Linux 배포에 따라 다릅니다. Linux 배포에서 로컬 자격 증명 관리를 위한 기본 제공 GUI 도구를 제공하지 않는 경우 타사 도구를 설치하여 로컬 자격 증명을 관리할 수 있습니다. 예를 들어 Linux 로컬 자격 증명을 관리하기 위한 오픈 소스 GUI 도구인 [Seahorse](https://wiki.gnome.org/Apps/Seahorse/)를 사용할 수 있습니다.

1. 로컬 자격 증명 관리 도구를 열고 저장된 자격 증명을 찾습니다.
2. `<connection_type_key>/<corrupted_connection_name>` 키가 있는 항목(예: `StorageExplorer_CustomConnections_Accounts_v1/account1`)을 찾습니다.
3. 이러한 항목을 삭제하고 연결을 다시 추가합니다.
---

이 단계를 실행한 후에도 이 오류가 계속 발생하거나 연결이 손상된 것으로 의심되는 항목을 공유하려면 GitHub 페이지에서 [문제를 여세요](https://github.com/microsoft/AzureStorageExplorer/issues).

## <a name="issues-with-sas-url"></a>SAS URL 문제

SAS URL을 통해 서비스에 연결 중이고 오류가 발생하는 경우 다음을 수행합니다.

- URL이 리소스를 읽거나 나열하는 데 필요한 권한을 제공하는지 확인합니다.
- URL이 만료되지 않았는지 확인합니다.
- SAS URL이 액세스 정책을 기반으로 하는 경우 액세스 정책이 철회되지 않았는지 확인합니다.

실수로 잘못된 SAS URL을 사용하여 연결했는데 분리할 수 없는 경우 다음 단계를 수행합니다.

1. Storage Explorer를 실행하는 경우 F12 키를 눌러 개발자 도구 창을 엽니다.
2. **애플리케이션** 탭의 왼쪽 트리에서 **로컬 스토리지** > **file://** 을 선택합니다.
3. 문제가 있는 SAS URI의 서비스 유형과 연결된 키를 찾습니다. 예를 들어 잘못된 SAS URI가 Blob 컨테이너에 대한 것이면 `StorageExplorer_AddStorageServiceSAS_v1_blob`으로 명명된 키를 찾아봅니다.
4. 키의 값은 JSON 배열이어야 합니다. 잘못된 URI와 연결된 개체를 찾아 삭제합니다.
5. Storage Explorer를 다시 로드하려면 Ctrl+R 키를 누릅니다.

## <a name="linux-dependencies"></a>Linux 종속성

### <a name="snap"></a>Snap

Storage Explorer 1.10.0 이상은 Snap Store에서 스냅으로 사용할 수 있습니다. Storage Explorer 스냅은 모든 종속성을 자동으로 설치하며 새 버전의 스냅을 사용할 수 있게 되면 자동으로 업데이트됩니다. Storage Explorer 스냅을 설치하는 것이 좋습니다.

Storage Explorer를 사용하려면 암호 관리자를 사용해야 합니다. Storage Explorer가 올바르게 작동하려면 암호 관리자를 수동으로 연결해야 할 수 있습니다. 다음 명령을 실행하여 Storage Explorer를 시스템의 암호 관리자에 연결할 수 있습니다.

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>.tar.gz 파일

.tar.gz 파일로 애플리케이션을 다운로드할 수도 있지만 종속성을 수동으로 설치해야 합니다.

.tar.gz 다운로드에서 제공되는 Storage Explorer는 다음 버전의 Ubuntu에서만 지원됩니다. Storage Explorer는 다른 Linux 배포에서 작동할 수 있지만 공식적으로 지원되지는 않습니다.

- Ubuntu 20.04 x64
- Ubuntu 18.04 x64
- Ubuntu 16.04 x64

Storage Explorer 하려면 .net Core 3.1이 시스템에 설치 되어 있어야 합니다.

> [!NOTE]
> 1.8.0 1.20.1를 통해 Storage Explorer 버전은 .net Core 2.1이 필요 합니다. Storage Explorer 버전 1.7.0 이하에는 .NET Core 2.0이 필요합니다.

### <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Storage Explorer .tar.gz 파일을 다운로드합니다.
2. [.NET Core Runtime](/dotnet/core/install/linux) 설치

   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-3.1
   ```

### <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Storage Explorer .tar.gz 파일을 다운로드합니다.
2. [.NET Core Runtime](/dotnet/core/install/linux) 설치

   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-3.1
   ```

### <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Storage Explorer .tar.gz 파일을 다운로드합니다.
2. [.NET Core Runtime](/dotnet/core/install/linux) 설치

   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-3.1
   ```

---

Storage Explorer에 필요한 많은 라이브러리에는 Canonical의 표준 Ubuntu가 미리 설치되어 있습니다. 사용자 지정 환경에는 이러한 라이브러리 중 일부가 누락되었을 수 있습니다. Storage Explorer를 시작하는 데 문제가 있는 경우 시스템에 다음 패키지가 설치되어 있는지 확인하는 것이 좋습니다.

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>최신 버전의 .NET Core용 Storage Explorer 패치

Storage Explorer 1.7.0 이하의 경우 Storage Explorer에서 사용하는 .NET Core 버전을 패치해야 할 수 있습니다.

1. [NuGet에서](https://www.nuget.org/packages/StreamJsonRpc/1.5.43) StreamJsonRpc 버전 1.5.43을 다운로드합니다. 페이지 오른쪽에서 "패키지 다운로드" 링크를 찾습니다.
2. 패키지를 다운로드한 후 파일 확장명을 `.nupkg`에서 `.zip`으로 변경합니다.
3. 패키지의 압축을 풉니다.
4. `streamjsonrpc.1.5.43/lib/netstandard1.1/` 폴더를 엽니다.
5. `StreamJsonRpc.dll`을 Storage Explorer 폴더의 다음 위치로 복사합니다.
   - `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   - `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>Azure Portal의 "탐색기에서 열기"가 작동하지 않음

Azure Portal의 **탐색기에서 열기** 단추가 작동하지 않으면 호환 가능한 브라우저를 사용하고 있는지 확인합니다. 다음 브라우저는 호환성 테스트를 거쳤습니다.
- Microsoft Edge
- Mozilla Firefox
- Google Chrome
- Microsoft Internet Explorer

## <a name="gathering-logs"></a>로그 수집

GitHub에 문제를 보고할 때 문제 진단에 도움이 되는 특정 로그를 수집하라는 요청을 받을 수 있습니다.

### <a name="storage-explorer-logs"></a>Storage Explorer 로그

Storage Explorer는 버전 1.16.0부터 자체 애플리케이션 로그에 다양한 정보를 기록합니다. 도움말 > 로그 디렉터리 열기를 클릭하면 로그에 쉽게 액세스할 수 있습니다. 기본적으로 Storage Explorer는 낮은 수준의 세부 정보를 기록합니다. 세부 정보 표시 수준을 변경하려면 이름이 `STG_EX_LOG_LEVEL`인 환경 변수와 다음 값을 추가합니다.
- `silent`
- `critical`
- `error`
- `warning`
- `info` (기본 수준)
- `verbose`
- `debug`

로그는 실행하는 Storage Explorer의 각 세션에 대해 폴더별로 분할됩니다. 공유해야 하는 로그 파일 종류에 관계없이 다른 폴더에 있는 다른 세션의 파일과 함께 zip 아카이브에 저장하는 것이 좋습니다.

### <a name="authentication-logs"></a>인증 로그

로그인 또는 Storage Explorer의 인증 라이브러리와 관련된 문제의 경우 인증 로그를 수집해야 하는 경우가 많습니다. 인증 로그는 다음 위치에 저장됩니다.
- Windows: `C:\Users\<your username>\AppData\Local\Temp\servicehub\logs`
- macOS 및 Linux `~/.ServiceHub/logs`

일반적으로 다음 단계에 따라 로그를 수집할 수 있습니다.

1. **설정(왼쪽의 기어 아이콘)**  >  **애플리케이션**  >  **로그인** 으로 이동하여 **자세한 인증 로깅** 을 선택합니다. 인증 라이브러리 문제로 인해 Storage Explorer가 실행되지 않는 경우 이 작업이 수행됩니다.
2. Storage Explorer를 닫습니다.
1. 선택 사항/권장 사항: `logs` 폴더에서 기존 로그를 지웁니다. 이렇게 하면 당사에 보내야 하는 정보의 양이 줄어듭니다.
4. Storage Explorer를 열고 문제를 재현합니다.
5. Storage Explorer 닫기
6. `logs` 폴더의 콘텐츠를 압축합니다.

### <a name="azcopy-logs"></a>AzCopy 로그

데이터 전송에 문제가 있는 경우 AzCopy 로그를 가져와야 할 수 있습니다. AzCopy 로그는 다음 두 가지 방법을 통해 쉽게 찾을 수 있습니다.
- 활동 로그에 여전히 실패한 전송이 있는 경우 "AzCopy 로그 파일로 이동"을 클릭합니다.
- 이전에 실패한 전송의 경우 AzCopy 로그 폴더로 이동합니다. 이 폴더는 다음 위치에서 찾을 수 있습니다.
  - Windows: `C:\Users\<your username>\.azcopy`
  - macOS and Linux `~/.azcopy

### <a name="network-logs"></a>네트워크 로그

일부 문제의 경우 Storage Explorer에서 수행한 네트워크 호출에 대한 로그를 제공해야 합니다. Windows에서는 Fiddler를 사용하여 이 작업을 수행할 수 있습니다.

> [!NOTE]
> Fiddler 추적에는 추적 수집 중에 브라우저에서 입력하거나 보낸 암호가 포함될 수 있습니다. Fiddler 추적을 삭제하는 방법에 대한 지침을 참조해야 합니다. Fiddler 추적을 GitHub에 업로드하지 마세요. Fiddler 추적을 안전하게 보낼 수 있는 위치가 표시됩니다.

1부: Fiddler 설치 및 구성

1. Fiddler 설치
2. Fiddler 시작
3. 도구 > 옵션으로 이동합니다.
4. HTTPS 탭을 클릭합니다.
5. Capture CONNECT 및 Decrypt HTTPS 트래픽이 선택되어 있는지 확인합니다.
6. 작업 단추를 클릭합니다.
7. 다음 대화 상자에서 "루트 인증서 신뢰"를 선택한 다음 "예"를 선택합니다.
8. 작업 단추를 다시 클릭합니다.
9. "루트 인증서를 바탕 화면으로 내보내기"를 선택 합니다.
10. 바탕 화면으로 이동합니다.
11. FiddlerRoot.cer 파일을 찾습니다.
12. 두 번 클릭하여 열기
13. "세부 정보" 탭으로 이동합니다.
14. "파일에 복사..." 클릭
15. 내보내기 마법사에서 다음 옵션을 선택합니다.
    - Base-64로 인코딩된 X.509
    - 파일 이름에 대해서는 `C:\Users\<your user dir>\AppData\Roaming\StorageExplorer\certs`로 이동한 다음, 파일 이름으로 저장할 수 있습니다.
16. 인증서 창 닫기
17. Storage Explorer 시작
18. 편집 > 프록시 구성으로 이동합니다.
19. 대화 상자에서 "앱 프록시 설정 사용"을 선택하고 URL을 http://localhost 로, 포트를 8888로 설정합니다.
20. 확인을 클릭합니다.
21. Storage Explorer 다시 시작
22. Fiddler에서 `storageexplorer:` 프로세스의 네트워크 호출이 표시되는 것을 확인해야 합니다.

2부: 문제 재현
1. Fiddler 이외의 모든 앱을 닫습니다.
2. Fiddler 로그를 지웁니다(왼쪽 상단의 보기 메뉴 옆에 있는 X 아이콘).
3. 선택/권장: 몇 분 동안 Storage Explorer 설정 된 네트워크 호출이 표시 되는 경우이를 마우스 오른쪽 단추로 클릭 하 고 ' 지금 필터링 ' > ' 숨기기 (프로세스 이름) '를 선택 합니다.
4. Storage Explorer 시작
5. 문제 재현
6. 파일 > 저장 > 모든 세션...을 클릭하고 원하는 위치에 저장합니다.
7. Fiddler 및 Storage Explorer를 닫습니다.

3부: Fiddler 추적 삭제
1. Fiddler 추적(.saz 파일)을 두 번 클릭합니다.
2. `ctrl`+`f` 누르기
3. 나타나는 대화 상자에서 검색 = 요청 및 응답, 검사 = 헤더 및 본문 옵션이 설정되어 있는지 확인합니다.
4. Fiddler 추적을 수집하는 동안 사용한 암호, 강조 표시된 항목을 검색하고 마우스 오른쪽 단추를 클릭한 다음 제거 > 선택한 세션을 선택합니다.
5. 추적을 수집하는 동안 브라우저에 암호를 정확하게 입력했지만 Ctrl+F를 사용하여 항목을 찾을 수 없고 암호를 변경하고 싶지 않거나 사용한 암호가 다른 계정에 사용된 경우에는 .saz 파일 전송을 건너뛰면 됩니다. 후회하는 것보다 안전한 것이 낫습니다. :)
6. 새 이름으로 추적을 다시 저장합니다.
7. 선택 사항: 원본 추적 삭제

## <a name="next-steps"></a>다음 단계

이러한 솔루션 중 어느 것도 효과가 없는 경우 다음을 수행할 수 있습니다.
- 지원 티켓 만들기
- [GitHub에서 문제 열기](https://github.com/Microsoft/AzureStorageExplorer/issues) 왼쪽 아래에 있는 **GitHub에 문제 보고** 단추를 선택하여 이 작업을 수행할 수도 있습니다.

![사용자 의견](./media/storage-explorer-troubleshooting/feedback-button.PNG)
