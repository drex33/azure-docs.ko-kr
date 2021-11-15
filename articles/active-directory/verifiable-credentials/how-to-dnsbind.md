---
title: 사용자 도메인을 DID(탈중앙화 ID)(미리 보기)에 연결 - Azure Active Directory 확인 가능한 자격 증명
description: DNS Bind의 방법에 대해 알아볼까요?
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 50954d4fef64ffabddf3ec8898ef81a030a77f95
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577349"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did-preview"></a>도메인을 DID(탈중앙화 ID)에 연결(미리 보기)

> [!IMPORTANT]
> Azure Active Directory 확인 가능한 자격 증명은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서의 내용
> [!div class="checklist"]
> * DID(탈중앙화 ID)를 자신의 도메인에 연결해야 하는 이유는 무엇인가요?
> * DID(탈중앙화 ID)와 도메인을 연결하려면 어떻게 해야 하나요?
> * 도메인을 연결하는 프로세스는 어떻게 작동하나요?
> * 확인되었거나 확인되지 않은 도메인 논리는 각각 어떻게 작동하나요?

## <a name="prerequisites"></a>사전 요구 사항

DID(탈중앙화 ID)를 도메인에 연결하려면 다음을 완료해야 합니다.

- [시작](get-started-verifiable-credentials.md) 및 후속 [자습서 세트](enable-your-tenant-verifiable-credentials.md)를 완료합니다.

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>DID(탈중앙화 ID)를 자신의 도메인에 연결해야 하는 이유는 무엇인가요?

DID(탈중앙화 ID)는 기존 시스템에 고정되지 않은 식별자로 시작합니다. DID(탈중앙화 ID)는 사용자 또는 조직이 소유하고 제어할 수 있기 때문에 유용합니다. 조직과 상호 작용하는 엔터티가 DID(탈중앙화 ID)의 '담당자'를 알지 못한다면 DID은 유용하지 않습니다.

도메인에 DID를 연결하면 모든 엔터티가 해당 계정과 도메인 간의 관계를 암호화하여 확인할 수 있으므로 초기 신뢰 문제가 해결됩니다.

## <a name="how-do-we-link-dids-and-domains"></a>DID(탈중앙화 ID)와 도메인을 연결하려면 어떻게 해야 하나요?

[잘 알려진 DID 구성](https://identity.foundation/.well-known/resources/did-configuration/)이라는 DIF(Decentralized Identity Foundation)에서 작성한 개방형 표준을 구현하여 도메인과 DID의 연결을 설정합니다. Azure AD(Azure Active Directory)의 확인 가능한 자격 증명 서비스를 사용하면 조직에서 사용자가 DID에 제공한 도메인 정보를 포함하고 잘 알려진 구성 파일을 생성하여 DID와 도메인 간의 링크를 만들 수 있습니다.

1. Azure AD는 조직 설치 중에 제공하는 도메인 정보를 사용하여 DID 문서 내에서 서비스 엔드포인트를 작성합니다. DID와 상호 작용하는 모든 당사자는 DID와 연결된 도메인을 볼 수 있습니다.  

    ```json
    "service": [
      {
        "id": "#linkeddomains",
        "type": "LinkedDomains",
        "serviceEndpoint": {
          "origins": [
            "https://www.contoso.com/"
          ]
        }
      }
    ]
    ```

2. Azure AD의 확인 가능한 자격 증명 서비스는 도메인에서 호스트할 수 있는 잘 알려진 구성 리소스를 생성합니다. 구성 파일에는 도메인의 출처를 가진 DID로 서명된 'DomainLinkageCredential'의 자체 발급되어 확인 가능한 자격 증명이 포함되어 있습니다. 루트 도메인 URL에 저장된 구성 문서의 예는 다음과 같습니다.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

잘 알려진 구성 파일을 만든 후에는 확인 가능한 자격 증명을 위해 AAD를 사용하도록 설정할 때 지정한 도메인 이름을 사용하여 파일을 사용할 수 있도록 해야 합니다.

* 도메인의 루트에 있는 잘 알려진 구성 파일을 호스팅합니다.
* 리디렉션을 사용하지 않습니다.
* HTTP를 사용하여 구성 파일을 배포합니다.

>[!IMPORTANT]
>Microsoft Authenticator는 리디렉션을 인식하지 못합니다. 지정된 URL은 최종 대상 URL이어야 합니다.

## <a name="user-experience"></a>사용자 환경 

사용자가 발급 흐름을 진행하거나 확인 가능한 자격 증명을 제공하는 경우 조직 및 조직의 DID에 대한 정보를 알고 있어야 합니다. 도메인이 확인 가능한 자격 증명 전자 지갑인 Microsoft Authenticator인 경우 사용된 문서에서 도메인과 DID의 관계의 유효성을 검사하고 결과에 따라 사용자에게 두 가지 다른 환경을 제공합니다.

## <a name="verified-domain"></a>확인된 도메인

**확인됨** 아이콘이 Microsoft Authenticator에 표시되려면 몇 가지 사항이 충족되어야 합니다.

* 자동 발급된 SIOP(Self-Issued OpenID) 요청을 서명하는 DID에는 연결된 도메인에 대한 서비스 엔드포인트가 있어야 합니다.
* 루트 도메인은 리디렉션을 사용하지 않으며 HTTP를 사용합니다.
* DID 문서에 나열된 도메인에는 확인 가능한 잘 알려진 리소스가 있습니다.
* 잘 알려진 리소스의 확인 가능한 자격 증명은 Microsoft Authenticator가 흐름을 시작하는 데 사용한 SIOP에 서명하는 데 사용된 것과 동일한 DID로 서명됩니다.

앞에서 설명한 모든 조건이 충족되면 Microsoft Authenticator에는 확인된 페이지가 표시되고 해당 도메인의 유효성이 검사됩니다.

![새 권한 요청](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>확인되지 않은 도메인

위의 조건 중 하나라도 충족되지 않으면 Microsoft Authenticator는 사용자에게 해당 도메인은 확인되지 않은 도메인이며 사용자가 위험한 트랜잭션을 수행하고 있으니 주의해야 한다는 경고가 전체 페이지에 표시됩니다. 다음과 같은 이유로 이 경로를 사용하도록 선택했습니다.

* DID가 도메인에 고정되어 있지 않습니다.
* 구성이 제대로 설정되지 않았습니다.
* 사용자가 상호 작용하는 DID는 악의적이며 도메인을 소유하고 있다고 증명할 수 없습니다(실제로 소유하고 있지 않음). 마지막 이유에 따라 경고 메시지가 증식하지 않도록 사용자가 익숙한 도메인에 DID를 연결하는 것은 필수입니다.

![자격 증명 추가 화면에서 확인되지 않은 도메인 경고](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>잘 알려진 구성 배포

1. 확인 가능한 자격 증명의 설정 페이지로 이동하고 **이 도메인 확인** 을 선택합니다.

   ![설정에서 이 도메인 확인](media/how-to-dnsbind/settings-verify.png) 

2. 아래 이미지에 표시된 did-configuration.json 파일을 다운로드합니다.

   ![잘 알려진 구성 다운로드](media/how-to-dnsbind/verify-download.png) 

3. linked_did 값(JWT)을 복사하고, [https://jwt.ms/](https://www.jwt.ms)를 열고, JWT를 붙여넣고, 도메인이 올바른지 확인합니다.

4. DID를 복사하고 [ION 네트워크 탐색기](https://identity.foundation/ion/explorer)를 열어 동일한 도메인이 이미 사용한 문서에 포함되어 있는지 확인합니다. 

5. 지정된 위치에서 잘 알려진 구성 리소스를 호스팅합니다. 예: `https://www.example.com/.well-known/did-configuration.json`

6. Microsoft Authenticator를 통해 발행 및 제시를 테스트하여 유효성을 검사합니다. Authenticator '안전하지 않은 앱에 대한 경고'가 설정되어 있는지 확인합니다.

>[!NOTE]
>기본값으로 '안전하지 않은 앱 경고'가 설정되어 있습니다.

축하합니다. 이제 DID로 사용자의 신뢰를 웹에 부트스트랩했습니다.

## <a name="next-steps"></a>다음 단계

온보딩 과정에서 잘못된 도메인 정보를 입력하거나 변경한 경우에는 [옵트아웃](how-to-opt-out.md)해야 합니다. 지금은 DID 문서 업데이트를 지원하지 않습니다. 옵트아웃하고 다시 옵트인하면 새로운 DID가 만들어집니다.
