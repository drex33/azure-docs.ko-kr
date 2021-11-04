---
title: Azure 컨테이너 앱 미리 보기에서 HTTPS 수신 설정
description: Azure Container Apps를 사용 하 여 앱에서 공용 및 개인 끝점 사용
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: e4b465b593fadcacce59a14c9fbced0780c252a9
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578295"
---
# <a name="set-up-https-ingress-in-azure-container-apps-preview"></a>Azure 컨테이너 앱 미리 보기에서 HTTPS 수신 설정

Azure Container Apps를 사용 하면 수신을 활성화 하 여 컨테이너 앱을 공용 웹에 노출할 수 있습니다. 수신을 사용 하도록 설정 하는 경우 들어오는 HTTPS 요청을 사용 하도록 Azure Load Balancer, 공용 IP 주소 또는 다른 Azure 리소스를 만들 필요가 없습니다.

수신이 사용 되 면 컨테이너 앱은 다음과 같은 특성을 제공 합니다.

- TLS 종료 지원
- HTTP/1.1 및 HTTP/2 지원
- 끝점은 항상 TLS 1.2를 사용 하며, 수신 지점에서 종료 됩니다.
- 끝점은 항상 포트 80 (HTTP의 경우) 및 443 (HTTPS의 경우)을 노출 합니다.
  - 기본적으로 포트 80에 대 한 HTTP 요청은 443에서 HTTPS로 자동으로 리디렉션됩니다.

## <a name="configuration"></a>구성

수신은 응용 프로그램 수준 설정입니다. 수신 설정에 대 한 변경 내용은 모든 수정 버전에 동시에 적용 되며 새 수정 버전을 생성 하지 않습니다.

수신 구성 섹션의 형식은 다음과 같습니다.

```json
{
  ...
  "configuration": {
      "ingress": {
          "external": true,
          "targetPort": 80,
          "transport": auto
      }
  }
}
```

수신 구성 시 다음 설정을 사용할 수 있습니다.

| 속성 | Description | 값 | 필수 |
|---|---|---|---|
| `external` | 수신 IP와 정규화 된 도메인 이름은 인터넷 외부에서 표시 하거나 VNET 내에서 내부적으로 볼 수 있습니다. |`true` 외부 표시 유형, `false` 내부 표시 유형 (기본값) | Yes |
| `targetPort` | 컨테이너가 들어오는 요청을 수신 대기 하는 포트입니다. | 컨테이너에서 사용 하는 포트 번호로이 값을 설정 합니다. 응용 프로그램 수신 끝점은 항상 포트에서 노출 됩니다 `443` . | Yes |
| `transport` | HTTP/1.1 또는 HTTP/2를 사용 하거나 전송 유형을 자동으로 검색 하도록 설정할 수 있습니다. | `http` http/2에 대 한 http/1의 경우 `http2` `auto` 전송 유형을 자동으로 검색 하려면 (기본값) | No |
| `allowInsecure` | 컨테이너 앱에 대 한 안전 하지 않은 트래픽을 허용 합니다. | `false` (기본값), `true`<br><br>로 설정 `true` 된 경우 포트 80에 대 한 HTTP 요청은 HTTPS를 사용 하 여 포트 443으로 자동 리디렉션되지 않으므로 안전 하지 않은 연결을 허용 합니다. | No |

> [!NOTE]
> 응용 프로그램에 대 한 수신 기능을 사용 하지 않도록 설정 하려면 `ingress` 구성 속성을 완전히 생략 하면 됩니다.

## <a name="ip-addresses-and-domain-names"></a>IP 주소 및 도메인 이름

수신이 사용 되 면 응용 프로그램에 FQDN (정규화 된 도메인 이름)이 할당 됩니다. 도메인 이름은 다음 형식을 사용 합니다.

|수신 표시 유형 설정 | 정규화된 도메인 이름 |
|---|---|
| 외부 | `<APP_NAME>.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io`|
| 내부 | `<APP_NAME>.internal.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io` |

컨테이너 앱 환경에는 수신 표시 유형을 사용 하는 응용 프로그램에 대 한 단일 공용 IP 주소 `external` 와 `internal` 수신 표시 유형이 있는 응용 프로그램에 대 한 단일 내부 ip 주소가 있습니다. 따라서 수신 표시가 있는 컨테이너 앱 환경 내의 모든 응용 프로그램은 `external` 단일 공용 IP 주소를 공유 합니다. 마찬가지로 수신 표시가 있는 컨테이너 앱 환경 내의 모든 응용 프로그램은 `internal` 단일 내부 IP 주소를 공유 합니다. HTTP 트래픽은 호스트 헤더의 FQDN을 기준으로 개별 응용 프로그램으로 라우팅됩니다.

환경 설정을 쿼리하여 환경의 고유 식별자에 액세스할 수 있습니다.

[!INCLUDE [container-apps-get-fully-qualified-domain-name](../../includes/container-apps-get-fully-qualified-domain-name.md)]

> [!div class="nextstepaction"]
> [크기 조정 관리](scale-app.md)
