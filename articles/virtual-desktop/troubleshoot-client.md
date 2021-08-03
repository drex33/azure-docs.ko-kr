---
title: 원격 데스크톱 클라이언트 Azure Virtual Desktop 문제 해결 - Azure
description: Azure Virtual Desktop 테넌트 환경에서 클라이언트 연결을 설정할 때 발생하는 문제를 해결하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9534425344a09bb2e2e733cde5294d08d175eb96
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111755342"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>원격 데스크톱 클라이언트 문제 해결

이 문서에서는 원격 데스크톱 클라이언트와 관련된 일반적인 문제 및 해결 방법에 대해 설명합니다.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Windows 7 또는 Windows 10용 원격 데스크톱 클라이언트가 응답을 중지하거나 열 수 없음

1\.2.790 버전부터 정보 페이지 또는 명령을 사용하여 사용자 데이터를 다시 설정할 수 있습니다.

다음 명령을 사용하여 사용자 데이터를 제거하고, 기본 설정을 복원하고, 모든 작업 영역에서 구독을 취소합니다.

```cmd
msrdcw.exe /reset [/f]
```

이전 버전의 원격 데스크톱 클라이언트를 사용하는 경우 클라이언트를 제거한 후 다시 설치하는 것이 좋습니다.

## <a name="web-client-wont-open"></a>웹 클라이언트가 열리지 않음

먼저 브라우저에서 다른 웹 사이트를 열어 인터넷 연결을 테스트합니다. 예를 들어 [www.bing.com](https://www.bing.com)입니다.

**nslookup** 을 사용하여 DNS에서 FQDN을 확인할 수 있는지 다음과 같이 확인합니다.

```cmd
nslookup rdweb.wvd.microsoft.com
```

Windows 7 또는 Windows 10용 원격 데스크톱 클라이언트와 같은 다른 클라이언트를 사용하여 연결을 시도하고 웹 클라이언트를 열 수 있는지 확인 합니다.

### <a name="cant-open-other-websites-while-connected-to-the-web-client"></a>웹 클라이언트에 연결되어 있는 동안 다른 웹 사이트를 열 수 없습니다.

웹 클라이언트에 연결되어 있는 동안 다른 웹 사이트를 열 수 없는 경우 네트워크 연결 문제 또는 네트워크 중단이 있을 수 있습니다. 네트워크 지원에 문의하는 것이 좋습니다.

### <a name="nslookup-cant-resolve-the-name"></a>Nslookup에서 이름을 확인할 수 없습니다.

nslookup에서 이름을 확인할 수 없는 경우 네트워크 연결 문제 또는 네트워크 중단이 있을 수 있습니다. 네트워크 지원에 문의하는 것이 좋습니다.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>클라이언트는 연결할 수 없지만 네트워크의 다른 클라이언트는 연결할 수 있음

웹 클라이언트를 사용하는 동안 브라우저가 오작동을 일으키거나 작동을 중지하는 경우 다음 지침에 따라 문제를 해결합니다.

1. 브라우저를 다시 시작합니다.
2. 브라우저 쿠키를 지웁니다. [Internet Explorer에서 쿠키 파일을 삭제하는 방법](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)을 참조하십시오.
3. 브라우저 캐시를 지웁니다. [브라우저의 브라우저 캐시 지우기](https://binged.it/2RKyfdU)를 참조하세요.
4. 프라이빗 모드에서 브라우저를 엽니다.

## <a name="client-doesnt-show-my-resources"></a>클라이언트가 내 리소스를 표시하지 않습니다.

먼저 사용 중인 Azure Active Directory 계정을 확인합니다. Azure Virtual Desktop에 사용할 계정과 다른 Azure Active Directory 계정으로 이미 로그인한 경우, 로그아웃하거나 개인 브라우저 창을 사용해야 합니다.

Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/connect-web-2019.md)의 웹 클라이언트 링크를 사용하여 리소스에 연결합니다.

이 작업이 수행되지 않으면 앱 그룹이 작업 영역과 연결되어 있는지 확인하세요.

## <a name="web-client-stops-responding-or-disconnects"></a>웹 클라이언트의 응답이 중지되거나 연결이 끊어짐

다른 브라우저 또는 클라이언트를 사용하여 연결해 보세요.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>다른 브라우저 및 클라이언트도 오작동하거나 열리지 않음

브라우저를 전환한 후에도 문제가 계속되면 브라우저와 관련된 문제가 아니라 네트워크에 문제가 있을 수 있습니다. 네트워크 지원에 문의하는 것이 좋습니다.

## <a name="web-client-keeps-prompting-for-credentials"></a>웹 클라이언트에서 자격 증명을 묻는 메시지가 표시됨

웹 클라이언트에서 자격 증명을 입력하라는 메시지가 표시되면 다음 지침을 따르세요.

1. 웹 클라이언트 URL이 올바른지 확인합니다.
2. 사용 중인 자격 증명이 URL에 연결된 Azure Virtual Desktop 환경의 자격 증명인지 확인합니다.
3. 브라우저 쿠키를 지웁니다. 자세한 내용은 [Internet Explorer에서 쿠키 파일을 삭제하는 방법](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)을 참조하세요.
4. 브라우저 캐시를 지웁니다. 자세한 내용은 [브라우저의 브라우저 캐시 지우기](https://binged.it/2RKyfdU)를 참조하세요.
5. 프라이빗 모드에서 브라우저를 엽니다.

## <a name="windows-client-blocks-azure-virtual-desktop-classic-feed"></a>Windows 클라이언트는 Azure Virtual Desktop(클래식) 피드를 차단합니다.

Windows 클라이언트 피드에 Azure Virtual Desktop(클래식) 앱이 표시되지 않는 경우 다음 지침을 따르세요.

1. 조건부 액세스 정책에 Azure Virtual Desktop(클래식)과 연결된 앱 ID가 포함되어 있는지 확인합니다.
2. 조건부 액세스 정책이 Azure Virtual Desktop(클래식) 앱 ID를 제외한 모든 액세스를 차단하는지 확인합니다. 그렇다면 클라이언트에서 피드를 검색할 수 있도록 앱 ID **9cdead84-a844-4324-93f2-b2e6bb768d07** 을 정책에 추가해야 합니다.

목록에서 앱 ID 9cdead84-a844-4324-93f2-b2e6bb768d07을 찾을 수 없는 경우 Azure Virtual Desktop 리소스 공급자를 등록해야 합니다. 리소스 공급자를 등록하려면 다음 단계를 따릅니다.

1. Azure Portal에 로그인합니다.
2. **구독** 으로 이동한 다음, 구독을 선택합니다.
3. 페이지 왼쪽에 있는 메뉴에서 **리소스 공급자** 를 선택합니다.
4. **Microsoft.DesktopVirtualization** 을 찾아 선택한 다음, **다시 등록** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- Azure Virtual Desktop 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)을 참조하세요.
- Azure Virtual Desktop 환경에서 Azure Virtual Desktop 환경 및 호스트 풀을 만드는 데 발생하는 문제를 해결하려면 [환경 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조하세요.
- Azure Virtual Desktop에서 VM(가상 머신)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조하세요.
- Azure Virtual Desktop 에이전트 또는 세션 연결과 관련된 문제를 해결하려면 [일반적인 Azure Virtual Desktop 에이전트 문제 해결](troubleshoot-agent.md)을 참조하세요.
- Azure Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Azure Virtual Desktop PowerShell](troubleshoot-powershell.md)을 참조하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
