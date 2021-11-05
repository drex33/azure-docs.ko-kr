---
title: F5 배포 가이드를 통한 안전한 하이브리드 액세스
titleSuffix: Azure AD
description: 안전한 하이브리드 액세스를 위해 Azure IaaS에서 F5 BIG-IP VE(Virtual Edition) VM을 배포하는 방법에 대한 자습서
services: active-directory
author: davidmu1
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.reviewer: miccohen
ms.openlocfilehash: 3e00bce1090a623efbab0c84722e4dbc46200f5f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039813"
---
# <a name="deploy-an-f5-big-ip-virtual-edition-vm-to-azure-active-directory"></a>Azure Active Directory에 F5 BIG-IP Virtual Edition VM 배포

이 자습서에서는 Azure IaaS에서 BIG-IP VE(Vitural Edition)를 배포하는 엔드투엔드 프로세스를 안내합니다. 이 자습서를 마치면 다음을 수행할 수 있습니다.

- SHA(Secure Hybrid Access) 개념 증명을 모델링하기 위해 완벽하게 준비된 BIG-IP VM(Virtual Machine)

- 새 BIG-IP 시스템 업데이트 및 핫픽스를 테스트하는 데 사용할 준비 인스턴스

## <a name="prerequisites"></a>사전 요구 사항

이전 F5 BIG-IP 환경 또는 지식은 필요하지 않지만 [F5 BIG-IP 용어](https://www.f5.com/services/resources/glossary)를 숙지하는 것이 좋습니다. SHA용 Azure에서 BIG-IP를 배포하려면 다음이 필요합니다.

- 유료 Azure 구독 또는 12개월 [평가판 구독](https://azure.microsoft.com/free/).

- 다음 F5 BIG-IP 라이선스 SKU 중 하나

  - F5 BIG-IP® Best 번들

  - F5 BIG-IP APM(Access Policy Manager)™ 독립 실행형 라이선스

  - 기본 BIG-IP F5 BIG-IP® LTM(Local Traffic Manager)™에 대한 F5 BIG-IP Access Policy Manager™ (APM) 추가 기능 라이선스
  
  - BIG-IP 전체 기능 90일 [평가판 라이선스](https://www.f5.com/trial/big-ip-trial.php).

- SSL(Secure Socket Layer)을 통해 웹 애플리케이션을 게시하기 위한 와일드카드 또는 SAN(주체 대체 이름) 인증서입니다. 테스트를 위한 무료 90일 인증서를 [암호화해 보겠습니다](https://letsencrypt.org/).

- BIG-IP 관리 인터페이스를 보호하기 위한 SSL 인증서입니다. 웹앱을 게시하는 데 사용되는 인증서는 주체가 BIG-IP의 FQDN(정규화된 도메인 이름)에 해당하는 경우 사용할 수 있습니다. 예를 들어, 제목이 `*.contoso.com`인 와일드카드 인증서는 `https://big-ip-vm.contoso.com:8443`에 적합합니다.

VM 배포 및 기본 시스템 구성에는 약 30분이 소요되며 이 시점에서 BIG-IP 플랫폼은 [F5 BIG-IP와 Azure Active Directory 통합](f5-aad-integration.md)에 나열된 SHA 시나리오를 구현할 준비가 됩니다.

시나리오를 테스트하기 위해 이 자습서에서는 BIG-IP가 AD(Active Directory) 환경을 포함하는 Azure 리소스 그룹에 배포된다고 가정합니다. 환경은 DC(도메인 컨트롤러) 및 웹 호스트(IIS) VM으로 구성되어야 합니다. 이러한 서버를 BIG-IP VM의 다른 위치에 두는 것도 정상입니다. 지정된 시나리오를 지원하는 데 필요한 각 역할에 BIG-IP를 제공한다면 가능합니다. VPN 연결을 통해 BIG-IP VM을 다른 환경에 연결하는 시나리오도 지원됩니다.

테스트를 위해 여기에 언급된 항목이 없는 경우 이 [스크립트](https://github.com/Rainier-MSFT/Cloud_Identity_Lab)를 사용하여 전체 AD 도메인 환경을 Azure에 배포할 수 있습니다. 이 [스크립팅 자동화](https://github.com/jeevanbisht/DemoSuite)를 사용하여 샘플 테스트 애플리케이션의 컬렉션을 프로그래밍 방식으로 IIS 웹 호스트에 배포할 수도 있습니다.

>[!NOTE]
>[Azure Portal](https://portal.azure.com/#home)은 지속적으로 진화하고 있으므로 이 자습서의 일부 단계는 Azure Portal에서 관찰된 실제 레이아웃과 다를 수 있습니다.

## <a name="azure-deployment"></a>Azure 배포

BIG-IP를 다양한 토폴로지에 배포할 수 있습니다. 이 가이드에서는 단일 NIC(네트워크 인터페이스) 배포에 대해 집중적으로 설명합니다. 그러나 BIG-IP 배포에 고가용성, 네트워크 분리 또는 1GB 이상의 처리량을 위해 여러 네트워크 인터페이스가 필요한 경우 F5의 사전 컴파일된 [ARM(Azure Resource Manager) 템플릿](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)을 사용하는 것이 좋습니다.

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps)에서 BIG-IP VE를 배포하려면 다음 작업을 완료합니다.

1. VM을 만들 수 있는 권한이 있는 계정으로 [Azure Portal](https://portal.azure.com/#home)에 로그인합니다. 예: contributor

2. 위쪽 리본 검색 상자에 **마켓플레이스** 를 입력한 다음 **Enter** 키를 누릅니다.

3. 마켓플레이스 필터에 **F5** 를 입력하고 **Enter** 키를 누릅니다.

4. 위쪽 리본에서 **+ 추가** 를 선택하고 마켓플레이스 필터에 **F5** 를 입력한 다음 **Enter** 키를 누릅니다.

5. **F5 BIG-IP 가상 버전(BYOL)**  > **소프트웨어 계획** > **F5 BIG-IP VE - 모든(BYOL, 2개의 부팅 위치)** 을 선택합니다.

6. **만들기** 를 선택합니다.

![소프트웨어 요금제를 선택하는 단계가 표시되는 이미지](./media/f5ve-deployment-plan/software-plan.png)

7. **기본** 메뉴를 단계별로 실행하고 다음 설정을 사용합니다.

 |  프로젝트 세부 정보     |  값     |
 |:-------|:--------|
 |Subscription|BIG-IP VM 배포에 대한 대상 구독|
 |Resource group | 기존 Azure 리소스 그룹은 BIG-IP VM을 배포하거나 새로 만듭니다. DC 및 IIS VM의 동일한 리소스 그룹이어야 합니다.|
 | **인스턴스 세부 정보**|  |
 |VM 이름| 예제 BIG-IP-VM |
 |지역 | BIG-IP-VM에 대한 Azure 지역 대상 지정 |
 |가용성 옵션| 프로덕션에서 VM을 사용하는 경우에만 사용하도록 설정|
 |이미지| F5 BIG-IP VE - 모두(BYOL, 부팅 위치 2개)|
 |Azure Spot 인스턴스| 아니요. 그렇지만 필요한 경우 자유롭게 사용하도록 설정할 수 있습니다. |
 |크기| 최소 사양은 2개의 vCPU 및 8Gb 메모리여야 합니다.|
 |**관리자 계정**|  |
 |인증 유형|지금은 암호를 선택합니다. 나중에 키 쌍으로 전환할 수 있습니다. |
 |사용자 이름|해당 관리 인터페이스에 액세스하기 위한 BIG-IP 로컬 계정으로 만들 ID입니다. 사용자 이름은 대/소문자를 구분합니다.|
 |암호|강력한 암호를 통해 관리자 액세스 보호|
 |**인바운드 포트 규칙**|  |
 |공용 인바운드 포트|없음|

8. 모든 기본값을 그대로 두고 **다음: Disk** 를 선택하고 **다음: 네트워킹** 을 선택합니다.

9. **네트워킹** 메뉴에서 이러한 설정을 완료합니다.

 |Linux|      값 |
 |:--------------|:----------------|
 |가상 네트워크|DC 및 IIS VM에서 사용하는 것과 동일한 Azure VNet 또는 새로 만듭니다.|
 |서브넷| DC 및 IIS VM에서 사용하는 것과 동일한 내부 서브넷 또는 새로 만듭니다.|
 |공용 IP |  없음|
 |NIC 네트워크 보안 그룹| 이전 단계에서 선택한 Azure 서브넷이 NSG(네트워크 보안 그룹)와 이미 연결되어 있으면 없음을 선택합니다. 그렇지 않으면 기본을 선택합니다.|
 |네트워킹 가속화| 끄기 |
 |**부하 분산**.|     |
 |VM 부하 분산| 아니요|

10. **다음: 관리** 를 선택하고 이러한 설정을 완료합니다.

 |모니터링|    값 |
 |:---------|:-----|
 |상세 모니터링| 끄기|
 |부트 진단|사용자 지정 스토리지 계정을 사용하도록 설정합니다. Azure Portal의 직렬 콘솔 옵션을 통해 BIG-IP SSH(Secure Shell) 인터페이스에 연결할 수 있습니다. 사용 가능한 Azure storage 계정을 선택합니다.|
 |**ID**|  |
 |시스템 할당 관리 ID|끄기|
 |Azure Active Directory|이 옵션은 현재 BIG-IP에서 지원되지 않습니다.|
 |**자동 종료**|    |
 |자동 종료 사용| 테스트하는 경우 매일 BIG-IP-VM을 종료하도록 설정하는 것이 좋습니다.|

11. 모든 기본값을 그대로 두고 **다음: 고급** 을 선택하고 **다음: 태그** 를 선택합니다.

12. **만들기** 를 선택하여 배포를 시작하기 전에 **다음: 검토 + 만들기** 를 선택해 BIG-IP-VM 구성을 검토합니다.

13. BIG-IP VM을 완벽하게 배포하는 데 걸리는 시간은 일반적으로 5분입니다. 완료되면 **리소스로 이동** 을 선택하지 않고 Azure Portal의 왼쪽 메뉴를 확장해 **리소스 그룹** 을 선택하여 새 BIG-IP-VM으로 이동합니다. VM을 만들지 못한 경우 **뒤로** 및 **다음** 을 선택합니다.

## <a name="network-configuration"></a>네트워크 구성

BIG-IP VM이 처음 부팅될 때 해당 NIC는 연결된 Azure 서브넷의 DHCP(Dynamic Host Configuration Protocol) 서비스에서 발급한 **기본** 개인 IP를 사용하여 프로비전 됩니다. 이 IP는 다음과 통신하기 위해 BIG-IP의 TMOS(트래픽 관리 운영 체제)에서 사용됩니다.

- 다른 호스트 및 서비스와 통신

- 공용 인터넷에 대한 아웃바운드 액세스

- BIG-IP 웹 구성 및 SSH 관리 인터페이스에 대한 인바운드 액세스

이러한 관리 인터페이스 중 하나를 인터넷에 노출하면 BIG-IP 공격 노출 영역이 늘어나지만 배포 중에 BIG-IP의 기본 IP가 공용 IP로 프로비전되지 않습니다. 대신, 게시 서비스를 위해 보조 내부 IP 및 연결된 공용 IP가 프로비전됩니다.
VM 공용 IP와 개인 IP 간의 일대일 매핑을 통해 외부 트래픽이 VM에 도달할 수 있습니다. 그러나 방화벽과 동일한 방식으로 트래픽을 허용하는 Azure NSG 규칙도 필요합니다.

이 다이어그램은 Azure에서 일반 운영 및 관리를 위한 기본 IP로 구성된 BIG-IP VE의 단일 NIC 배포와 게시 서비스를 위한 별도의 가상 서버 IP를 보여 줍니다.
이 배열에서 NSG 규칙은 BIG-IP 가상 서버로 전달되기 전에 `intranet.contoso.com`으로 향하는 원격 트래픽이 게시된 서비스의 공용 IP로 라우팅되도록 허용합니다.

![이미지는 단일 NIC 배포를 보여줍니다](./media/f5ve-deployment-plan/single-nic-deployment.png). 기본적으로 Azure VM에 발급된 개인 및 공용 IP는 항상 동적이므로 VM을 다시 시작할 때마다 변경될 수 있습니다. BIG-IP 관리 IP를 고정으로 변경하여 예기치 않은 연결 문제를 방지하고 게시 서비스에 사용되는 보조 IP와 동일하게 수행합니다.

1. BIG-IP VM의 메뉴에서 **설정** > **네트워킹** 으로 이동합니다.

2. 네트워킹 보기에서 **네트워크 인터페이스** 오른쪽에 있는 링크를 선택합니다.

![네트워크 구성을 보여주는 이미지](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>VM 이름은 배포 중에 임의로 생성됩니다.

3. 왼쪽 창에서 **IP 구성** 을 선택한 다음, **ipconfig1** 행을 선택합니다.

4. **IP 할당** 옵션을 고정으로 설정하고 필요한 경우 BIG-IP VM 기본 IP 주소를 변경합니다. 그런 다음, **저장** 을 선택하고 ipconfig1 메뉴를 닫습니다.

>[!NOTE]
>이 기본 IP를 사용하여 BIG-IP-VM을 연결하고 관리합니다.

5. 위쪽 리본에서 **+ 추가** 를 선택하고 보조 개인 IP의 이름을 제공합니다(예: ipconfig2).

6. 개인 IP 주소 설정의 **할당** 옵션을 **정적** 으로 설정합니다. 다음에 더 높거나 낮은 연속 IP를 제공하면 순서대로 유지하는 데 도움이 됩니다.

7. 공용 IP 주소를 **연결** 로 설정하고 **만들기** 를 선택합니다.

8. 새 공용 IP 주소의 이름(예: BIG-IP-VM_ipconfig2_Public)을 입력합니다.

9. 메시지가 표시되면 **SKU** 를 표준으로 설정합니다.

10. 메시지가 표시되면 **계층** 을 **전체** 로 설정합니다.

11. 그리고 **할당** 옵션을 **정적** 으로 설정한 다음, **확인** 을 두 번 선택합니다.

이제 BIG-IP-VM을 다음으로 설정할 준비가 되었습니다.

- **기본 개인 IP**: 웹 구성 유틸리티 및 SSH를 통해 BIG-IP-VM 관리 전용입니다. 이는 단일 IP 시스템에서 **자체 IP** 로 게시된 백 엔드 서비스에 연결하는 데 사용됩니다. 또한 NTP, AD, LDAP 등의 외부 서비스에 연결합니다.

- **보조 개인 IP**: 게시된 서비스에 대한 인바운드 요청을 수신 대기하는 BIG-IP APM 가상 서버를 만들 때 사용됩니다.

- **공용 IP**: 보조 개인 IP와 연결되어 공용 인터넷의 클라이언트 트래픽이 게시된 서비스에 대한 BIG-IP 가상 서버에 연결할 수 있습니다.

이 예제에서는 VM 공용 및 개인 IP 간의 일대일 관계를 보여줍니다. Azure VM NIC에는 기본 IP가 하나만 있을 수 있으며, 추가로 생성된 IP는 항상 보조로 참조됩니다.

>[!NOTE]
>BIG-IP 서비스를 게시하려면 보조 IP 매핑이 필요합니다.

![모든 보조 IP를 표시하는 이미지](./media/f5ve-deployment-plan/secondary-ips.png)

BIG-IP **액세스 단계별 구성** 을 사용하여 SHA를 구현하려면 5~11 단계를 반복하여 BIG-IP APM을 통해 게시할 모든 추가 서비스에 대한 개인 및 공용 IP 쌍을 추가로 만듭니다. BIG-IP **고급 구성** 을 사용하여 서비스를 게시하는 경우에도 동일한 방법을 사용할 수 있습니다. 그러나 이 시나리오에서는 [SNI(서버 이름 표시기)](https://support.f5.com/csp/#/article/K13452) 구성을 사용하여 공용 IP 오버헤드를 방지할 수 있는 옵션이 있습니다. 이 구성에서 BIG-IP 가상 서버는 수신하는 모든 클라이언트 트래픽을 수락하고 대상으로 라우팅합니다.

## <a name="dns-configuration"></a>DNS 구성

클라이언트에서 게시된 SHA 서비스를 BIG-IP-VM의 공용 IP로 확인하도록 DNS를 구성하는 것이 중요합니다.

다음 단계는 SHA 서비스에 사용되는 공용 도메인의 DNS 영역을 Azure에서 관리한다고 가정합니다. 그러나 로케이터 레코드를 만드는 동일한 DNS 원칙은 DNS 영역을 관리하는 위치에 관계없이 계속 적용됩니다.

1. 아직 열려 있지 않은 경우 포털의 왼쪽 메뉴를 확장하고 **리소스 그룹** 옵션을 통해 BIG-IP-VM으로 이동합니다.

2. BIG-IP VM의 메뉴에서 **설정** > **네트워킹** 으로 이동합니다.

3. BIG-IP-VM 네트워킹 보기의 드롭다운 IP 구성 목록에서 첫 번째 보조 IP를 선택하고 해당 **NIC 공용 IP** 에 대한 링크를 선택합니다.

![NIC 공용 IP를 표시하는 스크린샷](./media/f5ve-deployment-plan/networking.png)

4. 왼쪽 창의 **설정** 섹션 아래에서 **구성** 을 선택하여 선택한 보조 IP에 대한 공용 IP 및 DNS 속성 메뉴를 표시합니다.

5. 별칭 레코드 **만들기** 를 선택하고 드롭다운 목록에서 **DNS 영역** 을 선택합니다. DNS 영역이 아직 없는 경우 Azure 외부에서 관리되거나 Azure AD에서 확인한 도메인 접미사에 대해 DNS 영역을 만들 수 있습니다.

6. 다음 세부 정보를 사용하여 첫 번째 DNS 별칭 레코드를 만듭니다.

 | 필드 | 값 |
 |:-------|:-----------|
 |Subscription| BIG-IP-VM과 동일한 구독|
 |DNS 영역| 게시된 웹 사이트에서 사용할 확인된 도메인 접미사에 대한 권한이 있는 DNS 영역(예: www.contoso.com) |
 |이름 | 지정한 호스트 이름은 선택한 보조 IP와 연결된 공용 IP로 확인됩니다. IP 매핑에 대한 올바른 DNS를 정의해야 합니다. 네트워킹 구성 섹션의 마지막 이미지(예: intranet.contoso.com > 13.77.148.215)를 참조하세요.|
 | TTL | 1 |
 |TTL 단위 | 시간 |

7. Azure에 대해 **만들기** 를 선택하여 해당 설정을 공용 DNS에 저장합니다.

8. **DNS 이름 레이블(선택 사항)** 을 그대로 두고 공용 IP 메뉴를 닫기 전에 **저장** 을 선택합니다.

9. 1~6단계를 반복하여 BIG-IP의 단계별 구성을 사용하여 게시하려는 모든 서비스에 대한 추가 DNS 레코드를 만듭니다.

  DNS 레코드가 있으면 [DNS 검사기](https://dnschecker.org/) 같은 온라인 도구를 사용하여 만든 레코드가 모든 전역 공용 DNS 서버에 성공적으로 전파되었는지 확인할 수 있습니다.

  [GoDaddy](https://www.godaddy.com/)와 같은 외부 공급자를 사용하여 DNS 도메인 네임스페이스를 관리하는 경우 자체 DNS 관리 기능을 사용하여 레코드를 만들어야 합니다.

>[!NOTE]
>DNS 레코드를 테스트하고 자주 전환하는 경우 PC의 로컬 호스트 파일을 사용할 수도 있습니다. Windows PC의 로컬 호스트 파일은 키보드에서 Win + R을 누르고 **실행** 상자에 *드라이버* 를 입력하여 액세스할 수 있습니다. Localhost 레코드는 다른 클라이언트가 아닌 로컬 PC에 대한 DNS 확인만 제공한다는 것에 유의해야 합니다.

## <a name="client-traffic"></a>클라이언트 트래픽

기본적으로 Azure VNet 및 연결된 서브넷은 인터넷 트래픽을 수신할 수 없는 개인 네트워크입니다. BIG-IP-VM의 NIC는 배포 중에 지정된 NSG에 연결되어야 합니다. 외부 웹 트래픽이 BIG-IP-VM에 도달하려면 공용 인터넷에서 포트 443(HTTPS) 및 80(HTTP)을 허용하도록 인바운드 NSG 규칙을 정의해야 합니다.

1. BIG-IP VM의 기본 **개요** 메뉴에서 **네트워킹** 을 선택합니다.

2. 인바운드 규칙 **추가** 를 선택하여 다음 NSG 규칙 속성을 입력합니다.

 |     필드   |   값        |
 |:------------|:------------|
 |원본| 모두|
 |원본 포트 범위| *|
 |대상 IP 주소|모든 BIG-IP-VM 보조 개인 IP를 쉼표로 구분한 목록입니다.|
 |대상 포트| 80,443|
 |프로토콜| TCP |
 |작업| 허용|
 |우선 순위|100~4096 사이의 사용 가능한 최소값|
 |이름 | 설명이 포함된 이름으로, 예를 들면 `BIG-IP-VM_Web_Services_80_443`과 같습니다.|

3. **추가** 를 선택하여 변경 내용을 커밋하고 **네트워킹** 메뉴를 닫습니다.

모든 위치의 HTTP 및 HTTPS 트래픽이 이제 모든 BIG-IP-VM 보조 인터페이스에 연결할 수 있습니다. 포트 80을 허용하면 BIG-IP APM이 사용자를 HTTP에서 HTTPS로 자동 리디렉션할 수 있습니다. 필요할 때마다 이 규칙을 편집하여 대상 IP를 추가하거나 제거할 수 있습니다.

## <a name="manage-big-ip"></a>BIG-IP 관리

BIG-IP 시스템은 웹 구성 UI를 통해 관리되며 다음 권장 메서드 중 하나를 사용하여 액세스할 수 있습니다.

- BIG-IP의 내부 네트워크 내의 컴퓨터에서

- BIG-IP-VM의 내부 네트워크에 연결된 VPN 클라이언트에서

- [Azure AD 애플리케이션 프록시](../app-proxy/application-proxy-add-on-premises-application.md)를 통해 게시

나머지 구성으로 진행하기 전에 가장 적합한 메서드를 결정해야 합니다. 필요한 경우 공용 IP를 사용하여 BIG-IP의 기본 IP를 구성해 인터넷에서 웹 구성에 직접 연결할 수 있습니다. 그런 다음, 해당 기본 IP에 대한 8443 트래픽을 허용하는 NSG 규칙을 추가합니다. 원본을 신뢰할 수 있는 자체 IP로 제한해야 합니다. 그렇지 않으면 누구나 연결할 수 있습니다.

준비가 되면 BIG-IP VM의 웹 구성에 연결하고 VM 배포 중에 지정된 자격 증명으로 로그인할 수 있습니다.

- 내부 네트워크의 VM 또는 VPN을 통해 연결하는 경우 BIG-IP의 기본 IP 및 웹 구성 포트에 직접 연결합니다. 예들 들어 `https://<BIG-IP-VM_Primary_IP:8443`입니다. 브라우저에서 연결이 안전하지 않다는 메시지가 표시되지만 BIG-IP가 구성될 때까지 프롬프트를 무시할 수 있습니다. 브라우저에서 액세스를 차단하는 경우 캐시를 지우고 다시 시도합니다.

- 애플리케이션 프록시 통해 웹 구성을 게시한 경우 정의된 URL을 사용하여 포트를 추가하지 않고 외부에서 웹 구성에 액세스합니다(예: `https://big-ip-vm.contoso.com`). 예를 들어 웹 구성 포트를 사용하여 내부 URL을 정의해야 합니다.(예: `https://big-ip-vm.contoso.com:8443`)

BIG-IP 시스템은 일반적으로 명령줄(CLI) 작업 및 루트 수준 액세스에 사용되는 기본 SSH 환경을 통해 관리될 수도 있습니다. 다음을 포함하여 CLI에 연결하기 위한 몇 가지 옵션이 있습니다.

- [Azure Bastion 서비스:](../../bastion/bastion-overview.md) 모든 위치에서 vNET 내의 모든 VM에 대한 빠르고 안전한 연결을 허용합니다.

- JIT 접근 방식을 통해 PuTTY와 같은 SSH 클라이언트를 통해 직접 연결

- 직렬 콘솔: 포털에 있는 VM 메뉴의 지원 및 문제 해결 섹션 아래쪽에 제공됩니다. 파일 전송을 지원하지 않습니다.

- 웹 구성과 마찬가지로 공용 IP를 사용하여 BIG-IP의 기본 IP를 구성하고 SSH 트래픽을 허용하는 NSG 규칙을 추가하여 인터넷에서 CLI에 직접 연결할 수도 있습니다. 이 메서드를 사용하는 경우 원본을 신뢰할 수 있는 자체 IP로 제한해야 합니다.  

## <a name="big-ip-license"></a>BIG-IP 라이선스

게시 서비스 및 SHA에 대해 구성하려면 먼저 APM 모듈을 사용하여 BIG-IP 시스템을 활성화하고 프로비전해야 합니다.

1. 웹 구성에 다시 로그인하고 **일반 속성** 페이지에서 **활성화를** 선택합니다.

2. **기본 등록 키** 필드에 F5에서 제공하는 대/소문자 구분 키를 입력합니다.

3. **활성화 메서드** 를 **자동** 으로 설정된 채로 두고 **다음** 을 선택하면, BIG-IP가 라이선스의 유효성을 검사하고 EULA(최종 사용자 사용권 계약)를 표시합니다.

4. **수락** 을 선택하고 활성화가 완료될 때까지 기다린 후 **계속** 을 선택합니다.

5. 다시 로그인하고 라이선스 요약 페이지의 아래쪽에서 **다음** 을 선택합니다. 이제 BIG-IP는 SHA에 필요한 다양한 기능을 제공하는 모듈 목록을 표시합니다. 이 메시지가 표시되지 않으면 주 탭에서 **시스템** > **리소스 프로비전** 으로 이동합니다.

6. APM(액세스 정책)의 프로비전 열을 확인합니다.

![액세스 프로비전을 보여주는 이미지](./media/f5ve-deployment-plan/access-provisioning.png)

7. **제출** 을 선택하고 경고를 수락합니다.

8. 인내심을 갖고 BIG-IP가 새로운 기능을 완전히 소개할 때까지 기다립니다. 완전히 초기화되기 전에 여러 번 순환할 수 있습니다. 

9. 준비가 되면 **계속** 을 선택하고 **정보** 탭에서 **설치 유틸리티 실행** 을 선택합니다.

>[!IMPORTANT]
>F5 라이선스는 한 번에 하나의 BIG-IP VE 인스턴스에서 사용되는 것으로 제한됩니다. 한 인스턴스에서 다른 인스턴스로 라이선스를 마이그레이션해야 하는 이유가 있을 수 있습니다. 이 경우 서비스를 해제하기 전에 활성 인스턴스에서 평가판 라이선스를 [취소](https://support.f5.com/csp/article/K41458656)해야 합니다. 그렇지 않으면 라이선스가 영구적으로 손실됩니다.

## <a name="provision-big-ip"></a>BIG-IP 프로비전

BIG-IP 웹 구성에 대한 관리 트래픽을 보호하는 것이 가장 중요합니다. 웹 구성 채널이 손상되지 않도록 보호할 수 있도록 디바이스 관리 인증서를 구성합니다.

1. 왼쪽 탐색 모음에서 **시스템** > **인증서 관리** > **트래픽 인증서 관리** > **SSL 인증서 목록** > **가져오기** 로 이동합니다.

2. **가져오기 형식** 드롭다운 목록에서 **PKCS 12 (IIS)** 및 **파일 선택** 을 선택합니다. FQDN을 포함하는 주체 이름 또는 SAN이 있는 SSL 웹 인증서를 찾습니다. 다음 몇 단계에서 BIG-IP-VM을 할당합니다.

3. 인증서의 암호를 입력한 다음 **가져오기** 를 선택합니다.

4. 왼쪽 탐색 모음에서 **시스템** > **플랫폼** 으로 이동합니다.

5. 정규화된 호스트 이름 및 해당 환경의 시간대를 사용하여 BIG-IP-VM을 구성한 다음 **업데이트** 합니다.

![일반 속성을 보여주는 이미지](./media/f5ve-deployment-plan/general-properties.png)

6. 왼쪽 탐색 모음에서 **시스템** > **구성** > **디바이스** > **NTP** 로 이동합니다.

7. 신뢰할 수 있는 NTP 원본을 지정하고 **추가** 를 선택한 다음, **업데이트** 를 선택합니다. 예를 들어 `time.windows.com`

이제 이전 단계에서 지정한 BIG-IP FQDN을 기본 개인 IP로 확인하려면 DNS 레코드가 필요합니다. 환경의 내부 DNS 또는 BIG-IP의 웹 구성에 연결하는 데 사용할 PC의 localhost 파일에 레코드를 추가해야 합니다. 어느 방법을 사용하든 웹 구성에 직접 연결할 때 브라우저 경고가 더 이상 표시되지 않습니다. 즉, 애플리케이션 프록시 또는 다른 역방향 프록시를 통하지 않습니다.

## <a name="ssl-profile"></a>SSL 프로필

역방향 프록시로서 BIG-IP 시스템은 투명 프록시라고도 하는 단순 전달 서비스 또는 클라이언트와 서버 간의 교환에 적극적으로 참여하는 전체 프록시로 작동할 수 있습니다.
전체 프록시는 두 개의 고유한 연결을 만듭니다. 프런트 엔드 TCP 클라이언트 연결과 별도의 백 엔드 TCP 서버 연결이 중간에 소프트 간격으로 결합됩니다. 클라이언트는 **가상 서버** 라고도 하는 프록시 수신기 한쪽 끝에 연결하고 프록시는 백 엔드 서버에 대한 별도의 독립적인 연결을 설정합니다. 양쪽에서 양방향입니다.
이 전체 프록시 모드에서 F5 BIG-IP 시스템은 트래픽을 검사할 수 있으므로 요청 및 응답과 상호 작용할 수도 있습니다. 부하 분산 및 웹 성능 최적화와 같은 특정 기능뿐만 아니라 애플리케이션 계층 보안, 웹 가속, 페이지 라우팅 및 보안 원격 액세스와 같은 고급 트래픽 관리 서비스는 이 기능을 기반으로 합니다.
SSL 기반 서비스를 게시할 때 클라이언트와 백 엔드 서비스 간의 트래픽을 암호 해독하고 암호화하는 프로세스는 BIG IP SSL 프로필에서 처리됩니다.

다음과 같은 두 가지 형식의 프로필이 있습니다.

- **클라이언트 SSL**: SSL을 사용하여 내부 서비스를 게시하기 위해 BIG-IP 시스템을 설정하는 가장 일반적인 방법은 클라이언트 SSL 프로필을 만드는 것입니다. 클라이언트 SSL 프로필을 사용하면 BIG-IP 시스템에서 인바운드 클라이언트 요청을 다운스트림 서비스로 보내기 전에 암호를 해독할 수 있습니다. 그런 다음 클라이언트에 전송하기 전에 아웃바운드 백 엔드 응답을 암호화합니다.

- **서버 SSL**: HTTPS용으로 구성된 백 엔드 서비스의 경우 서버 SSL 프로필을 사용하도록 BIG-IP를 구성할 수 있습니다. 서버 SSL 프로필을 사용하는 경우, 이를 대상 백 엔드 서비스로 보내기 전에 BIG-IP가 클라이언트 요청을 다시 암호화합니다. 서버에서 암호화된 응답을 반환하는 경우에는 구성된 클라이언트 SSL 프로필을 통해 클라이언트에 전송하기 전에 BIG-IP 시스템에서 응답을 해독하고 다시 암호화합니다.

클라이언트 및 서버 SSL 프로필을 둘 다의 프로비전하면 모든 SHA 시나리오에서 사용할 수 있는 BIG-IP가 사전 구성됩니다.

1. 왼쪽 탐색 모음에서 **시스템** > **인증서 관리** > **트래픽 인증서 관리** > **SSL 인증서 목록** > **가져오기** 로 이동합니다.

2. **가져오기 형식** 드롭다운 목록에서 **PKCS 12 (IIS)** 를 선택합니다.

3. 가져온 인증서의 이름(예: `ContosoWildcardCert`)을 제공합니다.

4. **파일 선택** 을 선택하여 게시된 서비스에 대해 사용하려는 도메인 접미사에 해당하는 주체 이름을 가진 SSL 웹 인증서를 찾습니다.

5. 가져온 인증서의 **암호** 를 입력한 다음 **가져오기** 를 선택합니다.

6. 왼쪽 탐색 모음에서 **로컬 트래픽** > **프로필** > **SSL** > **클라이언트** 로 이동한 후 **만들기** 를 선택합니다.

7. **새 클라이언트 SSL 프로필** 페이지에서 새 클라이언트 SSL 프로필에 대한 식별 이름을 입력하고 상위 프로필이 `clientssl`로 설정되었는지 확인합니다.

![BIG-IP의 업데이트를 보여주는 이미지](./media/f5ve-deployment-plan/client-ssl.png)

8. **인증서 키 체인** 행에서 오른쪽 끝 확인란을 선택하고 **추가** 를 선택합니다.

9. 세 개의 드롭다운 목록에서 암호 없이 가져온 와일드카드 인증서를 선택한 다음, **추가** > **완료** 를 선택합니다.

![BIG-IP contoso 와일드카드의 업데이트를 보여주는 이미지](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. 6~9단계를 반복하여 **SSL 서버 인증서 프로필** 을 만듭니다. 상단 리본에서 **SSL** > **서버** > **만들기** 를 선택합니다.

11. **새 서버 SSL 프로필** 페이지에서 새 서버 SSL 프로필에 대한 식별 이름을 입력하고 상위 프로필이 `serverssl`로 설정되었는지 확인합니다.

12. 인증서 및 키 행의 맨 오른쪽 확인란을 선택하고 드롭다운 목록에서 가져온 인증서를 선택한 다음 **완료** 를 선택합니다.

![업데이트 Big-IP 서버의 모든 프로필을 보여주는 이미지](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>SSL 인증서를 조달할 수 없는 경우 자체 서명된 통합 BIG-IP 서버 및 클라이언트 SSL 인증서를 사용할 수 있습니다. 브라우저에 인증서 오류가 표시됩니다.

SHA용 BIG-IP를 준비하는 마지막 단계는 게시 리소스와 SSO에 의존하는 디렉터리 서비스를 찾을 수 있는지 확인하는 것입니다. BIG-IP에는 로컬/.../hosts 파일로 시작하거나 레코드를 찾을 수 없는 경우 BIG-IP 시스템에서 구성된 DNS 서비스를 사용하는 두 가지 이름 확인 원본이 있습니다. 호스트 파일 메서드는 FQDN을 사용하는 APM 노드 및 풀에 적용되지 않습니다.

1. 웹 구성에서 **시스템** > **구성** > **디바이스** > **DNS** 로 이동합니다.

2. **DNS 조회 서버 목록** 에서 환경 DNS 서버의 IP 주소를 입력합니다.

3. **추가** > **업데이트** 를 선택합니다.

별도의 선택적 단계로, 로컬 BIG-IP 계정을 관리하는 대신 AD에 대해 BIG-IP sysadmins를 인증하는 [LDAP 구성](https://somoit.net/f5-big-ip/authentication-using-active-directory)을 고려할 수 있습니다.

## <a name="update-big-ip"></a>BIG-IP 업데이트

[시나리오 기반 지침](f5-aad-integration.md)에서 다루는 모든 새로운 기능의 잠금을 해제하도록 BIG-IP-VM을 업데이트해야 합니다. 주 페이지의 왼쪽 위에 있는 BIG-IP 호스트 이름 위로 마우스 포인터를 가져가면 시스템 TMOS 버전을 확인할 수 있습니다. [F5 다운로드](https://downloads.f5.com/esd/productlines.jsp)에서 다운로드할 수 있는 v15.x 이상을 실행하는 것이 좋습니다. [지침](https://support.f5.com/csp/article/K34745165)을 사용하여 주 시스템 OS(TMOS)를 업데이트합니다.

주 TMOS 업데이트를 수행할 수 없는 경우 다음 단계를 사용하여 최소한의 단계별 구성만 업그레이드하는 것이 좋습니다.

1. BIG-IP 웹 구성의 주 탭에서 **액세스** > **단계별 구성** 으로 이동합니다.

2. **단계별 구성** 페이지에서 **단계별 구성 업그레이드** 를 선택합니다.

![BIG-IP를 업데이트하는 방법을 보여주는 이미지](./media/f5ve-deployment-plan/update-big-ip.png)

3. **단계별 구성 업그레이드** 대화 상자에서 **파일 선택** 을 선택하여 다운로드한 사용 사례 팩을 업로드하고 **업로드 후 설치** 를 선택합니다.

4. 업그레이드가 완료되면 **계속** 을 선택합니다.

## <a name="backup-big-ip"></a>BIG-IP 백업

이제 BIG-IP 시스템이 완전히 프로비전되면 해당 구성의 전체 백업을 수행하는 것이 좋습니다.

1. **시스템** > **보관 파일** > **만들기** 로 이동

2. 고유한 **파일 이름** 을 제공하고 **암호화** 암호를 사용합니다.

3. 디바이스 및 SSL 인증서도 백업하려면 **프라이빗 키** 옵션을 **포함** 으로 설정합니다.

4. **완료** 를 선택하고 프로세스가 완료될 때까지 기다립니다.

5. 백업 결과의 상태를 제공하는 작업 상태가 표시됩니다. **확인** 을 선택합니다.

6. 백업 링크를 선택하여 UCS(사용자 구성 집합) 보관 파일을 로컬로 저장하고 **다운로드** 를 선택합니다.

선택적 단계로 [Azure 스냅샷](../../virtual-machines/windows/snapshot-copy-managed-disk.md)을 사용하여 전체 시스템 디스크를 백업할 수도 있습니다. 웹 구성 백업과는 달리 TMOS 버전 간 테스트 또는 새 시스템으로 롤백하기 위한 일부 대체 기능을 제공합니다.

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>BIG-IP 복원

BIG-IP를 복원하는 것은 백업과 유사한 절차를 따르고, BIG-IP VM 간에 구성을 마이그레이션하는 데에도 사용할 수 있습니다. 백업을 가져오기 전에 지원되는 업그레이드 경로에 대한 세부 정보를 확인해야 합니다.

1. **시스템** > **보관 파일** 로 이동

2. 복원하려는 백업의 링크를 선택하거나, **업로드** 단추를 선택하여 목록에 없는 이전에 저장된 UCS 보관 파일을 찾습니다.

3. 백업에 대한 암호를 입력하고 **복원** 을 선택합니다.

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>작성 시 AzVmSnapshot cmdlet은 날짜를 기준으로 가장 최근의 스냅샷을 복원하도록 제한됩니다. 스냅샷은 VM 리소스 그룹의 루트에 저장됩니다. 스냅샷을 복원하면 Azure VM이 다시 시작되므로 신중하게 시간을 정해야 합니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure에서 BIG-IP VE 암호 초기화](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
- [포털을 사용하지 않고 암호 초기화](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

- [BIG-IP VE 관리에 사용되는 NIC 변경](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

- [단일 NIC 구성의 경로 정보](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

- [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>다음 단계

[배포 시나리오](f5-aad-integration.md)를 선택하고 구현을 시작합니다.
