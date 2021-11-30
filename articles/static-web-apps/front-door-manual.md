---
title: '자습서: azure Static Web Apps에 대 한 Azure Front 도어 수동 구성'
description: Azure 정적 Web Apps에 대 한 Azure Front 도어 설정 배우기
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cshoe
ms.openlocfilehash: 2b61ca6ec42b790183e7cca0ce146ece32a2aecc
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133264990"
---
# <a name="tutorial-manually-configure-azure-front-door-for-azure-static-web-apps"></a>자습서: azure Static Web Apps에 대 한 Azure Front 도어 수동 구성

정적 웹 앱에 대 한 CDN [Azure Front 도어](../frontdoor/front-door-overview.md) 를 추가 하는 방법을 알아봅니다.  Azure 전면 도어는 웹 응용 프로그램을 빠르게 제공 하기 위한 확장 가능 하 고 안전한 진입점입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - Azure Front 도어 인스턴스 만들기
> - Azure 정적 Web Apps 사이트에 Azure Front 도어 연결

> [!NOTE]
> 이 자습서에는 Azure 정적 Web Apps 표준 계획이 필요 합니다.

## <a name="copy-url"></a>URL 복사

1. Azure Portal로 이동합니다.

1. Azure Front 도어를 적용 하려는 정적 웹 앱을 엽니다.

1. *개요* 섹션으로 이동합니다.

1. 나중에 사용 하기 위해 *URL* 을 클립보드에 복사 합니다.

## <a name="add-azure-front-door"></a>Azure Front Door 추가

1. Azure Portal로 이동합니다.

1. **리소스 만들기** 를 선택합니다.

1. **Azure Front 도어** 를 검색 합니다.

1. **전방 도어** 를 선택 합니다.

    표준/Premium 서비스의 단계가이 자습서에서 제공 하는 것과 다르므로 *Front 도어 standard/Premium* 로 레이블이 지정 된 서비스를 선택 하지 않도록 합니다.

1. **만들기** 를 선택합니다.

1. *기본 사항* 탭에서 다음 값을 입력 합니다.

    | 설정 | 값 |
    |---|---|
    | Subscription | Azure 구독을 선택합니다. |
    | Resource group | 리소스 그룹 이름을 입력합니다. 이 이름은 일반적으로 정적 웹 앱에서 사용 하는 것과 동일한 그룹 이름입니다. |
    | 리소스 그룹 위치 | 새 리소스 그룹을 만드는 경우 가장 가까운 위치를 입력 합니다. |

    **다음: 구성 >** 을 선택합니다.

1. *구성* 탭에서 *프런트 엔드/domains* 옆에 있는 **더하기 기호** 를 선택 하 고 다음 값을 입력 합니다.

    | 설정 | 값 |
    |---|---|
    | 호스트 이름 | 프런트 도어 호스트의 고유한 이름을 입력 합니다. |

    폼의 나머지 부분에 대 한 기본값을 그대로 적용 하 고 **추가** 를 선택 합니다.

1. *백 엔드 풀* 옆에 있는 **더하기 기호** 를 선택 하 고 다음 값을 입력 합니다.

    | 설정 | 값 |
    |---|---|
    | 이름 | **내 정적 웹 앱 풀** 을 입력 합니다. |

1. **+ 백 엔드 추가** 를 선택 하 고 다음 값을 입력 합니다.

    | 설정 | 값 |
    |---|---|
    | 백 엔드 호스트 유형 | **사용자 지정 호스트** 를 선택합니다. |
    | 백 엔드 호스트 이름 | 정적 웹 앱의 호스트 이름을 입력 합니다. 값에 후행 슬래시 또는 프로토콜이 포함 되지 않았는지 확인 합니다. (예: `desert-rain-04056.azurestaticapps.net` )  |
    | 백 엔드 호스트 헤더 | 정적 웹 앱의 호스트 이름을 입력 합니다. 값에 후행 슬래시 프로토콜이 포함 되지 않았는지 확인 합니다. (예: `desert-rain-04056.azurestaticapps.net` ) |

    폼의 나머지 부분에 대 한 기본값을 그대로 적용 하 고 **추가** 를 선택 합니다.

1. **추가** 를 선택합니다.

1. *라우팅 규칙* 옆에 있는 **더하기 기호** 를 선택 하 고 다음 값을 입력 합니다.

    | 설정 | 값 |
    |---|---|
    | 이름 | **내 경로 규칙** 을 입력 합니다. |

    폼의 나머지 부분에 대 한 기본값을 그대로 적용 하 고 **추가** 를 선택 합니다.

1. **검토 + 만들기** 를 선택합니다.

1. **만들기** 를 선택합니다.

    만들기 프로세스를 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

1. **리소스로 이동** 을 선택합니다.

1. **개요** 를 선택합니다.

1. *프런트 엔드 호스트* 라는 링크를 선택 합니다.

    이 링크를 선택 하면 사이트가 완전히 전파 되지 않는 경우 404 오류가 표시 될 수 있습니다. 페이지를 새로 고치는 대신 몇 분 정도 기다렸다가 *개요* 창으로 돌아가서 *프론트 엔드 호스트* 링크를 선택 합니다.

1. *개요* 창에서 **Front 문 ID** 라는 값을 복사 하 여 나중에 사용할 수 있도록 파일에 붙여넣습니다.

> [!IMPORTANT]
> 기본적으로 Azure Front 도어는 트래픽 통계에 영향을 줄 수 있는 [상태 프로브](../frontdoor/front-door-health-probes.md) 를 구성 합니다. [상태 프로브](../frontdoor/front-door-health-probes.md)에 대 한 기본값을 편집할 수 있습니다.

## <a name="update-static-web-app-configuration"></a>정적 웹 앱 구성 업데이트

Front 도어와의 통합을 완료 하려면 응용 프로그램 구성 파일을 다음으로 업데이트 해야 합니다.

- 프런트 도어를 통해서만 사이트로 트래픽 제한
- 프런트 도어 인스턴스로부터만 사이트로 트래픽을 제한 합니다.
- 사이트에 액세스할 수 있는 도메인 정의

사이트에 대 한 [staticwebapp.config json](configuration.md) 파일을 열고 다음과 같이 변경 합니다.

1. 구성 파일에 다음 섹션을 추가 하 여 Front 도어만 사용 하도록 트래픽을 제한 합니다.

    ```json
    "networking": {
      "allowedIpRanges": ["AzureFrontDoor.Backend"]
    }
    ```

1. 사이트에 액세스할 수 있는 Azure Front 도어 인스턴스와 도메인을 정의 하려면 섹션을 추가 `forwardingGateway` 합니다.

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

    먼저, 프런트 도어 인스턴스의 트래픽만 허용 하도록 앱을 구성 합니다. 모든 백 엔드 요청에서 Front 도어가 자동으로 `X-Azure-FDID` Front 도어 인스턴스 ID를 포함 하는 헤더를 추가 합니다. 이 헤더를 요구 하도록 정적 웹 앱을 구성 하면 트래픽이 프런트 도어 인스턴스로만 제한 됩니다. `forwardingGateway`구성 파일의 섹션에서 섹션을 추가 `requiredHeaders` 하 고 헤더를 정의 합니다 `X-Azure-FDID` . 을 `<YOUR-FRONT-DOOR-ID>` 이전에 설정한 *FRONT 도어 ID* 로 바꿉니다.

    다음으로 azure 전면 도어 호스트 이름 (Azure Static Web Apps 호스트 이름 아님)을 배열에 추가 합니다 `allowedForwardedHosts` . 프런트 도어 인스턴스에 구성 된 사용자 지정 도메인이 있는 경우이 목록에도 포함 합니다.

    이 예제에서를 `my-sitename.azurefd.net` 사이트의 Azure Front 도어 호스트 이름으로 바꿉니다.

이 구성을 사용 하면 생성 된 호스트 이름을 통해 사이트를 더 이상 사용할 수 `*.azurestaticapps.net` 없지만 앞 도어 인스턴스에 구성 된 호스트 이름을 통해서만 사이트를 사용할 수 있습니다.

> [!NOTE]
> 정적 웹 앱에서 기존 파일에 업데이트를 배포 하는 경우 Azure Front 도어가 [ttl (time-to-live](https://wikipedia.org/wiki/Time_to_live) )이 만료 될 때까지 파일의 이전 버전을 계속 제공할 수 있습니다. 영향을 받는 경로에 대 한 [Azure Front 도어 캐시를 제거](../frontdoor/front-door-caching.md#cache-purge) 하 여 최신 파일이 제공 되는지 확인 합니다.

이제 Front 도어가 사이트를 관리 하 고 있으므로 Azure Static Web Apps 사용자 지정 도메인 기능을 사용 하지 않습니다. Azure Front 도어가 사용자 지정 도메인을 추가 하기 위한 별도의 프로세스를 포함 합니다. [Front 문에 사용자 지정 도메인 추가](../frontdoor/front-door-custom-domain.md)를 참조 하세요. 사용자 지정 도메인을 Front 문에 추가 하는 경우 목록에 포함 하려면 정적 웹 앱 구성 파일을 업데이트 해야 `allowedForwardedHosts` 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 추가](apis.md)
