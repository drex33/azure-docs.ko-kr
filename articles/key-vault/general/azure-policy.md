---
title: Azure Policy과 Key Vault 통합
description: Azure Policy와 Azure Key Vault를 통합하는 방법 알아보기
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: dcbbe63754bdcfc4ded249720b58940e0c219bf9
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529505"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Azure Policy과 Key Vault 통합

[Azure Policy](../../governance/policy/index.yml)는 사용자에게 대규모로 Azure 환경을 감사 및 관리하는 기능을 제공하는 거버넌스 도구입니다. Azure Policy는 할당된 정책 규칙을 준수하도록 Azure 리소스에 가드 레일을 적용하는 기능을 제공합니다. 이를 통해 사용자는 Azure 환경의 감사, 실시간 적용 및 수정을 수행할 수 있습니다. 정책에 의해 수행되는 감사 결과는 규정 준수 대시보드의 사용자가 사용할 수 있으며, 여기에서 어떤 리소스와 구성 요소가 규정을 준수하는지 드릴다운할 수 있습니다.  자세한 내용은 [Azure Policy 서비스 개요](../../governance/policy/overview.md)를 참조하세요.

예제 사용 시나리오:

- 회사의 Key Vault에 있는 인증서의 최소 키 크기와 최대 유효 기간에 대한 요구 사항을 구현하여 회사의 보안 상태를 개선하려고 하지만, 어떤 팀이 규정을 준수하고 어떤 팀이 준수하지 않는지 잘 모릅니다.
- 현재 조직에서 감사를 수행하기 위한 솔루션이 없거나, 조직 내의 개별 팀에 규정 준수를 보고하도록 요청하여 환경에 대한 수동 감사를 수행하고 있습니다. 이 작업을 자동화하고, 실시간으로 감사를 수행하고, 감사의 정확성을 보장하는 방법을 찾고 있습니다.
- 회사 보안 정책을 적용하고 개인이 자체 서명된 인증서를 만들 수 없도록 하는 것이 좋습니다. 그러나 사용자의 인증서 만들기를 자동으로 차단하는 방법은 없습니다. 
- 테스트 팀에 대한 몇 가지 요구 사항을 완화하려고 하지만 프로덕션 환경을 엄격히 제어하려고 합니다. 리소스의 적용을 분리하는 간단한 자동화된 방법이 필요합니다.
- 라이브 사이트 문제가 발생할 경우 새 정책의 적용을 롤백할 수 있는지 확인하려고 할 수 있습니다. 정책 적용을 해제하려면 원클릭 솔루션이 필요합니다. 
- 사용자 환경을 감사하는 타사 솔루션을 사용하고 내부 Microsoft 제품을 사용하려고 합니다.

## <a name="types-of-policy-effects-and-guidance"></a>정책 효과 및 지침의 유형

**감사**: 정책의 영향을 감사로 설정하면, 정책은 환경을 파격적으로 변경하지 않습니다. 지정된 범위 내의 정책 정의를 따르지 않는 인증서와 같은 구성 요소만 정책 준수 대시보드에서 비규격으로 표시하여 경고가 발생하도록 합니다. 정책 효과를 선택하지 않은 경우 감사가 기본값입니다.

**거부**: 정책의 영향을 거부로 설정하면 정책에서 인증서와 같은 새 구성 요소 생성을 차단하고 정책 정의를 따르지 않는 기존 구성 요소의 새 버전을 차단합니다. Key Vault 내의 기존 비규격 리소스는 영향을 받지 않습니다. '감사' 기능은 계속 작동합니다.

## <a name="available-built-in-policy-definitions"></a>사용 가능한 “기본 제공” 정책 정의

Key Vault는 키 자격 증명 모음과 해당 키, 인증서, 비밀 개체를 관리하는 데 사용할 수 있는 정책 세트를 만들었습니다. 이러한 정책은 '기본 제공' 정책이므로 사용하도록 설정하기 위해 사용자 지정 JSON을 작성하지 않아도 되며, Azure Portal에서 할당할 수 있습니다. 조직의 요구에 맞게 특정 매개 변수를 사용자 지정할 수 있습니다.

# <a name="certificate-policies"></a>[인증서 정책](#tab/certificates)

### <a name="certificates-should-have-the-specified-maximum-validity-period-preview"></a>인증서에는 지정된 최대 유효 기간이 있어야 함(미리 보기)

이 정책을 사용하여 Key Vault에 저장된 인증서의 최대 유효 기간을 관리할 수 있습니다. 인증서의 최대 유효 기간을 제한하는 것은 좋은 보안 방법입니다. 인증서의 프라이빗 키가 손상되었으나 감지되지 않을 경우, 수명이 짧은 인증서를 사용하여 지속적인 손상 시간 프레임을 최소화하고 공격자가 얻을 수 있는 인증서 가치를 낮춥니다.

### <a name="certificates-should-use-allowed-key-types-preview"></a>인증서는 허용된 키 유형을 사용해야 함(미리 보기)

이 정책을 사용하면 Key Vault에 있을 수 있는 인증서의 유형을 제한할 수 있습니다. 이 정책을 사용하여 인증서 프라이빗 키가 RSA, ECC 또는 HSM에서 지원되는지 확인할 수 있습니다. 다음 목록에서 허용되는 인증서 유형을 선택할 수 있습니다.

- RSA
- RSA - HSM
- ECC
- ECC - HSM

### <a name="certificates-should-have-the-specified-lifetime-action-triggers-preview"></a>인증서에 지정된 수명 작업 트리거가 있어야 함(미리 보기)

이 정책을 사용하면 특정 만료 기간(일) 이내 또는 사용 가능한 수명의 특정 비율에 도달한 인증서에 대해 지정된 수명 작업을 관리할 수 있습니다.

### <a name="certificates-should-be-issued-by-the-specified-integrated-certificate-authority-preview"></a>인증서는 지정된 통합 인증 기관에서 발급해야 함(미리 보기)

Key Vault 통합 인증 기관(Digicert 또는 GlobalSign)을 사용하며 사용자가 이러한 공급자 중 하나를 사용하도록 하려면 이 정책을 사용하여 선택을 감사하거나 적용합니다. 이 정책은 인증서의 발급 정책에서 선택한 CA와 키 자격 증명 모음에 정의된 CA 공급자를 평가합니다. 이 정책은 Key Vault에서 자체 서명된 인증서의 생성을 감사하거나 거부하는 데도 사용할 수 있습니다.

### <a name="certificates-should-be-issued-by-the-specified-non-integrated-certificate-authority-preview"></a>인증서는 지정된 비통합 인증 기관에서 발급해야 함(미리 보기)

내부 인증 기관 또는 Key Vault와 연결되지 않은 인증 기관을 사용하며 사용자가 제공하는 목록의 인증 기관을 사용하도록 하려면 이 정책을 사용하여 발급자 이름별로 허용되는 인증 기관 목록을 만들 수 있습니다. 이 정책은 Key Vault에서 자체 서명된 인증서의 생성을 감사하거나 거부하는 데도 사용할 수 있습니다.

### <a name="certificates-using-elliptic-curve-cryptography-should-have-allowed-curve-names-preview"></a>타원 곡선 암호화를 사용하는 인증서에는 허용되는 곡선 이름이 있어야 함(미리 보기)

타원 곡선 암호화 또는 ECC 인증서를 사용하는 경우 아래 목록에서 허용되는 곡선 이름 목록을 사용자 지정할 수 있습니다. 기본 옵션은 다음의 모든 곡선 이름을 허용합니다.

- P-256
- P-256K
- P-384
- P-521

## <a name="certificates-using-rsa-cryptography-manage-minimum-key-size-for-rsa-certificates-preview"></a>RSA 암호화를 사용하는 인증서, RSA 인증서의 최소 키 크기 관리(미리 보기)

RSA 인증서를 사용하는 경우 인증서에 포함해야 하는 최소 키 크기를 선택할 수 있습니다. 아래 목록에서 옵션 하나를 선택할 수 있습니다.

- 2048비트
- 3072비트
- 4096비트

## <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>지정된 만료 기간(일) 내에 있는 인증서 관리(미리 보기)

적절히 모니터링되고 있지 않은 인증서가 만료되기 전에 순환되지 않을 경우 서비스 중단이 발생할 수 있습니다. 이 정책은 Key Vault에 저장된 인증서가 모니터링되도록 하는 데 중요합니다. 다른 만료 임계값(예: 180, 90, 60 및 30일 임계값)을 사용해어 이 정책을 여러 번 적용하는 것이 좋습니다. 이 정책은 조직의 인증서 만료를 모니터링하고 심사하는 데 사용할 수 있습니다.

# <a name="key-policies"></a>[키 정책](#tab/keys)

### <a name="keys-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>키는 지정된 일 수보다 오랫동안 활성화되지 않아야 함(미리 보기)

키가 지정된 일 수보다 오랫동안 활성화되지 않았는지 확인하려는 경우 이 정책을 사용하여 키가 활성화된 기간을 감사할 수 있습니다.

**키에 활성화 날짜가 설정되어 있는 경우** 이 정책은 키의 **활성화 날짜** 에서 현재 날짜까지 경과된 일 수를 계산합니다. 일 수가 설정된 임계값을 초과하는 경우 키가 정책을 준수하지 않는 것으로 표시됩니다.

**키에 활성화 날짜가 설정되어 있지 않은 경우** 이 정책은 키를 **만든 날짜** 에서 현재 날짜까지 경과된 일 수를 계산합니다. 일 수가 설정된 임계값을 초과하는 경우 키가 정책을 준수하지 않는 것으로 표시됩니다.

### <a name="keys-should-be-the-specified-cryptographic-type-rsa-or-ec-preview"></a>키는 지정된 암호화 유형 RSA 또는 EC여야 함(미리 보기)

이 정책을 사용하면 키 자격 증명 모음에 있을 수 있는 키의 유형을 제한할 수 있습니다. 이 정책을 사용하여 키가 RSA, ECC 또는 HSM에서 지원되는지 확인할 수 있습니다. 다음 목록에서 허용되는 인증서 유형을 선택할 수 있습니다.

- RSA
- RSA - HSM
- ECC
- ECC - HSM

### <a name="keys-using-elliptic-curve-cryptography-should-have-the-specified-curve-names-preview"></a>타원 곡선 암호화를 사용하는 키는 지정된 곡선 이름이 있어야 함(미리 보기)

타원 곡선 암호화 또는 ECC 키를 사용하는 경우 아래 목록에서 허용되는 곡선 이름 목록을 사용자 지정할 수 있습니다. 기본 옵션은 다음의 모든 곡선 이름을 허용합니다.

- P-256
- P-256K
- P-384
- P-521

### <a name="keys-should-have-expirations-dates-set-preview"></a>키에는 만료 날짜 집합이 설정되어 있어야 함(미리 보기)

이 정책은 만료 날짜가 비준수로 설정되지 않은 키 자격 증명 모음 및 플래그 키의 모든 키를 감사합니다. 또한 이 정책을 사용하여 만료 날짜가 설정되지 않은 키 생성을 차단할 수도 있습니다.

### <a name="keys-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>키는 만료 전 지정된 일 수보다 커야 함(미리 보기)

키가 만료에 너무 가까운 경우 조직에서 키 회전 지연이 발생하여 운영이 중단될 수 있습니다. 키는 오류에 대응할 수 있는 충분한 시간을 제공하기 위해 만료 전 지정된 일 수에서 회전되어야 합니다. 이 정책은 만료 날짜에 너무 가까운 키를 감사하고 이 임계값을 일 단위로 설정할 수 있도록 합니다. 또한 이 정책을 사용하여 만료 날짜에 너무 가까운 새 키 생성을 방지할 수도 있습니다.

### <a name="keys-should-be-backed-by-a-hardware-security-module-preview"></a>키는 하드웨어 보안 모듈에서 지원되어야 함(미리 보기)

HSM은 키를 저장하는 하드웨어 보안 모듈입니다. HSM은 암호화 키에 대한 물리적 보호 계층을 제공합니다. 암호화 키는 소프트웨어 키보다 높은 수준의 보안을 제공하는 물리적 HSM을 벗어날 수 없습니다. 일부 조직에는 HSM 키를 사용해야 하는 규정 준수 요구 사항이 있습니다. 이 정책을 사용하여 HSM이 백업되지 않은 키 자격 증명 모음에 저장된 키를 감사할 수 있습니다. 또한 이 정책을 사용하여 HSM이 백업되지 않은 새 키 생성을 차단할 수도 있습니다. 이 정책은 모든 키 유형, RSA 및 ECC에 적용됩니다.

### <a name="keys-using-rsa-cryptography-should-have-a-specified-minimum-key-size-preview"></a>RSA 암호화를 사용하는 키에는 지정된 최소 키 크기가 있어야 함(미리 보기)

키 크기가 작은 RSA 키를 사용하는 것은 안전한 디자인 방식이 아닙니다. 최소 키 크기를 사용하도록 요구하는 감사 및 인증 표준에 따라 달라질 수 있습니다. 다음 정책을 사용하면 키 자격 증명 모음에 최소 키 크기 요구 사항을 설정할 수 있습니다. 이 최소 요구 사항을 충족하지 않는 키를 감사할 수 있습니다. 이 정책은 최소 키 크기 요구 사항을 충족하지 않는 새 키 생성을 차단하는 데에도 사용할 수 있습니다.

### <a name="keys-should-have-the-specified-maximum-validity-period-preview"></a>키에는 지정된 최대 유효 기간이 있어야 함(미리 보기)

키 자격 증명 모음 내에서 키가 유효한 최대 일 수를 지정하여 조직의 규정 준수 요구 사항을 관리합니다. 설정한 임계값보다 긴 시간 동안 유효한 키는 비준수로 표시됩니다. 또한 이 정책을 사용하여 지정한 최대 유효 기간보다 긴 만료 날짜가 설정된 새 키 생성을 차단할 수 있습니다.

# <a name="secret-policies"></a>[비밀 정책](#tab/secrets)

### <a name="secrets-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>비밀은 지정된 일 수보다 오랫동안 활성화되지 않아야 함(미리 보기)

비밀이 지정된 일 수보다 오랫동안 활성화되지 않았는지 확인하려는 경우 이 정책을 사용하여 비밀이 활성화된 기간을 감사할 수 있습니다.

**비밀에 활성화 날짜가 설정되어 있는 경우** 이 정책은 비밀의 **활성화 날짜** 에서 현재 날짜까지 경과된 일 수를 계산합니다. 일 수가 설정된 임계값을 초과하는 경우 비밀이 정책을 준수하지 않는 것으로 표시됩니다.

**비밀에 활성화 날짜가 설정되어 있지 않은 경우** 이 정책은 비밀을 **만든 날짜** 에서 현재 날짜까지 경과된 일 수를 계산합니다. 일 수가 설정된 임계값을 초과하는 경우 비밀이 정책을 준수하지 않는 것으로 표시됩니다.

### <a name="secrets-should-have-content-type-set-preview"></a>비밀에는 콘텐츠 형식 집합이 있어야 함(미리 보기)

일반 텍스트 또는 인코딩된 파일은 키 자격 증명 모음 비밀로 저장할 수 있습니다. 그러나 조직에서 키로 저장된 암호, 연결 문자열 또는 인증서에 대해 다른 순환 정책 및 제한을 설정하는 것이 좋습니다. 콘텐츠 형식 태그를 사용하면 사용자는 비밀 값을 읽지 않고도 비밀 개체에 저장된 내용을 볼 수 있습니다. 이 정책을 사용하여 콘텐츠 형식 태그 집합이 없는 비밀을 감사할 수 있습니다. 또한 이 정책을 사용하여 콘텐츠 형식 태그 집합이 없는 경우 새 비밀 생성을 방지할 수도 있습니다.

### <a name="secrets-should-have-expiration-date-set-preview"></a>비밀에는 만료 날짜 집합이 설정되어 있어야 함(미리 보기)

이 정책은 만료 날짜가 비준수로 설정되지 않은 키 자격 증명 모음 및 플래그 비밀의 모든 비밀을 감사합니다. 또한 이 정책을 사용하여 만료 날짜가 설정되지 않은 비밀 생성을 차단할 수도 있습니다.

### <a name="secrets-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>비밀은 만료 전 지정된 일 수보다 커야 함(미리 보기)

비밀이 만료에 너무 가까운 경우 조직에서 비밀 회전 지연이 발생하여 운영이 중단될 수 있습니다. 비밀은 오류에 대응할 수 있는 충분한 시간을 제공하기 위해 만료 전 지정된 일 수에서 회전되어야 합니다. 이 정책은 만료 날짜에 너무 가까운 비밀을 감사하고 이 임계값을 일 단위로 설정할 수 있도록 합니다. 또한 이 정책을 사용하여 만료 날짜에 너무 가까운 새 비밀 생성을 방지할 수도 있습니다.

### <a name="secrets-should-have-the-specified-maximum-validity-period-preview"></a>비밀에는 지정된 최대 유효 기간이 있어야 함(미리 보기)

키 자격 증명 모음 내에서 비밀이 유효한 최대 일 수를 지정하여 조직의 규정 준수 요구 사항을 관리합니다. 설정한 임계값보다 긴 시간 동안 유효한 비밀은 비준수로 표시됩니다. 또한 이 정책을 사용하여 지정한 최대 유효 기간 보다 긴 만료 날짜가 설정된 새 비밀 생성을 차단할 수도 있습니다.

# <a name="key-vault-policies"></a>[Key Vault 정책](#tab/keyvault)

### <a name="key-vault-should-use-a-virtual-network-service-endpoint"></a>Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함

이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Key Vault를 감사합니다.

### <a name="resource-logs-in-key-vault-should-be-enabled"></a>Key Vault에서 리소스 로그를 사용하도록 설정해야 함

리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다.

### <a name="key-vaults-should-have-purge-protection-enabled"></a>키 자격 증명 모음에 제거 방지를 사용하도록 설정해야 함

키 자격 증명 모음을 악의적으로 삭제하면 데이터가 영구적으로 손실될 수 있습니다. 조직의 악의적인 내부자가 잠재적으로 키 자격 증명 모음을 삭제하고 제거할 수 있습니다. 제거 보호는 일시 삭제된 키 자격 증명 모음에 대해 필수 보존 기간을 적용하여 내부자 공격으로부터 보호합니다. 일시 삭제 보존 기간 동안에는 조직 또는 Microsoft 내부의 어느 누구도 키 자격 증명 모음을 제거할 수 없습니다.

---

## <a name="example-scenario"></a>예제 시나리오

100개 인증서를 포함하는 여러 팀에서 사용하는 Key Vault를 관리하며, Key Vault에 2년보다 오랫 동안 인증서가 없었는지 확인하려고 합니다.

1. **인증서에는 지정된 최대 유효 기간이 있어야 함** 정책을 할당하고, 인증서의 최대 유효 기간을 24개월로 지정한 후 정책의 효과를 "감사"로 설정합니다. 
1. [Azure Portal에서 규정 준수 보고서](#view-compliance-results)를 보고 20개의 인증서가 비규격이며 2년 넘게 유효하고, 나머지 인증서는 규격인지 확인합니다. 
1. 이러한 인증서의 소유자에게 연락하여 2년 넘게 인증서가 유효할 수 없다는 새로운 보안 요구 사항을 전달합니다. 일부 팀은 최대 유효 기간인 2년 이내에 응답하고 15개의 인증서를 갱신했습니다. 다른 팀은 응답하지 않으며 여전히 Key Vault에 5개의 비규격 인증서가 있습니다.
1. 할당한 정책의 효과를 “거부"로 변경합니다. 비규격 인증서 5개는 해지되지 않으며 계속 작동합니다. 그러나 유효 기간이 2년보다 긴 인증서는 갱신할 수 없습니다. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Azure Portal을 통해 Key Vault 정책 사용 및 관리

### <a name="select-a-policy-definition"></a>정책 정의 선택

1. Azure 포털에 로그인합니다. 
1. 검색 창에서 "정책"을 검색하고 **정책** 을 선택합니다.

    ![검색 창을 보여주는 스크린샷.](../media/policy-img1.png)

1. 정책 창에서 **정의** 를 선택합니다.

    ![정의 옵션을 강조 표시하는 스크린샷.](../media/policy-img2.png)

1. 범주 필터에서 **모두 선택** 을 선택 취소하고 **Key Vault** 를 선택합니다. 

    ![범주 필터와 선택된 Key Vault 범주를 보여주는 스크린샷.](../media/policy-img3.png)

1. 이제 Azure Key Vault에 대해 모든 정책을 퍼블릭 미리 보기로 사용할 수 있습니다. 위의 정책 지침 섹션을 읽고 이해했는지 확인하고 범위에 할당하려는 정책을 선택합니다.  

    ![공개 미리 보기에 사용할 수 있는 정책을 보여주는 스크린샷.](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>범위에 정책 할당 

1. 적용하려는 정책을 선택합니다. 이 예제에서는 **인증서 유효 기간 관리** 정책을 선택합니다. 왼쪽 위 모서리에 있는 할당 단추를 클릭합니다.

    ![인증서 유효 기간 관리 정책을 보여주는 스크린샷.](../media/policy-img5.png)
  
1. 정책을 적용할 구독을 선택합니다. 구독 내에서 단일 리소스 그룹으로만 범위를 제한하도록 선택할 수 있습니다. 전체 구독에 정책을 적용하고 일부 리소스 그룹을 제외하려는 경우 제외 목록을 구성할 수도 있습니다. 정책의 효과(감사 또는 거부)를 적용하려면 정책 적용 선택기를 **사용** 으로 설정하고, 효과(감사 또는 거부)를 해제하려면 **사용 안 함** 으로 설정합니다. 

    ![구독 내에서 단일 리소스 그룹으로만 범위를 제한하도록 선택할 수 있는 위치를 보여주는 스크린샷.](../media/policy-img6.png)

1. 화면 맨 위에 있는 매개 변수 탭을 클릭하여 원하는 최대 유효 기간(월)을 지정합니다. 위 섹션의 지침에 따라 정책의 효과를 **감사** 또는 **거부** 중에서 선택합니다. 그런 후에 검토 + 만들기 단추를 선택합니다. 

    ![원하는 최대 유효 기간(월)을 지정할 수 있는 매개 변수 탭을 보여주는 스크린샷.](../media/policy-img7.png)

### <a name="view-compliance-results"></a>규정 준수 결과 보기

1. 정책 블레이드로 돌아가서 규정 준수 탭을 선택합니다. 규정 준수 결과를 보려는 정책 할당을 클릭합니다.

    ![규정 준수 결과를 보려는 정책 할당을 선택할 수 있는 규정 준수 탭을 보여주는 스크린샷.](../media/policy-img8.png)

1. 이 페이지에서 규격 또는 비호환 자격 증명 모음을 기준으로 결과를 필터링할 수 있습니다. 여기에서 정책 할당 범위 내의 비규격 Key Vault 목록을 볼 수 있습니다. 자격 증명 모음의 구성 요소(인증서)가 비규격인 경우 자격 증명 모음은 비규격으로 간주됩니다. 개별 자격 증명 모음을 선택하여 개별 비규격 구성 요소(인증서)를 볼 수 있습니다. 


    ![정책 할당 범위 내의 비규격 키 자격 증명 모음 목록을 보여주는 스크린샷.](../media/policy-img9.png)

1. 비규격 자격 증명 모음 내 구성 요소 이름 보기


    ![비규격 자격 증명 모음 내 구성 요소 이름을 볼 수 있는 위치를 보여주는 스크린샷.](../media/policy-img10.png)

1. 사용자가 Key Vault 내에서 리소스를 만들 수 있는 기능이 거부되는지 여부를 확인해야 하는 경우 **구성 요소 이벤트(미리 보기)** 탭을 클릭하여 요청자 및 요청의 타임스탬프를 포함하는 거부된 인증서 작업의 요약을 볼 수 있습니다. 


    ![Azure Key Vault 작동 원리 개요](../media/policy-img11.png)

## <a name="feature-limitations"></a>기능 제한 사항

"거부" 효과의 정책을 할당하면 비규격 리소스 생성을 거부하기 시작하는 데 최대 30분(평균적인 경우) 및 1시간(최악의 경우)이 소요될 수 있습니다. 자격 증명 모음의 기존 구성 요소에 대한 정책 평가는 포털 UI에서 규정 준수 결과를 볼 수 있을 때까지 최대 1시간(평균적인 경우) 및 2시간(최악의 경우)이 소요될 수 있습니다. 규정 준수 결과가 "시작되지 않음"으로 표시되는 경우 다음과 같은 이유 때문일 수 있습니다.
- 정책 평가를 아직 완료하지 않았습니다. 초기 평가 대기 시간이 최악의 시나리오에서 최대 2시간이 걸릴 수 있습니다. 
- 정책 할당 범위에 Key Vault가 없습니다.
- 인증서가 정책 할당 범위 내에 있는 Key Vault가 없습니다.

> [!NOTE]
> Azure Key Vault용과 같은 Azure Policy [리소스 공급자 모드](../../governance/policy/concepts/definition-structure.md#resource-provider-modes)는 [구성 요소 규정 준수](../../governance/policy/how-to/get-compliance-data.md#component-compliance) 페이지에서 규정 준수에 대한 정보를 제공합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 서비스](../../governance/policy/overview.md)에 대해 자세히 알아보기
- Key Vault 샘플을 참조하세요. [Key Vault 기본 제공 정책 정의](../../governance/policy/samples/built-in-policies.md#key-vault)
- [Key Vault에 대한 Azure 보안 벤치마크 지침](/security/benchmark/azure/baselines/key-vault-security-baseline?source=docs#network-security)에 대해 알아보기
