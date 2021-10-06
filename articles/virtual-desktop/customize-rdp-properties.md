---
title: PowerShell을 사용하여 RDP 속성 사용자 지정 - Azure
description: PowerShell cmdlet을 사용하여 Azure Virtual Desktop의 RDP 속성을 사용자 지정하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 282044d0ee3d07ae4eaa2c63d8d0bcebae0251aa
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544492"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>호스트 풀의 원격 데스크톱 프로토콜(RDP) 속성 사용자 지정

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md)를 참조하세요.

다중 모니터 환경 및 오디오 리디렉션과 같은 호스트 풀의 RDP(원격 데스크톱 프로토콜) 속성을 사용자 지정하면 사용자의 요구에 따라 최적의 환경을 제공할 수 있습니다. 기본 RDP 파일 속성을 변경하려는 경우 Azure Portal을 사용하거나 **Update-AzWvdHostPool** 의 *-CustomRdpProperty* 매개 변수를 사용하여 Azure Virtual Desktop에서 RDP 속성을 사용자 지정할 수 있습니다.

지원되는 속성 및 해당 기본값의 전체 목록은 [지원되는 RDP 파일 설정](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext)을 참조하세요.

## <a name="default-rdp-file-properties"></a>기본 RDP 파일 속성

RDP 파일에는 기본적으로 다음과 같은 속성이 있습니다.

|RDP 속성|데스크톱 및 RemoteApp 모두|
|---|---|
|다중 모니터 모드|사용|
|드라이브 리디렉션 사용|드라이브, 클립보드, 프린터, COM 포트, 스마트 카드, 디바이스 및 usbdevicestore|
|원격 오디오 모드|로컬로 재생|
|VideoPlayback|사용|
|EnableCredssp|사용|

>[!NOTE]
>- 다중 모니터 모드는 데스크톱 앱 그룹에 대해서만 사용하도록 설정되며 RemoteApp 앱 그룹에 대해서는 무시됩니다.
>- 모든 기본 RDP 파일 속성은 Azure Portal에 노출됩니다.
>- 기본적으로 CustomRdpProperty 필드는 Azure Portal null입니다. null CustomRdpProperty 필드는 호스트 풀에 모든 기본 RDP 속성을 적용합니다. 빈 CustomRdpProperty 필드는 호스트 풀에 기본 RDP 속성을 적용하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 PowerShell 모듈을 설정하고 Azure에 로그인하려면 [Azure Virtual Desktop PowerShell 모듈 설정](powershell-module.md)의 지침을 따르세요.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Azure Portal의 RDP 속성 구성

Azure Portal의 RDP 속성을 구성하려면 다음을 수행합니다.

1. <https://portal.azure.com> 에서 Azure에 로그인합니다.
2. 검색 창에 **Azure Virtual Desktop** 을 입력합니다.
3. 서비스에서 **Azure Virtual Desktop** 을 선택합니다.
4. Azure Virtual Desktop 페이지의 화면 왼쪽에 있는 메뉴에서 **호스트 풀** 을 선택합니다.
5. 업데이트할 **호스트 풀의 이름** 을 선택합니다.
6. 화면 왼쪽의 메뉴에서 **RDP 속성** 을 선택합니다.
7. 원하는 속성을 설정합니다.
   - 또는 **고급** 탭을 열고 다음 섹션의 PowerShell 예제와 같이 세미콜론으로 구분된 형식으로 RDP 속성을 추가할 수 있습니다.
8. 선택을 마치면 **저장** 을 선택하여 변경 내용을 저장합니다.

다음 섹션에서는 PowerShell에서 사용자 지정 RDP 속성을 수동으로 편집하는 방법을 설명합니다.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>단일 사용자 지정 RDP 속성 추가 또는 편집

단일 사용자 지정 RDP 속성을 추가하거나 편집하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

방금 실행한 cmdlet이 속성을 업데이트했는지 확인하려면 이 cmdlet을 실행합니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

예를 들어 0301HP 라는 호스트 풀의 ‘audiocapturemode’ 속성을 확인하는 경우 이 cmdlet을 입력합니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>여러 사용자 지정 RDP 속성 추가 또는 편집

여러 사용자 지정 RDP 속성을 추가하거나 편집하려면 사용자 지정 RDP 속성을 세미콜론으로 구분된 문자열로 제공하여 다음 PowerShell cmdlet을 실행합니다.

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

다음 cmdlet을 실행하여 RDP 속성이 추가되었는지 확인할 수 있습니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

이전 cmdlet 예제에 따라 0301HP 호스트 풀에 여러 RDP 속성을 설정하는 경우 cmdlet은 다음과 같습니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>모든 사용자 지정 RDP 속성 다시 설정

[단일 사용자 지정 RDP 속성 추가 또는 편집](#add-or-edit-a-single-custom-rdp-property)의 지침에 따라 개별 사용자 지정 RDP 속성을 기본값으로 다시 설정하거나 다음 PowerShell cmdlet을 실행하여 호스트 풀에 대한 모든 사용자 지정 RDP 속성을 다시 설정할 수 있습니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

설정이 성공적으로 제거되었는지 확인하려면 이 cmdlet을 입력합니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>다음 단계

이제 지정된 호스트 풀에 대한 RDP 속성을 사용자 지정했으므로 Azure Virtual Desktop 클라이언트에 로그인하여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 방법 가이드는 선택한 클라이언트를 사용하여 세션에 연결하는 방법을 보여 줍니다.

- [Windows Desktop 클라이언트를 사용하여 연결](./user-documentation/connect-windows-7-10.md)
- [웹 클라이언트를 사용하여 연결](./user-documentation/connect-web.md)
- [Android 클라이언트와 연결](./user-documentation/connect-android.md)
- [macOS 클라이언트와 연결](./user-documentation/connect-macos.md)
- [iOS 클라이언트와 연결](./user-documentation/connect-ios.md)
