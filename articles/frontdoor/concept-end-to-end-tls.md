---
title: Azure Front 도어를 사용 하 여 종단 간 TLS
description: Azure Front 도어를 사용할 때 종단 간 TLS 암호화에 대해 알아봅니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/02/2021
ms.author: duau
ms.openlocfilehash: 5e0cac6f9ba6a245ec201666adb2c5cfeed79c38
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343100"
---
# <a name="end-to-end-tls-with-azure-front-door"></a>Azure Front 도어를 사용 하 여 종단 간 TLS

이전에 SSL(Secure Sockets Layer)로 알려진 TLS(전송 계층 보안)는 웹 서버와 브라우저 간에 암호화된 링크를 설정하기 위한 표준 보안 기술입니다. 이 링크를 통해 웹 서버와 웹 브라우저 간에 전달 되는 모든 데이터는 개인 및 암호화 된 상태로 유지 됩니다.

보안 또는 규정 준수 요구 사항을 충족 하기 위해 Azure Front 도어 (AFD)는 종단 간 TLS 암호화를 지원 합니다. 전면 도어 TLS/SSL 오프 로드는 TLS 연결을 종료 하 고, Azure 프런트 도어에서 트래픽을 해독 한 후 백 엔드에 전달 하기 전에 트래픽을 다시 암호화 합니다. 백 엔드에 대 한 연결은 공용 IP를 통해 발생 하기 때문입니다. 클라이언트에서 백 엔드로 종단 간 TLS 암호화를 적용 하도록 Azure Front 문에 전달 프로토콜로 HTTPS를 구성 하는 것이 좋습니다.

## <a name="end-to-end-tls-encryption"></a>엔드투엔드 TLS 암호화

종단 간 TLS를 사용 하면 전역 부하 분산 및 캐싱과 같은 Azure Front 도어 기능에서 기능과 하는 동시에 백 엔드로 전송 되는 동안 중요 한 데이터를 보호할 수 있습니다. 일부 기능에는 URL 기반 라우팅, TCP 분할, 클라이언트에 가장 가까운에 지 위치에 대 한 캐싱 및에 지에서 HTTP 요청을 사용자 지정 하는 작업도 포함 됩니다.

Azure 전면 도어는에 지에서 TLS 세션을 오프 로드 하 고 클라이언트 요청을 해독 합니다. 그런 다음 요청을 백 엔드 풀의 적절 한 백 엔드로 라우팅하는 구성 된 라우팅 규칙을 적용 합니다. 그러면 Azure 프런트 도어가 백 엔드에 대 한 새 TLS 연결을 시작 하 고 백 엔드에 요청을 전송 하기 전에 백 엔드의 인증서를 사용 하 여 모든 데이터를 다시 암호화 합니다. 백 엔드에서의 모든 응답은 최종 사용자에 게 다시 동일한 프로세스를 통해 암호화 됩니다. 종단 간 TLS를 사용 하도록 설정 하기 위해 전달 프로토콜로 HTTPS를 사용 하도록 Azure Front 도어를 구성할 수 있습니다.

## <a name="supported-tls-versions"></a>지원 되는 TLS 버전

Azure 전면 도어는 tls 버전 1.0, 1.1 및 1.2의 세 가지 버전의 TLS 프로토콜을 지원 합니다. 9 월 2019 일 이후에 만들어진 모든 Azure Front 도어 프로필은 TLS 1.2를 기본 최소값으로 사용 하지만 TLS 1.0 및 TLS 1.1은 이전 버전과의 호환성을 위해 계속 지원 됩니다.

Azure 전면 도어가 RFC 5246에서 클라이언트/상호 인증을 도입 하는 TLS 1.2를 지원 하지만, 현재 Azure Front 도어가 클라이언트/상호 인증을 지원 하지 않습니다.

Azure Portal 또는 [azure REST API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)를 사용 하 여 사용자 지정 도메인 HTTPS 설정의 Azure Front 문에 최소 TLS 버전을 구성할 수 있습니다. 현재는 1.0과 1.2 중에서 선택할 수 있습니다. 따라서 최소 버전으로 TLS 1.2을 지정 하면 Azure Front 도어가 클라이언트에서 허용 하는 최소 허용 TLS 버전을 제어 합니다. Azure Front 도어가 백 엔드에 대 한 TLS 트래픽을 시작 하면 백 엔드가 안정적이 고 일관 되 게 수락할 수 있는 최고의 TLS 버전을 협상 하려고 시도 합니다.

## <a name="supported-certificates"></a>지원되는 인증서

TLS/SSL 인증서를 만들 때 [Microsoft 신뢰할 수 있는 Ca 목록](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT)에 포함 된 허용 된 Ca (인증 기관)를 사용 하 여 완전 한 인증서 체인을 만들어야 합니다. 허용되지 않는 CA를 사용하는 경우 요청이 거부됩니다.

내부 CA 또는 자체 서명된 인증서의 인증서는 허용되지 않습니다.

## <a name="online-certificate-status-protocol-ocsp-stapling"></a>OCSP (온라인 인증서 상태 프로토콜) 스테이플링

용 OSCP 스테이플링은 Azure Front 도어에서 기본적으로 지원 되며 구성이 필요 하지 않습니다.

## <a name="backend-tls-connection-azure-front-door-to-backend"></a>백 엔드 TLS 연결 (Azure Front 도어를 백 엔드)

HTTPS 연결의 경우 Azure Front 도어가 백 엔드 *호스트* 이름과 일치 하는 주체 이름을 가진 유효한 CA (인증 기관)의 인증서를 제공 하는 것으로 예상 합니다. 예를 들어 백 엔드 호스트 이름이로 설정 되  `myapp-centralus.contosonews.net`   고, TLS 핸드셰이크 중에 백 엔드에서 표시 하는 인증서에  `myapp-centralus.contosonews.net`   또는 `*.contosonews.net`   주체 이름이 없는 경우 Azure Front 도어가 연결을 거부 하 고 오류로 인해 연결을 거부 합니다.

> [!NOTE]
> 인증서에 리프 및 중간 인증서를 포함 하는 완전 한 인증서 체인이 있어야 합니다. 루트 CA는 [Microsoft의 신뢰할 수 있는 Ca 목록](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT)에 포함 되어야 합니다. 전체 체인이 없는 인증서가 표시되면 해당 인증서와 관련된 요청이 예상대로 작동하지 않을 수 있습니다.

보안 관점에서 Microsoft는 인증서 주체 이름 확인을 사용 하지 않도록 설정 하는 것을 권장 하지 않습니다. 예를 들어 테스트용으로와 같은 특정 사용 사례에서 원본은 자체 서명 된 인증서를 사용 해야 합니다. 실패 한 HTTPS 연결을 해결 하기 위한 해결 방법으로, Azure Front 문에 대해 인증서 주체 이름 확인을 사용 하지 않도록 설정할 수 있습니다. 사용 하지 않도록 설정 하는 옵션은 Azure Portal의 Azure Front 도어 설정과 Azure Front 도어 API의 BackendPoolsSettings에 있습니다. 

## <a name="frontend-tls-connection-client-to-front-door"></a>프런트 엔드 TLS 연결 (클라이언트에서 전면 도어로)

Azure Front 도어 사용자 지정 도메인의 콘텐츠를 안전 하 게 배달 하기 위해 HTTPS 프로토콜을 사용 하도록 설정 하려면 Azure Front 도어에서 관리 되는 인증서를 사용 하거나 사용자 고유의 인증서를 사용 하도록 선택할 수 있습니다.  

* Azure 전면 도어 관리 되는 인증서는 DigiCert을 통해 표준 TLS/SSL 인증서를 제공 하며 Azure Front 도어의 Key Vault에 저장 됩니다.   

* 사용자 고유의 인증서를 사용 하도록 선택 하는 경우 표준 TLS, 확장 된 유효성 검사 인증서 또는 와일드 카드 인증서 일 수 있는 지원 되는 CA에서 인증서를 등록할 수 있습니다.  

* 자체 서명된 인증서는 지원되지 않습니다.  [사용자 지정 도메인에 대해 HTTPS를 사용 하도록 설정 하는 방법을](front-door-custom-domain-https.md)알아봅니다.

### <a name="certificate-autorotation"></a>인증서 autorotation

Azure Front 도어 관리 인증서 옵션의 경우 인증서가 관리 되 고 Azure Front 도어가 만료 시간 90 일 이내에 자동으로 회전 합니다. azure front 도어 Standard/Premium 관리 되는 인증서 옵션의 경우 인증서가 관리 되 고 azure Front 도어가 만료 시간 45 일 이내에 자동으로 회전 합니다. Azure Front 도어 관리 되는 인증서를 사용 하 고 있고 표준/Premium SKU의 인증서 만료 날짜가 60 일 또는 30 일 미만인 경우 지원 티켓을 제출 합니다. 

사용자 고유의 사용자 지정 TLS/SSL 인증서:

1. 키 자격 증명 모음에서 최신 버전의 인증서를 사용할 수 있는 경우 인증서가 최신 버전으로 자동으로 회전 되도록 비밀 버전을 ' 최신 '으로 설정 합니다. 사용자 지정 인증서의 경우 인증서가 만료 된 시간에 관계 없이 인증서가 1-2 일 이내에 최신 버전의 인증서로 자동 회전 됩니다.

1. 특정 버전을 선택 하면 autorotation 지원 되지 않습니다. 인증서를 회전 하려면 새 버전을 수동으로 다시 선택 해야 합니다. 새 버전의 인증서/비밀을 배포하는 데 최대 24시간이 걸립니다.

    Front 도어의 서비스 사용자에 게 키 자격 증명 모음에 대 한 액세스 권한이 있는지 확인 해야 합니다. 키 자격 증명 모음에 대 한 액세스 권한을 부여 하는 방법을 참조 하세요. Azure Front 도어가 업데이트 된 인증서 롤아웃 작업을 수행 하면 인증서에 대 한 주체 이름 또는 SAN (주체 대체 이름)이 변경 되지 않은 경우 프로덕션 작동 중단 시간이 발생 하지 않습니다.

## <a name="supported-cipher-suites"></a>지원 되는 암호 그룹

TLS 1.2의 경우 지원되는 암호 그룹은 다음과 같습니다.

* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

> [!NOTE]
> Windows 10 이상 버전의 경우 보안을 강화하기 위해 두 ECDHE 암호 그룹 중 하나 또는 둘 다를 사용하도록 설정하는 것이 좋습니다. Windows 8.1, 8 및 7은 이러한 ECDHE 암호 그룹과 호환되지 않습니다. DHE 암호 그룹은 이러한 운영 체제와의 호환성을 위해 제공되었습니다.

TLS1.0/1.1이 설정된 사용자 지정 도메인을 사용하면 다음과 같은 암호 도구 모음이 지원됩니다.

* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
* TLS_RSA_WITH_AES_256_GCM_SHA384
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA
* TLS_RSA_WITH_AES_128_CBC_SHA
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

Azure 전면 도어는 특정 암호 그룹을 구성 하는 기능을 지원 하지 않습니다. 인증 기관에서 사용자 고유의 사용자 지정 TLS/SSL 인증서를 가져올 수 있습니다 (예: Verisign, Entrust 또는 DigiCert). 그런 다음 인증서를 생성할 때 특정 암호 그룹을 인증서에 표시합니다. 

## <a name="next-steps"></a>다음 단계

* Azure Front 도어에 대 한 [사용자 지정 도메인을 구성](front-door-custom-domain.md) 합니다.
* [사용자 지정 도메인에 대해 HTTPS를 사용 하도록 설정](front-door-custom-domain-https.md)합니다.
