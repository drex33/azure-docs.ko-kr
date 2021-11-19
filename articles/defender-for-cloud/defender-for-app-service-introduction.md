---
title: App Service용 Microsoft Defender - 이점 및 특징
description: App Service용 Microsoft Defender의 기능과 구독에서 이를 사용하는 방법에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: overview
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: 898a6dbae646d038b97929787498c7d7e2bf92db
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526817"
---
# <a name="protect-your-web-apps-and-apis"></a>웹앱 및 API 보호

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="prerequisites"></a>필수 구성 요소

Defender for Cloud는 기본적으로 App Service와 통합되어 배포 및 온보딩이 필요하지 않으며 통합이 투명하게 이루어집니다.

App Service용 Microsoft Defender를 사용하여 Azure App Service 요금제를 보호하려면

- 전용 머신과 연결된 지원되는 App Service 요금제. 지원되는 요금제는 [가용성](#availability)에 나열되어 있습니다.

- [빠른 시작: 향상된 보안 기능 사용](enable-enhanced-security.md)에 설명된 대로 구독에서 Defender for Cloud의 향상된 보호 기능을 사용해야 합니다.

    > [!TIP]
    > 선택적으로, App Service용 Microsoft Defender와 같이 개별 Microsoft Defender 플랜을 사용할 수 있습니다.

## <a name="availability"></a>가용성

| 양상                       | 세부 정보                                                                                                                                                                                        |
|------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 릴리스 상태:               | GA(일반 공급)                                                                                                                                                                      |
| 가격 책정:                     | App Service용 Microsoft Defender는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)에 표시된 대로 요금이 청구됩니다.<br>모든 요금제의 총 컴퓨팅 인스턴스에 따라 청구됩니다.       |
| 지원되는 App Service 요금제: | [소비 계획에 대한 Azure Functions](../azure-functions/functions-scale.md)를 제외한 [모든 App Service 요금제](https://azure.microsoft.com/pricing/details/app-service/plans/)가 지원됩니다. |
| 클라우드:                      | :::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: 국가(Azure Government, Azure 중국 21Vianet)                                                     |
|                              |                                                                                                                                                                                                |

## <a name="what-are-the-benefits-of-microsoft-defender-for-app-service"></a>App Service용 Microsoft Defender의 이점은 무엇인가요?

Azure App Service는 웹앱 및 API를 빌드하고 호스팅하기 위한 완전 관리형 플랫폼입니다. 플랫폼은 완전 관리형이기 때문에 인프라에 대해 걱정할 필요가 없습니다. 엔터프라이즈급 성능, 보안 및 규정 준수 요구 사항을 충족하는 관리, 모니터링 및 운영 인사이트를 제공합니다. 자세한 내용은 [Azure App Service](https://azure.microsoft.com/services/app-service/)를 참조하세요.

**App Service용 Microsoft Defender** 는 클라우드 규모를 사용하여 App Service를 통해 실행되는 애플리케이션을 대상으로 하는 공격을 식별합니다. 공격자는 웹 애플리케이션을 검색하여 약점을 찾아 악용합니다. 특정 환경으로 라우팅되기 전에 Azure에서 실행되는 애플리케이션에 대한 요청은 여러 게이트웨이를 통과하면서 검사 및 기록됩니다. 그런 다음, 이 데이터는 악용 및 공격자를 식별하고, 나중에 사용할 새 패턴을 학습하는 데 사용됩니다.

App Service용 Microsoft Defender를 사용하도록 설정하면 이 Defender 플랜에서 제공하는 다음과 같은 서비스를 즉시 이용할 수 있습니다.

- **보안** - App Service용 Defender에서 App Service 요금제가 적용되는 리소스를 평가하고 해당 결과에 따라 보안 권장 사항을 생성합니다. 이 권장 사항의 세부 지침을 사용하여 앱 서비스 리소스를 강화하세요.

- **검색** - App Service용 Defender는 다음을 모니터링하여 App Service 리소스에 대한 다양한 위협을 검색합니다.
    - App Service가 실행 중인 VM 인스턴스와 관리 인터페이스
    - App Service 앱에서 송수신한 요청 및 응답
    - 기본 샌드박스 및 VM
    - App Service 내부 로그 - Azure가 클라우드 공급자로 사용하는 가시성 덕분에 사용 가능

클라우드 네이티브 솔루션인 App Service용 Defender는 여러 대상에 적용되는 공격 방법을 식별할 수 있습니다. 예를 들어, 단일 호스트의 경우 여러 호스트의 유사한 엔드포인트로 크롤링하는 소규모 IP 하위 집합에서 분산 공격을 식별하는 것은 어려울 수 있습니다.

로그 데이터와 인프라를 함께 사용하면 떠돌고 있는 새로운 공격에서부터 고객 머신의 손상에 이르기까지 모든 것을 알려줄 수 있습니다. 따라서 웹앱이 악용된 후 App Service용 Microsoft Defender를 배포하더라도 지속적인 공격을 탐지할 수 있습니다.


## <a name="what-threats-can-defender-for-app-service-detect"></a>App Service용 Defender가 검색할 수 있는 위협은 무엇인가요?

### <a name="threats-by-mitre-attck-tactics"></a>MITRE ATT&CK 전술의 위협

Defender for Cloud는 App Service 리소스에 대한 여러 위협을 모니터링합니다. 이 경고에는 사전 공격에서 명령 및 제어에 이르는 MITRE ATT&CK 전술의 거의 모든 목록이 포함됩니다.

- **공격 전 위협** - Defender for Cloud는 공격자가 약점을 위해 애플리케이션을 검색하는 데 자주 사용하는 여러 유형의 취약성 스캐너 실행을 검색할 수 있습니다.

- **초기 액세스 위협** - [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684)는 알려진 악의적인 IP 주소가 Azure App Service FTP 인터페이스에 연결될 때 경고 트리거를 포함하는 이러한 경고를 지원합니다.

- **실행 위협** - Defender for Cloud는 높은 권한 명령, Windows App Service의 Linux 명령, 파일리스 공격 동작, 디지털 통화 마이닝 도구 및 기타 여러 의심스러운 악성 코드 실행 활동을 실행하려는 시도를 검색할 수 있습니다.

### <a name="dangling-dns-detection"></a>현수 DNS 검색

또한 App Service용 Defender는 App Service 웹 사이트가 해제될 때 DNS 등록자에 남아있는 모든 DNS 항목을 식별합니다. 이러한 항목을 현수 DNS 항목이라고 합니다. 웹 사이트를 제거하고 DNS 등록자에서 사용자 지정 도메인을 제거하지 않으면 DNS 항목이 존재하지 않는 리소스를 가리키고 하위 도메인이 인수에 취약해집니다. Defender for Cloud는 DNS 등록자에서 *기존* 현수 DNS 항목을 검색하지 않습니다. App Service 웹 사이트가 서비스 해제되고 해당 사용자 지정 도메인(DNS 항목)이 삭제되지 않으면 이를 경고합니다.

하위 도메인 인수는 조직에 일반적이고 심각도가 높은 위협입니다. 위협 행위자가 현수 DNS 항목을 검색하면 대상 주소에 자체 사이트를 만듭니다. 그런 다음, 조직의 도메인을 대상으로 하는 트래픽은 위협 행위자의 사이트로 보내지고 광범위한 악의적인 활동에 해당 트래픽을 사용할 수 있습니다.

현수 DNS 보호는 도메인이 Azure DNS 또는 외부 도메인 등록 기관으로 관리되는지 여부에 관계없이 사용할 수 있고 Windows의 App Service 및 Linux의 App Service 모두에 적용합니다.

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="검색된 현수 DNS 항목에 대한 알림의 예입니다. App Service용 Microsoft Defender를 사용하여 환경에 대한 이 경고 및 기타 경고를 수신합니다." lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

[현수 DNS 항목 방지 및 하위 도메인 인수 방지](../security/fundamentals/subdomain-takeover.md)에서 현수 DNS 및 하위 도메인 인수 위협에 대해 자세히 알아보세요.

App Service 경고의 전체 목록은 [경고 참조 테이블](alerts-reference.md#alerts-azureappserv)을 참조하세요.

> [!NOTE]
> 사용자 지정 도메인이 App Service 리소스를 직접 가리키지 않거나 현수 DNS 보호가 실행된 이후 Defender for Cloud가 웹 사이트에 대한 트래픽을 모니터링하지 않은 경우(사용자 정의 도메인을 식별하는 데 도움이 되는 로그가 없기 때문), Defender for Cloud는 현수 DNS 경고를 트리거하지 않을 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 App Service용 Microsoft Defender에 대해 알아보았습니다. 

> [!div class="nextstepaction"]
> [향상된 보호 사용](enable-enhanced-security.md)

관련 자료는 다음 문서를 참조하세요. 

- 경고를 Microsoft Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM, SOAR 또는 IT Service Management 솔루션으로 경고 스트리밍](export-to-siem.md)의 지침을 따릅니다.
- App Service용 Microsoft Defender 경고 목록은 [경고 참조 테이블](alerts-reference.md#alerts-azureappserv)를 참조하세요.
- App Service 요금제에 대한 자세한 내용은 [App Service 요금제](https://azure.microsoft.com/pricing/details/app-service/plans/)를 참조하세요.
