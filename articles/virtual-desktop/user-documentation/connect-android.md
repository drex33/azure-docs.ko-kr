---
title: Android에서 Azure Virtual Desktop에 연결 - Azure
description: Android 클라이언트를 사용하여 Azure Virtual Desktop에 연결하는 방법.
author: Heidilohr
ms.topic: how-to
ms.date: 03/25/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: c411654983054d5d6993d4cc7d6a8cf66cb045ff
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132806366"
---
# <a name="connect-to-azure-virtual-desktop-with-the-android-client"></a>Android 클라이언트를 사용하여 Azure Virtual Desktop에 연결

> 적용 대상: Android 4.1 이상, ChromeOS 53 이상이 설치된 Chromebooks

>[!IMPORTANT]
>이 내용은 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](../virtual-desktop-fall-2019/connect-android-2019.md)를 참조하세요.

다운로드 가능한 클라이언트를 사용하여 Android 디바이스에서 Azure Virtual Desktop 리소스에 액세스할 수 있습니다. Google Play Store를 지원하는 Chromebook 디바이스에서 Android 클라이언트를 사용할 수도 있습니다. 이 가이드에서는 Android 클라이언트를 설정하는 방법을 알려줍니다.

## <a name="install-the-android-client"></a>Android 클라이언트 설치

시작하려면 Android 디바이스에 클라이언트를 [다운로드](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)하여 설치합니다.

## <a name="subscribe-to-a-feed"></a>피드 구독

관리자가 제공한 피드를 구독하여 Android 디바이스에서 액세스할 수 있는 관리되는 리소스 목록을 가져옵니다.

피드를 구독하려면 다음을 수행합니다.

1. 연결 센터에서 를 **+** 탭한 다음 **작업 영역 을 탭합니다.**
2. **피드 URL** 필드에 피드 URL을 입력합니다. 피드 URL은 URL 또는 이메일 주소일 수 있습니다.
   - URL을 사용하는 경우 관리자가 제공한 URL(일반적으로 <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>)을 사용합니다.
   - 이메일을 사용하려면 이메일 주소를 입력하세요. 관리자가 서버를 그런 방식으로 구성한 경우 클라이언트는 이메일 주소와 연결된 URL을 검색합니다.
   - US Gov 포털을 통해 연결하려면 <https://rdweb.wvd.azure.us/api/arm/feeddiscovery>를 사용합니다.
3. **다음** 을 탭합니다.
4. 자격 증명을 입력하라는 메시지가 표시되면 입력합니다.
   - **사용자 이름** 에 리소스에 액세스할 권한이 있는 사용자 이름을 지정합니다.
   - **암호** 의 경우 사용자 이름과 연결된 암호를 지정합니다.
   - 관리자가 인증을 구성하는 경우 추가 요소를 제공하라는 메시지가 표시될 수도 있습니다.

구독 후 연결 센터에 원격 리소스가 표시되어야 합니다.

피드를 구독하면 피드의 콘텐츠가 정기적으로 자동 업데이트됩니다. 관리자가 변경한 내용에 따라 리소스를 추가, 변경 또는 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Android 클라이언트를 사용하는 방법에 대한 자세한 내용은 [Android 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/)을 참조하세요.
