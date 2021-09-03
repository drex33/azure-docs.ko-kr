---
title: Azure Virtual Desktop Windows 10 또는 7에 연결 - Azure
description: Windows Desktop 클라이언트를 사용하여 Azure Virtual Desktop에 연결하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 07/20/2021
ms.author: helohr
manager: femila
ms.custom: template-how-to
ms.openlocfilehash: 644572ccfcc8ee0218e472757073588f2b47d4c5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452682"
---
# <a name="connect-with-the-windows-desktop-client"></a>Windows Desktop 클라이언트와 연결

Windows 데스크톱 클라이언트를 사용하여 Windows 10, Windows 10 IoT Enterprise, Windows 7이 설치된 디바이스의 Azure Virtual Desktop 리소스에 액세스할 수 있습니다. 

> [!IMPORTANT]
> Windows 8 또는 Windows 8.1은 이 방법에서 지원되지 않습니다.
> 
> 이 방법은 Azure Resource Manager 개체만 지원합니다. Azure Resource Manager 없이 개체를 지원하려면 [Windows 데스크톱(클래식) 클라이언트를 사용하여 연결](../virtual-desktop-fall-2019/connect-windows-7-10-2019.md)을 참조하세요.
> 
> RADC(RemoteApp 및 데스크톱 연결) 클라이언트나 원격 데스크톱 연결(MSTSC) 클라이언트도 이 방법에서 지원되지 않습니다.

## <a name="install-the-windows-desktop-client"></a>Windows Desktop 클라이언트 설치

Windows 버전에 따라 클라이언트를 다운로드합니다.

- [Windows 64비트](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32비트](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

설치 중에 액세스를 확인하려면 다음 중 하나를 선택합니다.

- **사용자 전용으로 설치**
- **이 머신의 모든 사용자에 대해 설치**(관리자 권한 필요)

설치 후 클라이언트를 시작하려면 **시작** 메뉴를 사용하여 **원격 데스크톱** 을 검색합니다.

## <a name="subscribe-to-a-workspace"></a>작업 영역 구독

작업 영역을 구독하려면 다음 중 하나를 선택합니다.

- 회사 또는 학교 계정을 사용하고 클라이언트가 사용 가능한 리소스를 검색하도록 합니다.
- 리소스의 특정 URL 사용

구독한 후에 리소스를 시작하려면 **연결 센터** 로 이동하여 리소스를 두 번 클릭합니다.

> [!TIP]
> **시작** 메뉴에서 리소스를 시작하려면 작업 영역 이름이 있는 폴더를 찾거나 검색 창에 리소스 이름을 입력하면 됩니다.

### <a name="use-a-user-account"></a>사용자 계정 사용

1. 기본 페이지에서 **구독** 을 선택합니다.
2. 메시지가 표시되면 사용자 계정으로 로그인합니다.

작업 영역별로 그룹화된 리소스가 **연결 센터** 에 나타납니다.

   > [!NOTE]
   > Windows 클라이언트는 자동으로 Azure Virtual Desktop(클래식)으로 기본 설정됩니다. 
   > 
   > 그러나 클라이언트가 추가 Azure Resource Manager 리소스를 검색하면 해당 리소스를 자동으로 추가하거나 사용자에게 사용 가능함을 알립니다.

### <a name="use-a-specific-url"></a>특정 URL 사용

1. 기본 페이지에서 **URL로 구독** 을 선택합니다.
2. *작업 영역 URL* 또는 *이메일 주소* 를 입력합니다.
   - **작업 영역 URL** 의 경우 관리자가 제공한 URL을 사용합니다.

   |사용 가능한 리소스|URL|
   |-|-|
   |Azure Virtual Desktop(클래식)|`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`|
   |Azure Virtual Desktop|`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`|
   |Azure Virtual Desktop(US Gov)|`https://rdweb.wvd.azure.us/api/arm/feeddiscovery`|
   |Azure Virtual Desktop(중국)|`https://rdweb.wvd.azure.cn/api/arm/feeddiscovery`|
   
   - **이메일** 의 경우 이메일 주소를 사용합니다. 
      
   관리자가 [이메일 검색](/windows-server/remote/remote-desktop-services/rds-email-discovery)을 사용하도록 설정한 경우 클라이언트는 이메일과 연결된 URL을 찾습니다.

3. **다음** 을 선택합니다.
4. 메시지가 표시되면 사용자 계정으로 로그인합니다.

작업 영역별로 그룹화된 리소스가 **연결 센터** 에 나타납니다.

## <a name="next-steps"></a>다음 단계

클라이언트를 사용하는 방법에 대한 자세한 내용은 [Windows Desktop 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)을 참조하세요.

클라이언트의 기능에 대해 자세히 알고자 하는 관리자라면 [관리자용 Windows Desktop 클라이언트](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin)를 확인하세요.
