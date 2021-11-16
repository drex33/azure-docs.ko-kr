---
title: App Service 환경 사용
description: App Service Environment를 사용하여 격리된 애플리케이션을 호스트하는 방법을 알아봅니다.
author: madsd
ms.topic: article
ms.date: 07/06/2021
ms.author: madsd
ms.openlocfilehash: 6a01e1c746579ea7d51a0b30fd554d2e2e6bb66b
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519645"
---
# <a name="using-an-app-service-environment"></a>App Service Environment 사용

> [!NOTE]
> 이 문서에서는 격리된 v2 App Service 요금제와 함께 사용되는 App Service Environment v3에 대해 설명합니다.
> 

ASE(App Service Environment)는 선택한 VNet(Azure Virtual Network)에 직접 주입되는 Azure App Service의 단일 테넌트 배포입니다. 단일 고객에게만 사용되는 시스템입니다. ASE에 배포된 앱은 ASE 서브넷에 적용되는 네트워킹 기능을 적용합니다. 앱에서 관련 네트워킹 기능을 적용하기 위해 사용 설정을 해야 하는 추가 기능은 없습니다. 

## <a name="create-an-app-in-an-ase"></a>ASE에서 앱 만들기

ASE에서 앱을 만들려는 경우 일반적으로 앱을 만들 때 사용하는 것과 같은 프로세스를 사용하지만, 몇 가지 세부적인 차이점이 있습니다. 새 App Service 계획을 만들 때는 다음을 수행합니다.

- 앱을 배포하기 위한 지리적 위치를 선택하는 대신 ASE를 위치로 선택합니다.
- ASE에서 만든 모든 App Service 플랜은 Isolated v2 가격 책정 계층에 있어야 합니다.

ASE가 없는 경우 [App Service Environment 만들기][MakeASE]의 지침에 따라 만들 수 있습니다.
ASE에서 앱을 만들려면

1. **리소스 만들기** > **웹 + 모바일** > **웹앱** 을 선택합니다.
1. 구독을 선택합니다.
1. 새 리소스 그룹의 이름을 입력하거나 **기존 항목 사용** 을 선택하고 드롭다운 목록에서 이름을 선택합니다.
1. 앱의 이름을 입력합니다. ASE에서 App Service 계획을 이미 선택한 경우 앱의 도메인 이름에 ASE의 도메인 이름이 반영됩니다.
1. 게시 유형, 스택 및 운영 체제를 선택합니다.
1. 지역을 선택합니다. 여기에서 기존 App Service Environment v3을 선택해야 합니다. 앱을 만드는 동안 ASEv3을 만들 수 없습니다. ![ ASE에서 앱 만들기][1]
1. ASE에서 기존 App Service 플랜을 선택하거나 새로 만듭니다. 새 앱을 만드는 경우 App Service 플랜에 대해 원하는 크기를 선택합니다. Isolated v2 가격 책정 SKU는 앱에 대해 선택할 수 있는 유일한 SKU입니다. App Service 요금제를 새로 만드는 작업은 일반적으로 20분 미만이 소요됩니다.
![Isolated v2 가격 책정 계층][2]
1. **다음: 모니터링** 을 선택합니다. 앱에서 App Insights를 사용하도록 설정하려는 경우 여기에서 만들기 흐름 중에 해당 설정을 수행할 수 있습니다.
1.  **다음: 태그** 를 선택합니다. 원하는 태그를 앱에 추가합니다.
1. **검토 + 만들기** 를 선택하고 정보가 올바른지 확인한 다음 **만들기** 를 선택합니다.

Windows 및 Linux 앱은 동일한 ASE에 있을 수 있지만 동일한 App Service 요금제에 있을 수는 없습니다.

## <a name="how-scale-works"></a>확장이 작동하는 방식

모든 App Service 앱은 App Service 계획에서 실행됩니다. App Service 환경은 App Service 계획을 포함하고 App Service 계획은 앱을 포함합니다. 앱을 스케일링할 경우 App Service 플랜과 같은 플랜 내에 있는 모든 앱 또한 스케일링합니다.

App Service 플랜을 스케일링하면 필요한 인프라가 자동으로 추가됩니다. 인프라가 추가되는 동안 스케일링 작업의 시간이 지연됩니다. App Service 계획의 크기를 조정하고 동일한 OS 및 크기의 또 다른 크기 조정 작업을 실행하는 경우 요청된 크기 조정이 시작될 때까지 몇 분 정도 지연될 수 있습니다. 한 크기 및 OS에 대한 크기 조정 작업은 크기와 OS의 다른 조합의 크기 조정에 영향을 미치지 않습니다. 예를 들어 Windows I2v2 App Service 계획의 크기를 조정하는 경우 Windows I2v2의 크기를 조정하는 다른 요청은 약간 지연될 수 있지만 Windows I3v2 App Service 계획에 대한 크기 조정 작업은 즉시 시작됩니다. 스케일링은 일반적으로 20분 미만이 소요됩니다.

다중테넌트 App Service *공유* 리소스 풀을 쉽게 지원할 수 있으므로 크기 조정이 즉시 가능합니다. ASE는 단일 테넌트 서비스이므로 공유 버퍼가 없으며 필요에 따라 리소스가 할당됩니다.

## <a name="app-access"></a>앱 액세스

내부 VIP를 사용하는 ASE에서 앱을 만드는 데 사용되는 도메인 접미사는 *.&lt;asename&gt;.appserviceenvironment.net* 입니다. ASE 이름이 _my-ase_ 이고 해당 ASE에서 _contoso_ 라는 앱을 호스트하는 경우 다음과 같은 URL에서 앱에 액세스하게 됩니다.

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

내부 VIP를 사용하는 ASE에서 호스트되는 앱은 ASE와 동일한 가상 네트워크에 있거나 가상 네트워크에 연결되어 있는 경우에만 액세스할 수 있습니다. 또한 게시는 동일한 가상 네트워크에 있거나 가상 네트워크에 연결되어 있는 경우에만 가능하도록 제한됩니다. 

외부 VIP를 사용하는 ASE에서 앱을 만드는 데 사용되는 도메인 접미사는 *.&lt;asename&gt;.p.azurewebsites.net* 입니다. ASE 이름이 _my-ase_ 이고 해당 ASE에서 _contoso_ 라는 앱을 호스트하는 경우 다음과 같은 URL에서 앱에 액세스하게 됩니다.

- contoso.my-ase.p.azurewebsites.net
- contoso.scm.my-ase.p.azurewebsites.net

ASE를 만드는 방법에 대한 자세한 내용은 [App Service Environment 만들기][MakeASE]를 참조하세요.

SCM URL은 Kudu 콘솔에 액세스하거나 웹 배포를 사용하여 앱을 게시하는 데 사용됩니다. Kudu 콘솔에 대한 자세한 내용은 [Azure App Service용 Kudu 콘솔][Kudu]을 참조하세요. Kudu 콘솔은 디버깅, 파일 업로드, 파일 편집 등을 위한 웹 UI를 제공합니다.

### <a name="dns-configuration"></a>DNS 구성 

외부 VIP를 사용하여 ASE를 만든 경우 앱은 자동으로 공용 DNS에 배치됩니다. 내부 VIP를 사용하여 ASE를 만든 경우 DNS를 구성해야 할 수 있습니다. ASE를 만드는 동안 Azure DNS 프라이빗 영역을 자동으로 구성하도록 선택한 경우 DNS는 ASE VNet에서 구성됩니다. DNS 수동 구성을 선택한 경우에는 자체 DNS 서버를 사용하거나 Azure DNS 프라이빗 영역을 구성해야 합니다. ASE의 인바운드 주소를 찾으려면 **ASE 포털 > IP 주소** UI로 이동합니다. 

![IP 주소 UI][6]

자체 DNS 서버를 사용하려면 다음 레코드를 추가해야 합니다.

1. &lt;ASE 이름&gt;.appserviceenvironment.net 영역 만들기
1. 해당 영역에 *로 ASE에서 사용하는 인바운드 IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 @로 ASE에서 사용하는 인바운드 IP 주소를 가리키는 A 레코드 만들기
1. &lt;ASE 이름&gt;.appserviceenvironment.net에 scm이라는 영역 만들기
1. scm 영역에 *로 ASE에서 사용하는 인바운드 주소를 가리키는 A 레코드 만들기

Azure DNS 프라이빗 영역에서 DNS를 구성하려면 다음을 수행합니다.

1. &lt;ASE 이름&gt;.appserviceenvironment.net이라는 Azure DNS 프라이빗 영역 만들기
1. 해당 영역에 *로 인바운드 IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 @로 인바운드 IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 *.scm으로 인바운드 IP 주소를 가리키는 A 레코드 만들기

ASE 기본 도메인 접미사에 대한 DNS 설정은 해당 이름으로만 액세스할 수 있도록 앱을 제한하지 않습니다. ASE의 앱에 대한 유효성 검사 없이 사용자 지정 도메인 이름을 설정할 수 있습니다. 그런 다음 *contoso.net* 이라는 영역을 만들려는 경우 이를 수행하고 인바운드 IP 주소를 가리킬 수 있습니다. 사용자 지정 도메인 이름은 앱 요청에 대해서는 작동하지만 scm 사이트의 경우에는 작동하지 않습니다. scm 사이트는 *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net* 에서만 사용할 수 있습니다. 

## <a name="publishing"></a>게시

다중 테넌트 App Service와 마찬가지로 ASE에서도 다음 메서드를 사용하여 게시할 수 있습니다.

- 웹 배포
- CI(연속 통합)
- Kudu 콘솔에서 끌어서 놓기
- IDE(예: Visual Studio, Eclipse, IntelliJ IDEA)

내부 VIP ASE를 사용하는 경우 게시 엔드포인트는 인바운드 주소를 통해서만 사용할 수 있습니다. 인바운드 주소에 대한 네트워크 액세스 권한이 없는 경우 해당 ASE에 앱을 게시할 수 없습니다.  또한 IDE에 직접 게시하려면 IDE에 ASE의 인바운드 주소에 대한 네트워크 액세스 권한이 있어야 합니다.

GitHub, Azure DevOps와 같은 인터넷 기반 CI 시스템은 추가 변경 없이는 게시 엔드포인트의 인터넷 액세스가 불가능하기 때문에 내부 VIP ASE와 연동되지 않습니다. ASE가 포함된 가상 네트워크에 자체적으로 호스트된 릴리스 에이전트를 설치하여 Azure DevOps에서 내부 VIP ASE에 게시를 사용하도록 설정할 수 있습니다. 

## <a name="storage"></a>스토리지

ASE에는 ASE의 모든 앱을 위한 1TB 용량의 스토리지가 있습니다. 격리 가격 책정 SKU의 App Service 플랜의 경우 스토리지 용량이 250GB로 제한됩니다. ASE에서 250GB의 스토리지는 App Service 플랜당 1TB 제한까지 추가됩니다. App Service 플랜은 4개 이상 사용할 수 있지만 1TB 제한을 초과하는 스토리지는 더 이상 추가되지 않습니다.

## <a name="logging"></a>로깅

ASE를 Azure Monitor와 통합하여 ASE에 대한 로그를 Azure Storage, Azure Event Hubs 또는 Log Analytics에 보낼 수 있습니다. 다음 항목은 오늘 로그됩니다.

|상황 |메시지 |
|----------|--------|
|ASE 서브넷의 공간이 거의 다 참 | 지정된 ASE가 있는 서브넷의 공간이 거의 다 찼습니다. 남은 주소가 {0} 있습니다. 해당 주소가 모두 사용되면 ASE를 스케일링할 수 없습니다.  |
|ASE가 총 인스턴스 제한에 도달함 | 지정된 ASE가 ASE의 총 인스턴스 제한에 도달했습니다. 현재 최대 200개의 인스턴스 중 {0}개의 App Service 요금제 인스턴스를 포함합니다. |
|ASE가 일시 중단됨 | 지정한 ASE가 일시 중단되었습니다. ASE 일시 중단 현상은 계정 부족 또는 잘못된 가상 네트워크 구성으로 인해 발생할 수 있습니다. 근본 원인을 해결하고 ASE를 다시 시작하여 트래픽을 계속 처리합니다. |
|ASE 업그레이드가 시작됨 | 지정된 ASE에 대한 플랫폼 업그레이드가 시작되었습니다. 스케일링 작업에서 지연이 발생합니다. |
|ASE 업그레이드가 완료됨 | 지정된 ASE에 대한 플랫폼 업그레이드가 완료되었습니다. |
|App Service 요금제 만들기가 시작됨 | App Service 요금제({0}) 만들기가 시작되었습니다. 원하는 상태: {1} I{2}v2 작업자.
|스케일링 작업이 완료됨 | App Service 요금제({0}) 만들기가 완료되었습니다. 현재 상태: {1} I{2}v2 작업자. |
|스케일링 작업 실패 | App Service 요금제({0}) 만들기에 실패했습니다. 이는 ASE가 최대 인스턴스 수에서 작동하거나 서브넷 주소가 부족하기 때문일 수 있습니다. |
|스케일링 작업이 시작됨 | App Service 플랜({0})의 스케일링이 시작되었습니다. 현재 상태: {1} I(2)v2. 원하는 상태: {3} I{4}v2 작업자.|
|스케일링 작업이 완료됨 | App Service 플랜({0})의 스케일링이 완료되었습니다. 현재 상태: {1} I{2}v2 작업자. |
|스케일링 작업이 중단됨 | App Service 요금제({0})이 스케일링 중에 중단되었습니다. 이전의 원하는 상태: {1} I{2}v2 작업자. 새로운 원하는 상태: {3} I{4}v2 작업자. |
|스케일링 작업 실패 | App Service 플랜({0})의 스케일링에 실패했습니다. 현재 상태: {1} I{2}v2 작업자. |

ASE에서 로깅을 사용하도록 설정하려면 다음을 수행합니다.

1. 포털에서 **진단 설정** 으로 이동합니다.
1. **진단 설정 추가** 를 선택합니다.
1. 로그 통합에 대한 이름을 제공합니다.
1. 원하는 로그 대상을 선택하고 구성합니다.
1. **AppServiceEnvironmentPlatformLogs** 를 선택합니다.
![ASE 진단 로그 설정][4]

Log Analytics와 통합하는 경우 ASE 포털에서 **로그** 를 선택하고 **AppServiceEnvironmentPlatformLogs** 에 대한 쿼리를 만들어 로그를 볼 수 있습니다. 로그는 ASE를 트리거하는 이벤트가 있는 경우에만 내보내집니다. ASE에 관련 이벤트가 없는 경우 로그가 없는 것입니다. Log Analytics 작업 영역에서 로그의 예제를 신속하게 보려면 ASE의 App Service 요금제를 사용하여 스케일링 작업을 수행합니다. 그런 다음 **AppServiceEnvironmentPlatformLogs** 에 대해 쿼리를 실행하여 해당 로그를 볼 수 있습니다. 

### <a name="creating-an-alert"></a>경고 만들기

로그에 대한 경고를 만들려면 [Azure Monitor를 사용한 로그 경고 만들기, 보기 및 관리](../../azure-monitor/alerts/alerts-log.md)의 지침을 따르세요. 개요:

* ASE 포털에서 경고 페이지 열기
* **새 경고 규칙** 선택
* Log Analytics 작업 영역으로 사용할 리소스 선택
* “AppServiceEnvironmentPlatformLogs | ResultDescription이 포함되는 위치” 등 원하는 쿼리를 사용하도록 사용자 지정 로그 검색을 사용하여 조건을 설정합니다. 임계값을 적정값으로 설정합니다. 
* 원하는 대로 작업 그룹을 추가하거나 만듭니다. 작업 그룹은 메일 또는 SMS 메시지 전송 등의 경고에 대한 응답을 정의하는 위치입니다.
* 경고 이름을 지정하고 저장합니다.

## <a name="internal-encryption"></a>내부 암호화

App Service Environment는 시스템 내에서 내부 구성 요소나 통신을 볼 수 없는 블랙 박스 시스템으로 작동합니다. 높은 처리량을 활성화하기 위해 내부 구성 요소 사이에는 기본적으로 암호화가 사용되지 않습니다. 시스템은 트래픽을 모니터링하거나 액세스하는 데 액세스할 수 없기 때문에 보안이 유지됩니다. 종단간 암호화 방식에서 데이터 경로를 완벽하게 암호화해야 하지만 규정 준수 요구 사항이 있는 경우 ASE **구성** UI를 사용하도록 설정할 수 있습니다.

![내부 암호화 사용][5]

이렇게 하면 프런트 엔드 및 작업자 간에 ASE에서 내부 네트워크 트래픽을 암호화하고, 페이지 파일을 암호화하고, 작업자 디스크도 암호화합니다. InternalEncryption clusterSetting을 활성화하면 시스템 성능에 영향을 줄 수 있습니다. InternalEncryption을 활성화하도록 변경하는 경우 변경이 완전히 전파될 때까지 ASE는 불안정한 상태가 됩니다. ASE에 있는 인스턴스의 수에 따라 변경 내용의 전체 전파를 완료하는 데 몇 시간이 걸릴 수 있습니다. ASE를 사용하는 동안에는 이를 사용하도록 설정하지 않는 것이 좋습니다. 현재 사용 중인 ASE에서 이를 사용하도록 설정해야 하는 경우 작업이 완료될 때까지 백업 환경으로 트래픽을 전환하는 것이 좋습니다.

## <a name="upgrade-preference"></a>업그레이드 기본 설정

ASE가 여러 개 있는 경우 일부 ASE를 다른 ASE보다 먼저 업그레이드해야 할 수 있습니다. 이 동작은 ASE 포털을 통해 사용하도록 설정할 수 있습니다.  **구성** 아래 **업그레이드 기본 설정** 을 지정하는 옵션이 있습니다. 가능한 세 가지 값은 다음과 같습니다.

- **없음**: Azure는 특정 일괄 처리 없이 ASE를 업그레이드합니다. 이 값은 기본값입니다.
- **초기**: ASE는 App Service 업그레이드의 초반부에서 업그레이드됩니다.
- **후기**: ASE는 App Service 업그레이드의 후반부에서 업그레이드됩니다.

원하는 값을 선택하고 **저장** 을 선택합니다.  모든 ASE의 기본값은 **없음** 입니다.

![ASE 구성 포털][5]

**upgradePreferences** 기능을 사용하면 “초기” ASE가 “후기” ASE 이전에 업그레이드되기 때문에 여러 개의 ASE가 있는 경우에 가장 적합합니다. 여러 ASE가 있는 경우 개발 및 테스트 ASE를 “초기”로 설정하고 프로덕션 ASE는 “후기”로 설정해야 합니다.

## <a name="delete-an-ase"></a>ASE 삭제

ASE를 삭제하려면 다음을 수행합니다.

1. **App Service Environment** 창 상단의 **삭제** 를 선택합니다.
1. ASE의 이름을 입력하여 ASE 삭제를 확인합니다. ASE를 삭제하면 해당 ASE 내의 모든 콘텐츠도 함께 삭제됩니다.
![ASE 삭제][3]
1. **확인** 을 선택합니다.

<!--Image references-->

[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png
[6]: ./media/using/using-ip-addresses.png

<!--Links-->

[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: ./integrate-with-application-gateway.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md