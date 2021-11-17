---
title: 확인 가능한 자격 증명을 발급자로 해지하는 방법 - Azure Active Directory 확인 가능한 자격 증명(미리 보기)
description: 발급한 확인 가능한 자격 증명을 해지하는 방법 알아보기
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 8548410c2df423f8635092002401501705fb24a8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006834"
---
# <a name="revoke-a-previously-issued-verifiable-credential-preview"></a>이전에 발급한 확인 가능한 자격 증명(미리 보기) 해지

VC(확인 가능한 자격 증명)를 사용하는 프로세스의 일부로 자격 증명을 발급해야 할 뿐만 아니라 자격 증명을 해지해야 하는 경우도 있습니다. 이 문서에서는 VC 사양의 **Status** 속성 부분을 살펴보고 해지 프로세스, 자격 증명을 해지해야 하는 이유 및 일부 데이터 및 개인 정보 보호에 미치는 영향을 자세히 살펴봅니다.

> [!IMPORTANT]
> Azure Active Directory 확인 가능한 자격 증명은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="status-property-in-verifiable-credentials-specification"></a>확인 가능한 자격 증명 사양의 상태 속성

확인 가능한 자격 증명을 해지하는 경우의 의미를 이해하려면 **상태 확인** 이 무엇이며 현재 어떻게 작동하는지를 파악하는 데 도움이 될 수 있습니다.

[W3C 확인 가능한 자격 증명 사양](https://www.w3.org/TR/vc-data-model/)은 섹션 [4.9](https://www.w3.org/TR/vc-data-model/#status)의 **status** 속성을 참조합니다.

"이 사양은 확인 가능한 자격 증명의 현재 상태(예: 일시 중지 또는 해지 여부)에 대한 정보 검색을 위해 다음 **credentialStatus** 속성을 정의합니다."

그러나 W3C 사양은 **상태 확인** 구현 방법에 대한 형식을 정의하지 않습니다.

"상태 체계에 대한 데이터 모델, 형식 및 프로토콜을 정의하는 것은 이 사양의 범위를 벗어납니다. 확인 가능한 자격 증명 상태 검사를 구현하려는 구현자를 위해 사용 가능한 상태 체계를 포함하는 확인 가능한 자격 증명 확장 레지스트리 [VC-EXTENSION-REGISTRY]가 있습니다."

>[!NOTE]
>현재 Microsoft의 상태 확인 구현은 독점적이지만, 아직 표준에 맞게 DID 커뮤니티를 사용하고 있습니다.

## <a name="how-does-the-status-property-work"></a>**status** 속성은 어떻게 작동하나요?

모든 Microsoft에서 확인 가능한 자격 증명에는 credentialStatus라는 특성이 있습니다. 이 특성은 Microsoft가 사용자를 대신하여 관리하는 상태 API로 채워집니다. 다음은 이에 대한 예입니다.

```json
    "credentialStatus": {
      "id": "https://portableidentitycards.azure-api.net/v1.0/7952032d-d1f3-4c65-993f-1112dab7e191/portableIdentities/card/status",
      "type": "PortableIdentityCardServiceCredentialStatus2020"
    }
```

오픈 소스 확인 가능한 자격 증명 SDK는 상태 API 호출을 처리하고 필요한 데이터를 제공합니다.

API를 호출하고 올바른 정보를 제공하면 API는 True 또는 False를 반환합니다. 확인 가능한 자격 증명이 발급자와 함께 활성 상태이고 확인 가능한 자격 증명이 발급자에 의해 적극적으로 해지되었음을 나타내는 False인 경우 True입니다.

## <a name="why-you-may-want-to-revoke-a-vc"></a>VC를 해지하려는 이유는 무엇인가요?

각 고객은 확인 가능한 자격 증명을 해지하려는 고유한 이유가 있지만 지금까지 몇 가지 일반적인 테마를 제공합니다. 

- 학생 ID: 이 학생은 더 이상 대학에서 활성 학생이 아닙니다.
- 직원 ID: 이 직원은 더 이상 활성 직원이 아닙니다.
- 상태 드라이버 라이선스: 이 드라이버가 더 이상 해당 상태에 있지 않습니다.

## <a name="how-to-set-up-a-verifiable-credential-with-the-ability-to-revoke"></a>해지할 수 있는 권한으로 확인 가능한 자격 증명을 설정하는 방법

모든 확인 가능한 자격 증명 데이터는 기본적으로 Microsoft에 저장되지 않습니다. 따라서 확인 가능한 특정 자격 증명 ID를 해지하기 위해 참조할 데이터가 없습니다. 발급자는 Microsoft에서 인덱싱할 수 있는 자격 증명 특성의 특정 필드를 지정하고 이후에 솔트 및 해시를 지정해야 합니다.

>[!NOTE]
>해싱은 ```preimage```라고 하는 입력을 바꾸고 고정 길이를 가진 해시라는 출력을 생성하는 단방향 암호화 작업입니다. 지금은 해시 작업을 되돌리는 것이 불가능합니다.

인덱싱하고 싶은 확인 가능한 자격 증명의 특성을 Microsoft에 알려줄 수 있습니다. 인덱싱의 의미는 인덱싱된 값을 사용하여 해지할 VC에 대해 확인 가능한 자격 증명을 검색하는 것입니다.

**예:** Alice는 Woodgrove 직원입니다. Woodgrove는 Contoso에서 근무하고 있습니다. Woodgrove의 IT 관리자인 Jane은 확인 가능한 자격 증명 해지 검색 쿼리에서 Alice의 이메일을 검색합니다. 이 예에서 Jane은 Woodgrove에서 확인된 직원 자격 증명의 이메일 필드를 인덱싱했습니다. 

인덱스를 포함하도록 규칙 파일을 수정하는 방법에 대한 예는 아래를 참조하세요.

```json
{
  "attestations": {
    "idTokens": [
      { 
        "mapping": {
          "Name": { "claim": "name" },
          "email": { "claim": "email", "indexed": true}
        },
        "configuration": "https://login.microsoftonline.com/tenant-id-here7/v2.0/.well-known/openid-configuration",
        "client_id": "c0d6b785-7a08-494e-8f63-c30744c3be2f",
        "redirect_uri": "vcclient://openid"
      }
    ]
  },
  "validityInterval": 25920000,
  "vc": {
    "type": ["WoodgroveEmployee"]
  }
}
```

>[!NOTE]
>규칙 파일에서 하나의 특성만 인덱싱할 수 있습니다.  

## <a name="how-do-i-revoke-a-verifiable-credential"></a>확인 가능한 자격 증명을 해지하는 방법

인덱스 클레임이 설정되고 사용자에게 확인 가능한 자격 증명이 발급된 후에는 VC 블레이드에서 확인 가능한 자격 증명을 해지하는 방법을 확인할 수 있습니다.

1. Azure Active Directory에서 확인 가능한 자격 증명 블레이드로 이동합니다.
1. 이전에 인덱스 클레임을 설정하고 사용자에게 확인 가능한 자격 증명을 발급한 확인 가능한 자격 증명을 선택합니다. =
1. 왼쪽 메뉴에서 **자격 증명 해지**
   ![자격 증명 해지](media/how-to-issuer-revoke/settings-revoke.png)를 선택합니다. 
1. 해지하려는 사용자의 인덱스 특성을 검색합니다. 

   ![해지할 자격 증명 찾기](media/how-to-issuer-revoke/revoke-search.png)

    >[!NOTE]
    >확인 가능한 자격 증명에서 인덱싱된 클레임의 해시만 저장하기 때문에 정확히 일치하는 경우에만 검색 결과가 채워집니다. IT 관리자가 검색한 입력을 사용하고 동일한 해시 알고리즘을 사용하여 데이터베이스에 해시 일치가 있는지 확인합니다.
    
1. 일치하는 항목을 찾으면 해지하려는 자격 증명 오른쪽에 있는 **해지** 옵션을 선택합니다.

   ![해지 후에도 사용자에게 자격 증명이 있음을 알리는 경고](media/how-to-issuer-revoke/warning.png) 

1. 해지가 성공하면 상태 업데이트가 표시되고 페이지 맨 위에 녹색 배너가 표시됩니다. 
   ![설정에서 이 도메인 확인](media/how-to-issuer-revoke/revoke-successful.png) 

이제 신뢰 당사자가 이 특정 확인 가능한 자격 증명의 상태를 확인하기 위해 호출할 때마다 테넌트를 대신하여 작동하는 Microsoft의 상태 API가 'false' 응답을 반환합니다.

## <a name="next-steps"></a>다음 단계

흐름에 익숙해지도록 테스트 자격 증명을 사용하여 자체적으로 기능을 테스트합니다. [자습서를 검토](get-started-verifiable-credentials.md)하여 검증 가능한 자격 증명을 발급하도록 테넌트를 구성하는 방법에 대한 정보를 볼 수 있습니다.