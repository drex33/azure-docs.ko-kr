---
title: Azure Virtual Desktop 화면 캡처 보호
titleSuffix: Azure
description: Azure Virtual Desktop에 대한 화면 캡처 보호를 설정하는 방법입니다.
author: gundarev
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: denisgun
ms.service: virtual-desktop
ms.openlocfilehash: ddf70a56d3a787387d1364c88f26cd0faa5df6b3
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219895"
---
# <a name="screen-capture-protection"></a>화면 캡처 보호

화면 캡처 보호 기능은 클라이언트 엔드포인트에서 중요한 정보가 캡처되지 않도록 방지합니다. 이 기능을 사용하도록 설정하면 원격 콘텐츠가 자동으로 차단되거나 스크린샷 및 화면 공유에서 숨겨집니다. 또한 원격 데스크톱 클라이언트는 화면을 캡처할 수 있는 악성 소프트웨어로부터 콘텐츠를 숨깁니다.

## <a name="prerequisites"></a>필수 조건

화면 캡처 보호 기능은 세션 호스트 수준에서 구성되고 클라이언트에 적용됩니다. 이 기능을 지원하는 클라이언트만 원격 세션에 연결할 수 있습니다. 현재 Windows 데스크톱 클라이언트만 화면 캡처 보호를 지원합니다. 전체 데스크톱만 지원됩니다. 사용자가 지원되지 않는 클라이언트를 사용하여 보호된 세션 호스트에 연결하거나 보호된 세션 호스트에 게시된 RemoteApp에 액세스하려고 한다고 가정합니다. 이 경우 0x1151 오류와 함께 연결이 실패합니다. 

## <a name="configure-screen-capture-protection"></a>화면 캡처 보호 구성

1. 화면 캡처 보호를 구성하려면 Azure Virtual Desktop에 대한 규칙 및 설정을 추가하는 관리 템플릿을 설치해야 합니다. 
2. [Azure Virtual Desktop 정책 템플릿 파일](https://aka.ms/avdgpo)(AVDGPTemplate.cab)을 다운로드하고 cab 파일 및 zip 보관 파일의 내용을 추출합니다.
3. *terminalserver-avd.admx* 파일을 *%windir%\PolicyDefinitions* 폴더에 복사합니다.
4. *en-us\terminalserver-avd.adml* 파일을 *%windir%\PolicyDefinitions\en-us* 폴더에 복사합니다.
5. 파일이 올바르게 복사되었는지 확인하려면 그룹 정책 편집기를 열고 **컴퓨터 구성** -> **관리 템플릿** -> **Windows 구성 요소** -> **원격 데스크톱 서비스** -> **원격 데스크톱 세션 호스트** -> **Azure Virtual Desktop** 으로 이동합니다.
6. 아래와 같이 하나 이상의 Azure Virtual Desktop 정책이 표시되어야 합니다.

   :::image type="content" source="media/azure-virtual-desktop-gpo.png" alt-text="그룹 정책 편집기의 스크린샷" lightbox="media/azure-virtual-desktop-gpo.png":::

   > [!TIP]
   > Active Directory 도메인의 그룹 정책 중앙 저장소에 관리 템플릿을 설치할 수도 있습니다.
   > 그룹 정책 관리 템플릿의 중앙 저장소에 대한 자세한 내용은 [Windows에서 그룹 정책 관리 템플릿의 중앙 저장소를 만들고 관리하는 방법](/troubleshoot/windows-client/group-policy/create-and-manage-central-store)을 참조하세요.

7. **"화면 캡처 보호 사용"** 정책을 열고 **"사용"** 으로 설정합니다.

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

- 이 기능은 특정 공용 운영 체제 기능 및 API 집합을 통해 원격 데스크톱 창이 캡처되지 않도록 보호합니다. 그러나 예를 들어 누군가가 화면 사진을 찍는 경우를 방지할 수 없듯이 이 기능이 콘텐츠를 엄격하게 보호한다는 보장은 없습니다.
- 고객은 클립보드, 드라이브 및 프린터 리디렉션을 사용하지 않도록 설정하여 이 기능을 함께 사용해야 합니다. 리디렉션을 사용하지 않도록 설정하면 사용자가 원격 세션에서 캡처된 화면 콘텐츠를 복사하지 못하도록 방지할 수 있습니다.
- 이 기능이 사용하도록 설정된 경우 사용자는 Microsoft Teams와 같은 로컬 협업 소프트웨어를 사용하여 원격 데스크톱 창을 공유할 수 없습니다. Microsoft Teams를 사용하는 경우 로컬 Teams 앱과 미디어 최적화로 실행되는 Teams는 보호된 콘텐츠를 공유할 수 없습니다.
- 사용자가 웹 클라이언트 1.0.24.14를 사용하여 보호된 리소스에 연결하려고 하면 오류 메시지 대신 인증 프롬프트가 표시됩니다.

## <a name="next-steps"></a>다음 단계

* Azure Virtual Desktop 보안 모범 사례에 대해 알아보려면 [Azure Virtual Desktop 보안 모범 사례](security-guide.md)를 참조하세요.
