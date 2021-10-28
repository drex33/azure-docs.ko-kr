---
title: Azure Active Directory 확인 가능한 자격 증명을 사용자 지정하는 방법(미리 보기)
description: 이 문서에서는 사용자 지정 확인 가능한 자격 증명을 만드는 방법을 보여줍니다.
services: active-directory
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 1a42387be183d1fa2263e609bcbfed5f388e901c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004087"
---
# <a name="how-to-customize-your-verifiable-credentials-preview"></a>확인 가능한 자격 증명을 사용자 지정하는 방법(미리 보기)

확인 가능한 자격 증명은 규칙과 표시 파일의 두 가지 구성 요소로 구성됩니다. 규칙 파일은 사용자가 확인 가능한 자격 증명을 받기 전에 제공해야 하는 사항을 결정합니다. 표시 파일은 자격 증명의 브랜딩과 클레임의 스타일을 제어합니다. 이 가이드에서는 조직의 요구 사항을 충족하도록 두 파일을 수정하는 방법을 설명합니다. 

> [!IMPORTANT]
> Azure Active Directory 확인 가능한 자격 증명은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="rules-file-requirements-from-the-user"></a>규칙 파일: 사용자의 요구 사항

규칙 파일은 확인 가능한 자격 증명의 중요한 속성을 설명하는 간단한 JSON 파일입니다. 특히 확인 가능한 자격 증명을 채우기 위해 클레임을 사용하는 방법을 설명합니다.

현재 규칙 파일에서 구성할 수 있는 세 가지 입력 형식이 있습니다. 이러한 형식은 확인 가능한 자격 증명 발급 서비스에서 확인 가능한 자격 증명에 클레임을 삽입하고 DID로 해당 정보를 증명하는 데 사용됩니다. 다음에 설명과 함께 세 가지 형식이 나와 있습니다.

- ID 토큰
- 확인 가능한 프레젠테이션을 통해 확인 가능한 자격 증명.
- 자체 증명 클레임

**ID 토큰:** 샘플 앱 및 자습서는 ID 토큰을 사용합니다. 이 옵션이 구성되면 Open ID Connect 구성 URI를 제공하고 VC에 포함되어야 하는 클레임을 포함해야 합니다. 이 요구 사항을 충족하고 계정에서 관련 클레임을 추가하기 위해 Authenticator 앱에 '로그인'하라는 메시지가 사용자에게 표시됩니다. 

**확인 가능한 자격 증명:** 발급 흐름의 최종 결과는 확인 가능한 자격 증명을 생성하는 것이지만 사용자에게 자격 증명을 발급하기 위해 확인 가능한 자격 증명을 제시하도록 요청할 수도 있습니다. 규칙 파일은 제시된 확인 가능한 자격 증명에서 특정 클레임을 가져와서 조직에서 새로 발급된 확인 가능한 자격 증명에 해당 클레임을 포함할 수 있습니다. 

**자체 증명 클레임:** 이 옵션을 선택하면 사용자가 인증자에게 정보를 직접 입력할 수 있습니다. 현재, 자체 증명 클레임에 대해 지원되는 유일한 입력은 문자열입니다. 

![확인 가능한 자격 증명 카드의 자세히 보기](media/credential-design/issuance-doc.png) 

**정적 클레임:** 규칙 파일에서 정적 클레임을 선언할 수도 있지만, 이 입력은 사용자가 입력한 것이 아닙니다. 발급자는 규칙 파일에서 정적 클레임을 정의하며 확인 가능한 자격 증명의 다른 클레임과 유사합니다. vc.type 뒤에 credentialSubject를 추가하고 속성과 클레임을 선언하면 됩니다. 

```json
"vc": {
    "type": [ "StaticClaimCredential" ],
    "credentialSubject": {
      "staticClaim": true,
      "anotherClaim": "Your Claim Here"
    },
  }
}
```


## <a name="input-type-id-token"></a>입력 형식: ID 토큰

ID 토큰을 입력으로 가져오려면 규칙 파일이 OIDC 호환 ID 시스템의 잘 알려진 엔드포인트를 구성해야 합니다. 해당 시스템에서 [발급자 서비스 통신 예](issuer-openid.md)의 올바른 정보로 애플리케이션을 등록해야 합니다. 또한 client_id는 규칙 파일에 넣어야 하며 범위 매개 변수는 올바른 범위로 채워야 합니다. 예를 들어, ID 토큰에 이메일 클레임을 반환하려면 Azure Active Directory에 이메일 범위가 필요합니다.
```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
            "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["https://schema.org/EducationalCredential", "https://schemas.ed.gov/universityDiploma2020", "https://schemas.contoso.edu/diploma2020" ]
      }
    }
```

| 속성 | 설명 |
| -------- | ----------- |
| `attestations.idTokens` | 사용자 정보 소싱을 위해 지원되는 OpenID Connect ID 공급자의 배열입니다. |
| `...mapping` | 각 ID 토큰의 클레임이 결과로 확인 가능한 자격 증명의 특성에 매핑되는 방식을 설명하는 개체입니다. |
| `...mapping.{attribute-name}` | 결과로 확인 가능한 자격 증명에 채워져야 하는 속성입니다. |
| `...mapping.{attribute-name}.claim` | 속성을 채우는 데 값을 사용해야 하는 ID 토큰의 클레임입니다. |
| `...configuration` | ID 공급자의 구성 문서 위치입니다. 이 URL은 [ID 제공자 메타 데이터에 대한 OpenID Connect 표준](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata)을 준수해야 합니다. 구성 문서에는 `issuer`, `authorization_endpoint`, `token_endpoint` 및 `jwks_uri` 필드가 포함되어야 합니다. |
| `...client_id` | 클라이언트 등록 프로세스 중에 얻은 클라이언트 ID입니다. |
| `...scope` | IDP가 ID 토큰에서 올바른 클레임을 반환할 수 있어야 하는 공백으로 구분된 범위 목록입니다. |
| `...redirect_uri` | 항상 `vcclient://openid/` 값을 사용해야 합니다. |
| `validityInterval` | 확인 가능한 자격 증명의 수명을 나타내는 시간(초)입니다. 이 기간이 경과하면 확인 가능한 자격 증명은 더 이상 유효하지 않습니다. 이 값을 생략하면 각 확인 가능한 자격 증명이 명시적으로 철회될 때까지 유효한 상태로 유지됩니다. |
| `vc.type` | 확인 가능한 자격 증명이 충족하는 스키마를 나타내는 문자열의 배열입니다. 자세한 내용은 아래 섹션을 참조하세요. |

### <a name="vctype-choose-credential-types"></a>vc.type: 자격 증명 형식 선택 

모든 확인 가능한 자격 증명은 해당 규칙 파일에서 해당 "형식"을 선언해야 합니다. 자격 증명 형식은 확인 가능한 자격 증명을 다른 조직에서 발급한 자격 증명과 구별하고 발급자와 검증자 간의 상호 운용성을 보장합니다. 자격 증명 형식을 나타내려면 자격 증명이 충족하는 하나 이상의 자격 증명 형식을 제공해야 합니다. 각 형식은 고유한 문자열로 표시됩니다. 전체 고유성을 보장하기 위해 종종 URI가 사용됩니다. URI는 주소를 지정할 필요가 없습니다. 문자열로 처리됩니다. 

예를 들어, Contoso University에서 발급한 졸업장 자격 증명은 다음 형식을 선언할 수 있습니다.

| 형식 | 목적 |
| ---- | ------- |
| `https://schema.org/EducationalCredential` | Contoso University에서 발급한 졸업장에 schema.org의 `EducationaCredential` 개체가 정의한 속성이 포함됨을 선언합니다. |
| `https://schemas.ed.gov/universityDiploma2020` | Contoso University에서 발급한 졸업장에 미국 교육부가 정의한 속성이 포함됨을 선언합니다. |
| `https://schemas.contoso.edu/diploma2020` | Contoso University에서 발급한 졸업장에 Contoso University가 정의한 속성이 포함됨을 선언합니다. |

세 가지 형식을 모두 선언하여 Contoso University의 졸업장을 사용하여 검증 기관의 다양한 요청을 충족할 수 있습니다. 은행은 사용자에게 `EducationCredential` 세트를 요청할 수 있으며 졸업장을 사용하여 요청을 충족할 수 있습니다. 그러나 Contoso University 동문회는 `https://schemas.contoso.edu/diploma2020` 형식의 자격 증명을 요청할 수 있으며, 졸업장은 또한 요청을 충족합니다.

자격 증명의 상호 운용성을 보장하려면 관련 조직과 긴밀히 협력하여 업계에서 사용할 자격 증명 형식, 스키마 및 URI를 정의하는 것이 좋습니다. 많은 업계 기관이 확인 가능한 자격 증명의 내용을 정의하기 위해 용도를 변경할 수 있는 공식 문서의 구조에 대한 지침을 제공합니다. 또한 자격 증명 검증자와 긴밀히 협력하여 확인 가능한 자격 증명을 요청하고 사용하는 방법을 이해해야 합니다.

## <a name="input-type-verifiable-credential"></a>입력 형식: 확인 가능한 자격 증명

>[!NOTE]
>확인 가능한 자격 증명을 요청하는 규칙 파일은 자격 증명 요청에 프레젠테이션 교환 형식을 사용하지 않습니다. 발급 서비스가 표준 자격 증명 매니페스트를 지원하면 업데이트됩니다. 

```json
{
    "attestations": {
      "presentations": [
        {
          "mapping": {
            "first_name": {
              "claim": "$.vc.credentialSubject.firstName",
            },
            "last_name": {
              "claim": "$.vc.credentialSubject.lastName",
              "indexed": true
            }
          },
          "credentialType": "VerifiedCredentialNinja",
          "contracts": [
            "https://beta.did.msidentity.com/v1.0/3c32ed40-8a10-465b-8ba4-0b1e86882668/verifiableCredential/contracts/VerifiedCredentialNinja"
          ],
          "issuers": [
            {
              "iss": "did:ion:123"
            }
          ]
        }
      ]
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }
  ```

| 속성 | 설명 |
| -------- | ----------- |
| `attestations.presentations` | 입력으로 요청되는 확인 가능한 자격 증명의 배열입니다. |
| `...mapping` | 각 제시된 확인 가능한 자격 증명의 클레임이 결과로 확인 가능한 자격 증명의 특성에 매핑되는 방식을 설명하는 개체입니다. |
| `...mapping.{attribute-name}` | 결과로 확인 가능한 자격 증명에 채워져야 하는 속성입니다. |
| `...mapping.{attribute-name}.claim` | 속성을 채우는 데 값을 사용해야 하는 확인 가능한 자격 증명의 클레임입니다. |
| `...mapping.{attribute-name}.indexed` | 철회하기 위해 저장할 확인 가능한 자격 증명당 하나만 활성화할 수 있습니다. 자세한 내용은 [자격 증명을 철회하는 방법에 대한 문서](how-to-issuer-revoke.md)를 참조하세요. |
| `credentialType` | 사용자에게 제시하도록 요청하는 확인 가능한 자격 증명의 credentialType입니다. |
| `contracts` | 확인 가능한 자격 증명 서비스 포털에 있는 계약의 URI입니다. |
| `issuers.iss` | 사용자에게 요청되는 확인 가능한 자격 증명의 발급자 DID. |
| `validityInterval` | 확인 가능한 자격 증명의 수명을 나타내는 시간(초)입니다. 이 기간이 경과하면 확인 가능한 자격 증명은 더 이상 유효하지 않습니다. 이 값을 생략하면 각 확인 가능한 자격 증명이 명시적으로 철회될 때까지 유효한 상태로 유지됩니다. |
| `vc.type` | 확인 가능한 자격 증명이 충족하는 스키마를 나타내는 문자열의 배열입니다. |


## <a name="input-type-self-attested-claims"></a>입력 형식: 자체 증명 클레임

발급 흐름 중에 사용자에게 일부 자체 증명 정보를 입력하라는 메시지가 표시될 수 있습니다. 현재는 '문자열'이 유일한 입력 형식입니다. 
```json
{
  "attestations": {
    "selfIssued": {
      "mapping": {
        "alias": {
          "claim": "name"
        }
      },
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }



```
| 속성 | 설명 |
| -------- | ----------- |
| `attestations.selfIssued` | 사용자가 입력해야 하는 자체 발급 클레임의 배열입니다. |
| `...mapping` | 각 자체 발급 클레임이 결과로 확인 가능한 자격 증명의 특성에 매핑되는 방식을 설명하는 개체입니다. |
| `...mapping.alias` | 결과로 확인 가능한 자격 증명에 채워져야 하는 속성입니다. |
| `...mapping.alias.claim` | 속성을 채우는 데 값을 사용해야 하는 확인 가능한 자격 증명의 클레임입니다. |
| `validityInterval` | 확인 가능한 자격 증명의 수명을 나타내는 시간(초)입니다. 이 기간이 경과하면 확인 가능한 자격 증명은 더 이상 유효하지 않습니다. 이 값을 생략하면 각 확인 가능한 자격 증명이 명시적으로 철회될 때까지 유효한 상태로 유지됩니다. |
| `vc.type` | 확인 가능한 자격 증명이 충족하는 스키마를 나타내는 문자열의 배열입니다. |


## <a name="display-file-verifiable-credentials-in-microsoft-authenticator"></a>표시 파일: Microsoft Authenticator의 확인 가능한 자격 증명

확인 가능한 자격 증명은 브랜드를 반영하는 데 사용할 수 있는 제한된 옵션 세트를 제공합니다. 이 문서에서는 자격 증명을 사용자 지정하는 방법과 사용자에게 발급된 자격 증명을 멋지게 디자인하기 위한 모범 사례를 제공합니다.

사용자에게 발급된 확인 가능한 자격 증명은 Microsoft Authenticator에서 카드로 표시됩니다. 관리자는 조직의 브랜드에 맞는 카드 색상, 아이콘 및 텍스트 문자열을 선택할 수 있습니다.

![발급 설명서](media/credential-design/detailed-view.png) 

또한 카드에는 사용자에게 카드의 용도, 카드에 포함된 속성 등을 알리는 데 사용할 수 있는 사용자 지정 가능한 필드가 포함되어 있습니다.

## <a name="create-a-credential-display-file"></a>자격 증명 표시 파일 만들기

규칙 파일과 마찬가지로 표시 파일은 확인 가능한 자격 증명의 콘텐츠가 Microsoft Authenticator 앱에 어떻게 표시되는지를 설명하는 간단한 JSON 파일입니다. 

>[!NOTE]
> 현재 이 표시 모델은 Microsoft Authenticator에서만 사용됩니다.

표시 파일의 구조는 다음과 같습니다.

```json
{
    "default": {
      "locale": "en-US",
      "card": {
        "title": "University Graduate",
        "issuedBy": "Contoso University",
        "backgroundColor": "#212121",
        "textColor": "#FFFFFF",
        "logo": {
          "uri": "https://contoso.edu/images/logo.png",
          "description": "Contoso University Logo"
        },
        "description": "This digital diploma is issued to students and alumni of Contoso University."
      },
      "consent": {
        "title": "Do you want to get your digital diploma from Contoso U?",
        "instructions": "Please log in with your Contoso U account to receive your digital diploma."
      },
      "claims": {
        "vc.credentialSubject.name": {
          "type": "String",
          "label": "Name"
        }
      }
    }
}
```

| 속성 | 설명 |
| -------- | ----------- |
| `locale` | 확인 가능한 자격 증명의 언어입니다. 다음에 사용하도록 예약됩니다. | 
| `card.title` | 사용자에게 자격 증명 형식을 표시합니다. 권장 최대 길이는 25자입니다. | 
| `card.issuedBy` | 발급 조직의 이름을 사용자에게 표시합니다. 권장 최대 길이는 40자입니다. |
| `card.backgroundColor` | 16진수 형식으로 카드의 배경색을 결정합니다. 모든 카드에 미묘한 그라데이션이 적용됩니다. |
| `card.textColor` | 16진수 형식으로 카드의 텍스트 색상을 결정합니다. 검은색이나 흰색을 사용하는 것이 좋습니다. |
| `card.logo` | 카드에 표시되는 로고입니다. 제공된 URL은 공개적으로 주소를 지정할 수 있어야 합니다. 휴대 전화 크기에 관계없이 권장되는 최대 높이는 36px, 최대 너비는 100px입니다. 배경이 투명한 PNG가 권장됩니다. | 
| `card.description` | 각 카드 옆에 추가 텍스트가 표시됩니다. 어떤 용도로든 사용할 수 있습니다. 권장 최대 길이는 100자입니다. |
| `consent.title` | 카드가 발급될 때 표시되는 추가 텍스트입니다. 발급 프로세스에 대한 세부 정보를 제공하는 데 사용됩니다. 권장 길이는 100자입니다. |
| `consent.instructions` | 카드가 발급될 때 표시되는 추가 텍스트입니다. 발급 프로세스에 대한 세부 정보를 제공하는 데 사용됩니다. 권장 길이는 100자입니다. |
| `claims` | 각 자격 증명에 포함된 특성의 레이블을 제공할 수 있습니다. |
| `claims.{attribute}` | 레이블이 적용되는 자격 증명 속성을 나타냅니다. |
| `claims.{attribute}.type` | 특성 유형을 나타냅니다. 현재 '문자열'만 지원합니다. |
| `claims.{attribute}.label` | 인증서에 표시될 속성의 레이블로 사용해야 하는 값입니다. 규칙 파일에 사용된 레이블과 다를 수 있습니다. 권장 최대 길이는 40자입니다. |

>[!NOTE]
  >클레임이 규칙 파일에 포함된 다음, 표시 파일에서 생략된 경우, 두 가지 유형의 경험이 있습니다. iOS에서는 클레임이 위 이미지에 표시된 세부 정보 섹션에 표시되지 않지만 Android에서는 클레임이 표시됩니다.  

## <a name="next-steps"></a>다음 단계

이제 확장 가능한 자격 증명 디자인에 대해 더 잘 이해하고 자신의 요구 사항을 충족하는 자신만의 자격 증명을 만드는 방법을 알게 되었습니다.

- [발급자 서비스 통신 예](issuer-openid.md)
