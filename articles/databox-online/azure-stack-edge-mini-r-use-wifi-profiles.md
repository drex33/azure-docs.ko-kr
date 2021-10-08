---
title: Azure Stack Edge Mini R 디바이스에서 Wi-Fi 프로필 사용
description: 보안 수준이 높은 엔터프라이즈 네트워크 및 비공개 네트워크에서 Azure Stack Edge Mini R 디바이스에 대한 Wi-Fi 프로필을 만드는 방법을 설명합니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/07/2021
ms.author: alkohli
ms.openlocfilehash: 34fe33f3c1d5be747e96ae71567424026c7c7a2a
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669210"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Azure Stack Edge Mini R 디바이스에서 Wi-Fi 프로필 사용

이 문서에서는 Azure Stack Edge Mini R 디바이스에서 무선 네트워크(Wi-Fi) 프로필을 사용하는 방법을 설명합니다.

Wi-Fi 프로필을 준비하는 방법은 무선 네트워크 유형에 따라 다릅니다.

- WPA2(Wi-Fi 보호 액세스 2) - 홈 네트워크 또는 Wi-Fi 오픈 핫스팟과 같은 비공개 네트워크에서 다른 디바이스에서 사용하는 것과 동일한 암호를 사용하여 기존 무선 프로필을 다운로드하여 사용할 수 있습니다.

- 보안 수준이 높은 엔터프라이즈 환경에서는 WPA2 - 엔터프라이즈 네트워크를 통해 디바이스에 액세스합니다. 이 유형의 네트워크에서 각 클라이언트 컴퓨터는 고유한 Wi-Fi 프로필을 가지며 인증서를 통해 인증됩니다. 필요한 구성을 결정하려면 네트워크 관리자와 협력해야 합니다.

나중에 두 유형의 네트워크에 대한 프로필 요구 사항에 대해 자세히 설명합니다.

두 경우 모두 디바이스에서 프로필을 테스트하거나 사용하기 전에 프로필이 조직의 보안 요구 사항을 충족하는지 확인하는 것이 매우 중요합니다.

## <a name="about-wi-fi-profiles"></a>Wi-Fi 프로필 정보

Wi-Fi 프로필에는 Azure Stack Edge Mini R 디바이스를 무선 네트워크에 연결하는 데 필요한 SSID(서비스 집합 식별자) 또는 **네트워크 이름**, 암호 키 및 보안 정보가 포함됩니다.

다음 코드 예는 일반적인 무선 네트워크에서 사용할 프로파일의 기본 설정을 보여 줍니다.

* `SSID`는 네트워크 이름입니다.

* `name`은 Wi-Fi 연결에 대한 사용자에게 친숙한 이름입니다. 사용자가 디바이스에서 사용 가능한 연결을 찾아볼 때 표시되는 이름입니다.

* 프로파일은 컴퓨터가 네트워크 범위(`connectionMode` = `auto`) 내에 있을 때 컴퓨터를 무선 네트워크에 자동으로 연결하도록 구성됩니다.

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

Wi-Fi 프로필 설정에 대한 자세한 내용은 [Windows 10 이상 디바이스에 대한 Wi-Fi 설정 추가](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile)의 **엔터프라이즈 프로필** 을 참조하고 [Cisco Wi-Fi 프로필 구성](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile)을 참조하세요.

Azure Stack Edge Mini R 디바이스에서 무선 연결을 사용하려면 디바이스에서 Wi-Fi 포트를 구성한 다음 디바이스에 Wi-Fi 프로필을 추가합니다. 엔터프라이즈 네트워크에서는 디바이스에 인증서도 업로드합니다. 그런 다음 디바이스의 로컬 웹 UI에서 무선 네트워크에 연결할 수 있습니다. 자세한 내용은 [Azure Stack Edge Mini R에서 무선 연결 관리](./azure-stack-edge-mini-r-manage-wifi.md)를 참조하세요.

## <a name="profile-for-wpa2---personal-network"></a>WPA2용 프로필 - 비공개 네트워크

WPA2(Wi-Fi 보호 액세스 2) - 홈 네트워크 또는 Wi-Fi 오픈 핫스팟과 같은 비공개 네트워크에서 여러 디바이스가 동일한 프로필과 동일한 암호를 사용할 수 있습니다. 홈 네트워크에서 휴대폰과 랩톱은 동일한 무선 프로필과 암호를 사용하여 네트워크에 연결합니다.

예를 들어 Windows 10 클라이언트는 사용자를 위한 런타임 프로필을 생성할 수 있습니다. 무선 네트워크에 로그인하면 Wi-Fi 암호를 입력하라는 메시지가 표시되고 해당 암호를 제공하면 연결됩니다. 이 환경에서는 인증서가 필요하지 않습니다.

이 유형의 네트워크에서는 랩톱에서 Wi-Fi 프로필을 내보낸 다음 Azure Stack Edge Mini R 디바이스에 추가할 수 있습니다. 자세한 내용은 아래의 [Wi-Fi 프로필 내보내기](#export-a-wi-fi-profile)를 참조하세요.

> [!IMPORTANT]
> Azure Stack Edge Mini R 디바이스에 대한 Wi-Fi 프로필을 만들기 전에 네트워크 관리자에게 문의하여 무선 네트워킹에 대한 조직의 보안 요구 사항을 확인합니다. 무선 네트워크가 요구 사항을 충족한다는 것을 확인할 때까지 디바이스에서 Wi-Fi 프로필을 테스트하거나 사용해서는 안 됩니다.

## <a name="profiles-for-wpa2---enterprise-network"></a>WPA2용 프로필 - 엔터프라이즈 네트워크

WPA2(무선 보호 액세스 2) - 엔터프라이즈 네트워크에서 네트워크 관리자와 협력하여 Azure Stack Edge Mini R 디바이스를 네트워크에 연결하는 데 필요한 Wi-Fi 프로필 및 인증서를 얻어야 합니다.

매우 안전한 네트워크의 경우 Azure 디바이스는 EAP-TLS(확장 가능한 인증 프로토콜-전송 계층 보안)와 함께 PEAP(보호된 확장 가능한 인증 프로토콜)를 사용할 수 있습니다. EAP-TLS를 사용하는 PEAP는 컴퓨터 인증을 사용합니다. 클라이언트와 서버는 인증서를 사용하여 서로 ID를 확인합니다.

> [!NOTE]
> * PEAP Microsoft Challenge Handshake 인증 프로토콜 버전 2(PEAP MSCHAPv2)를 사용한 사용자 인증은 Azure Stack Edge Mini R 디바이스에서 지원되지 않습니다.
> * Azure Stack Edge Mini R 함수에 액세스하려면 EAP-TLS 인증이 필요합니다. Active Directory를 사용하여 설정한 무선 연결은 작동하지 않습니다.

네트워크 관리자는 각 컴퓨터에 대해 고유한 Wi-Fi 프로필과 클라이언트 인증서를 생성합니다. 네트워크 관리자는 각 디바이스에 대해 별도의 인증서를 사용할 것인지 공유 인증서를 사용할 것인지 결정합니다.

작업장에 있는 둘 이상의 물리적 위치에서 작업하는 경우 네트워크 관리자는 무선 연결에 대해 둘 이상의 사이트별 Wi-Fi 프로필 및 인증서를 제공해야 할 수 있습니다.

엔터프라이즈 네트워크에서는 네트워크 관리자가 제공하는 Wi-Fi 프로필의 설정을 변경하지 않는 것이 좋습니다. 수행할 수 있는 유일한 조정 방법은 자동 연결 설정입니다. 자세한 내용은 Windows 10 이상 디바이스의 Wi-Fi 설정에서 [기본 프로필](/mem/intune/configuration/wi-fi-settings-windows#basic-profile)을 참조하세요.

보안 수준이 높은 엔터프라이즈 환경에서는 기존 무선 네트워크 프로필을 템플릿으로 사용할 수 있습니다.

* 업무용 컴퓨터에서 회사 무선 네트워크 프로필을 다운로드할 수 있습니다. 자세한 내용은 아래의 [Wi-Fi 프로필 내보내기](#export-a-wi-fi-profile)를 참조하세요.

* 조직의 다른 사용자가 이미 무선 네트워크를 통해 Azure Stack Edge Mini R 디바이스에 연결하고 있는 경우 해당 디바이스에서 Wi-Fi 프로필을 다운로드할 수 있습니다. 자세한 내용은 [Wi-Fi 프로필 다운로드](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile)를 참조하세요.

## <a name="export-a-wi-fi-profile"></a>Wi-Fi 프로필 내보내기

컴퓨터의 Wi-Fi 인터페이스에 대한 프로필을 내보내려면 다음 단계를 수행합니다.

1. 무선 프로필을 내보내는 데 사용할 컴퓨터가 장치에서 사용 하는 Wi-Fi 네트워크에 연결할 수 있는지 확인 합니다.

1. 컴퓨터의 무선 프로필을 보려면 **시작** 메뉴에서 **명령 프롬프트**(cmd.exe)를 열고 다음 명령을 입력합니다.

   `netsh wlan show profiles`

   출력은 다음과 비슷할 것입니다.

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

1. 프로필을 내보내려면 다음 명령을 입력합니다.

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>" key=clear`

   예를 들어 다음 명령은 ContosoFTINET 프로필을 XML 형식으로 `gusp`라는 사용자의 다운로드 폴더에 저장합니다.

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads key=clear

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>디바이스에 인증서, Wi-Fi 프로필 추가

필요한 Wi-Fi 프로필 및 인증서가 있는 경우 다음 단계를 수행하여 무선 연결을 위해 Azure Stack Edge Mini R 디바이스를 구성합니다.

1. WPA2 - 엔터프라이즈 네트워크의 경우 [인증서 업로드](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates)의 지침에 따라 필요한 인증서를 디바이스에 업로드합니다.

1. Wi-Fi 프로필을 Mini R 디바이스에 업로드한 다음 [추가, Wi-Fi 프로필에 연결](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile)의 안내에 따라 연결합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Mini R에 대한 네트워크 구성](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md) 방법을 알아봅니다.
- [Azure Stack Edge Mini R에서 Wi-Fi를 관리](azure-stack-edge-mini-r-manage-wifi.md)하는 방법을 알아봅니다.
