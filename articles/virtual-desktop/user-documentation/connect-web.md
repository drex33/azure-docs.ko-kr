---
title: 웹 클라이언트를 사용하여 Azure Virtual Desktop에 연결 - Azure
description: 웹 클라이언트를 사용하여 Azure Virtual Desktop에 연결하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 3804714a1a21a482166fbf7d592f5ee094ce6a06
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359715"
---
# <a name="connect-to-azure-virtual-desktop-with-the-web-client"></a>웹 클라이언트를 사용하여 Azure Virtual Desktop에 연결

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](../virtual-desktop-fall-2019/connect-web-2019.md)를 참조하세요.

웹 클라이언트를 사용하면 시간이 오래 걸리는 설치 프로세스 없이 웹 브라우저에서 Azure Virtual Desktop 리소스에 액세스할 수 있습니다.

>[!NOTE]
>웹 클라이언트에서는 현재 모바일 OS를 지원하지 않습니다.

## <a name="supported-operating-systems-and-browsers"></a>지원되는 운영 체제 및 브라우저

>[!IMPORTANT]
>2021 년 9 월 30 일까 지 Azure 가상 데스크톱 웹 클라이언트는 더 이상 Internet Explorer를 지원 하지 않습니다. 대신 Microsoft Edge를 사용 하 여 웹 클라이언트에 연결 하는 것이 좋습니다. 자세한 내용은 [블로그 게시물](https://aka.ms/WVDSupportIE11)을 참조하세요.

HTML5 지원 브라우저가 제대로 작동하는 동안에는 다음 운영 체제 및 브라우저를 공식적으로 지원합니다.

| 브라우저           | 지원되는 OS                     | 메모               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | 버전 55 이상 |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>원격 리소스 피드에 액세스

브라우저에서 <https://rdweb.wvd.microsoft.com/arm/webclient>의 Azure Resource Manager 통합 버전 Azure Virtual Desktop 웹 클라이언트로 이동한 후 사용자 계정으로 로그인합니다.

>[!NOTE]
>Azure Resource Manager 통합 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 대신 <https://rdweb.wvd.microsoft.com/webclient>에서 리소스에 연결합니다.
>
> US Gov 포털을 사용하는 경우에는 <https://rdweb.wvd.azure.us/arm/webclient/index.html>을 사용합니다.
> 
> Azure 중국 포털에 연결하려면 <https://rdweb.wvd.azure.cn/arm/webclient/index.html>을 사용합니다.

>[!NOTE]
>Azure Virtual Desktop에 사용할 계정과 다른 Azure Active Directory 계정으로 이미 로그인한 경우, 로그아웃하거나 개인 브라우저 창을 사용해야 합니다.

이제 로그인한 후에는 리소스 목록이 표시됩니다. **모든 리소스** 탭에서 일반 앱과 같이 선택하여 리소스를 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

웹 클라이언트를 사용하는 방법에 대한 자세한 내용은 [웹 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)을 참조하세요.
