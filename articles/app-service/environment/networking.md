---
title: App Service Environment 네트워킹
description: App Service Environment 네트워킹 세부 정보
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: cdedc94e64a0646c7e4ffa8c93f082d040e440c7
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524078"
---
# <a name="app-service-environment-networking"></a>App Service Environment 네트워킹

> [!NOTE]
> 이 문서에서는 격리된 v2 App Service 요금제와 함께 사용되는 App Service Environment v3에 대해 설명합니다.
> 

ASE (App Service Environment)는 Windows 및 Linux 컨테이너, 웹 앱, api apps, 논리 앱 및 함수 앱을 호스트 하는 Azure App Service의 단일 테 넌 트 배포입니다. ASE를 설치할 때 배포 하려는 Azure Virtual Network를 선택 합니다. 모든 인바운드 및 아웃 바운드 응용 프로그램 트래픽은 지정 하는 가상 네트워크 내에 있습니다. ASE는 가상 네트워크의 단일 서브넷에 배포 됩니다. 동일한 서브넷에 다른 어떤 것도 배포할 수 없습니다.

## <a name="subnet-requirements"></a>서브넷 요구 사항

서브넷은 Microsoft. Web/hostingEnvironments에 위임 되어야 하며 비어 있어야 합니다.

서브넷의 크기는 ASE 내의 App Service 계획 인스턴스의 크기 조정 제한에 영향을 줄 수 있습니다. `/24`프로덕션 규모를 지원할 수 있는 충분 한 주소를 확보 하기 위해 서브넷에 주소 공간 (256 주소)을 사용 하는 것이 좋습니다.

더 작은 서브넷을 사용 하려면 ASE 및 네트워크 설정에 대 한 다음 세부 정보를 알고 있어야 합니다.

지정 된 서브넷에는 관리 목적으로 예약 된 5 개의 주소가 있습니다. 관리 주소를 기반으로 하는 ASE는 지원 인프라를 동적으로 확장 하 고 구성 및 부하에 따라 4 ~ 27 개의 주소를 사용 합니다. 나머지 주소는 App Service 계획의 인스턴스에 사용할 수 있습니다. 서브넷의 최소 크기는 `/27` 주소 공간 (32 주소)입니다.

서브넷 내에서 주소가 부족 한 경우 ASE에서 App Service 계획을 확장 하는 것을 제한 하거나, 지원 인프라의 크기를 조정할 수 없는 경우 집약적 트래픽 로드 중에 대기 시간이 길어질 수 있습니다.

## <a name="addresses"></a>주소

ASE에는 생성 시 다음과 같은 네트워크 정보가 있습니다.

| 주소 형식 | description |
|--------------|-------------|
| ASE 가상 네트워크 | ASE가 배포 되는 가상 네트워크 |
| ASE 서브넷 | ASE가 배포되는 서브넷 |
| 도메인 접미사 | 이 ASE에서 만든 앱에서 사용하는 도메인 접미사 |
| 가상 IP | 이 설정은 ASE에서 사용 하는 VIP 유형입니다. 가능한 두 값은 내부 및 외부입니다. |
| 인바운드 주소 | 인바운드 주소는 이 ASE의 앱이 도달하는 주소입니다. 내부 VIP가 있으면 ASE 서브넷의 주소입니다. 주소가 외부에 있으면 공개 주소가 됩니다. |
| 기본 아웃바운드 주소 | 이 ASE의 앱은 인터넷에 대한 아웃바운드 호출을 수행할 때 기본값으로 이 주소를 사용합니다. |

ASEv3는 ASE 포털의 **IP 주소** 부분에 있는 ASE에서 사용하는 주소에 대한 세부 정보를 포함합니다.

![ASE 주소 UI](./media/networking/networking-ip-addresses.png)

ASE에서 App Service 요금제를 스케일링하면 ASE 서브넷에서 더 많은 주소를 사용할 수 있습니다. 사용되는 주소 수는 보유한 App Service 요금제 인스턴스 수와 ASE에서 수신하는 트래픽 양에 따라 달라집니다. ASE의 앱은 ASE 서브넷에 전용 주소를 포함하지 않습니다. 앱에서 ASE 서브넷의 앱에 사용되는 특정 주소는 시간이 지남에 따라 변경됩니다.

## <a name="ports-and-network-restrictions"></a>포트 및 네트워크 제한

앱에서 트래픽을 수신 하려면 NSGs (인바운드 네트워크 보안 그룹) 규칙이 ASE 서브넷을 통해 필요한 포트에서 트래픽을 수신할 수 있도록 해야 합니다. 트래픽을 수신 하는 포트 외에도 AzureLoadBalancer가 포트 80에서 ASE 서브넷에 연결할 수 있는지 확인 해야 합니다. 내부 VM 상태 검사에 사용 됩니다. 가상 네트워크에서 ASE 서브넷으로 포트 80 트래픽을 계속 제어할 수 있습니다.

일반적인 권장 사항은 다음 인바운드 NSG 규칙을 구성 하는 것입니다.

|포트|원본|대상|
|-|-|-|
|80,443|VirtualNetwork|ASE 서브넷 범위|

ASE가 작동 하기 위한 최소 요구 사항은 다음과 같습니다.

|포트|원본|대상|
|-|-|-|
|80|AzureLoadBalancer|ASE 서브넷 범위|

최소 필수 규칙을 사용 하는 경우 응용 프로그램 트래픽에 대해 하나 이상의 규칙이 필요할 수 있으며, 배포 또는 디버깅 옵션을 사용 하는 경우 ASE 서브넷에 대 한이 트래픽도 허용 해야 합니다. 이러한 규칙의 원본은 VirtualNetwork 또는 하나 이상의 특정 클라이언트 Ip 또는 IP 범위 일 수 있습니다. 대상은 항상 ASE 서브넷 범위가 됩니다.

기본 앱 액세스 포트는 다음과 같습니다.

|Windows Server Update Services와 함께|포트|
|-|-|
|HTTP/HTTPS|80, 443|
|FTP/FTPS|21, 990, 10001-10020|
|Visual Studio 원격 디버깅|4022, 4024, 4026|
|웹 배포 서비스|8172|

## <a name="network-routing"></a>네트워크 라우팅

제한 없이 UDRs (경로 테이블)를 설정할 수 있습니다. ASE의 모든 아웃 바운드 응용 프로그램 트래픽을 Azure 방화벽과 같은 송신 방화벽 장치로 강제로 터널링 하 고 응용 프로그램 종속성 외에는 걱정할 필요가 없습니다. Application Gateway와 같은 WAF 디바이스를 ASE에 대한 인바운드 트래픽 앞에 배치하여 해당 ASE의 특정 앱을 노출할 수 있습니다. ASE에서 응용 프로그램의 아웃 바운드 주소를 사용자 지정 하려는 경우 ASE 서브넷에 NAT 게이트웨이를 추가할 수 있습니다.

## <a name="dns"></a>DNS

다음 섹션에서는 ase에 대 한 DNS 고려 사항 및 구성 인바운드와 ASE에서의 아웃 바운드에 대해 설명 합니다.

### <a name="dns-configuration-to-your-ase"></a>ASE에 대한 DNS 구성

외부 VIP를 사용하여 ASE를 만든 경우 앱은 자동으로 공용 DNS에 배치됩니다. 내부 VIP를 사용하여 ASE를 만든 경우 DNS를 구성해야 할 수 있습니다. ASE를 만드는 동안 자동으로 구성 된 개인 영역 Azure DNS 하도록 선택한 경우 ASE 가상 네트워크에 DNS가 구성 됩니다. DNS 수동 구성을 선택한 경우에는 자체 DNS 서버를 사용하거나 Azure DNS 프라이빗 영역을 구성해야 합니다. ASE의 인바운드 주소를 찾으려면 **ASE 포털 > IP 주소** UI로 이동합니다. 

자체 DNS 서버를 사용하려면 다음 레코드를 추가해야 합니다.

1. 다음에 대 한 영역 만들기 `<ASE-name>.appserviceenvironment.net`
1. 해당 영역에 *로 ASE에서 사용하는 인바운드 IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 @로 ASE에서 사용하는 인바운드 IP 주소를 가리키는 A 레코드 만들기
1. `<ASE-name>.appserviceenvironment.net`명명 된 scm에서 영역 만들기
1. scm 영역에 *로 ASE 프라이빗 엔드포인트에서 사용하는 인바운드 IP 주소를 가리키는 A 레코드 만들기

Azure DNS 프라이빗 영역에서 DNS를 구성하려면 다음을 수행합니다.

1. `<ASE-name>.appserviceenvironment.net`이라는 Azure DNS 프라이빗 영역 만들기
1. 해당 영역에 *로 인바운드 IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 @로 인바운드 IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 *.scm으로 인바운드 IP 주소를 가리키는 A 레코드 만들기

앱을 만들 때 제공 되는 기본 도메인 외에도 앱에 사용자 지정 도메인을 추가할 수 있습니다. ILB ASE의 앱에 대한 유효성 검사 없이 사용자 지정 도메인 이름을 설정할 수 있습니다. 사용자 지정 도메인을 사용 하는 경우 DNS 레코드를 구성 했는지 확인 해야 합니다. 위의 지침에 따라 기본 도메인 이름을 사용자 지정 도메인 이름으로 바꿔 사용자 지정 도메인 이름에 대 한 DNS 영역 및 레코드를 구성할 수 있습니다. 사용자 지정 도메인 이름은 앱 요청에 대해서는 작동하지만 scm 사이트의 경우에는 작동하지 않습니다. scm 사이트는 *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net* 에서만 사용할 수 있습니다.

### <a name="dns-configuration-from-your-ase"></a>ASE의 DNS 구성

ASE의 앱은 가상 네트워크가 구성 된 DNS를 사용 합니다. 일부 앱에서 가상 네트워크가 구성 된 것과 다른 DNS 서버를 사용 하도록 하려면 앱 설정 WEBSITE_DNS_SERVER 및 WEBSITE_DNS_ALT_SERVER를 사용 하 여 앱 단위로 수동으로 설정할 수 있습니다. WEBSITE_DNS_ALT_SERVER 앱 설정은 보조 DNS 서버를 구성합니다. 보조 DNS 서버는 주 DNS 서버에서 응답이 없는 경우에만 사용됩니다.

## <a name="limitations"></a>제한 사항

ASE를 고객 가상 네트워크에 배포 하는 동안 ASE에서 사용할 수 없는 몇 가지 네트워킹 기능이 있습니다.

* SMTP 트래픽을 보냅니다. 여전히 메일로 트리거된 경고가 있을 수 있지만 앱이 포트 25에서 아웃바운드 트래픽을 보낼 수 없습니다.
* Network Watcher 또는 NSG Flow를 사용하여 아웃바운드 트래픽 모니터링

## <a name="more-resources"></a>추가 리소스

- [환경 변수 및 앱 설정 참조](../reference-app-settings.md)