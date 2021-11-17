---
title: Azure Video Analyzer 액세스 정책
description: 이 문서에서는 Azure Video Analyzer가 액세스 정책에서 JWT 토큰을 사용하여 비디오를 보호하는 방법을 설명합니다.
ms.topic: reference
ms.date: 11/04/2021
ms.openlocfilehash: 9d9a0f4db83d132fae5bafb2a25d25075296b4ad
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486133"
---
# <a name="access-policies"></a>액세스 정책

액세스 정책은 지정된 Video Analyzer 비디오 리소스에 대한 액세스 권한 및 기간을 정의합니다. 이러한 액세스 정책을 통해 타사(비 AAD 클라이언트) JWT 토큰을 사용하여 다음을 사용하도록 설정하는 클라이언트 API에 대한 권한 부여를 제공할 수 있으므로 제어 및 유연성이 향상됩니다. 

- 비디오 메타데이터에 대한 액세스 
- 비디오 스트리밍에 대한 액세스 

## <a name="access-policy-definition"></a>액세스 정책 정의

```json
"name": "accesspolicyname1", 
"properties": { 
    "role": "Reader", 
    "authentication": { 
        "@type": "#Microsoft.VideoAnalyzer.JwtAuthentication", 
        "issuers": [ 
            "issuer1", 
            "issuer2" 
        ], 
        "audiences": [ 
            "audience1" 
        ], 
        "claims": [ 
            { 
                "name":"claimname1", 
                "value":"claimvalue1" 
            }, 
            { 
                "name":"claimname2", 
                "value":"claimvalue2" 
            } 
        ], 
        "keys": [ 
            { 
                "@type": "#Microsoft.VideoAnalyzer.RsaTokenKey", 
                "alg": "RS256", 
                "kid": "123", 
                "n": "YmFzZTY0IQ==", 
                "e": "ZLFzZTY0IQ==" 
            }, 
            { 
                "@type": "#Microsoft.VideoAnalyzer.EccTokenKey", 
                "alg": "ES256", 
                "kid": "124", 
                "x": "XX==", 
                "y": "YY==" 
            } 
        ] 
    } 
} 
```

> [!NOTE] 
> 키 유형은 하나만 필요합니다. 

### <a name="roles"></a>역할

현재 읽기 권한자 역할만 지원됩니다.

### <a name="issuer-matching-rules"></a>발급자 일치 규칙

여러 문제를 정책에 지정할 수 있으며 토큰에는 단일 발급자를 지정할 수 있습니다.  토큰 발급자가 정책에 지정된 발급자 중 하나인 경우 발급자가 일치합니다.

### <a name="audience-matching-rules"></a>대상 그룹 일치 규칙

대상 그룹 값이 비디오 리소스에 대해 ${System.Runtime.BaseResourceUrlPattern}인 경우 JWT 토큰에 제공된 대상은 기본 리소스 URL과 일치해야 합니다. 그렇지 않으면 토큰 대상 그룹이 액세스 정책의 대상 그룹과 일치해야 합니다.

### <a name="claims-matching-rules"></a>클레임 일치 규칙

액세스 정책 및 JWT 토큰에 여러 클레임을 지정할 수 있습니다.  유효성 검사를 통과하기 위해 토큰에 액세스 정책의 모든 클레임을 제공해야 하지만 JWT 토큰에는 액세스 정책에 나열되지 않은 추가 클레임이 있을 수 있습니다.

### <a name="keys"></a>구성

RSA 및 ECC 형식이라는 두 가지 유형의 키가 지원됩니다.

[RSA](https://wikipedia.org/wiki/RSA_(cryptosystem))

* @type- \#Microsoft.VideoAnalyzer.RsaTokenKey
* alg - 알고리즘  256, 384 또는 512가 될 수 있습니다. 
* kid - 키 ID
* n - 모듈러스
* e - 공용 지수 

[ECC](https://wikipedia.org/wiki/Elliptic-curve_cryptography)        

* @type- \#Microsoft.VideoAnalyzer.EccTokenKey
* alg - 알고리즘  256, 384 또는 512가 될 수 있습니다.
* kid - 키 ID
* x - 좌표 값
* y - 좌표 값

### <a name="token-validation-process"></a>토큰 유효성 검사 프로세스

고객은 자체 JWT 토큰을 만들어야 하며 다음 방법을 사용하여 유효성을 검사합니다.

- 키 ID와 일치하는 정책 목록에서 유효성을 검사합니다.
  - 토큰 서명
  - 토큰 만료
  - 발급자
  - 사용자
  - 추가 클레임

### <a name="policy-audience-and-token-matching-examples"></a>정책 대상 그룹 및 토큰 일치 예제:

| **정책 대상 그룹**                      | 요청된 URL                         | 토큰 URL                            | 결과 |
| ---------------------------------------- | ------------------------------------- | ------------------------------------ | ------ |
| (모든 리터럴)                            | (임의)                                 | (일치)                              | 허용  |
| (모든 리터럴)                            | (임의)                                 | (일치하지 않음)                          | 거부   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/*                | 허용  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/{videoName}      | 거부   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/vid*                    | 허용  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/*                | 허용  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{baseVideoName}* | 허용  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{videoName}      | 허용  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}Suffix | https://fqdn/videos/{videoName}      | 거부   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{otherVideoName}  | https://fqdn/videos/{videoName}      | 거부   |

> [!NOTE]  
> Video Analyzer는 최대 20개의 정책을 지원합니다.  ${System.Runtime.BaseResourceUrlPattern}을 사용하면 하나의 액세스 정책 및 여러 토큰을 사용하여 특정 리소스에 보다 유연하게 액세스할 수 있습니다.  그러면 이러한 토큰을 통해 대상 그룹을 기반으로 하는 다양한 Video Analyzer 리소스에 액세스할 수 있습니다. 

## <a name="creating-a-token"></a>토큰 만들기

이 섹션에서는 문서의 후반부에서 사용할 JWT 토큰을 만듭니다.  JWT 토큰을 생성하는 샘플 애플리케이션을 사용하고 액세스 정책을 만드는 데 필요한 모든 필드를 제공합니다.

> [!NOTE] 
> RSA 또는 ECC 인증서를 기반으로 JWT 토큰을 생성하는 방법을 잘 알고 있는 경우 이 섹션을 건너뛸 수 있습니다.

1. [AVA C# 샘플 리포지토리](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)를 복제합니다. 그런 다음, JWTTokenIssuer 애플리케이션 폴더 *src/jwt-token-issuer로* 이동하여 JWTTokenIssuer 애플리케이션을 찾습니다.
2. Visual Studio Code를 연 다음 JWTTokenIssuer 애플리케이션을 다운로드한 폴더로 이동합니다. 이 폴더에는 *\*.csproj* 파일이 있어야 합니다.
3. 탐색기 창에서 *program.cs* 파일로 이동합니다.
4. 77줄에서 대상을 Video Analyzer 엔드포인트로 변경한 후 /videos/ \* 를 하여 다음과 같이 보입니다.

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```

   > [!NOTE] 
   > Video Analyzer 엔드포인트는 Azure Portal Video Analyzer 리소스의 개요 섹션에서 찾을 수 있습니다.

   :::image type="content" source="media/player-widget/client-api-url.png" alt-text="플레이어 위젯 엔드포인트를 보여 주는 스크린샷.":::
    
5. 78줄에서 발급자 값을 인증서의 발급자 값으로 변경합니다. 예: `https://contoso.com`
6. 파일을 저장합니다.    

   > [!NOTE]
   > 선택 메시지가 표시될 수 `Required assets to build and debug are missing from 'jwt token issuer'. Add them?` `Yes` 있습니다.
   
   :::image type="content" source="media/player-widget/visual-studio-code-required-assets.png" alt-text="Visual Studio Code의 필수 자산 프롬프트를 보여 주는 스크린샷.":::
   
7. 명령 프롬프트 창을 열고 JWTTokenIssuer 파일이 있는 폴더로 이동합니다. 두 명령 `dotnet build` 다음에 `dotnet run`을 실행합니다. Visual Studio Code에 C# 확장이 있는 경우 F5를 선택하여 JWTTokenIssuer 애플리케이션을 실행할 수도 있습니다.

애플리케이션이 빌드된 다음 실행됩니다. 빌드 후 자체 서명된 인증서를 만들고 해당 인증서에서 JWT 토큰 정보를 만듭니다. 또한 JWTTokenIssuer가 빌드된 디렉터리의 debug 폴더에 있는 JWTTokenIssuer.exe 파일을 실행할 수도 있습니다. 애플리케이션을 실행하는 이점은 다음과 같이 입력 옵션을 지정할 수 있다는 것입니다.

- `JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]`

JWTTokenIssuer는 JWT 토큰과 다음과 같은 필수 구성 요소를 만듭니다.

- `Issuer`, `Audience`, `Key Type`, `Algorithm`, `Key Id`, `RSA Key Modulus`, `RSA Key Exponent`, `Token`

나중에 사용할 수 있도록 이러한 값을 복사해야 합니다.


## <a name="creating-an-access-policy"></a>액세스 정책 만들기

액세스 정책을 만드는 방법에는 다음 두 가지가 있습니다.

### <a name="in-the-azure-portal"></a>Azure Portal에서

1. Azure Portal에 로그인 하 고 비디오 분석기 계정이 있는 리소스 그룹으로 이동 합니다.
1. Video Analyzer 리소스를 선택합니다.
1. **Video Analyzer** 아래에서 **액세스 정책** 을 선택 합니다.

   :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="플레이어 위젯-포털 액세스 정책.":::
   
1. **새로 만들기** 를 선택 하 고 다음 정보를 입력 합니다.

   > [!NOTE] 
   > 이러한 값은 이전 단계에서 만든 JWTTokenIssuer 애플리케이션에서 제공됩니다.

   - 액세스 정책 이름 - 임의의 이름입니다.

   - 발급자 - JWT 토큰 발급자와 일치해야 합니다. 

   - JWT 토큰의 대상 그룹--기본값입니다 `${System.Runtime.BaseResourceUrlPattern}` .

   - 키 유형-RSA 

   - 알고리즘 - 지원되는 값은 RS256, RS384, RS512입니다.

   - 키 ID-인증서에서 생성 됩니다. 자세한 내용은 [토큰 만들기](#creating-a-token)를 참조하세요.

   - RSA 키 모듈러스-인증서에서 생성 됩니다. 자세한 내용은 [토큰 만들기](#creating-a-token)를 참조하세요.

   - RSA 키 지 수-인증서에서 생성 됩니다. 자세한 내용은 [토큰 만들기](#creating-a-token)를 참조하세요.

   :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="플레이어 위젯 - 액세스 정책 포털"::: 
   
1. **저장** 을 선택합니다.
### <a name="create-access-policy-via-api"></a>API를 통해 액세스 정책 만들기

ARM(Azure Resource Manager) API 참조 

## <a name="next-steps"></a>다음 단계

[개요](overview.md)
