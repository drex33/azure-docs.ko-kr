---
title: Azure Key Vault 개요 - Azure Key Vault
description: Azure Key Vault는 보안 비밀 저장소로, 모두 하드웨어 보안 모듈에서 지원하는 비밀, 키 및 인증서에 대한 관리를 제공합니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 50504d2e36c490c90c7c8bdbb8b737837b64eaff
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163039"
---
# <a name="about-azure-key-vault"></a>Azure Key Vault 정보

Azure Key Vault는 다음 문제를 해결하는 데 도움이 됩니다.

- **비밀 관리** - Azure Key Vault를 사용하여 토큰, 암호, 인증서, API 키 및 기타 비밀에 대한 액세스를 안전하게 저장하고 엄격히 제어할 수 있습니다.
- **키 관리** - Azure Key Vault를 키 관리 솔루션으로 사용할 수 있습니다. Azure Key Vault를 사용하면 데이터를 암호화하는 데 사용되는 암호화 키를 쉽게 만들고 제어할 수 있습니다.
- **인증서 관리** - Azure Key Vault를 사용하면 Azure 및 내부 연결 리소스와 함께 사용할 퍼블릭 및 프라이빗 TLS/SSL(Transport Layer Security/Secure Sockets Layer) 인증서를 쉽게 프로비전, 관리 및 배포할 수 있습니다.

Azure Key Vault에는 다음과 같은 두 가지 서비스 계층이 있습니다. 소프트웨어 키로 암호화하는 표준 계층 및 HSM(하드웨어 보안 모듈) 보호 키를 포함하는 프리미엄 계층. 표준 계층과 프리미엄 계층 간의 비교를 보려면 [Azure Key Vault 가격 책정 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.

## <a name="why-use-azure-key-vault"></a>Azure Key Vault를 사용하는 이유는?

### <a name="centralize-application-secrets"></a>애플리케이션 비밀 중앙 집중화

Azure Key Vault에 애플리케이션 비밀을 중앙 집중식으로 스토리지하면 배포를 제어할 수 있습니다. Key Vault를 사용하면 비밀이 우발적으로 유출될 가능성이 대폭 감소합니다. Key Vault를 사용하면 애플리케이션 개발자가 더 이상 애플리케이션에 보안 정보를 저장할 필요가 없습니다. 애플리케이션에 보안 정보를 저장하지 않으면 코드의 해당 정보 부분을 만들 필요가 없습니다. 예를 들어 애플리케이션이 데이터베이스에 연결해야 할 수 있습니다. 연결 문자열을 앱 코드에 저장하는 대신 Key Vault에 안전하게 저장할 수 있습니다.

애플리케이션은 URI를 사용하여 필요한 정보에 안전하게 액세스할 수 있습니다. 이러한 URI를 사용하면 애플리케이션이 특정 버전의 비밀을 검색할 수 있습니다. Key Vault에 저장된 비밀 정보를 보호하기 위해 사용자 지정 코드를 작성할 필요가 없습니다.

### <a name="securely-store-secrets-and-keys"></a>안전하게 비밀 및 키 저장

Key Vault에 액세스하려면 호출자(사용자 또는 애플리케이션)가 액세스할 수 있도록 적절한 인증 및 권한 부여가 필요합니다. 인증은 호출자의 ID를 확인하는 반면 권한 부여는 수행할 수 있는 작업을 결정합니다.

인증은 Azure Active Directory를 통해 수행됩니다. 권한 부여는 Azure RBAC(Azure 역할 기반 액세스 제어) 또는 Key Vault 액세스 정책을 통해 수행할 수 있습니다. Azure RBAC는 자격 증명 모음을 관리하고 자격 증명 모음에 저장된 데이터에 액세스하는 데 사용할 수 있지만 키 자격 증명 모음 액세스 정책은 자격 증명 모음에 저장된 데이터에 액세스하려는 경우에만 사용할 수 있습니다.

Azure Key Vault는 소프트웨어로 보호되거나 Azure Key Vault 프리미엄 계층을 통해 HSM(하드웨어 보안 모듈)에서 보호될 수 있습니다. 소프트웨어 보호 키, 비밀 및 인증서는 업계 표준 알고리즘 및 키 길이를 사용하여 Azure에서 보호됩니다.  추가 보증이 필요한 경우, HSM 경계를 벗어나지 않는 HSM에서 키를 가져오거나 생성할 수 있습니다. Azure Key Vault는 FIPS(Federal Information Processing Standards) 140-2 수준 2의 유효성이 검사된 nCipher HSM을 사용합니다. nCipher 도구를 사용하여 HSM에서 Azure Key Vault로 키를 이동할 수 있습니다.

마지막으로 Azure Key Vault는 Microsoft에서 데이터를 보거나 추출할 수 없게 설계되어 있습니다.

### <a name="monitor-access-and-use"></a>액세스 및 사용 모니터링

Key Vault를 두 개 만들었으면 키와 비밀이 액세스되는 방법 및 시기를 모니터링해야 합니다. 자격 증명 모음에 대한 로깅을 사용하도록 설정하여 활동을 모니터링할 수 있습니다. Azure Key Vault를 다음과 같이 구성할 수 있습니다.

- 스토리지 계정에 보관합니다.
- 이벤트 허브로 스트리밍합니다.
- 로그를 Azure Monitor 로그로 보냅니다.

로그를 제어하고 로그에 대한 액세스를 제한하여 보호할 수 있으며 더 이상 필요하지 않은 로그를 삭제할 수도 있습니다.

### <a name="simplified-administration-of-application-secrets"></a>애플리케이션 비밀의 관리 간소화

중요한 데이터를 저장할 때 몇 가지 단계를 수행해야 합니다. 보안 정보는 보호되어야 하며 수명 주기를 따라야 하고 가용성이 높아야 합니다. Azure Key Vault에서는 다음을 통해 이러한 요구 사항을 충족하는 과정을 단순화합니다.

- 하드웨어 보안 모듈의 사내 지식에 대한 필요성을 제거합니다.
- 조직의 사용량 급증에 맞춰 단기간에 확장합니다.
- 한 지역 전체와 보조 지역으로 Key Vault의 콘텐츠를 복제합니다. 데이터 복제를 사용하면 고가용성이 보장되고 장애 조치(failover)를 트리거하는 관리자의 모든 작업이 필요없습니다.
- Azure CLI 및 PowerShell을 통해 표준 Azure 관리 옵션을 제공합니다.
- 공용 CA에서 구입한 인증서에 대한 등록 및 갱신과 같은 특정 작업을 자동화합니다.

또한 Azure Key Vault를 통해 애플리케이션 비밀을 격리할 수 있습니다. 애플리케이션은 액세스가 허용된 자격 증명 모음에만 액세스할 수 있으며, 특정 작업만 수행하도록 제한할 수 있습니다. 애플리케이션당 Azure Key Vault를 만들고 Key Vault에 저장된 비밀을 특정 애플리케이션 및 개발자 팀으로 제한할 수 있습니다.

### <a name="integrate-with-other-azure-services"></a>다른 Azure 서비스와 통합

Azure의 안전한 저장소로서 Key Vault는 다음과 같은 시나리오를 간소화하는 데 사용됐습니다.
-  [Azure 디스크 암호화](../../security/fundamentals/encryption-overview.md)
-  SQL 서버 및 Azure SQL Database의 [상시 암호화](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 및 [투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption) 기능
- [Azure App Service](../../app-service/configure-ssl-certificate.md).

Key Vault 자체는 스토리지 계정, 이벤트 허브 및 로그 분석과 통합할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [키, 비밀 및 인증서](about-keys-secrets-certificates.md)에 대한 자세한 정보
- [빠른 시작: CLI를 사용하여 Azure Key Vault 만들기](../secrets/quick-create-cli.md)
- [인증, 요청 및 응답](../general/authentication-requests-and-responses.md)
- [Key Vault 개발자 가이드](../general/developers-guide.md)
