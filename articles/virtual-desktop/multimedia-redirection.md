---
title: Azure Virtual Desktop의 멀티미디어 리디렉션 - Azure
description: Azure Virtual Desktop에 멀티미디어 리디렉션을 사용하는 방법(미리 보기)
author: Heidilohr
ms.topic: how-to
ms.date: 08/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 574a10f6ef79ff3d40f5d62e49db9ebf198d2a79
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567872"
---
# <a name="multimedia-redirection-for-azure-virtual-desktop-preview"></a>Azure Virtual Desktop에 대한 멀티미디어 리디렉션

> [!IMPORTANT]
> Azure Virtual Desktop에 대한 멀티미디어 리디렉션은 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

>[!NOTE]
>Azure Virtual Desktop은 현재 Microsoft 365 Government(GCC), GCC-High 환경 및 Microsoft 365 DoD용 Azure Virtual Desktop에서 멀티미디어 리디렉션을 지원하지 않습니다.
>
>Azure Virtual Desktop의 멀티미디어 리디렉션은 Windows 10 머신의 Windows 데스크톱 클라이언트에서만 사용할 수 있습니다. 멀티미디어 리디렉션에는 Windows 데스크톱 클라이언트 버전 1.2.2222 이상이 필요합니다.

MMR(멀티미디어 리디렉션)을 사용하면 Azure Virtual Desktop 브라우저에서 비디오를 시청하면서 비디오를 원활하게 재생할 수 있습니다. 멀티미디어 리디렉션은 더 빠른 처리 및 렌더링을 위해 브라우저에서 로컬 머신으로 미디어 요소를 원격 처리합니다. Microsoft Edge 및 Google Chrome은 모두 멀티미디어 리디렉션 기능을 지원합니다. 그러나 Azure Virtual Desktop에 대한 멀티미디어 리디렉션의 퍼블릭 미리 보기 버전은 YouTube 재생이 제한되어 있습니다. 조직의 배포 내에서 YouTube를 테스트하려면 [확장을 사용하도록 설정](#managing-group-policies-for-the-multimedia-redirection-browser-extension)해야 합니다.

## <a name="requirements"></a>요구 사항

Azure Virtual Desktop에서 멀티미디어 리디렉션을 사용하려면 먼저 다음을 수행해야 합니다.

1. [Windows PC의 Teams에 대한 하드웨어 요구 사항](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)에 부합하는 Windows 10 또는 Windows 10 IoT Enterprise 디바이스에 [Windows 데스크톱 클라이언트](./user-documentation/connect-windows-7-10.md#install-the-windows-desktop-client)를 설치합니다. 버전 1.2.2222 이상 클라이언트를 설치하면 클라이언트 디바이스에 멀티미디어 리디렉션 플러그 인(MsMmrDVCPlugin.dll)도 설치됩니다. 업데이트 및 새 버전에 대한 자세한 내용은 [Windows Desktop 클라이언트의 새로운 기능](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)을 참조하세요.

2. [참가자 그룹에 대한 클라이언트 머신을 구성합니다](create-host-pools-azure-marketplace.md).

3. [멀티미디어 리디렉터 서비스](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWIzIk) 및 필요한 브라우저 확장을 VM(가상 머신)에 설치합니다.

4. 사용자가 참가자 프로그램에 액세스할 수 있도록 클라이언트 머신을 구성합니다. 참가자 그룹용 클라이언트를 구성하려면 레지스트리 정보를 다음과 같이 설정합니다.

   - **키**: HKLM\\Software\\Microsoft\\MSRDC\\Policies
   - **형식**: REG_SZ
   - **이름:** ReleaseRing
   - **데이터**: insider

   참가자 프로그램에 대한 자세한 내용은 [관리자용 Windows 데스크톱 클라이언트](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin#configure-user-groups)를 참조하세요.

5. [MSI 설치 관리자(MsMmrHostMri)](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWIzIk)를 사용하여 Azure VM에 인터넷 브라우저용 멀티미디어 리디렉션 확장을 설치합니다. Azure Virtual Desktop용 멀티미디어 리디렉션은 현재 Microsoft Edge 및 Google Chrome만 지원합니다.

## <a name="managing-group-policies-for-the-multimedia-redirection-browser-extension"></a>멀티미디어 리디렉션 브라우저 확장에 대한 그룹 정책 관리

멀티미디어 리디렉션 MSI를 사용하면 브라우저 확장이 설치됩니다. 그러나 이 서비스는 아직 퍼블릭 미리 보기로 제공되므로 사용자 환경은 달라질 수 있습니다. 알려진 문제에 대한 자세한 내용은 [알려진 문제](#known-issues-and-limitations)를 참조하세요.

경우에 따라 그룹 정책을 변경하여 브라우저 확장을 관리하고 사용자 환경을 개선할 수 있습니다. 예를 들면 다음과 같습니다.

- 사용자 상호 작용 없이 확장을 설치할 수 있습니다.
- 멀티미디어 리디렉션을 사용하는 웹 사이트를 제한할 수 있습니다.
- 기본적으로 Google Chrome에서 확장 아이콘을 고정할 수 있습니다. 확장 아이콘은 Microsoft Edge에서 기본적으로 고정되어 있으므로 Chrome에서는 이 설정을 변경하기만 하면 됩니다.

### <a name="configure-microsoft-edge-group-policies-for-multimedia-redirection"></a>멀티미디어 리디렉션을 위해 Microsoft Edge 그룹 정책 구성

그룹 정책을 구성하려면 Microsoft Ege 관리 템플릿을 편집해야 합니다. **관리 템플릿 Microsoft Edge 확장** > **확장 관리 설정 구성** 에서 확장 구성 옵션을 확인합니다.

다음 코드는 브라우저에서 멀티미디어 리디렉션 확장을 설치하고 YouTube에서 멀티미디어 리디렉션 로드만 허용하는 Microsoft Edge 그룹 정책의 예입니다.

```cmd
{ "joeclbldhdmoijbaagobkhlpfjglcihd": { "installation_mode": "force_installed", "runtime_allowed_hosts": [ "*://*.youtube.com" ], "runtime_blocked_hosts": [ "*://*" ], "update_url": "https://edge.microsoft.com/extensionwebstorebase/v1/crx" } }
```

그룹 정책 구성에 대한 자세한 내용은 그룹 [Microsoft Edge 그룹 정책](/DeployEdge/configure-microsoft-edge)을 참조하세요.

### <a name="configure-google-chrome-group-policies-for-multimedia-redirection"></a>멀티미디어 리디렉션을 위해 Google Chrome 그룹 정책 구성

Google Chrome 그룹 정책을 구성하려면 Google Chrome 관리 템플릿을 편집해야 합니다. **관리 템플릿** >  > **Google** > **Google Chrome 확장** > **확장 관리 설정** 에서 확장 구성 옵션을 확인합니다.

다음 예제는 [멀티미디어 리디렉션을 위해 Microsoft Edge 그룹 정책 구성](#configure-microsoft-edge-group-policies-for-multimedia-redirection)의 코드 예제와 매우 유사합니다. 이 정책은 오른쪽 위 메뉴에 고정된 아이콘을 사용하여 멀티미디어 리디렉션 확장을 강제로 설치하고 YouTube에서 멀티미디어 리디렉션만 로드하도록 허용합니다.

```cmd
{ "lfmemoeeciijgkjkgbgikoonlkabmlno": { "installation_mode": "force_installed", "runtime_allowed_hosts": [ "*://*.youtube.com" ], "runtime_blocked_hosts": [ "*://*" ], "toolbar_pin": "force_pinned", "update_url": "https://clients2.google.com/service/update2/crx" } }
```

[Google Chrome 그룹 정책](https://support.google.com/chrome/a/answer/187202#zippy=%2Cwindows) 구성에 대한 추가 정보입니다.

## <a name="run-the-multimedia-redirection-extension-manually-on-a-browser"></a>브라우저에서 멀티미디어 리디렉션 확장을 수동으로 실행

MMR은 Microsoft Edge 및 Google Chrome 브라우저용 원격 앱 및 세션 데스크톱을 사용합니다. [요구 사항](#requirements)을 충족했으면 지원되는 브라우저를 엽니다. 그룹 정책을 사용하여 브라우저 또는 확장을 설치하지 않은 경우 확장을 수동으로 실행해야 합니다. 이 섹션에서는 현재 지원되는 브라우저 중 하나에서 확장을 수동으로 실행하는 방법을 설명합니다.

### <a name="microsoft-edge"></a>Microsoft Edge

Microsoft Edge에서 확장을 수동으로 실행하려면 오버플로 메뉴에서 노란색 느낌표를 찾습니다. Azure Virtual Desktop 멀티미디어 리디렉션 확장을 사용하도록 설정하라는 메시지가 표시됩니다. **확장 사용** 을 선택합니다.

### <a name="google-chrome"></a>Google Chrome

Google Chrome에서 확장을 수동으로 실행하려면 다음 스크린샷과 같이 새 확장이 설치되었다는 알림 메시지를 찾습니다. 

![Google Chrome 작업 표시줄의 스크린샷 "새 확장이 추가되었습니다."라는 알림 탭이 있습니다.](media/chrome-notification.png)

사용자가 확장을 설정할 수 있도록 하는 알림을 선택합니다. 또한 사용자는 멀티미디어 리디렉션이 연결된 경우 아이콘에서 볼 수 있도록 확장을 고정해야 합니다.

### <a name="the-multimedia-redirection-status-icon"></a>멀티미디어 리디렉션 상태 아이콘

브라우저에서 멀티미디어 리디렉션이 활성 상태인지 여부를 신속하게 알리기 위해 다음 아이콘 상태를 추가했습니다.

| 아이콘 상태  | 정의  |
|-----------------|-----------------|
| [상태가 적용되지 않은 기본 Azure Virtual Desktop 프로그램 아이콘](/media/icon-default.png) | 상태가 적용되지 않은 기본 아이콘 모양 |
| [멀티미디어 리디렉션이 작동하지 않음을 나타내는 x를 포함하는 빨간색 사각형이 있는 Azure Virtual Desktop 프로그램 아이콘](/media/icon-disconnect.png) | 내부에 "X"가 있는 빨간색 사각형은 클라이언트가 멀티미디어 리디렉션에 연결할 수 없음을 의미합니다. |
| [멀티미디어 리디렉션이 작동 중임을 나타내는 확인 표시가 있는 녹색 사각형을 포함하는 Azure Virtual Desktop 프로그램 아이콘](/media/icon-connect.png) | 내부에 확인 표시가 있는 녹색 사각형은 클라이언트가 멀티미디어 리디렉션에 성공적으로 연결되었음을 의미합니다. |

이 아이콘을 선택하면 모든 웹 사이트에서 멀티미디어 리디렉션을 사용하거나 사용하지 않도록 설정할 수 있는 확인란이 있는 팝업 메뉴가 표시됩니다. 또한 서비스의 각 구성 요소에 대한 버전 번호도 나열됩니다.

## <a name="send-feedback-during-public-preview"></a>퍼블릭 미리 보기 중에 피드백 보내기

문제가 발생하면 클라이언트와 VM 호스트의 피드백 허브에서 문제를 보고할 수 있습니다.

피드백 보내려면 다음을 수행합니다.

1. 클라이언트와 서버 모두에서 **피드백 허브** 를 엽니다.

2. **문제 보고** 를 선택합니다.

3. 두 문제 보고서에서 동일한 제목을 사용하지만 시작 부분에 "[Client]" 또는 "[Host]"를 포함하여 보고서를 제출하는 위치를 지정합니다.

    예를 들어 클라이언트에서 문제를 제출하는 경우 다음과 같이 형식을 지정합니다.

    >[클라이언트] 보고서 제목

    호스트에서 문제를 제출하는 경우 다음과 같이 형식을 지정합니다.

    >[호스트] 보고서 제목

4. **자세한 설명** 필드에서 발생한 문제를 설명합니다. 문제가 발생했을 때 시청하던 비디오의 URL도 포함하는 것이 좋습니다.

5. 완료되면 **다음** 을 선택합니다.

6. **문제** 풍선을 선택한 후, 다음 스크린샷과 같이 두 개의 드롭다운 메뉴에서 **앱** 및 **원격 데스크톱** 을 선택합니다.

    !["2. 범주 선택" 창 스크린샷 사용자가 문제 풍선을 선택한 후, 아래의 드롭다운 메뉴에서 앱 및 원격 데스크톱을 선택했습니다.](media/problem-category.png)

7. **다음** 을 선택합니다.

8. 제출하려는 것과 유사한 문제가 목록에 있는지 확인합니다.
   
   - 활성 버그에 연결되는 풍선이 표시되는 경우 버그 설명이 보고 중인 문제와 일치하는지 확인합니다. 일치하는 경우 다음 스크린샷에 표시된 것처럼 해당 풍선을 선택하고 **버그에 연결** 을 선택합니다.

    !["3. 유사한 피드백 찾기" 창 스크린샷 사용자가 "버그에 연결 번호 32350300 활성" 옵션에 대한 풍선을 선택했습니다.](media/link-to-bug.png)

    - 유사한 문제가 표시되지 않으면 **새 버그 만들기** 를 선택합니다.

    !["3. 유사한 피드백 찾기" 창 스크린샷 이번에는 "버그에 연결" 옵션이 없고 대신 사용자가 "새 버그 만들기"를 선택했습니다.](media/make-new-bug.png)

9. **다음** 을 선택합니다.

10. **세부 정보 추가** 창에서 **원격 데스크톱에 대한 데이터 포함(기본값)** 을 선택하고 가능한 한 많은 정보를 사용하여 모든 질문에 답변합니다.

    문제에 대한 비디오 녹화를 추가하려면 **원격 데스크톱에 대한 데이터 포함(기본값)** 을 선택하고 **녹음/녹화 시작** 단추를 선택합니다. 녹화하는 동안 원격 데스크톱을 열고 문제가 발생한 프로세스를 수행합니다. 완료되면 브라우저로 돌아가 동영상을 테스트하여 제대로 녹화되었는지 확인합니다.

    완료되면 첨부 파일 및 진단을 Microsoft로 보낸 다음, **제출** 을 선택합니다.

### <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

다음 문제는 이미 확인된 내용이므로 보고할 필요가 없습니다.

- 멀티미디어 리디렉션은 웹 클라이언트가 아닌 Windows 데스크톱 클라이언트에서만 작동합니다.

- 멀티미디어 리디렉션은 현재 보호된 콘텐츠를 지원하지 않기 때문에 Pluralsight 및 Netflix의 동영상이 작동하지 않습니다.

- 퍼블릭 미리 보기 동안에는 YouTube를 제외한 모든 사이트에서 멀티미디어 리디렉션이 사용하지 않도록 설정됩니다. 그러나 확장 프로그램을 사용하는 경우 모든 웹 사이트에 대해 멀티미디어 리디렉션을 사용하도록 설정할 수 있습니다. 조직이 회사 웹 사이트에서 이 기능을 테스트할 수 있도록 확장을 추가했습니다.

- MSI 설치 관리자가 내부 테스트 중에 이 확장을 설치하지 못할 가능성이 약간 있습니다. 이 문제가 발생하는 경우 Microsoft Edge 스토어 또는 Google Chrome 스토어에서 멀티미디어 리디렉션 확장을 설치해야 합니다.

    - [멀티미디어 리디렉션 브라우저 확장(Microsoft Edge)](https://microsoftedge.microsoft.com/addons/detail/wvd-multimedia-redirectio/joeclbldhdmoijbaagobkhlpfjglcihd)
    - [멀티미디어 브라우저 확장(Google Chrome)](https://chrome.google.com/webstore/detail/wvd-multimedia-redirectio/lfmemoeeciijgkjkgbgikoonlkabmlno)

- MSI 설치 관리자를 사용하여 호스트 컴퓨터에 이 확장을 설치하면 사용자가 브라우저를 처음 열거나 경고 또는 오류 메시지를 표시할 때 확장을 수락하라는 메시지가 표시됩니다. 사용자가 이 프롬프트를 거부하면 확장이 로드되지 않을 수 있습니다. 이 문제를 방지하려면 [그룹 정책을 편집](#managing-group-policies-for-the-multimedia-redirection-browser-extension)하여 확장을 설치합니다.

- 비디오 창의 크기를 조정하면 창의 크기가 비디오 자체보다 더 빠르게 조정됩니다. 창을 최소화하거나 최대화하는 경우에도 이 문제가 발생합니다.

## <a name="next-steps"></a>다음 단계

Azure Virtual Desktop의 다른 부분에 대한 비디오 스트리밍에 관심이 있는 경우 [Azure Virtual Desktop용 Teams](teams-on-avd.md)를 확인해 보세요.
