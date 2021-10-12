---
title: FortiGate 배포 가이드
description: Fortinet FortiGate 차세대 방화벽 제품을 설정하고 사용합니다.
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: b1b3a2dca9e11e12a72ec99685ff71beb279473e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353935"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>FortiGate Azure Virtual Machine 배포 가이드

이 배포 가이드를 사용하여 Azure Virtual Machine으로 배포된 Fortinet FortiGate 차세대 방화벽 제품을 설정하고 사용하는 방법에 대해 알아봅니다. 또한 Azure Active Directory를 통해 VPN 인증을 제공하도록 FortiGate SSL VPN Azure AD 갤러리 앱을 구성합니다.

## <a name="redeem-the-fortigate-license"></a>FortiGate 라이선스 사용

Fortinet FortiGate 차세대 방화벽 제품은 Azure IaaS(Infrastructure as a Service)에서 가상 머신으로 사용할 수 있습니다. 이 가상 머신에는 종량제 및 BYOL(사용자 라이선스 필요)의 두 가지 라이선스 모드가 있습니다.

BYOL 가상 머신 배포 옵션을 통해 사용할 Fortinet의 FortiGate 라이선스를 구매한 경우 Fortinet의 제품 정품 인증 페이지(https://support.fortinet.com )에서 이 라이선스를 사용합니다. 결과 라이선스 파일의 확장명은 .lic입니다.

## <a name="download-firmware"></a>펌웨어 다운로드

이 문서를 작성할 당시 Fortinet FortiGate Azure VM은 SAML 인증에 필요한 펌웨어 버전과 함께 제공되지 않았습니다. Fortinet에서 최신 버전을 가져와야 합니다.

1. https://support.fortinet.com/ 에 로그인합니다.
2. **다운로드** > **펌웨어 이미지** 로 이동합니다.
3. **릴리스 정보** 의 오른쪽에서 **다운로드** 를 선택합니다.
4. **v6.00** > **6.4** > **6.4.2** 를 차례로 선택합니다.
5. 동일한 행에서 **HTTPS** 링크를 선택하여 **FGT_VM64_AZURE-v6-build1723-FORTINET.out** 을 다운로드합니다.
6. 나중에 사용할 수 있도록 해당 파일을 저장합니다.

## <a name="deploy-the-fortigate-vm"></a>FortiGate VM 배포

1. Azure Portal로 이동하여 FortiGate 가상 머신을 배포할 구독에 로그인합니다.
2. 새 리소스 그룹을 만들거나 FortiGate 가상 머신을 배포할 리소스 그룹을 엽니다.
3. **추가** 를 선택합니다.
4. **Marketplace 검색** 에서 *Forti* 를 입력합니다. **Fortinet FortiGate 차세대 방화벽** 을 선택합니다.
5. 소프트웨어 플랜을 선택합니다(라이선스가 있으면 BYOL, 없으면 종량제). **만들기** 를 선택합니다.
6. VM 구성을 채웁니다.

    ![[가상 머신 만들기]의 스크린샷](virtual-machine.png)

7. **인증 유형** 을 **암호** 로 설정하고, VM의 관리 자격 증명을 입력합니다.
8. **검토 + 만들기** > **만들기** 를 선택합니다.
9. VM 배포가 완료될 때까지 기다립니다.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>고정 공용 IP 주소 설정 및 정규화된 도메인 이름 할당

일관된 사용자 환경을 제공하려면 FortiGate VM의 공용 IP 주소를 고정 주소로 할당하는 것이 좋습니다. 그리고 FQDN(정규화된 도메인 이름)에 매핑합니다.

1. Azure Portal로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. **개요** 화면에서 공용 IP 주소를 선택합니다.

    ![Fortigate SSL VPN의 스크린샷](public-ip-address.png)

3. **고정** > **저장** 을 선택합니다.

FortiGate VM이 배포되는 환경에 대해 공개적으로 라우팅할 수 있는 도메인 이름을 소유하고 있는 경우 VM에 대한 호스트(A) 레코드를 만듭니다. 이 레코드는 앞에서 고정 주소로 할당된 공용 IP 주소에 매핑됩니다.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>8443 TCP 포트에 대한 새 인바운드 네트워크 보안 그룹 규칙 만들기

1. Azure Portal로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. 왼쪽 메뉴에서 **네트워킹** 을 선택합니다. 네트워크 인터페이스가 나열되고, 인바운드 포트 규칙이 표시됩니다.
3. **인바운드 포트 규칙 추가** 를 선택합니다.
4. TCP 8443에 대한 새 인바운드 포트 규칙을 만듭니다.

    ![[인바운드 보안 규칙 추가]의 스크린샷](port-rule.png)

5. **추가** 를 선택합니다.

## <a name="create-a-second-virtual-nic-for-the-vm"></a>VM에 대한 두 번째 가상 NIC 만들기

사용자가 내부 리소스를 사용할 수 있게 하려면 두 번째 가상 NIC를 FortiGate VM에 추가해야 합니다. 가상 NIC가 상주하는 Azure의 Virtual Network에는 이러한 내부 리소스로 라우팅할 수 있는 연결이 있어야 합니다.

1. Azure Portal로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. FortiGate VM이 아직 중지되지 않은 경우 **중지** 를 선택하고 VM이 종료될 때까지 기다립니다.
3. 왼쪽 메뉴에서 **네트워킹** 을 선택합니다.
4. **네트워크 인터페이스 연결** 을 선택합니다.
5. **네트워크 인터페이스 만들기 및 연결** 을 선택합니다.
6. 새 네트워크 인터페이스에 대한 속성을 구성한 다음, **만들기** 를 선택합니다.

    ![네트워크 인터페이스 만들기의 스크린샷](new-network-interface.png)

7. FortiGate VM을 시작합니다.


## <a name="configure-the-fortigate-vm"></a>FortiGate VM 구성

다음 섹션에서는 FortiGate VM을 설정하는 방법을 안내합니다.

### <a name="install-the-license"></a>라이선스 설치

1. `https://<address>`로 이동합니다. 여기서 `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 배포에 BYOL 모델을 사용하는 경우 라이선스를 업로드하라는 메시지가 표시됩니다. 앞에서 만든 라이선스 파일을 선택하여 업로드합니다. **확인** 을 선택하고 FortiGate VM을 다시 시작합니다.

    ![FortiGate VM 라이선스의 스크린샷](license.png)

5. 다시 부팅되면 관리자 자격 증명으로 다시 로그인하여 라이선스의 유효성을 검사합니다.

### <a name="update-firmware"></a>펌웨어 업데이트

1. `https://<address>`로 이동합니다. 여기서 `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 왼쪽 메뉴에서 **시스템** > **펌웨어** 를 선택합니다.
5. **펌웨어 관리** 에서 **브라우저** 를 선택하고, 앞에서 다운로드한 펌웨어 파일을 선택합니다.
6. 경고를 무시하고 **백업 구성 및 업그레이드** 를 선택합니다.

    ![펌웨어 관리 스크린샷](backup-configure-upgrade.png)

7. **계속** 을 선택합니다.
8. FortiGate 구성을 .conf 파일로 저장하라는 메시지가 표시되면 **저장** 을 선택합니다.
9. 펌웨어가 업로드되고 적용될 때까지 기다립니다. FortiGate VM이 다시 부팅될 때까지 기다립니다.
10. FortiGate VM이 다시 부팅되면 관리자 자격 증명으로 다시 로그인합니다.
11. 대시보드를 설정하라는 메시지가 표시되면 **나중에** 를 선택합니다.
12. 자습서 비디오가 시작되면 **확인** 을 선택합니다.

### <a name="change-the-management-port-to-tcp-8443"></a>관리 포트를 8443 TCP 포트로 변경

1. `https://<address>`로 이동합니다. 여기서 `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 왼쪽 메뉴에서 **시스템** 을 선택합니다.
5. **관리 설정** 에서 HTTPS 포트를 **8443** 으로 변경하고 **적용** 을 선택합니다.
6. 변경 내용이 적용되면 브라우저에서 [관리] 페이지를 다시 로드하려고 하지만 실패합니다. 지금부터 관리 페이지 주소는 `https://<address>:8443`입니다.

    ![[원격 인증서 업로드]의 스크린샷](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Azure AD SAML 서명 인증서 업로드

1. `https://<address>:8443`로 이동합니다. 여기서 `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 왼쪽 메뉴에서 **시스템** > **인증서** 를 선택합니다.
5. **가져오기** > **원격 인증서** 를 차례로 선택합니다.
6. Azure 테넌트의 FortiGate 사용자 지정 앱 배포에서 다운로드한 인증서를 찾습니다. 이 인증서를 선택하고, **확인** 을 선택합니다.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>사용자 지정 SSL 인증서 업로드 및 구성

사용 중인 FQDN을 지원하는 사용자 고유의 SSL 인증서를 사용하여 FortiGate VM을 구성할 수 있습니다. 프라이빗 키와 함께 PFX 형식으로 패키지된 SSL 인증서에 액세스할 수 있는 경우 해당 인증서를 이 용도로 사용할 수 있습니다.

1. `https://<address>:8443`로 이동합니다. 여기서 `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 왼쪽 메뉴에서 **시스템** > **인증서** 를 선택합니다.
5. **가져오기** > **로컬 인증서** > **PKCS #12 인증서** 를 선택합니다.
6. SSL 인증서 및 프라이빗 키가 포함된 .PFX 파일을 찾습니다.
7. .PFX 암호와 의미 있는 인증서 이름을 입력합니다. 그런 다음, **확인** 을 선택합니다.
8. 왼쪽 메뉴에서 **시스템** > **설정** 을 선택합니다.
9. **관리 설정** 에서 **HTTPS 서버 인증서** 옆에 있는 목록을 펼치고, 위에서 가져온 SSL 인증서를 선택합니다.
10. **적용** 을 선택합니다.
11. 브라우저 창을 닫고 `https://<address>:8443`로 이동합니다.
12. FortiGate 관리자 자격 증명으로 로그인합니다. 이제 올바른 SSL 인증서가 사용되는 것을 볼 수 있습니다.

### <a name="configure-authentication-timeout"></a>인증 시간 제한 구성

1. Azure Portal로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. 왼쪽 메뉴에서 **직렬 콘솔** 을 선택합니다.
3. [직렬 콘솔]에서 FortiGate VM 관리자 자격 증명으로 로그인합니다.
4. [직렬 콘솔]에서 다음 명령을 실행합니다.

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>네트워크 인터페이스에서 IP 주소를 가져오는지 확인

1. `https://<address>:8443`로 이동합니다. 여기서 `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
3. 왼쪽 메뉴에서 **네트워킹** 을 선택합니다.
4. [네트워크] 아래에서 **인터페이스** 를 선택합니다.
5. port1(외부 인터페이스) 및 port2(내부 인터페이스)를 검사하여 올바른 Azure 서브넷에서 IP 주소를 가져오는지 확인합니다.
    a. 두 포트 중 하나에서 DHCP를 통해 서브넷으로부터 IP 주소를 가져오지 않는 경우 마우스 오른쪽 단추로 해당 포트를 클릭하고 **편집** 을 선택합니다.
    b. [주소 지정 모드] 옆에 **DHCP** 가 선택되어 있는지 확인합니다.
    다. **확인** 을 선택합니다.

    ![네트워크 인터페이스 주소 지정의 스크린샷](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>FortiGate VM의 온-프레미스 회사 리소스에 대한 올바른 경로가 있는지 확인

멀티홈 Azure VM에는 동일한 가상 네트워크의 모든 네트워크 인터페이스가 있습니다(그러나 별도의 서브넷이 있을 수 있음). 이는 종종 FortiGate를 통해 게시되는 온-프레미스 회사 리소스에 대한 연결이 두 네트워크 인터페이스에 있음을 의미합니다. 따라서 온-프레미스 회사 리소스에 대한 요청이 수행될 때 올바른 인터페이스에서 나가도록 하는 사용자 지정 경로 항목을 만들어야 합니다.

1. `https://<address>:8443`로 이동합니다. 여기서 `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
3. 왼쪽 메뉴에서 **네트워킹** 을 선택합니다.
4. [네트워크] 아래에서 **고정 경로** 를 선택합니다.
5. **새로 만들기** 를 선택합니다.
6. [대상] 옆에 있는 **서브넷** 을 선택합니다.
7. [서브넷] 아래에서 온-프레미스 회사 리소스가 상주하는 서브넷 정보(예: 10.1.0.0/255.255.255.0)를 지정합니다.
8. [게이트웨이 주소] 옆에 port2가 연결된 Azure 서브넷의 게이트웨이를 지정합니다(예: 일반적으로 10.6.1.1과 같이 1로 끝남).
9. [인터페이스] 옆에서 내부 네트워크 인터페이스인 port2를 선택합니다.
10. **확인** 을 선택합니다.

    ![경로 구성의 스크린샷](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>FortiGate SSL VPN 구성

[자습서: FortiGate SSL VPN과 Azure Active Directory SSO(Single Sign-On) 통합](../../fortigate-ssl-vpn-tutorial.md)에 설명된 단계를 따릅니다.
