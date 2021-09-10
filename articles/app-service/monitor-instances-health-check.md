---
title: App Service 인스턴스의 상태 모니터링
description: 상태 검사를 사용하여 App Service 인스턴스의 상태를 모니터링하는 방법을 알아봅니다.
keywords: Azure App Service, 웹앱, 상태 검사, 경로 트래픽, 정상 인스턴스, 경로, 모니터링, 잘못된 인스턴스 제거, 비정상 인스턴스, 작업자 제거
author: msangapu-msft
ms.topic: article
ms.date: 07/19/2021
ms.author: msangapu
ms.custom: contperf-fy22q1
ms.openlocfilehash: 3efac96949d701bbc0147abf8712d4995f781d47
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771847"
---
# <a name="monitor-app-service-instances-using-health-check"></a>상태 검사를 사용하여 App Service 인스턴스 모니터링

이 문서에서는 Azure Portal의 상태 검사를 사용하여 App Service 인스턴스를 모니터링합니다. 상태 검사는 비정상 인스턴스에서 요청을 다시 라우팅하고 비정상 상태로 유지되는 경우 인스턴스를 대체하여 애플리케이션의 가용성을 높입니다. 상태 검사를 완전히 활용하려면 [App Service 플랜](./overview-hosting-plans.md)을 둘 이상의 인스턴스로 확장해야 합니다. 상태 검사 경로에서는 애플리케이션의 중요한 구성 요소를 확인해야 합니다. 예를 들어 애플리케이션이 데이터베이스 및 메시지 시스템에 종속된 경우 상태 검사 엔드포인트는 해당 구성 요소에 연결해야 합니다. 애플리케이션에서 중요한 구성 요소에 연결할 수 없는 경우 이 경로는 앱이 비정상임을 나타내는 500 수준 응답 코드를 반환해야 합니다.

![상태 검사 오류][1]

## <a name="what-app-service-does-with-health-checks"></a>App Service에서 상태 검사를 통해 수행하는 기능

- 앱에서 경로를 지정하는 경우 상태 검사는 1분 간격으로 App Service 앱의 모든 인스턴스에서 이 경로를 ping합니다.
- 인스턴스가 두 개 이상의 요청 후에 200-299(포함) 사이의 상태 코드를 사용하여 응답하지 않거나 ping에 응답하지 못하는 경우 시스템은 인스턴스가 비정상 상태임을 확인하고 제거합니다.
- 제거 후 상태 검사는 비정상 인스턴스를 계속 ping합니다. 계속해서 응답하지 않는 경우 App Service는 기본 VM을 다시 시작하여 인스턴스를 정상 상태로 되돌립니다.
- 인스턴스가 1시간 동안 비정상 상태로 유지되면 새 인스턴스로 바뀝니다.
- 또한 스케일 업하거나 스케일 아웃할 때 App Service는 새 인스턴스가 준비되도록 Health check 경로를 ping합니다.

> [!NOTE]
> 상태 검사는 302 리디렉션을 따르지 않습니다. 시간당 최대 하나의 인스턴스만 교체되며, App Service 계획 당 최대 3개의 인스턴스가 있습니다.
>

## <a name="enable-health-check"></a>상태 검사 사용하도록 설정

![Azure Portal에서 상태 검사 탐색][3]

- 상태 검사를 사용하도록 설정하려면 Azure Portal로 이동하여 App Service 앱을 선택합니다.
- **모니터링** 아래에서 **상태 검사** 를 선택합니다.
- **사용** 을 선택하고 애플리케이션에서 `/health`나 `/api/health`와 같은 올바른 URL 경로를 입력합니다.
- **저장** 을 클릭합니다.

> [!CAUTION]
> 상태 검사 구성을 변경하면 앱을 다시 시작합니다. 프로덕션 앱에 미치는 영향을 최소화하려면 [스테이징 슬롯을 구성](deploy-staging-slots.md)하고 프로덕션으로 교환하는 것이 좋습니다.
>

### <a name="configuration"></a>구성

상태 검사 옵션을 구성하는 외에도 다음과 같은 [앱 설정](configure-common.md)을 구성할 수 있습니다.

| 앱 설정 이름 | 허용되는 값 | Description |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | 인스턴스가 비정상으로 간주되고 부하 분산 장치에서 제거되는 데 필요한 실패한 요청 수입니다. 예를 들어 `2`로 설정하면 인스턴스가 ping `2`회 실패 후 제거됩니다. 기본값은 `10`입니다. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | 0 - 100 | 기본적으로 나머지 정상 인스턴스가 너무 많아지지 않도록 하기 위해 인스턴스의 절반 이하가 한꺼번에 부하 분산 장치에서 제외됩니다. 예를 들어, App Service 계획이 4개의 인스턴스로 확장되고 3개가 비정상이면 2개가 제외됩니다. 나머지 두 인스턴스(정상 1 및 비정상 1)에서는 계속해서 요청을 받습니다. 모든 인스턴스가 비정상인 최악의 시나리오에서는 인스턴스가 제외되지 않습니다. <br /> 이 동작을 재정의하려면 앱 설정을 `0` - `100` 사이의 값으로 설정합니다. 값이 높을수록 더 비정상 인스턴스가 많이 제거됩니다(기본값은 `50`). |

#### <a name="authentication-and-security"></a>인증 및 보안

상태 검사는 App Service의 [인증 및 권한 부여 기능](overview-authentication-authorization.md)과 통합됩니다. 이러한 보안 기능을 사용하도록 설정한 경우에는 추가 설정이 필요하지 않습니다.

사용자 고유의 인증 시스템을 사용하는 경우 상태 검사 경로에서 익명 액세스를 허용해야 합니다. 상태 검사 엔드포인트를 보호하려면 먼저 [IP 제한](app-service-ip-restrictions.md#set-an-ip-address-based-rule), [클라이언트 인증서](app-service-ip-restrictions.md#set-an-ip-address-based-rule) 또는 Virtual Network와 같은 기능을 사용하여 애플리케이션 액세스를 제한해야 합니다. 들어오는 요청 일치 `HealthCheck/1.0`의 `User-Agent`를 요구하여 상태 검사 엔드포인트를 보호할 수 있습니다. 이전 보안 기능으로 인해 요청이 이미 보호되고 있으므로 User-Agent는 스푸핑될 수 없습니다.

## <a name="monitoring"></a>모니터링

애플리케이션의 상태 검사 경로를 제공한 후 Azure Monitor를 사용하여 사이트의 상태를 모니터링할 수 있습니다. 포털의 **상태 검사** 블레이드에서 상단 도구 모음에 있는 **메트릭** 을 클릭합니다. 그러면 사이트의 기록 상태를 확인하고 새 경고 규칙을 만들 수 있는 새 블레이드가 열립니다. 사이트 모니터링에 대한 자세한 내용은 [Azure Monitor 가이드](web-sites-monitor.md)를 참조하세요.

## <a name="limitations"></a>제한 사항

- 프리미엄 함수 사이트에서 상태 검사를 사용하도록 설정하면 안 됩니다. 프리미엄 함수의 빠른 크기 조정으로 인해 상태 검사 요청이 HTTP 트래픽의 불필요한 변동을 야기할 수 있습니다. 프리미엄 기능에는 확장 결정을 알리는 데 사용되는 자체 내부 상태 프로브가 있습니다.
- **무료** 및 **공유** App Service 플랜에 대해 상태 검사를 사용하도록 설정하여 사이트 상태에 대한 메트릭을 유지하고 경고를 설정할 수 있지만 **무료** 및 **공유** 사이트는 스케일 아웃될 수 없으므로 비정상 인스턴스는 대체되지 않습니다. 2개 이상의 인스턴스로 스케일 아웃하고 상태 검사의 모든 이점을 활용할 수 있도록 **기본** 계층 이상으로 스케일 업해야 합니다. 앱의 가용성과 성능이 향상될 수 있기 때문에 프로덕션 관련 애플리케이션에 권장됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-happens-if-my-app-is-running-on-a-single-instance"></a>내 앱이 단일 인스턴스에서 실행되면 어떻게 되나요?

앱이 하나의 인스턴스로만 스케일되고 비정상 상태가 되면 애플리케이션이 완전히 중단되므로 부하 분산 장치에서 제거되지 않습니다. 둘 이상의 인스턴스로 스케일 아웃하여 상태 검사의 재라우팅 이점을 얻을 수 있습니다. 앱이 단일 인스턴스에서 실행되는 경우에도 상태 검사의 [모니터링](#monitoring) 기능을 사용하여 애플리케이션의 상태를 추적할 수 있습니다.
 
### <a name="why-are-the-health-check-request-not-showing-in-my-frontend-logs"></a>내 프런트 엔드 로그에 상태 검사 요청이 표시되지 않는 이유는 무엇인가요?

상태 검사 요청은 내부적으로 사이트로 전송되므로 [프런트 엔드 로그](troubleshoot-diagnostic-logs.md#enable-web-server-logging)에 요청이 표시되지 않습니다. 즉, 요청이 내부적으로 전송되는 요청이므로 `127.0.0.1`의 원본이 됩니다. 상태 검사 코드에 로그 문을 추가하여 상태 검사 경로가 ping될 때의 로그를 유지할 수 있습니다.

### <a name="are-the-health-check-requests-sent-over-http-or-https"></a>상태 검사 요청이 HTTP와 HTTPS 중에서 어떤 프로토콜을 통해 전송되나요?

사이트에 대해 [HTTPS 전용](configure-ssl-bindings.md#enforce-https)이 사용하도록 설정된 경우 상태 검사 요청은 HTTPS를 통해 전송됩니다. 그렇지 않으면 HTTP를 통해 전송됩니다.

### <a name="what-if-i-have-multiple-apps-on-the-same-app-service-plan"></a>동일한 App Service 플랜에 여러 앱이 있는 경우 어떻게 되나요?

비정상 인스턴스는 App Service 플랜의 다른 앱에 관계없이 항상 부하 분산 장치 순환에서 제거됩니다([`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT`](#configuration)에 지정된 비율까지). 인스턴스의 앱이 1시간 넘게 비정상 상태로 유지되는 경우 상태 검사를 사용하도록 설정된 다른 모든 앱도 비정상 상태인 경우에만 인스턴스가 대체됩니다. 상태 검사를 사용하도록 설정되지 않은 앱은 고려되지 않습니다. 

#### <a name="example"></a>예제 

상태 검사를 사용하도록 설정한 두 애플리케이션(또는 슬롯이 있는 하나의 앱) 앱 A와 앱 B가 있다고 가정해보겠습니다. 이러한 애플리케이션은 동일한 App Service 플랜에 있고 해당 플랜은 4개의 인스턴스로 스케일 아웃됩니다. 두 인스턴스에서 앱 A가 비정상 상태가 되면 부하 분산 장치는 해당하는 두 인스턴스에서 앱 A로의 요청 전송을 중지합니다. 앱 B가 정상이라고 가정하면 해당 인스턴스에서 요청은 여전히 앱 B로 라우팅됩니다. 해당 두 인스턴스에서 앱 A가 1시간 넘게 비정상 상태로 유지되는 경우 해당 인스턴스에서 앱 B **또한** 비정상 상태인 경우에만 인스턴스가 대체됩니다. 앱 B가 정상이면 인스턴스가 대체되지 않습니다.

![위의 예제 시나리오를 설명하는 시각적 다이어그램][2]

> [!NOTE]
> 플랜(사이트 C)에 상태 검사를 사용하도록 설정하지 않은 다른 사이트 또는 슬롯이 있는 경우 인스턴스 교체는 고려되지 않습니다.

### <a name="what-if-all-my-instances-are-unhealthy"></a>내 인스턴스가 모두 비정상이면 어떻게 되나요?

애플리케이션의 모든 인스턴스가 비정상인 시나리오에서 App Service는 `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT`에 지정된 비율까지 부하 분산 장치에서 인스턴스를 제거합니다. 이 시나리오에서 모든 비정상 앱 인스턴스를 부하 분산 장치 순환에서 제외하면 애플리케이션이 효과적으로 중단됩니다.

### <a name="does-health-check-work-on-app-service-environments"></a>상태 확인은 Azure App Service Environment에서 작동하나요?

예, ASE(App Service Environments)에서 플랫폼은 지정된 경로에서 인스턴스를 ping하고 부하 분산 장치에서 비정상 인스턴스를 제거하여 요청이 라우팅되지 않도록 합니다. 그러나 현재 이러한 비정상 인스턴스는 1시간 동안 비정상 상태를 유지하는 경우 새 인스턴스로 교체되지 않습니다.

## <a name="next-steps"></a>다음 단계
- [구독의 모든 자동 크기 조정 엔진 작업을 모니터링하기 위한 활동 로그 경고를 만듭니다.](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-alert)
- [구독에서 실패한 모든 자동 크기 조정 규모 감축/규모 확장 작업을 모니터링하기 위한 활동 로그 경고를 만듭니다.](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-failed-alert)
- [환경 변수 및 앱 설정 참조](reference-app-settings.md)

[1]: ./media/app-service-monitor-instances-health-check/health-check-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-multi-app-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
