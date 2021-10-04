---
title: '자습서: Azure Static Web Apps 대한 Azure Front Door 수동으로 구성'
description: Azure Static Web Apps 대한 Azure Front Door 설정 알아보기
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cshoe
ms.openlocfilehash: aa377c63f0282bb43e8f69088f7925d9ac5ffa0d
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400689"
---
# <a name="tutorial-manually-configure-azure-front-door-for-azure-static-web-apps"></a>자습서: Azure Static Web Apps 대한 Azure Front Door 수동으로 구성

정적 웹앱의 [CDN](../frontdoor/front-door-overview.md) Azure Front Door 추가하는 방법을 알아봅니다.  Azure Front Door 웹 애플리케이션을 빠르게 전달할 수 있는 확장 가능하고 안전한 진입점입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - Azure Front Door 인스턴스 만들기
> - Azure Font Door를 Azure Static Web Apps 사이트에 연결

> [!NOTE]
> 이 자습서에는 Azure Static Web Apps 표준 계획이 필요합니다.

## <a name="copy-url"></a>URL 복사

1. Azure Portal로 이동합니다.

1. Azure Front Door 적용하려는 정적 웹앱을 엽니다.

1. *개요* 섹션으로 이동합니다.

1. 나중에 사용할 수 있도록 *URL을* 클립보드에 복사합니다.

## <a name="add-azure-front-door"></a>Azure Front Door 추가

1. Azure Portal로 이동합니다.

1. **리소스 만들기** 를 선택합니다.

1. **Azure Front Door** 검색합니다.

1. **Front Door** 선택합니다.

    표준/Premium 서비스의 단계가 이 자습서에 제시된 것과 다르므로 *표준/Premium* 레이블이 Front Door 서비스를 선택하지 않도록 합니다.

1. **생성** 를 선택합니다.

1. 기본 *탭에서* 다음 값을 입력합니다.

    | 설정 | 값 |
    |---|---|
    | Subscription | Azure 구독을 선택합니다. |
    | Resource group | 리소스 그룹 이름을 입력합니다. 이 이름은 종종 정적 웹앱에서 사용하는 것과 동일한 그룹 이름입니다. |
    | 리소스 그룹 위치 | 새 리소스 그룹을 만드는 경우 가장 가까운 위치를 입력합니다. |

    **다음: 구성 >** 을 선택합니다.

1. *구성* 탭에서 *프런트 엔드/도메인* 옆에 있는 **더하기 기호를** 선택하고 다음 값을 입력합니다.

    | 설정 | 값 |
    |---|---|
    | 호스트 이름 | Front Door 호스트의 고유한 이름을 입력합니다. |

    양식의 나머지 부분에 대한 기본값을 그대로 적용하고 **추가를** 선택합니다.

1. 백 엔드 풀 옆에 있는 **더하기 기호를** 선택하고 다음 값을 *입력합니다.*

    | 설정 | 값 |
    |---|---|
    | 이름 | **my-static-web-app-pool을** 입력합니다. |

1. **+ 백 엔드 추가를** 선택하고 다음 값을 입력합니다.

    | 설정 | 값 |
    |---|---|
    | 백 엔드 호스트 유형 | **사용자 지정 호스트** 를 선택합니다. |
    | 백 엔드 호스트 이름 | 정적 웹앱의 호스트 이름을 입력합니다. 값에 후행 슬래시 또는 프로토콜이 포함되어 있지 않은지 확인합니다. (예: `desert-rain-04056.azurestaticapps.net` )  |
    | 백 엔드 호스트 헤더 | 정적 웹앱의 호스트 이름을 입력합니다. 값에 후행 슬래시 프로토콜이 포함되어 있지 않은지 확인합니다. (예: `desert-rain-04056.azurestaticapps.net` ) |

    양식의 나머지 부분에 대한 기본값을 그대로 적용하고 **추가를** 선택합니다.

1. **추가** 를 선택합니다.

1. *라우팅 규칙* 옆에 있는 **더하기 기호를** 선택하고 다음 값을 입력합니다.

    | 설정 | 값 |
    |---|---|
    | 이름 | **my-routing-rule 을** 입력합니다. |

    양식의 나머지 부분에 대한 기본값을 그대로 적용하고 **추가를** 선택합니다.

1. **검토 + 만들기** 를 선택합니다.

1. **만들기** 를 선택합니다.

    만들기 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

1. **리소스로 이동** 을 선택합니다.

1. **개요** 를 선택합니다.

1. *프런트 엔드 호스트* 라는 레이블이 있는 링크를 선택합니다.

    이 링크를 선택하면 사이트가 완전히 전파되지 않으면 404 오류가 표시 될 수 있습니다. 페이지를 새로 고치는 대신 몇 분 정도 기다렸다가 *개요* 창으로 *돌아가서 프런트 엔드 호스트라는* 레이블이 있는 링크를 선택합니다.

1. *개요* 창에서 **Front Door ID라는** 레이블이 있는 값을 복사하여 나중에 사용할 수 있는 파일에 붙여넣습니다.

> [!IMPORTANT]
> 기본적으로 Azure Front Door 트래픽 통계에 영향을 줄 수 있는 [상태 프로브를](../frontdoor/front-door-health-probes.md) 구성합니다. 상태 프로브 의 기본값을 편집할 수 [있습니다.](../frontdoor/front-door-health-probes.md)

## <a name="update-static-web-app-configuration"></a>정적 웹앱 구성 업데이트

Front Door 통합을 완료하려면 애플리케이션 구성 파일을 다음으로 업데이트해야 합니다.

- Front Door 통해서만 사이트로 트래픽 제한
- Front Door 인스턴스에서만 사이트로 트래픽 제한
- 사이트에 액세스할 수 있는 도메인 정의

사이트의 [staticwebapp.config.json](configuration.md) 파일을 열고 다음과 같은 내용을 변경합니다.

1. 구성 파일에 다음 섹션을 추가하여 트래픽이 Front Door만 사용하도록 제한합니다.

    ```json
    "networking": {
      "allowedIpRanges": ["AzureFrontDoor.Backend"]
    }
    ```

1. 사이트에 액세스할 수 있는 Azure Front Door 인스턴스 및 도메인을 정의하려면 `forwardingGateway` 섹션을 추가합니다.

    ```json
    "forwardingGateway": {
      "requiredHeaders": {
        "X-Azure-FDID" : "<YOUR-FRONT-DOOR-ID>"
      },
      "allowedForwardedHosts": [
        "my-sitename.azurefd.net"
      ]
    }
    ```

    먼저 Front Door 인스턴스의 트래픽만 허용하도록 앱을 구성합니다. 모든 백 엔드 요청에서 Front Door `X-Azure-FDID` Front Door 인스턴스 ID가 포함된 헤더를 자동으로 추가합니다. 이 헤더를 요구하도록 정적 웹앱을 구성하면 트래픽이 Front Door 인스턴스로만 제한됩니다. 구성 `forwardingGateway` 파일의 섹션에서 섹션을 추가하고 `requiredHeaders` `X-Azure-FDID` 헤더를 정의합니다. `<YOUR-FRONT-DOOR-ID>`를 이전에 따로 설정한 Front Door *ID로* 대체합니다.

    다음으로, Azure Static Web Apps 호스트 이름이 아닌 Azure Front Door 호스트 이름을 `allowedForwardedHosts` 배열에 추가합니다. Front Door 인스턴스에 구성된 사용자 지정 도메인이 있는 경우 이 목록에도 포함합니다.

    이 예제에서는 를 `my-sitename.azurefd.net` 사이트의 Azure Front Door 호스트 이름으로 대체합니다.

이 구성을 사용하면 생성된 호스트 이름을 통해 사이트를 더 이상 사용할 수 없지만 Front Door 인스턴스에 구성된 호스트 이름을 통해서만 사이트를 사용할 `*.azurestaticapps.net` 수 있습니다.

> [!NOTE]
> 정적 웹앱의 기존 파일에 업데이트를 배포하는 경우 Azure Front Door TS-Live가 [만료될](https://wikipedia.org/wiki/Time_to_live) 때까지 이전 버전의 파일을 계속 제공할 수 있습니다. 영향을 받는 경로에 대한 [Azure Front Door 캐시를 제거하여](../frontdoor/front-door-caching.md#cache-purge) 최신 파일이 제공되도록 합니다.

이제 Front Door 사이트를 관리하기 때문에 Azure Static Web Apps 사용자 지정 도메인 기능을 더 이상 사용하지 않습니다. Azure Front Door 사용자 지정 도메인을 추가하는 별도의 프로세스가 있습니다. Front Door [사용자 지정 도메인 추가를 참조하세요.](../frontdoor/front-door-custom-domain.md) Front Door 사용자 지정 도메인을 추가하는 경우 정적 웹앱 구성 파일을 업데이트하여 목록에 포함해야 `allowedForwardedHosts` 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 추가](apis.md)
