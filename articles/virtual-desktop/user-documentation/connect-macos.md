---
title: macOS 클라이언트를 사용하여 Azure Virtual Desktop에 커넥트 - Azure
description: macOS 클라이언트를 사용하여 Azure Virtual Desktop에 연결하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: cdc4e41f20c4cfdb6aa0eeb2c071934e31262f4b
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133362964"
---
# <a name="connect-to-azure-virtual-desktop-with-the-macos-client"></a>macOS 클라이언트를 사용하여 Azure Virtual Desktop에 연결

> 적용 대상: macOS 10.12 이상

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체가 있는 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](../virtual-desktop-fall-2019/connect-macos-2019.md)를 참조하세요.

다운로드 가능한 클라이언트를 사용하여 macOS 디바이스에서 Azure Virtual Desktop 리소스에 액세스할 수 있습니다. 이 가이드에서는 클라이언트를 설정하는 방법을 알려줍니다.

## <a name="install-the-client"></a>클라이언트 설치

시작하려면 macOS 디바이스에 클라이언트를 [다운로드](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)하여 설치합니다.

## <a name="subscribe-to-a-feed"></a>피드 구독

관리자가 제공한 피드를 구독하여 macOS 디바이스에서 사용할 수 있는 관리형 리소스 목록을 가져옵니다.

피드를 구독하려면 다음을 수행합니다.

1. 기본 페이지에서 **작업 영역 추가** 를 선택하여 서비스에 연결하고 리소스를 검색합니다.
2. 피드 URL을 입력합니다. 이는 URL 또는 이메일 주소일 수 있습니다.
   - URL을 사용하는 경우 관리자가 제공한 URL을 사용합니다. 일반적으로 URL은 <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>입니다.
   - 이메일을 사용하려면 이메일 주소를 입력하세요. 그러면 클라이언트가 이메일 주소와 연결된 URL을 검색합니다(관리자가 서버를 그렇게 구성한 경우).
   - US Gov 포털을 통해 연결하려면 <https://rdweb.wvd.azure.us/api/arm/feeddiscovery>를 사용합니다.
3. **추가** 를 선택합니다.
4. 메시지가 표시되면 사용자 계정으로 로그인합니다.

로그인하면 사용 가능한 리소스 목록이 표시됩니다.

피드를 구독하면 피드의 콘텐츠가 정기적으로 자동 업데이트됩니다. 관리자가 변경한 내용에 따라 리소스를 추가, 변경 또는 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

macOS 클라이언트에 대해 자세히 알아보려면 [macOS 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) 설명서를 확인하세요.
