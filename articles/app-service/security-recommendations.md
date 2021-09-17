---
title: 보안 권장 사항
description: 공유 책임 모델에 명시된 대로 보안 의무를 충족하는 데 도움이 되는 보안 권장 사항을 구현합니다. 앱의 보안을 개선합니다.
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 452c0479834ab7929696af2c95454662adfecfab
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538330"
---
# <a name="security-recommendations-for-app-service"></a>App Service에 대한 보안 권장 사항

이 문서에는 Azure App Service에 대한 보안 권장 사항이 포함되어 있습니다. 권장 사항을 구현하면 공유 책임 모델에 설명된 대로 보안 의무를 이행하는 데 도움이 되며 웹앱 솔루션의 전반적인 보안을 강화할 수 있습니다. Microsoft에서 서비스 공급자 책임을 이행하기 위해 수행하는 작업에 대한 자세한 내용은 [Azure 인프라 보안](../security/fundamentals/infrastructure.md)을 참조하세요.

## <a name="general"></a>일반

| 권장 | 주석 |
|-|-|----|
| 최신 정보 유지 | 지원되는 플랫폼, 프로그래밍 언어, 프로토콜, 프레임워크의 최신 버전을 사용합니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 권장 | 주석 |
|-|----|
| 익명 액세스 사용 안 함 | 익명 요청을 지원해야 하는 경우가 아니면 익명 액세스를 사용하지 않도록 설정합니다. Azure App Service 인증 옵션에 대한 자세한 내용은 [Azure App Service의 인증 및 권한 부여](overview-authentication-authorization.md)를 참조하세요.|
| 인증 필요 | 가능하면 인증 및 권한 부여를 처리하는 코드를 작성하는 대신 App Service 인증 모듈을 사용합니다. [Azure App Service의 인증 및 권한 부여](overview-authentication-authorization.md)를 참조하세요. |
| 인증된 액세스를 사용하여 백 엔드 리소스 보호 | 사용자 ID를 사용하거나 애플리케이션 ID를 사용하여 백 엔드 리소스에 인증할 수 있습니다. 애플리케이션 ID 사용을 선택한 경우 [관리 ID](overview-managed-identity.md)를 사용합니다.
| 클라이언트 인증서 인증 필요 | 클라이언트 인증서 인증은 사용자가 제공한 인증서를 사용하여 인증할 수 있는 클라이언트의 연결만 허용하여 보안을 강화합니다. |

## <a name="data-protection"></a>데이터 보호

| 권장 | 주석 |
|-|-|
| HTTP에서 HTTPs로 리디렉션 | 기본적으로 클라이언트는 HTTP 또는 HTTPS를 둘 다 사용하여 웹앱에 연결할 수 있습니다. HTTPS는 SSL/TLS 프로토콜을 사용하여 암호화 및 인증된 보안 연결을 제공하므로 HTTP를 HTTPs로 리디렉션하는 것이 좋습니다. |
| Azure 리소스에 대한 통신 암호화 | 앱에서 [SQL Database](https://azure.microsoft.com/services/sql-database/) 또는 [Azure Storage](../storage/index.yml)와 같은 Azure 리소스에 연결하는 경우 해당 연결은 Azure에서 유지됩니다. Azure의 공유 네트워킹을 통해 연결이 이동하므로 항상 모든 통신을 암호화해야 합니다. |
| 최신 TLS 버전을 사용할 수 있어야 합니다. | 2018년부터 새로운 Azure App Service 앱은 TLS 1.2를 사용합니다. 최신 버전의 TLS에는 이전 프로토콜 버전보다 향상된 보안 기능이 포함되어 있습니다. |
| FTPS 사용 | App Service는 파일을 배포하기 위해 FTP와 FTPS를 모두 지원합니다. 가능하면 FTP 대신 FTPS를 사용합니다. 이러한 프로토콜 중 하나 또는 둘 다 사용하지 않는 경우 [해당 프로토콜을 사용하지 않도록 설정](deploy-ftp.md#enforce-ftps)해야 합니다. |
| 애플리케이션 데이터 보호 | 애플리케이션 비밀(예: 데이터베이스 자격 증명, API 토큰, 프라이빗 키)을 코드 또는 구성 파일에 저장하지 않습니다. 일반적으로 허용되는 방법은 선택한 언어로 표준 패턴을 사용하여 [환경 변수](https://wikipedia.org/wiki/Environment_variable)로 액세스하는 것입니다. Azure App Service에서 [앱 설정](./configure-common.md) 및 [연결 문자열](./configure-common.md)을 통해 환경 변수를 정의할 수 있습니다. 앱 설정 및 연결 문자열은 Azure에 암호화되어 저장됩니다. 앱이 시작될 때 앱 설정이 앱의 프로세스 메모리에 삽입되기 직전에만 앱 설정의 암호가 해독됩니다. 암호화 키는 정기적으로 회전합니다. 또는 고급 비밀 관리를 위해 Azure App Service 앱을 [Azure Key Vault](../key-vault/index.yml)와 통합할 수 있습니다. [관리 ID를 사용하여 Key Vault에 액세스](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)하는 경우 App Service 앱에서 필요한 비밀에 안전하게 액세스할 수 있습니다. |

## <a name="networking"></a>네트워킹

| 권장 | 주석 |
|-|-|
| 고정 IP 제한 사용 | Windows에서 Azure App Service를 사용하면 앱에 액세스할 수 있는 IP 주소 목록을 정의할 수 있습니다. 허용 목록에는 서브넷 마스크에서 정의된 개별 IP 주소 또는 IP 주소 범위가 포함될 수 있습니다. 자세한 내용은 [Azure App Service 고정 IP 제한](app-service-ip-restrictions.md)을 참조하세요.  |
| 격리된 가격 책정 계층 사용 | 격리된 가격 책정 계층을 제외한 모든 계층은 Azure App Service의 공유 네트워크 인프라에서 앱을 실행합니다. 격리 계층은 전용 [App Service 환경](environment/intro.md) 내에서 앱을 실행하여 완벽한 네트워크 격리를 제공합니다. App Service 환경은 [Azure Virtual Network](../virtual-network/index.yml)의 사용자 인스턴스에서 실행됩니다.|
| 온-프레미스 리소스에 액세스할 때 보안 연결 사용 | [하이브리드 연결](app-service-hybrid-connections.md), [Virtual Network 통합](web-sites-integrate-with-vnet.md) 또는 [App Service 환경](environment/intro.md)을 사용하여 온-프레미스 리소스에 연결할 수 있습니다. |
| 인바운드 네트워크 트래픽에 대한 노출 제한 | 네트워크 보안 그룹을 사용하면 네트워크 액세스를 제한하고 노출된 엔드포인트 수를 제어할 수 있습니다. 자세한 내용은 [App Service Environment로의 인바운드 트래픽을 제어하는 방법](environment/app-service-app-service-environment-control-inbound-traffic.md)을 참조하세요. |

## <a name="monitoring"></a>모니터링

| 권장 | 주석 |
|-|-|
|App Service Azure Security Center Azure Defender 사용 | [App Service 대한 Azure Defender](../security-center/defender-for-app-service-introduction.md) 기본적으로 Azure App Service 통합됩니다. Security Center App Service 계획에서 다루는 리소스를 평가하고 해당 결과에 따라 보안 권장 사항을 생성합니다. [이러한 권장 사항의]()자세한 지침을 사용합니다. /security-center/recommendations-reference.md#appservices-recommendations)를 사용하여 App Service 리소스를 강화합니다. 또한 Azure Defender 위협 방지 기능을 제공하며, 사전 공격부터 명령 및 제어까지 거의 모든 MITRE ATT&CK 전술 목록을 포함하는 다양한 위협을 검색할 수 있습니다. Azure App Service 경고의 전체 목록은 [App Service 경고에 대한 Azure Defender 참조하세요.](../security-center/alerts-reference.md#alerts-azureappserv)|

## <a name="next-steps"></a>다음 단계

애플리케이션 공급자에게 추가 보안 요구 사항이 있는지 확인합니다. 보안 애플리케이션 개발에 대한 자세한 내용은 [보안 개발 설명서](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/)를 참조하세요.