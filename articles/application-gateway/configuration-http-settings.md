---
title: Azure Application Gateway HTTP 설정 구성
description: 이 문서에서는 Azure Application Gateway HTTP 설정을 구성하는 방법을 설명합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: f2fb9d2f6221928f093895914b8fc0082573a8b2
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866268"
---
# <a name="application-gateway-http-settings-configuration"></a>Application Gateway HTTP 설정 구성

애플리케이션 게이트웨이는 여기에서 지정한 구성을 사용하여 트래픽을 백 엔드 서버로 라우팅합니다. HTTP 설정을 만든 후에는 하나 이상의 요청 라우팅 규칙과 연결해야 합니다.

## <a name="cookie-based-affinity"></a>쿠키 기반 선호도

Azure Application Gateway는 사용자 세션을 유지 관리하기 위해 게이트웨이 관리 쿠키를 사용합니다. 사용자가 Application Gateway에 첫 번째 요청을 보내면 세션 정보를 포함하는 해시 값으로 응답의 선호도 쿠키가 설정되므로 해당 선호도 쿠키를 포함하는 후속 요청은 연결 유지 관리를 위해 동일한 백 엔드 서버로 라우팅됩니다.

이 기능은 동일한 서버에 사용자 세션을 유지하려는 경우와 사용자 세션의 세션 상태가 서버에 로컬로 저장되는 경우에 유용합니다. 애플리케이션에서 쿠키 기반 선호도를 처리할 수 없는 경우에는 이 기능을 사용할 수 없습니다. 기능을 사용하려면 클라이언트에서 쿠키를 지원하는지 확인합니다.
> [!NOTE]
> 일부 취약성 검색에서는 Secure 또는 HttpOnly 플래그가 설정되지 않아 Applicaton Gateway 선호도 쿠기가 플래그 지정될 수 있습니다. 이러한 검색은 쿠키의 데이터가 단방향 해시를 사용하여 생성된다는 점을 고려하지 않습니다. 쿠키는 사용자 정보를 포함하지 않으며 라우팅 용도로만 사용됩니다. 


[Chromium 브라우저](https://www.chromium.org/Home) [v80 업데이트](https://chromiumdash.appspot.com/schedule)에서는 [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) 특성이 없는 HTTP 쿠키를 SameSite=Lax로 처리하도록 요구했습니다. CORS(원본 간 리소스 공유) 요청의 경우 쿠키가 타사 컨텍스트에서 전송되어야 하면 *SameSite=None; Secure* 를 사용해야 하며 HTTPS를 통해서만 전송되어야 합니다. HTTP 전용 시나리오에서는 브라우저가 타사 컨텍스트에서 쿠키를 전송하지 않습니다. Chrome 업데이트의 목표는 보안을 강화하고 CSRF(교차 사이트 요청 위조) 공격을 방지하는 것입니다. 

이 변경 내용을 지원하기 위해 2020년 2월 17일부터 Application Gateway(모든 SKU 유형)는 기존 *ApplicationGatewayAffinity* 쿠키 외에도 *ApplicationGatewayAffinityCORS* 라는 다른 쿠키를 삽입합니다. *ApplicationGatewayAffinityCORS* 쿠키에는 두 개의 특성이 추가되었으므로( *“SameSite=None; Secure”* ) 원본 간 요청에서도 고정 세션이 유지됩니다.

기본 선호도 쿠키 이름은 *ApplicationGatewayAffinity* 이며 변경할 수 있습니다. 사용자 지정 선호도 쿠키 이름을 사용하는 경우 후속 쿠키는 CORS를 접미사로 사용하여 추가됩니다. 예를 들어 *CustomCookieNameCORS* 와 같습니다.

> [!NOTE]
> *SameSite=None* 특성을 설정한 경우 쿠키에 *Secure* 플래그도 포함되어야 하며 HTTPS를 통해 전송되어야 합니다.  CORS에서 세션 선호도가 필요한 경우 워크로드를 HTTPS로 마이그레이션해야 합니다. [개요](ssl-overview.md), [Azure Portal을 사용하여 TLS 종료로 애플리케이션 게이트웨이 구성](create-ssl-portal.md), [포털에서 Application Gateway를 사용하여 엔드투엔드 TLS 구성](end-to-end-ssl-portal.md)에서 Application Gateway에 대한 TLS 오프로드 및 엔드투엔드 TLS 설명서를 참조하세요.

## <a name="connection-draining"></a>연결 드레이닝

연결 드레이닝은 예정된 서비스 업데이트 중에 백 엔드 풀 멤버를 정상적으로 제거하는 데 도움이 됩니다. HTTP 설정에서 연결 드레이닝을 사용하도록 설정하여 백 엔드 풀의 모든 멤버에 이 설정을 적용할 수 있습니다. 이렇게 하면 백 엔드 풀의 모든 등록 취소 인스턴스가 기존 연결을 계속 유지하고 구성 가능한 제한 시간 동안 진행 중인 요청을 처리하며 새 요청이나 연결은 받지 않습니다. 단, 게이트웨이 관리형 세션 선호도로 인해 등록 취소 인스턴스에 바인딩된 요청은 예외이며 계속해서 등록 취소 인스턴스에 전달됩니다. 연결 드레이닝은 백 엔드 풀에서 명시적으로 제거된 백 엔드 인스턴스에 적용됩니다.

## <a name="protocol"></a>프로토콜

Application Gateway는 요청을 백 엔드 서버로 라우팅할 때 HTTP와 HTTPS를 모두 지원합니다. HTTP를 선택하면 백 엔드 서버로 라우팅되는 트래픽이 암호화되지 않습니다. 암호화되지 않은 통신이 허용되지 않는 경우 HTTPS를 선택합니다.

이 설정은 수신기의 HTTPS와 결합되어 [엔드투엔드 TLS](ssl-overview.md)를 지원합니다. 이렇게 하면 암호화된 중요한 데이터를 백 엔드에 안전하게 전송할 수 있습니다. 엔드투엔드 TLS를 사용할 수 있는 백 엔드 풀의 각 백 엔드 서버가 보안 통신을 허용하는 인증서로 구성되어야 합니다.

## <a name="port"></a>포트

이 설정은 백 엔드 서버가 애플리케이션 게이트웨이의 트래픽을 수신 대기하는 포트를 지정합니다. 1에서 65535 사이의 포트를 구성할 수 있습니다.

## <a name="request-timeout"></a>요청 시간 초과

이 설정은 애플리케이션 게이트웨이가 백 엔드 서버에서 응답을 받기 위해 대기하는 시간(초)입니다.

## <a name="override-back-end-path"></a>백 엔드 경로 재정의

이 설정을 사용하면 요청이 백 엔드로 전달될 때 사용할 선택적 사용자 지정 전달 경로를 구성할 수 있습니다. **백 엔드 경로 재정의** 필드의 사용자 지정 경로와 일치하는 들어오는 경로 부분이 전달된 경로에 복사됩니다. 다음 표는 이 기능의 작동 방식을 보여 줍니다.

- HTTP 설정이 기본 요청 라우팅 규칙에 연결된 경우

  | 원본 요청  | 백 엔드 경로 재정의 | 백 엔드에 전달되는 요청 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- HTTP 설정이 경로 기반 요청 라우팅 규칙에 연결된 경우

  | 원본 요청           | 경로 규칙       | 백 엔드 경로 재정의 | 백 엔드에 전달되는 요청 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | /override/            | /override/                   |

## <a name="use-for-app-service"></a>App Service에 사용

Azure App Service 백 엔드에 대한 두 가지 필수 설정을 선택하는 UI 전용 바로 가기입니다. 이 바로 가기를 사용하여 **백 엔드 주소에서 호스트 이름을 선택** 할 수 있으며, 아직 없는 경우 새 사용자 지정 프로브가 생성됩니다. (자세한 내용은 이 문서의 [백 엔드 주소에서 호스트 이름 선택 설정](#pick-host-name-from-back-end-address) 섹션을 참조하세요.) 새 프로브가 생성되고 프로브 헤더는 백 엔드 멤버의 주소에서 선택됩니다.

## <a name="use-custom-probe"></a>사용자 지정 프로브 사용

이 설정은 HTTP 설정과 [사용자 지정 프로브](application-gateway-probe-overview.md#custom-health-probe)를 연결합니다. 사용자 지정 프로브 1개만 HTTP 설정과 연결할 수 있습니다. 사용자 지정 프로브를 명시적으로 연결하지 않으면 [기본 프로브](application-gateway-probe-overview.md#default-health-probe-settings)가 백 엔드의 상태를 모니터링하는 데 사용됩니다. 백 엔드의 상태 모니터링을 보다 효율적으로 제어하려면 사용자 지정 프로브를 만드는 것이 좋습니다.

> [!NOTE]
> 해당 HTTP 설정이 수신기와 명시적으로 연결되지 않은 경우 사용자 지정 프로브는 백 엔드 풀의 상태를 모니터링하지 않습니다.

## <a name="pick-host-name-from-back-end-address"></a>백 엔드 주소에서 호스트 이름 선택

이 기능은 동적으로 요청의 ‘호스트’ 헤더를 백 엔드 풀의 호스트 이름으로 설정합니다. IP 주소 또는 FQDN이 사용됩니다.

이 기능은 백 엔드의 도메인 이름이 애플리케이션 게이트웨이의 DNS 이름과 다르고 백 엔드가 특정 호스트 헤더를 사용하여 올바른 엔드포인트를 확인하는 경우에 도움이 됩니다.

예를 들어 다중 테넌트 서비스가 백 엔드로 사용되는 경우입니다. App Service는 단일 IP 주소로 공유 공간을 사용하는 다중 테넌트 서비스입니다. 따라서 사용자 지정 도메인 설정에서 구성된 호스트 이름을 통해서만 App Service에 액세스할 수 있습니다.

기본적으로 사용자 지정 도메인 이름은 *example.azurewebsites.net* 입니다. App Service에 명시적으로 등록되지 않은 호스트 이름 또는 애플리케이션 게이트웨이의 FQDN을 통해 애플리케이션 게이트웨이를 사용하여 App Service에 액세스하려면 원래 요청의 호스트 이름을 App Service의 호스트 이름을 재정의합니다. 이렇게 하려면 **백 엔드 주소에서 호스트 이름 선택** 설정을 사용하도록 설정합니다.

기존 사용자 지정 DNS 이름이 App Service에 매핑된 사용자 지정 도메인의 경우에는 이 설정을 사용하도록 설정할 필요가 없습니다.

> [!NOTE]
> 전용 배포인 App Service Environment에서는 이 설정이 필요 없습니다.

## <a name="host-name-override"></a>호스트 이름 재정의

이 기능은 애플리케이션 게이트웨이에 들어오는 요청의 ‘호스트’ 헤더를 지정한 호스트 이름으로 바꿉니다.

예를 들어 **호스트 이름** 설정에 *www.contoso.com* 이 지정된 경우 요청이 백 엔드 서버에 전달될 때 원래 요청 *`https://appgw.eastus.cloudapp.azure.com/path1`이 *`https://www.contoso.com/path1`로 변경됩니다.

## <a name="next-steps"></a>다음 단계

- [백 엔드 풀에 대한 자세한 정보](configuration-overview.md#back-end-pool)
