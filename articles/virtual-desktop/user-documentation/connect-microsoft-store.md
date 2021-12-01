---
title: Microsoft Store 클라이언트를 사용하여 Azure Virtual Desktop에 커넥트 - Azure
description: Microsoft Store 클라이언트를 사용하여 Azure Virtual Desktop에 연결하는 방법.
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 57e9bc647a440da39abb78d319f790e05f285c08
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133365821"
---
# <a name="connect-with-the-microsoft-store-client"></a>Microsoft Store 클라이언트와 연결

>적용 대상: Windows 10.

Windows 10이 설치된 디바이스에서 Azure Virtual Desktop 리소스에 액세스할 수 있습니다.

## <a name="install-the-microsoft-store-client"></a>Microsoft Store 클라이언트 설치

관리자 권한이 필요하지 않은 현재 사용자의 클라이언트를 설치할 수 있습니다. 또는 관리자가 디바이스의 모든 사용자가 액세스할 수 있도록 클라이언트를 설치 및 구성할 수 있습니다.

설치가 완료되면 원격 데스크톱을 검색하여 시작 메뉴에서 클라이언트를 시작할 수 있습니다.

시작하려면 [Microsoft Store에서 클라이언트를 다운로드하여 설치](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS)합니다.

## <a name="subscribe-to-a-workspace"></a>작업 영역 구독

관리자가 제공한 작업 영역을 구독하여 PC에서 액세스할 수 있는 관리되는 리소스 목록을 가져옵니다.

작업 영역을 구독하려면 다음을 수행합니다.

1. 연결 센터 화면에서 **+추가** 를 탭한 다음, **작업 영역** 을 탭합니다.
2. 관리자가 제공한 작업 영역 URL 필드에 작업 영역 URL을 입력합니다. 작업 영역 URL은 URL 또는 이메일 주소일 수 있습니다.
   
   - 작업 영역 URL을 사용하는 경우 관리자가 제공한 URL을 사용합니다.
   - Azure Virtual Desktop에서 연결하는 경우 사용 중인 서비스 버전에 따라 다음 URL 중 하나를 사용합니다.
       - Windows Virtual Desktop(클래식): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`.
       - Azure Virtual Desktop: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`.
  
3. **구독** 을 탭합니다.
4. 자격 증명을 입력하라는 메시지가 표시되면 입력합니다.
5. 구독 후에는 작업 영역이 연결 센터에 표시되어야 합니다.

관리자가 변경한 내용에 따라 작업 영역을 추가, 변경 또는 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Microsoft Store 클라이언트를 사용하는 방법에 대한 자세한 내용은 [Microsoft Store 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/windows/)을 참조하세요.