---
title: 키 - Azure Key Vault 정보
description: 키에 대한 Azure Key Vault REST 인터페이스 및 개발자 세부 정보의 개요입니다.
services: key-vault
author: mbaldwin
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: mbaldwin
ms.openlocfilehash: d89cd4faa67f769f01c0d0a1c1288b6dc8776aed
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722623"
---
# <a name="about-keys"></a>키 정보

Azure Key Vault는 암호화 키를 저장하고 관리하는 두 가지 유형의 리소스를 제공합니다. 자격 증명 모음은 소프트웨어 보호 및 HSM 보호(하드웨어 보안 모듈) 키를 지원합니다. 관리형 HSM은 HSM 보호 키만 지원합니다. 

|리소스 유형|키 보호 방법|데이터 평면 엔드포인트 기준 URL|
|--|--|--|
| **자격 증명 모음** | 소프트웨어 보호<br/><br/>및<br/><br/>HSM 보호(프리미엄 SKU 포함)</li></ul> | https://{vault-name}.vault.azure.net |
| **관리형 HSM** | HSM 보호 | https://{hsm-name}.managedhsm.azure.net |
||||

- **자격 증명 모음** - 가장 일반적인 클라우드 애플리케이션 시나리오에 적합한 저렴하고 배포하기 쉬운 다중 테넌트, 영역 복원 가능(사용 가능한 경우), 고가용성 키 관리 솔루션을 제공합니다.
- **관리형 HSM** - 관리형 HSM은 암호화 키를 저장하고 관리하는 단일 테넌트, 영역 복원 가능(사용 가능한 경우), 고가용성 HSM을 제공합니다. 높은 값 키를 처리하는 애플리케이션 및 사용 시나리오에 가장 적합합니다. 가장 엄격한 보안, 규정 준수 및 규정 요구 사항을 충족하는 데도 도움이 됩니다. 

> [!NOTE]
> 자격 증명 모음을 사용하여 암호화 키 외에도 비밀, 인증서 및 스토리지 계정 키와 같은 여러 유형의 개체를 저장하고 관리할 수 있습니다.

Key Vault의 암호화 키는 JWK[JSON 웹 키] 개체로 표현됩니다. JSON(JavaScript Object Notation) 및 JOSE(JavaScript Object Signing and Encryption) 사양은 다음과 같습니다.

-   [JWK(JSON 웹 키)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JWE(JSON 웹 암호화)](https://datatracker.ietf.org/doc/html/draft-jones-json-web-encryption)  
-   [JWA(JSON 웹 알고리즘)](https://datatracker.ietf.org/doc/html/draft-ietf-jose-json-web-algorithms)  
-   [JWS(JSON 웹 서명)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

또한 기본 JWK/JWA 사양을 확장하여 Azure Key Vault 및 관리형 HSM 구현에 고유한 키 유형을 지원할 수 있습니다. 

HSM 보호 키(HSM 키라고도 함)는 HSM(하드웨어 보안 모듈)에서 처리되며 항상 HSM 보호 경계를 유지합니다. 

- 자격 증명 모음은 **FIPS 140-2 수준 2** 유효성이 검사된 HSM을 사용하여 공유 HSM 백 엔드 인프라에서 HSM 키를 보호합니다. 
- 관리형 HSM은 **FIPS 140-2 수준 3** 유효성이 검사된 HSM 모듈을 사용하여 키를 보호합니다. 각 HSM 풀은 자체 [보안 도메인](../managed-hsm/security-domain.md)을 포함하는 격리된 단일 테넌트 인스턴스로, 동일한 하드웨어 인프라를 공유하는 다른 모든 HSM에서 완벽한 암호화 격리를 제공합니다.

이러한 키는 단일 테넌트 HSM 풀에서 보호됩니다. RSA, EC 및 대칭 키를 소프트 형식으로 또는 지원되는 HSM 디바이스에서 내보내 가져올 수 있습니다. 또한 HSM 풀에서 키를 생성할 수도 있습니다. [BYOK(사용자 고유 키 가져오기) 사양](../keys/byok-specification.md)에 설명된 방법을 사용하여 HSM 키를 가져오는 경우 보안 운송 키 자료를 관리형 HSM 풀로 사용할 수 있습니다. 

지리적 경계에 대한 자세한 내용은 [Microsoft Azure 보안 센터](https://azure.microsoft.com/support/trust-center/privacy/)를 참조하세요.

## <a name="key-types-and-protection-methods"></a>키 유형 및 보호 방법

Key Vault는 RSA 및 EC 키를 지원합니다. 관리형 HSM은 RSA, EC 및 대칭 키를 지원합니다. 

### <a name="hsm-protected-keys"></a>HSM 보호 키

|키 유형|자격 증명 모음(프리미엄 SKU에만 해당)|관리형 HSM|
|--|--|--|
|**EC-HSM**: 타원 곡선 키 | 지원됨(P-256, P-384, P-521, P-256K)| 지원됨(P-256, P-256K, P-384, P-521)|
|**RSA-HSM**: RSA 키|지원됨(2048비트, 3072비트, 4096비트)|지원됨(2048비트, 3072비트, 4096비트)|
|**oct-HSM**: Symmetric 키|지원되지 않음|지원됨(128비트, 192비트, 256비트)|
|||

### <a name="software-protected-keys"></a>소프트웨어 보호 키

|키 유형|자격 증명 모음|관리형 HSM|
|--|--|--|
**RSA**: "소프트웨어 보호" RSA 키|지원됨(2048비트, 3072비트, 4096비트)|지원되지 않음
**EC**: "소프트웨어 보호" 타원 곡선 키 |지원됨(P-256, P-384, P-521, P-256K)|지원되지 않음
|||

### <a name="compliance"></a>규정 준수

|키 유형 및 대상|규정 준수|
|---|---|
|자격 증명 모음의 소프트웨어 보호 키(프리미엄 및 표준 SKU) | FIPS 140-2 수준 1|
|자격 증명 모음의 HSM 보호 키(프리미엄 SKU)| FIPS 140-2 수준 2|
|관리형 HSM의 HSM 보호 키|FIPS 140-2 수준 3|
|||

각 키 형식, 알고리즘, 작업, 특성 및 태그에 대한 자세한 내용은 [키 형식, 알고리즘 및 작업](about-keys-details.md)을 참조하세요.

## <a name="usage-scenarios"></a>사용 시나리오

| 사용 시기 | 예제 |
|--------------|-------------|
| 고객 관리형 키를 사용하는 통합 리소스 공급자를 위한 Azure 서버 쪽 데이터 암호화 | - [Azure Key Vault에서 고객 관리형 키를 사용하여 서버 쪽 암호화](../../security/fundamentals/encryption-models.md#server-side-encryption-using-customer-managed-keys-in-azure-key-vault) |
| 클라이언트 쪽 데이터 암호화 | - [Azure Key Vault를 사용하는 클라이언트 쪽 암호화](../../storage/common/storage-client-side-encryption.md?tabs=dotnet)|
| 키 없는 TLS | - 주요 [클라이언트 라이브러리](../general/client-libraries.md#client-libraries-per-language-and-object) 사용 |

## <a name="next-steps"></a>다음 단계
- [Key Vault 정보](../general/overview.md)
- [관리형 HSM 정보](../managed-hsm/overview.md)
- [비밀 정보](../secrets/about-secrets.md)
- [인증서 정보](../certificates/about-certificates.md)
- [Key Vault REST API 개요](../general/about-keys-secrets-certificates.md)
- [인증, 요청 및 응답](../general/authentication-requests-and-responses.md)
- [Key Vault 개발자 가이드](../general/developers-guide.md)