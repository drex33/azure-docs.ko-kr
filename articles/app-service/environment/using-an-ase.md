---
title: App Service Environment 사용 및 관리
description: App Service Environment에서 앱을 작성, 게시 및 스케일링하는 방법에 대해 알아봅니다. 이 문서에서는 일반적인 작업을 모두 알아봅니다.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 8/5/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: da32a2bbd4824e589a6673b043551dce67c32e70
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122568283"
---
# <a name="use-an-app-service-environment"></a>App Service 환경 사용
> [!NOTE]
> 이 문서에서는 격리된 App Service 요금제와 함께 사용되는 App Service Environment v2에 관해 설명합니다.
> 

ASE(App Service Environment)는 Azure App Service를 고객의 Azure Virtual Network 인스턴스의 서브넷에 배포한 것입니다. ASE는 다음과 같은 요소로 구성됩니다.

- **프런트 엔드**: App Service Environment에서 HTTP 또는 HTTPS가 종료되는 위치입니다.
- **작업자**: 앱을 호스트하는 리소스입니다.
- **데이터베이스**: 환경을 정의하는 정보가 담겨 있습니다.
- **스토리지**: 고객이 게시한 앱을 호스트하는 데 사용합니다.

앱 액세스를 위한 외부 또는 내부 VIP(가상 IP)로 ASE를 배포할 수 있습니다. 외부 VIP를 사용하는 배포를 일반적으로 *외부 ASE* 라고 합니다. 내부 VIP를 사용하는 배포는 ILB(내부 부하 분산 장치)를 사용하기 때문에 *ILB ASE* 라고 합니다. ILB ASE에 대해 자세히 알아보려면 [ILB ASE 만들기 및 사용][MakeILBASE]을 참조하세요.

## <a name="create-an-app-in-an-ase"></a>ASE에서 앱 만들기

ASE에서 앱을 만들려는 경우 일반적으로 앱을 만들 때 사용하는 것과 같은 프로세스를 사용하지만, 몇 가지 세부적인 차이점이 있습니다. 새 App Service 계획을 만들 때는 다음을 수행합니다.

- 앱을 배포하기 위한 지리적 위치를 선택하는 대신 ASE를 위치로 선택합니다.
- ASE에서 만든 모든 App Service 플랜은 격리 가격 책정 계층에만 있을 수 있습니다.

ASE가 없는 경우 [App Service Environment 만들기][MakeExternalASE]의 지침에 따라 만들 수 있습니다.

ASE에서 앱을 만들려면

1. **리소스 만들기** > **웹 + 모바일** > **웹앱** 을 선택합니다.

1. 앱의 이름을 입력합니다. ASE에서 이미 App Service 플랜을 선택한 경우 앱의 도메인 이름에 ASE의 도메인 이름이 반영됩니다.

    ![앱 이름 선택][1]

1. 구독을 선택합니다.

1. 새 리소스 그룹의 이름을 입력하거나 **기존 항목 사용** 을 선택하고 드롭다운 목록에서 이름을 선택합니다.

1. OS를 선택합니다.

1. ASE에서 기존 App Service 계획을 선택하거나 다음 단계를 통해 새 App Service 계획을 만듭니다.

    a. Azure Portal의 좌측 메뉴에서 **리소스 > 웹앱 만들기** 를 선택합니다.

    b. 구독을 선택합니다.

    다. 리소스 그룹을 선택하거나 만듭니다.

    d. 웹앱의 이름을 입력합니다.

    e. **코드** 또는 **DockerContainer** 를 선택합니다.

    f. 런타임 스택을 선택합니다.

    g. **Linux** 또는 **Windows** 를 선택합니다. 

    h. **지역** 드롭다운 목록에서 ASE를 선택합니다. 

    i. 새 App Service 플랜을 선택하거나 만듭니다. 새 App Service 플랜을 만드는 경우 적절한 **격리** SKU 크기를 선택합니다.

    ![격리 가격 책정 계층][2]

    > [!NOTE]
    > Linux 앱과 Windows 앱에 대하여 동일한 App Service 플랜은 없을 수 있지만, 동일한 App Service Environment는 있을 수 있습니다.
    >

1. **검토 + 만들기** 를 선택하고 정보가 올바른지 확인한 다음 **만들기** 를 선택합니다.

## <a name="how-scale-works"></a>확장이 작동하는 방식

모든 App Service 앱은 App Service 계획에서 실행됩니다. App Service 환경은 App Service 계획을 포함하고 App Service 계획은 앱을 포함합니다. 앱을 스케일링할 경우 App Service 플랜과 같은 플랜 내에 있는 모든 앱 또한 스케일링합니다.

App Service 플랜을 스케일링하면 필요한 인프라가 자동으로 추가됩니다. 인프라가 추가되는 동안 스케일링 작업의 시간이 지연됩니다. 여러 스케일링 작업을 순서대로 수행하는 경우 첫 번째 인프라 스케일링 요청이 수행되고 나머지는 큐에 대기합니다. 첫 번째 스케일링 작업이 완료되면 다른 인프라 요청이 모두 함께 수행됩니다. 또한 인프라가 추가되면 App Service 플랜은 적절한 수준으로 할당됩니다. 새 App Service 플랜을 만드는 작업은 추가 하드웨어를 요청하므로 그 자체로 스케일링 작업에 해당합니다.

다중 테넌트 App Service에서는 작업을 지원할 리소스 풀이 바로 사용 가능하도록 준비되어 있으므로 스케일링 작업을 즉시 실행할 수 있습니다. ASE에는 관련 버퍼가 없으며 리소스가 필요에 따라 할당됩니다.

ASE는 최대 100개의 인스턴스를 포함하도록 App Service 플랜을 스케일링할 수 있습니다. ASE에는 해당 ASE의 모든 App Service 플랜에 걸쳐 최대 201개의 인스턴스가 포함될 수 있습니다.

## <a name="ip-addresses"></a>IP 주소

App Service로 앱에 전용 IP 주소를 할당할 수 있습니다. 해당 기능은 [Azure App Service에 기존 사용자 지정 TLS/SSL 인증서 바인딩][ConfigureSSL]에 설명된 대로 IP 기반 TLS/SSL 바인딩을 구성한 후에 사용할 수 있습니다. ILB ASE에서 IP 기반 TLS/SSL 바인딩에 사용할 IP 주소를 더 추가할 수 없습니다.

외부 ASE를 사용하여 다중 테넌트 App Service와 동일한 방식으로 앱에 대한 IP 기반 TLS/SSL 바인딩을 구성할 수 있습니다. ASE에는 항상 하나의 여분의 주소가 있습니다(최대 IP 주소 30개). IP 주소를 사용할 때마다 다른 주소가 추가되므로 항상 주소를 즉시 사용할 수 있습니다. 다른 IP 주소를 할당하려면 시간이 지연됩니다. 해당 시간 지연으로 인해 IP 주소를 연속으로 빠르게 추가할 수 없습니다.

## <a name="front-end-scaling"></a>프런트 엔드 확장

App Service 플랜을 스케일 아웃할 때는 해당 플랜을 지원하도록 작업자가 자동으로 추가됩니다. 모든 ASE는 프런트 엔드 2개를 포함하는 상태로 작성됩니다. App Service 플랜의 인스턴스 15개마다 프런트 엔드가 하나씩 생성되는 속도로 프런트 엔드가 자동으로 스케일 아웃됩니다. 예를 들어 각각 5개의 인스턴스를 포함하는 3개의 App Service 플랜이 있는 경우 총 15개의 인스턴스와 3개의 프런트 엔드를 가집니다. 인스턴스를 총 30개로 스케일링하면 프런트 엔드를 네 개 가지게 되는 식입니다. 해당 패턴은 스케일 아웃할 때마다 계속됩니다.

기본적으로 할당되는 프런트 엔드 수는 보통의 로드에 적합합니다. 인스턴스 5개마다 프런트 엔드가 하나씩 생성되도록 비율을 더 낮게 변경할 수 있습니다. 프런트 엔드의 크기를 변경할 수도 있습니다. 기본적으로 프런트 엔드는 단일 코어입니다. Azure Portal에서 프런트 엔드의 크기를 2-코어 또는 4-코어로 변경할 수 있습니다.

비율 또는 프런트 엔드 크기를 변경하는 작업에는 비용이 청구됩니다. 자세한 내용은 [Azure App Service 가격][Pricing]을 참조하세요. ASE의 로드 용량을 개선하려는 경우 스케일링 비율을 조정하기 전에 먼저 2-코어 프런트 엔드로 스케일링하여 보다 향상된 기능을 얻을 수 있습니다. 프런트 엔드의 코어 크기를 변경하면 ASE가 업그레이드되며, 해당 변경 작업은 정기적인 업무 시간 외에 수행해야 합니다.

프런트 엔드 리소스는 ASE의 HTTP/HTTPS 엔드포인트입니다. 기본 프런트 엔드 구성에서는 프런트 엔드당 메모리 사용량이 일관되게 약 60%입니다. 프런트 엔드의 크기를 조정하는 주된 이유는 주로 HTTPS 트래픽으로 구동되는 CPU 사용량 때문입니다.

## <a name="app-access"></a>앱 액세스

외부 ASE에서 앱을 만드는 데 사용되는 도메인 접미사는 *.&lt;asename&gt;.p.azurewebsites.net* 입니다. 예를 들어 ASE 이름이 _external-ase_ 이고 해당 ASE에서 _contoso_ 라는 앱을 호스트하는 경우 다음과 같은 URL에서 앱에 액세스하게 됩니다.

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

외부 ASE를 만드는 방법에 대한 자세한 내용은 [App Service Environment 만들기][MakeExternalASE]를 참조하세요.

ILB ASE에서 앱을 만드는 데 사용되는 도메인 접미사는 *.&lt;asename&gt;.appserviceenvironment.net* 입니다. ASE의 이름이 _ilb-ase_ 이고 해당 ASE에서 _contoso_ 라는 앱을 호스트하는 경우 다음과 같은 URL에서 앱에 액세스하게 됩니다.

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

ILB ASE를 만드는 방법에 대한 자세한 내용은 [ILB ASE 만들기 및 사용하기][MakeILBASE]를 참조하세요.

SCM URL은 Kudu 콘솔에 액세스하거나 웹 배포를 사용하여 앱을 게시하는 데 사용됩니다. Kudu 콘솔에 대한 자세한 내용은 [Azure App Service용 Kudu 콘솔][Kudu]을 참조하세요. Kudu 콘솔은 디버깅, 파일 업로드, 파일 편집 등을 위한 웹 UI를 제공합니다.

### <a name="dns-configuration"></a>DNS 구성 

External ASE를 사용하는 경우 ASE에서 만든 앱이 Azure DNS로 등록됩니다. External ASE에는 앱을 공개적으로 사용할 수 있는 추가 단계가 없습니다. ILB ASE를 사용하여 자체 DNS를 관리해야 합니다. 사용자의 DNS 서버에서 또는 Azure DNS 프라이빗 영역을 사용하여 이 작업을 수행할 수 있습니다.

ILB ASE를 사용하여 자체 DNS 서버에서 DNS를 구성하려면 다음을 수행합니다.

1. &lt;ASE 이름&gt;.appserviceenvironment.net 영역 만들기
1. 해당 영역에 *로 ILB IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 @로 ILB IP 주소를 가리키는 A 레코드 만들기
1. &lt;ASE 이름&gt;.appserviceenvironment.net에 scm이라는 영역 만들기
1. scm 영역에 *로 ILB IP 주소를 가리키는 A 레코드 만들기

Azure DNS 프라이빗 영역에서 DNS를 구성하려면 다음을 수행합니다.

1. &lt;ASE 이름&gt;.appserviceenvironment.net이라는 Azure DNS 프라이빗 영역 만들기
1. 해당 영역에 *로 ILB IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 @로 ILB IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 *.scm으로 ILB IP 주소를 가리키는 A 레코드 만들기

ASE 기본 도메인 접미사에 대한 DNS 설정은 해당 이름으로만 액세스할 수 있도록 앱을 제한하지 않습니다. ILB ASE의 앱에 대한 유효성 검사 없이 사용자 지정 도메인 이름을 설정할 수 있습니다. 그런 다음 *contoso.net* 이라는 영역을 만들려는 경우 해당 작업을 수행하고 ILB IP 주소를 가리킬 수 있습니다. 사용자 지정 도메인 이름은 앱 요청에 대해서는 작동하지만 scm 사이트의 경우에는 작동하지 않습니다. scm 사이트는 *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net* 에서만 사용할 수 있습니다. 

*.&lt;asename&gt;.appserviceenvironment.net* 이라는 영역은 전역적으로 고유합니다. 2019년 5월 이전에는 고객이 ILB ASE의 도메인 접미사를 지정할 수 있었습니다. 도메인 접미사에 대해 *.contoso.com* 을 사용하려는 경우 해당 작업을 수행할 수 있었으며 scm 사이트를 포함하였습니다. 이 모델에는 기본 TLS/SSL 인증서 관리, scm 사이트에 Single Sign-On 부족 및 와일드카드 인증서를 사용하기 위한 요구 사항을 비롯한 과제가 있었습니다. ILB ASE 기본 인증서 업그레이드 프로세스도 중단되어 애플리케이션을 다시 시작했습니다. 이러한 문제를 해결하기 위해 ILB ASE 동작은 ASE 이름 및 Microsoft 소유의 접미사에 따라 도메인 접미사를 사용하도록 변경되었습니다. ILB ASE 동작에 대한 변경 사항은 2019년 5월 이후 만들어진 ILB ASE에 적용됩니다. 기존 ILB ASE는 여전히 ASE의 기본 인증서와 해당 DNS 구성을 관리해야 합니다.

## <a name="publishing"></a>게시

다중 테넌트 App Service와 마찬가지로 ASE에서도 다음 메서드를 사용하여 게시할 수 있습니다.

- 웹 배포
- FTP
- CI(연속 통합)
- Kudu 콘솔에서 끌어서 놓기
- IDE(예: Visual Studio, Eclipse, IntelliJ IDEA)

외부 ASE를 사용하는 경우 해당 게시 옵션은 모두 동일한 방식으로 작동합니다. 자세한 내용은 [Azure App Service의 배포][AppDeploy]를 참조하세요.

ILB ASE의 경우 게시 엔드포인트는 ILB를 통해서만 사용할 수 있습니다. ILB는 Virtual Network의 ASE 서브넷에 있는 프라이빗 IP에 있습니다. ILB에 대한 네트워크 액세스 권한이 없는 경우 해당 ASE에 앱을 게시할 수 없습니다. [ILB ASE 만들기 및 사용하기][MakeILBASE]에서 설명한 대로 시스템에서 앱에 대한 DNS를 구성해야 합니다. 해당 요구 사항에는 SCM 엔드포인트가 포함됩니다. 엔드포인트가 올바르게 정의되지 않은 경우에는 게시할 수 없습니다. 또한 직접 ILB에 게시하려면 IDE에 ILB에 대한 네트워크 액세스 권한이 있어야 합니다.

GitHub, Azure DevOps와 같은 인터넷 기반 CI 시스템은 추가 변경 없이는 게시 엔드포인트의 인터넷 액세스가 불가능하기 때문에 ILB ASE와 연동되지 않습니다. ILB ASE가 포함된 가상 네트워크에 자체적으로 호스트된 릴리스 에이전트를 설치하여 Azure DevOps에서 ILB ASE에 게시를 사용하도록 설정할 수 있습니다. 또는 Dropbox 등 풀 모델을 사용하는 CI 시스템을 사용할 수 있습니다.

ILB ASE의 앱에 대한 게시 엔드포인트에서는 ILB ASE가 만들어진 도메인을 사용합니다. 해당 도메인은 앱의 게시 프로필 및 앱의 포털 창(**개요** > **필수** 및 **속성**)에서 확인할 수 있습니다.

## <a name="storage"></a>스토리지

ASE에는 ASE의 모든 앱을 위한 1TB 용량의 스토리지가 있습니다. 격리 가격 책정 SKU의 App Service 플랜의 경우 스토리지 용량이 250GB로 제한됩니다. ASE에서 250GB의 스토리지는 App Service 플랜당 1TB 제한까지 추가됩니다. App Service 플랜은 4개 이상 사용할 수 있지만 1TB 제한을 초과하는 스토리지는 더 이상 추가되지 않습니다.

## <a name="logging"></a>로깅

ASE를 Azure Monitor와 통합하여 ASE에 대한 로그를 Azure Storage, Azure Event Hubs 또는 Log Analytics에 보낼 수 있습니다. 다음 항목은 오늘 로그됩니다.

| 상황 | 메시지 |
|---------|----------|
| ASE가 비정상 상태임 | 잘못된 가상 네트워크 구성으로 인해 지정된 ASE가 비정상 상태입니다. 비정상 상태가 지속되면 ASE가 일시 중단됩니다. 여기 https://docs.microsoft.com/azure/app-service/environment/network-info 에 정의된 지침을 따르세요. |
| ASE 서브넷의 공간이 거의 다 참 | 지정된 ASE가 있는 서브넷의 공간이 거의 다 찼습니다. 남은 주소가 {0} 있습니다. 해당 주소가 모두 사용되면 ASE를 스케일링할 수 없습니다.  |
| ASE가 총 인스턴스 제한에 도달함 | 지정된 ASE가 ASE의 총 인스턴스 제한에 도달했습니다. 현재 최대 201개의 인스턴스 중 {0}개의 App Service 플랜 인스턴스를 포함합니다. |
| ASE가 종속성에 도달할 수 없음 | 지정된 ASE가 {0}에 도달할 수 없습니다.  여기 https://docs.microsoft.com/azure/app-service/environment/network-info 에 정의된 지침을 따르세요. |
| ASE가 일시 중단됨 | 지정한 ASE가 일시 중단되었습니다. ASE 일시 중단 현상은 계정 부족 또는 잘못된 가상 네트워크 구성으로 인해 발생할 수 있습니다. 근본 원인을 해결하고 ASE를 다시 시작하여 트래픽을 계속 처리합니다. |
| ASE 업그레이드가 시작됨 | 지정된 ASE에 대한 플랫폼 업그레이드가 시작되었습니다. 스케일링 작업에서 지연이 발생합니다. |
| ASE 업그레이드가 완료됨 | 지정된 ASE에 대한 플랫폼 업그레이드가 완료되었습니다. |
| 스케일링 작업이 시작됨 | App Service 플랜({0})의 스케일링이 시작되었습니다. 원하는 상태: {1} I{2} 작업자.
| 스케일링 작업이 완료됨 | App Service 플랜({0})의 스케일링이 완료되었습니다. 현재 상태: {1} I{2} 작업자. |
| 스케일링 작업 실패 | App Service 플랜({0})의 스케일링에 실패했습니다. 현재 상태: {1} I{2} 작업자. |

ASE에서 로깅을 사용하도록 설정하려면 다음을 수행합니다.

1. 포털에서 **진단 설정** 으로 이동합니다.
1. **진단 설정 추가** 를 선택합니다.
1. 로그 통합에 대한 이름을 제공합니다.
1. 원하는 로그 대상을 선택하고 구성합니다.
1. **AppServiceEnvironmentPlatformLogs** 를 선택합니다.

![ASE 진단 로그 설정][4]

Log Analytics와 통합하는 경우 ASE 포털에서 **로그** 를 선택하고 **AppServiceEnvironmentPlatformLogs** 에 대한 쿼리를 만들어 로그를 볼 수 있습니다. 로그는 ASE를 트리거하는 이벤트가 있는 경우에만 내보내집니다. ASE에 관련 이벤트가 없으면 로그가 없는 것입니다. Log Analytics 작업 영역에서 로그의 예제를 신속하게 보려면 ASE의 App Service 플랜 중 하나를 사용하여 스케일링 작업을 수행합니다. 그런 다음 **AppServiceEnvironmentPlatformLogs** 에 대해 쿼리를 실행하여 해당 로그를 볼 수 있습니다. 

**경고 만들기**

로그에 대한 경고를 만들려면 [Azure Monitor를 사용한 로그 경고 만들기, 보기 및 관리](../../azure-monitor/alerts/alerts-log.md)의 지침을 따르세요. 개요:

* ASE 포털에서 경고 페이지 열기
* **새 경고 규칙** 선택
* Log Analytics 작업 영역으로 사용할 리소스 선택
* “AppServiceEnvironmentPlatformLogs | ResultDescription이 포함되는 위치” 등 원하는 쿼리를 사용하도록 사용자 지정 로그 검색을 사용하여 조건을 설정합니다. 임계값을 적정값으로 설정합니다. 
* 원하는 대로 작업 그룹을 추가하거나 만듭니다. 작업 그룹은 메일 또는 SMS 메시지 전송 등의 경고에 대한 응답을 정의하는 위치입니다.
* 경고 이름을 지정하고 저장합니다.

## <a name="upgrade-preference"></a>업그레이드 기본 설정

ASE가 여러 개 있는 경우 일부 ASE를 다른 ASE보다 먼저 업그레이드해야 할 수 있습니다. 이 동작은 ASE 포털을 통해 사용하도록 설정할 수 있습니다.  **구성** 아래 **업그레이드 기본 설정** 을 지정하는 옵션이 있습니다. 가능한 세 가지 값은 다음과 같습니다.

- **없음**: Azure는 특정 일괄 처리 없이 ASE를 업그레이드합니다. 이 값은 기본값입니다.
- **초기**: ASE는 App Service 업그레이드의 초반부에서 업그레이드됩니다.
- **후기**: ASE는 App Service 업그레이드의 후반부에서 업그레이드됩니다.

원하는 값을 선택하고 **저장** 을 선택합니다.  모든 ASE의 기본값은 **없음** 입니다.

![ASE 구성 포털][5]

**upgradePreferences** 기능을 사용하면 “초기” ASE가 “후기” ASE 이전에 업그레이드되기 때문에 여러 개의 ASE가 있는 경우에 가장 적합합니다. 여러 ASE가 있는 경우 개발 및 테스트 ASE를 “초기”로 설정하고 프로덕션 ASE는 “후기”로 설정해야 합니다.

## <a name="pricing"></a>가격 책정

*격리* 가격 책정 SKU는 ASE에서만 사용할 수 있습니다. ASE에서 호스트되는 모든 App Service 플랜에는 격리 가격 책정 SKU가 적용됩니다. App Service 플랜에 대한 격리 요금은 지역별로 다를 수 있습니다.

App Service 플랜에 대한 요금 외에 ASE 자체에 대한 고정 요금이 있습니다. 해당 고정 요금은 ASE 크기에 따라 변경되지 않습니다. 모든 App Service 플랜 인스턴스 15개마다 하나의 추가 프런트 엔드에 대한 기본 스케일링 속도의 ASE 인프라에 대한 비용을 지불합니다.

App Service 플랜 인스턴스 15개마다 하나의 프런트 엔드에 대한 기본 스케일링 속도가 충분히 빠르지 않을 경우 프런트 엔드가 추가되는 비율 또는 프런트 엔드의 크기를 조정할 수 있습니다. 이 비율 또는 크기를 조정할 경우 기본적으로 추가되지 않는 프런트 엔드 코어에 대해 비용을 지불하게 됩니다.

예를 들어 확장 비율을 10으로 조정하면 App Service 계획에서 인스턴스 10개마다 프런트 엔드 하나가 추가됩니다. 고정 비용을 지불하는 경우에는 인스턴스 15개마다 프런트 엔드 하나가 추가되는 확장 비율이 사용됩니다. 확장 비율이 10인 경우에는 App Service 계획 인스턴스 10개에 대해 추가되는 세 번째 프런트 엔드의 요금을 지불하게 됩니다. 인스턴스 수가 15개가 될 때는 요금을 지불하지 않아도 됩니다. 이 경우에는 프런트 엔드가 자동으로 추가되기 때문입니다.

프런트 엔드의 크기를 2-코어로 조정했으나 해당 비율은 조정하지 않은 경우 추가 코어에 대한 요금을 지불하게 됩니다. ASE는 두 개의 프런트 엔드를 사용하여 생성되므로 크기를 2-코어 프런트 엔드로 늘리는 경우 자동 스케일링 임계값보다 낮은 경우에도 두 개의 추가 코어에 대해 비용이 청구됩니다.

자세한 내용은 [Azure App Service 가격][Pricing]을 참조하세요.

## <a name="delete-an-ase"></a>ASE 삭제

ASE를 삭제하려면 다음을 수행합니다.

1. **App Service Environment** 창 상단의 **삭제** 를 선택합니다.

1. ASE의 이름을 입력하여 ASE 삭제를 확인합니다. ASE를 삭제하면 해당 ASE 내의 모든 콘텐츠도 함께 삭제됩니다.

    ![ASE 삭제][3]

1. **확인** 을 선택합니다.

## <a name="ase-cli"></a>ASE CLI

ASE의 관리자를 위한 명령줄 기능이 있습니다.  az cli 명령은 아래에 나와 있습니다.

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
