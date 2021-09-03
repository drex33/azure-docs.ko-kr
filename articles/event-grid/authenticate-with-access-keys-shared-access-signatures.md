---
title: 액세스 키 또는 공유 액세스 서명을 사용하여 Azure Event Grid 게시 클라이언트 인증
description: 이 문서에서는 액세스 키 및 공유 액세스 서명을 사용하여 Azure Event Grid 게시 클라이언트를 인증하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 5a271372afcc36df97d0a7afa37ee0b065a5a5e6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536075"
---
# <a name="authenticate-azure-event-grid-publishing-clients-using-access-keys-or-shared-access-signatures"></a>액세스 키 또는 공유 액세스 서명을 사용하여 Azure Event Grid 게시 클라이언트 인증
이 문서에서는 **액세스 키** 또는 **SAS(공유 액세스 서명)** 토큰을 사용하여 Azure Event Grid 토픽, 도메인, 파트너 네임스페이스에 이벤트를 게시하는 클라이언트 인증에 대한 정보를 제공합니다. 

> [!IMPORTANT]
> Azure AD ID를 사용하여 사용자 또는 애플리케이션을 인증하고 권한을 부여하면 키 기반 및 SAS(공유 액세스 서명) 인증보다 뛰어난 보안과 사용 편의성을 제공합니다. Azure AD를 사용하면 인증에 사용되는 비밀을 코드에 저장할 필요가 없으며 잠재적인 보안 취약성의 위험이 있습니다. Azure Event Grid 이벤트 게시 애플리케이션과 함께 Azure AD를 사용하는 것이 좋습니다. 자세한 내용은 [Azure Active Directory를 사용하여 게시 클라이언트 인증](authenticate-with-active-directory.md)을 참조하세요.

## <a name="authenticate-using-access-key"></a>액세스 키를 사용하여 인증
액세스 키 인증은 가장 간단한 인증 형태입니다. 액세스 키를 HTTP 헤더 또는 URL 쿼리 매개 변수로 전달할 수 있습니다. 

### <a name="access-key-in-an-http-header"></a>HTTP 헤더의 액세스 키
액세스 키를 HTTP 헤더의 `aeg-sas-key` 값으로 전달합니다.

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>쿼리 매개 변수로서의 액세스 키
`aeg-sas-key`를 쿼리 매개 변수로 지정할 수도 있습니다. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

토픽 또는 도메인에 대한 액세스 키를 가져오는 방법에 대한 지침은 [액세스 키 가져오기](get-access-keys.md)를 참조하세요.

## <a name="authenticate-using-sas"></a>SAS를 사용하여 인증 
Event Grid 리소스에 대한 SAS 토큰에는 리소스, 만료 시간 및 서명이 포함됩니다. SAS 토큰의 형식은 다음과 같습니다. `r={resource}&e={expiration}&s={signature}`

리소스는 이벤트를 전송할 Event Grid 항목의 경로입니다. 예를 들어 올바른 리소스 경로는 `https://<yourtopic>.<region>.eventgrid.azure.net/api/events`입니다. 지원되는 모든 API 버전을 보려면 [Microsoft.EventGrid 리소스 형식](/azure/templates/microsoft.eventgrid/allversions)을 참조하세요. 

첫째, 프로그래밍 방식으로 SAS 토큰을 생성 한 다음 `aeg-sas-token` 헤더 또는 `Authorization SharedAccessSignature` 헤더를 사용하여 Event Grid로 인증합니다. 

### <a name="generate-sas-token-programmatically"></a>프로그래밍 방식으로 SAS 토큰 생성
다음 예제에서는 Event Grid를 사용하기 위한 SAS 토큰을 만듭니다.

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

```python
def generate_sas_token(uri, key, expiry=3600):
    ttl = datetime.datetime.utcnow() + datetime.timedelta(seconds=expiry)
    encoded_resource = urllib.parse.quote_plus(uri)
    encoded_expiration_utc = urllib.parse.quote_plus(ttl.isoformat())

    unsigned_sas = f'r={encoded_resource}&e={encoded_expiration_utc}'
    signature = b64encode(HMAC(b64decode(key), unsigned_sas.encode('utf-8'), sha256).digest())
    encoded_signature = urllib.parse.quote_plus(signature)
    
    token = f'r={encoded_resource}&e={encoded_expiration_utc}&s={encoded_signature}'

    return token
```

### <a name="using-aeg-sas-token-header"></a>aeg-sas-token 헤더 사용
다음은 `aeg-sas-token` 헤더에 대한 값으로 SAS 토큰을 전달하는 예입니다. 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>인증 헤더 사용
다음은 `Authorization` 헤더에 대한 값으로 SAS 토큰을 전달하는 예입니다. 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>다음 단계
이벤트를 전달하는 이벤트 처리기를 사용한 인증에 대해 알아보려면 [이벤트 배달 인증](security-authentication.md) 을 참조하세요. 
