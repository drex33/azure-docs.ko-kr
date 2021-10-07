---
title: 빠른 시작 - 프라이빗 클라우드에서 Azure VMware VM 만들기 - Azure VMware Solution by CloudSimple
description: CloudSimple 프라이빗 클라우드에서 VMware 가상 머신을 만드는 방법에 대해 알아봅니다. Azure Portal에서 CloudSimple 포털에 액세스합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4eec39455834d6ebaa2fcb1ed2799b0e023d48bf
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616173"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>프라이빗 클라우드에서 VMware 가상 머신 만들기

프라이빗 클라우드에서 가상 머신을 만들려면 먼저 Azure Portal에서 CloudSimple 포털에 액세스합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

1. **모든 서비스** 를 선택합니다.
2. **CloudSimple 서비스** 를 검색합니다.
3. 프라이빗 클라우드를 만들려는 CloudSimple 서비스를 선택합니다.
4. **개요** 페이지에서 **CloudSimple 포털로 이동** 을 클릭하여 CloudSimple 포털에 대한 새 브라우저 탭을 엽니다.  메시지가 표시되면 Azure 로그인 자격 증명을 사용하여 로그인합니다.  

    ![CloudSimple 포털 시작](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>vCenter 웹 UI 시작

이제 vCenter를 시작하여 가상 머신 및 정책을 설정할 수 있습니다.

vCenter에 액세스하려면 CloudSimple 포털에서 시작합니다. 홈페이지의 **일반 작업** 에서 **vSphere Client 시작** 을 클릭합니다.  프라이빗 클라우드를 선택한 후 프라이빗 클라우드에서 **vSphere Client 시작** 을 클릭합니다.

   ![vSphere Client 시작](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>ISO 또는 vSphere 템플릿 업로드

  > [!WARNING]
  > ISO 업로드의 경우 vSphere HTML5 클라이언트를 사용합니다.  Flash 클라이언트를 사용하면 오류가 발생할 수 있습니다.

1. VM을 만들고 로컬 시스템에서 사용할 수 있도록 하려면 vCenter에 업로드하려는 ISO 또는 vSphere 템플릿을 가져옵니다.
2. vCenter에서 **디스크** 아이콘을 클릭하고 **vsanDatastore** 를 선택합니다. **Files** 를 클릭한 다음, **새 폴더** 를 클릭합니다.
    ![vCenter ISO](media/vciso00.png)

3. ‘ISO 및 템플릿’이라는 폴더를 만듭니다.

4. ISO 및 템플릿의 ISO 폴더로 이동하고 **파일 업로드** 를 클릭합니다. 화면의 지시에 따라 ISO를 업로드합니다.

## <a name="create-a-virtual-machine-in-vcenter"></a>vCenter에서 가상 머신 만들기

1. vCenter에서 **호스트 및 클러스터** 아이콘을 클릭합니다.

2. **워크로드** 를 마우스 오른쪽 단추로 클릭하고 **새 가상 머신** 을 선택합니다.
    ![새 가상 머신 메뉴 옵션을 강조 표시하는 스크린샷.](media/vcvm01.png)

3. **새 가상 머신 만들기** 를 선택하고 **다음** 을 클릭합니다.
    ![새 가상 머신 만들기 옵션을 강조 표시하는 스크린샷.](media/vcvm02.png)

4. 컴퓨터에 이름을 지정하고, **워크로드 VM의** 위치를 선택하고, **다음** 을 클릭합니다.
    ![워크로드 VM 옵션을 강조 표시하는 스크린샷.](media/vcvm03.png)

5. **워크로드** 컴퓨팅 리소스를 선택하고 **다음** 을 클릭합니다.
    ![워크로드 컴퓨팅 리소스를 강조 표시하는 스크린샷.](media/vcvm04.png)

6. **vsanDatastore** 를 선택하고 **다음** 을 클릭합니다.
    ![vsanDatastore 옵션을 강조 표시하는 스크린샷.](media/vcvm05.png)

7. 기본 ESXi 6.5 호환성 선택 항목을 유지하고 **다음** 을 클릭합니다.
    ![선택한 ESXi 6.5 호환성 옵션을 보여 주는 스크린샷.](media/vcvm06.png)

8. 만들려는 VM에 대한 ISO의 게스트 OS를 선택하고 **다음** 을 클릭합니다.
    ![VM에 대한 ISO의 게스트 OS를 선택하는 방법을 보여 주는 스크린샷.](media/vcvm07.png)

9. 하드 디스크 및 네트워크 옵션을 선택합니다. 새 CD/DVD 드라이브의 경우 **데이터 저장소 ISO 파일** 을 선택합니다.  공용 IP 주소에서 이 VM으로 들어오는 트래픽을 허용하려면 **vm-1** 을 네트워크로 선택합니다.
    ![데이터 저장소 ISO 파일을 선택하는 위치를 강조 표시하는 스크린샷](media/vcvm08.png)

10. 선택 창이 열립니다. 이전에 ISO 및 템플릿 폴더에 업로드한 파일을 선택하고 **확인** 을 클릭합니다.
    ![새 VM](media/vcvm10.png)

11. 설정을 검토하고 **확인** 을 클릭하여 VM을 만듭니다.
    ![설정을 검토하는 위치를 보여 주는 스크린샷](media/vcvm11.png)

이제 VM이 워크로드 컴퓨팅 리소스에 추가되고 사용할 준비가 되었습니다. 
![워크로드 컴퓨팅 리소스에 추가된 VM을 보여 주는 스크린샷.](media/vcvm12.png)

이제 기본 설정이 완료되었습니다. 온-프레미스 VM 인프라를 사용하는 방법과 유사하게 프라이빗 클라우드 사용을 시작할 수 있습니다.

다음 섹션에는 프라이빗 클라우드 워크로드에 DNS 및 DHCP 서버를 설정하고 기본 네트워킹 구성을 수정하는 방법에 대한 선택적 정보가 포함되어 있습니다.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>vCenter에 사용자 및 ID 소스 추가(선택 사항)

CloudSimple은 기본 vCenter 사용자 계정에 `cloudowner@cloudsimple.local`이라는 사용자 이름을 할당합니다. 시작하기 위한 추가 계정 설정이 필요하지 않습니다.  CloudSimple은 보통 일반적인 작업을 수행하는 데 필요한 권한을 관리자에게 할당합니다.  온-프레미스 Active Directory 또는 Azure AD를 프라이빗 클라우드의 [추가 ID 원본](set-vcenter-identity.md)으로 설정합니다.

## <a name="create-a-dns-and-dhcp-server-optional"></a>DNS 및 DHCP 서버 만들기(선택 사항)

프라이빗 클라우드 환경에서 실행되는 애플리케이션 및 워크로드에는 조회 및 IP 주소 할당을 위한 이름 확인 및 DHCP 서비스가 필요합니다. 이러한 서비스를 제공하려면 적절한 DHCP 및 DNS 인프라가 필요합니다. 프라이빗 클라우드 환경에서 이러한 서비스를 제공하려면 vCenter에 가상 머신을 구성하면 됩니다.

필수 구성 요소

* VLAN이 구성된 분산 포트 그룹

* 온-프레미스 또는 인터넷 기반 DNS 서버로 설정 라우팅

* 가상 머신 템플릿 또는 가상 머신을 생성할 ISO

다음 링크는 Linux 및 Windows에서 DHCP 및 DNS 서버를 설정하는 방법에 대한 참고 자료를 제공합니다.

#### <a name="linux-based-dns-server-setup"></a>Linux 기반 DNS 서버 설정

Linux에서는 DNS 서버를 설정하기 위한 다양한 패키지를 제공합니다.  오픈 소스 BIND DNS 서버를 설정하는 지침에 대한 링크는 다음과 같습니다.

[예제 설정](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Windows 기반 설정

이 Microsoft 항목에서는 Windows 서버를 DNS 서버 및 DHCP 서버로 설정하는 방법을 설명합니다.

[Windows 서버를 DNS 서버로](/windows-server/networking/dns/dns-top)

[Windows 서버를 DHCP 서버로](/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>네트워킹 구성 사용자 지정(선택 사항)

CloudSimple 포털의 네트워크 페이지를 사용하여 VM의 방화벽 테이블 및 공용 IP 주소에 대한 구성을 지정할 수 있습니다.

### <a name="allocate-public-ips"></a>공용 IP 할당

1. CloudSimple 포털에서 **네트워크 > 공용 IP** 로 이동합니다.
2. **공용 IP 할당** 을 클릭합니다.
3. IP 주소 항목을 식별하는 이름을 입력합니다.
4. 기본 위치를 유지합니다.
5. 원하는 경우 슬라이더를 사용하여 유휴 시간 제한을 변경합니다.
6. 공용 IP 주소를 할당할 로컬 IP 주소를 입력합니다.
7. 원하는 경우 관련 DNS 이름을 입력합니다.
8. **Done** 을 클릭합니다.

    ![공용 IP](media/quick-create-pc-public-ip.png)

공용 IP 주소를 할당하는 작업이 시작됩니다. **활동 > 작업** 페이지에서 작업 상태를 확인할 수 있습니다. 할당이 완료되면 새 항목이 공용 IP 페이지에 표시됩니다.

이 IP 주소를 매핑해야 하는 VM은 위에 지정된 로컬 주소를 사용하여 구성해야 합니다. IP 주소를 구성하는 절차는 VM 운영 체제에만 적용됩니다. 올바른 절차는 VM 운영 체제에 대한 설명서를 참조하세요.

#### <a name="example"></a>예제

예를 들어 Ubuntu 16.04에 대한 세부 정보는 다음과 같습니다.

/etc/network/interfaces 파일의 inet 주소 패밀리 구성에 정적 메서드를 추가합니다. 주소, 네트워크 마스크 및 게이트웨이 값을 변경합니다. 이 예에서는 eth0 인터페이스, 내부 IP 주소 192.168.24.10, 게이트웨이 주소 192.168.24.1 및 네트워크 마스크 255.255.255.0을 사용합니다. 사용자 환경에서 사용 가능한 서브넷 정보는 환영 이메일에 제공됩니다.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
인터페이스를 수동으로 사용하지 않도록 설정합니다.

```
sudo ifdown eth0
```
인터페이스를 다시 수동으로 사용하도록 설정합니다.

```
sudo ifup eth0
```

기본적으로 인터넷에서 들어오는 모든 트래픽이 **거부** 됩니다. 다른 모든 포트를 열려면 [방화벽 테이블](firewall.md)을 만듭니다.

내부 IP 주소를 고정 IP 주소로 구성한 후에는 VM 내에서 인터넷에 연결할 수 있는지 확인합니다.

```
ping 8.8.8.8
```
또한 인터넷에서 공용 IP 주소를 사용하여 VM에 연결할 수 있는지 확인합니다.

VM의 iptable 규칙이 포트 80 인바운드를 차단하지 않는지 확인합니다.
        
```
netstat -an | grep 80
```

포트 80에서 수신 대기하는 http 서버를 시작합니다.
       
```
python2.7 -m SimpleHTTPServer 80
```

또는

```
python3 -m http.server 80
```
바탕 화면에서 브라우저를 시작하고 공용 IP 주소의 포트 80를 가리켜 VM의 파일을 찾아봅니다.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>공용 IP에 대한 기본 CloudSimple 방화벽 규칙

* VPN 트래픽: VPN과 모든 워크로드 네트워크 및 관리 네트워크 간에 주고받는 모든 트래픽이 허용됩니다.
* 프라이빗 클라우드 내부 트래픽: 워크로드 네트워크와 관리 네트워크(위에 표시 됨) 간에 주고받는 모든 동-서 트래픽이 허용됩니다.
* 인터넷 트래픽:
  * 인터넷에서 워크로드 네트워크 및 관리 네트워크로 들어오는 모든 트래픽이 거부됩니다.
  * 워크로드 네트워크 또는 관리 네트워크에서 인터넷으로 나가는 모든 트래픽이 허용됩니다.

또한 방화벽 규칙 기능을 사용하여 트래픽을 보호하는 방법을 수정할 수도 있습니다. 자세한 내용은 [방화벽 테이블 및 규칙 설정](firewall.md)을 참조하세요.

## <a name="install-solutions-optional"></a>솔루션 설치(선택 사항)

프라이빗 클라우드 vCenter 환경을 최대한 활용하기 위해 CloudSimple 프라이빗 클라우드에 솔루션을 설치할 수 있습니다. 백업, 재해 복구, 복제 및 기타 기능을 설정하여 가상 머신을 보호할 수 있습니다. 예를 들면 VMware SRM(VMware Site Recovery Manager) 및 Veeam Backup & Replication이 있습니다.

솔루션을 설치하려면 제한된 기간 동안 추가 권한을 요청해야 합니다. [권한 에스컬레이션](escalate-private-cloud-privileges.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware VM 사용](quickstart-create-vmware-virtual-machine.md)
* [Azure ExpressRoute를 사용하여 온-프레미스 네트워크에 연결](on-premises-connection.md)
* [CloudSimple 네트워크에 VPN 게이트웨이 설정](vpn-gateway.md)
