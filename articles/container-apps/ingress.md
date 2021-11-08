---
title: Azure Container Apps 미리 보기에서 HTTPS 수신 설정
description: Azure Container Apps를 사용하여 앱에서 퍼블릭 및 프라이빗 엔드포인트 사용
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5979588ec977ef7655fab21ef30e4fd233c12b43
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026428"
---
# <a name="set-up-https-ingress-in-azure-container-apps-preview"></a>Azure Container Apps 미리 보기에서 HTTPS 수신 설정

Azure Container Apps를 사용하면 수신을 사용하도록 설정하여 컨테이너 앱을 퍼블릭 웹에 노출할 수 있습니다. 수신을 사용하도록 설정하면 들어오는 HTTPS 요청을 사용하도록 설정하기 위해 Azure Load Balancer, 공용 IP 주소 또는 기타 Azure 리소스를 만들 필요가 없습니다.

수신을 사용하도록 설정하면 컨테이너 앱에 다음과 같은 특징이 있습니다.

- TLS 종료 지원
- HTTP/1.1 및 HTTP/2 지원
- 엔드포인트는 항상 수신 지점에서 종료된 TLS 1.2를 사용합니다.
- 엔드포인트는 항상 포트 80(HTTP의 경우) 및 443(HTTPS의 경우)을 노출합니다.
  - 기본적으로 포트 80에 대한 HTTP 요청은 443에서 HTTPS로 자동으로 리디렉션됩니다.

## <a name="configuration"></a>Configuration

수신은 애플리케이션 전체 설정입니다. 수신 설정에 대한 변경 내용은 모든 수정 버전에 동시에 적용되며 새 수정 버전을 생성하지 않습니다.

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

수신을 구성할 때 사용할 수 있는 설정은 다음과 같습니다.

| 속성 | Description | 값 | 필수 |
|---|---|---|---|
| `external` | 수신 IP 및 정규화된 도메인 이름은 인터넷 외부에 표시되거나 VNET 내에서 내부적으로 표시될 수 있습니다. |`true` 외부 표시 유형, `false` 내부 표시 유형(기본값) | Yes |
| `targetPort` | 들어오는 요청을 위해 컨테이너가 수신 대기하는 포트입니다. | 이 값을 컨테이너에서 사용하는 포트 번호로 설정합니다. 애플리케이션 수신 엔드포인트는 항상 포트 에 `443` 노출됩니다. | Yes |
| `transport` | HTTP/1.1 또는 HTTP/2를 사용하거나 전송 형식을 자동으로 검색하도록 설정할 수 있습니다. | `http` HTTP/1의 `http2` 경우, HTTP/2의 경우 `auto` 전송 형식을 자동으로 검색합니다(기본값). | No |
| `allowInsecure` | 컨테이너 앱에 대한 안전하지 않은 트래픽을 허용합니다. | `false` (기본값), `true`<br><br>로 설정하면 `true` 포트 80에 대한 HTTP 요청이 HTTPS를 사용하여 포트 443으로 자동으로 리디렉션되지 않아 안전하지 않은 연결이 허용됩니다. | No |

> [!NOTE]
> 애플리케이션에 대한 수신을 사용하지 않도록 설정하려면 구성 속성을 완전히 생략하면 `ingress` 됩니다.

## <a name="ip-addresses-and-domain-names"></a>IP 주소 및 도메인 이름

수신을 사용하도록 설정하면 애플리케이션에 FQDN(정규화된 도메인 이름)이 할당됩니다. 도메인 이름은 다음과 같은 형식을 취합니다.

|수신 표시 유형 설정 | 정규화된 도메인 이름 |
|---|---|
| 외부 | `<APP_NAME>.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io`|
| 내부 | `<APP_NAME>.internal.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io` |

Container Apps 환경에는 수신 표시 유형이 있는 애플리케이션에 대한 단일 공용 IP `external` 주소와 수신 표시 유형이 있는 애플리케이션에 대한 단일 내부 IP 주소가 `internal` 있습니다. 따라서 수신 표시 유형이 있는 Container Apps 환경 내의 모든 `external` 애플리케이션은 단일 공용 IP 주소를 공유합니다. 마찬가지로 수신 표시 유형이 있는 Container Apps 환경 내의 모든 `internal` 애플리케이션은 단일 내부 IP 주소를 공유합니다. HTTP 트래픽은 호스트 헤더의 FQDN을 기반으로 개별 애플리케이션으로 라우팅됩니다.

환경 설정을 쿼리하여 환경의 고유 식별자 액세스 권한을 얻을 수 있습니다.

[!INCLUDE [container-apps-get-fully-qualified-domain-name](../../includes/container-apps-get-fully-qualified-domain-name.md)]

> [!div class="nextstepaction"]
> [크기 조정 관리](scale-app.md)
