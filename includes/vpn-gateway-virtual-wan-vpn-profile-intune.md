---
title: 파일 포함
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/26/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6c8f4e902929d6a610dc1f9224ac17b2d3b893d0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "126056833"
---
Microsoft Intune을 사용하여 Azure VPN 클라이언트(Windows 10)에 대한 프로필을 배포할 수 있습니다. 이 문서는 사용자 지정 설정을 사용하여 Intune 프로필을 만드는 데 도움을 줍니다.

> [!NOTE]
>* 이 문서는 인증용으로만 Azure Active Directory를 사용하는 프로필 배포에 적용됩니다.


## <a name="prerequisites"></a>필수 구성 요소

* 디바이스가 이미 Intune MDM에 등록되어 있습니다.
* Windows 10용 Azure VPN 클라이언트는 클라이언트 컴퓨터에 이미 배포되어 있습니다.
* Windows 버전 19H2 이상만 지원됩니다.

## <a name="modify-xml"></a><a name="xml"></a>XML 수정

다음 단계에서는 다음 설정을 통해 Intune용 사용자 지정 OMA-URI 프로필에 샘플 XML을 사용합니다.

* 자동 연결 켜기
* 신뢰할 수 있는 네트워크 검색이 활성화되었습니다.

지원되는 다른 옵션은 [VPNv2 CSP](/windows/client-management/mdm/vpnv2-csp) 문서를 참조하세요.

1. Azure Portal에서 VPN 프로필을 다운로드하고 패키지에서 *azurevpnconfig.xml* 파일의 압축을 풉니다.
1. 아래 텍스트를 복사하여 새 편집기 파일에 붙여넣습니다.

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>

        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. 다운로드한 프로필(azurevpnconfig.xml)의 항목으로 ```<ServerUrlList>``` 및 ```</ServerUrlList>``` 사이의 항목을 수정합니다. 사용자 환경에 맞게 "TrustedNetworkDetection" FQDN을 변경합니다.
1. Azure 다운로드 프로필(azurevpnconfig.xml)을 열어 텍스트를 강조 표시하고 (ctrl) + C를 눌러 전체 콘텐츠를 클립보드에 복사합니다. 
1. 이전 단계에서 복사한 텍스트를 ```<CustomConfiguration>  </CustomConfiguration>``` 태그 사이에 2단계에서 만든 파일에 붙여넣습니다. 파일을 xml 확장명으로 저장합니다.
1. ```<name>  </name>``` 태그에 값을 기록합니다. 프로필의 이름입니다. Intune에서 프로필을 만들 때 이 이름이 필요합니다. 파일을 닫고 저장된 위치를 기억하세요.

## <a name="create-intune-profile"></a>Intune 프로필 만들기

이 섹션에서는 사용자 지정 설정으로 Microsoft Intune 프로필을 만듭니다.

1. Intune에 로그인하고 **디바이스 -> 구성 프로필** 로 이동합니다. **+ 프로필 만들기** 를 선택합니다.
1. **플랫폼** 에 대해 **Windows 10 이상** 을 선택합니다. **프로필 유형** 에 대해 **템플릿** 및 **사용자 지정** 을 선택합니다. 그런 다음 **만들기** 를 선택합니다.
1. 프로필에 이름 및 설명을 지정한 후, **다음** 을 선택합니다.
1. **구성 설정** 탭에서 **추가** 를 선택합니다.

    * **이름:** 구성 이름을 입력합니다.
    * **설명:** 선택적 설명입니다.
    * **OMA-URI:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML```(이 정보는 \<name\> \</name\> 태그의 azurevpnconfig.xml 파일에서 찾을 수 있습니다).
    * **데이터 형식:** 문자열(XML 파일).

   폴더 아이콘을 선택하고 [XML](#xml) 단계의 6단계에서 저장한 파일을 선택합니다. **추가** 를 선택합니다.

   :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png" alt-text="구성 설정" lightbox="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png":::
1. **다음** 을 선택합니다.
1. **할당** 에서 구성을 푸시할 그룹을 선택합니다. 그다음에 **다음** 을 선택합니다.
1. 적용 가능성 규칙은 선택 사항입니다. 필요한 경우 규칙을 정의한 후, **다음** 을 선택합니다.
1. **리뷰 + 생성** 페이지에서 **생성** 를 선택합니다.

    :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/create-profile.png" alt-text="프로필 만들기":::
1. 이제 사용자 지정 프로필이 생성됩니다. 이 프로필을 배포하는 Microsoft Intune 단계는 [사용자 및 디바이스 프로필 할당](/mem/intune/configuration/device-profile-assign)을 참조하세요.