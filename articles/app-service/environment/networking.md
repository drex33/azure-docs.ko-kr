---
title: App Service Environment 네트워킹
description: App Service Environment 네트워킹 세부 정보
author: madsd
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 06/30/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 177a9095a6a1cfb15a7bd17e106406521d1eda14
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639019"
---
# <a name="app-service-environment-networking"></a>App Service Environment 네트워킹

> [!NOTE]
> 이 문서에서는 격리된 v2 App Service 요금제와 함께 사용되는 App Service Environment v3에 대해 설명합니다.
> 


ASE(App Service Environment)는 웹앱, api 앱 및 함수 앱을 호스트하는 Azure App Service의 단일 테넌트 배포입니다. ASE를 설치할 때 배포하려는 Azure VNet(Virtual Network)를 선택합니다. 모든 인바운드 및 아웃바운드 트래픽 애플리케이션은 사용자가 지정하는 VNet 내부에 있습니다. ASE는 VNet의 단일 서브넷에 배포됩니다. 동일한 서브넷에 다른 어떤 것도 배포할 수 없습니다.

## <a name="subnet-requirements"></a>서브넷 요구 사항

서브넷은 Microsoft. Web/hostingEnvironments에 위임 되어야 하며 비어 있어야 합니다.

서브넷의 크기는 ASE 내의 App Service 계획 인스턴스의 크기 조정 제한에 영향을 줄 수 있습니다. 프로덕션 규모를 지원할 수 있는 충분 한 주소를 확보 하기 위해 서브넷에/24 주소 공간 (256 주소)을 사용 하는 것이 좋습니다.

더 작은 서브넷을 사용 하려면 ASE 및 네트워크 설정에 대 한 다음 세부 정보를 알고 있어야 합니다.

지정 된 서브넷에는 관리 목적으로 예약 된 5 개의 주소가 있습니다. 관리 주소를 기반으로 하는 ASE는 지원 인프라를 동적으로 확장 하 고 구성, 규모 및 부하에 따라 4 ~ 27 개의 주소를 사용 합니다. 나머지 주소는 App Service 계획의 인스턴스에 사용할 수 있습니다. 서브넷의 최소 크기는/27 주소 공간 (32 주소)입니다.

주소가 부족 하 게 되 면 ASE에서 App Service 계획을 확장 하는 것을 제한 하거나, 지원 인프라의 크기를 조정할 수 없는 경우 집약적 트래픽 로드 중에 대기 시간이 길어질 수 있습니다.

## <a name="addresses"></a>주소 

ASE에는 생성 시 다음과 같은 네트워크 정보가 있습니다.

| 주소 형식 | description |
|--------------|-------------|
| ASE 가상 네트워크 | ASE가 배포되는 VNet |
| ASE 서브넷 | ASE가 배포되는 서브넷 |
| 도메인 접미사 | 이 ASE에서 만든 앱에서 사용하는 도메인 접미사 |
| 가상 IP | 이 설정은 ASE에서 사용 하는 VIP 유형입니다. 가능한 두 값은 내부 및 외부입니다. |
| 인바운드 주소 | 인바운드 주소는 이 ASE의 앱이 도달하는 주소입니다. 내부 VIP가 있으면 ASE 서브넷의 주소입니다. 주소가 외부에 있으면 공개 주소가 됩니다. |
| 기본 아웃바운드 주소 | 이 ASE의 앱은 인터넷에 대한 아웃바운드 호출을 수행할 때 기본값으로 이 주소를 사용합니다. |

ASEv3는 ASE 포털의 **IP 주소** 부분에 있는 ASE에서 사용하는 주소에 대한 세부 정보를 포함합니다.

![ASE 주소 UI](./media/networking/networking-ip-addresses.png)

ASE에서 App Service 요금제를 스케일링하면 ASE 서브넷에서 더 많은 주소를 사용할 수 있습니다. 사용되는 주소 수는 보유한 App Service 요금제 인스턴스 수와 ASE에서 수신하는 트래픽 양에 따라 달라집니다. ASE의 앱은 ASE 서브넷에 전용 주소를 포함하지 않습니다. 앱에서 ASE 서브넷의 앱에 사용되는 특정 주소는 시간이 지남에 따라 변경됩니다.

## <a name="ports"></a>포트

ASE는 포트 80 및 443에서 애플리케이션 트래픽을 수신합니다. 해당 포트가 차단되면 앱에 연결할 수 없습니다. 

> [!NOTE]
> 부하 분산 장치와 ASE 인프라 간에 트래픽 연결을 유지하려면 AzureLoadBalancer에서 ASE 서브넷으로 포트 80을 허용해야 합니다. ASE 가상 IP에 대한 포트 80 트래픽을 계속 제어할 수 있습니다.
> 

## <a name="extra-configurations"></a>추가 구성

제한 없이 NSG(네트워크 보안 그룹) 및 경로 테이블(UDR)을 설정할 수 있습니다. ASE의 모든 아웃바운드 트래픽을 Azure Firewall과 같은 송신 방화벽 디바이스로 강제 터널링할 수 있으며 애플리케이션 종속성 외에는 걱정할 필요가 없습니다. Application Gateway와 같은 WAF 디바이스를 ASE에 대한 인바운드 트래픽 앞에 배치하여 해당 ASE의 특정 앱을 노출할 수 있습니다. 인터넷에 대한 다른 전용 아웃바운드 주소의 경우 ASE와 함께 NAT Gateway를 사용할 수 있습니다. ASE와 NAT Gateway를 사용하려면 ASE 서브넷에 맞게 NAT Gateway를 구성합니다. 

## <a name="dns"></a>DNS

### <a name="dns-configuration-to-your-ase"></a>ASE에 대한 DNS 구성

외부 VIP를 사용하여 ASE를 만든 경우 앱은 자동으로 공용 DNS에 배치됩니다. 내부 VIP를 사용하여 ASE를 만든 경우 DNS를 구성해야 할 수 있습니다. ASE를 만드는 동안 Azure DNS 프라이빗 영역을 자동으로 구성하도록 선택한 경우 DNS는 ASE VNet에서 구성됩니다. DNS 수동 구성을 선택한 경우에는 자체 DNS 서버를 사용하거나 Azure DNS 프라이빗 영역을 구성해야 합니다. ASE의 인바운드 주소를 찾으려면 **ASE 포털 > IP 주소** UI로 이동합니다. 

자체 DNS 서버를 사용하려면 다음 레코드를 추가해야 합니다.

1. &lt;ASE 이름&gt;.appserviceenvironment.net 영역 만들기
1. 해당 영역에 *로 ASE에서 사용하는 인바운드 IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 @로 ASE에서 사용하는 인바운드 IP 주소를 가리키는 A 레코드 만들기
1. &lt;ASE 이름&gt;.appserviceenvironment.net에 scm이라는 영역 만들기
1. scm 영역에 *로 ASE 프라이빗 엔드포인트에서 사용하는 인바운드 IP 주소를 가리키는 A 레코드 만들기

Azure DNS 프라이빗 영역에서 DNS를 구성하려면 다음을 수행합니다.

1. `<ASE-name>.appserviceenvironment.net`이라는 Azure DNS 프라이빗 영역 만들기
1. 해당 영역에 *로 인바운드 IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 @로 인바운드 IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 *.scm으로 인바운드 IP 주소를 가리키는 A 레코드 만들기

ASE 기본 도메인 접미사에 대한 DNS 설정은 해당 이름으로만 액세스할 수 있도록 앱을 제한하지 않습니다. ASE의 앱에 대한 유효성 검사 없이 사용자 지정 도메인 이름을 설정할 수 있습니다. 그런 다음 *contoso.net* 이라는 영역을 만들려는 경우 이를 수행하고 인바운드 IP 주소를 가리킬 수 있습니다. 사용자 지정 도메인 이름은 앱 요청에 대해서는 작동하지만 scm 사이트의 경우에는 작동하지 않습니다. scm 사이트는 *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net* 에서만 사용할 수 있습니다. 

#### <a name="dns-configuration-from-your-ase"></a>ASE의 DNS 구성

ASE의 앱은 VNet이 구성된 DNS를 사용합니다. 일부 앱에서 VNet이 구성된 것과 다른 DNS 서버를 사용하도록 하려면 WEBSITE_DNS_SERVER 및 WEBSITE_DNS_ALT_SERVER 앱 설정을 사용하여 앱별로 수동 설정할 수 있습니다. WEBSITE_DNS_ALT_SERVER 앱 설정은 보조 DNS 서버를 구성합니다. 보조 DNS 서버는 주 DNS 서버에서 응답이 없는 경우에만 사용됩니다. 

## <a name="limitations"></a>제한 사항

ASE는 고객 VNet에 배포되지만 ASE에서 사용할 수 없는 몇 가지 네트워킹 기능이 있습니다. 

* SMTP 트래픽을 보냅니다. 여전히 메일로 트리거된 경고가 있을 수 있지만 앱이 포트 25에서 아웃바운드 트래픽을 보낼 수 없습니다.
* Network Watcher 또는 NSG Flow를 사용하여 아웃바운드 트래픽 모니터링

## <a name="more-resources"></a>추가 리소스

- [환경 변수 및 앱 설정 참조](../reference-app-settings.md)