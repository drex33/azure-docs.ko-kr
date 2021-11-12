---
title: Azure 앱 Configuration REST API-HMAC 인증
description: HMAC를 사용 하 여 Azure 앱 구성에 인증 REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: a301e57563d7a496ea4a0c0a6fbf17da165b9357
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370558"
---
# <a name="hmac-authentication---rest-api-reference"></a>HMAC 인증-REST API 참조

HMAC-SHA256 인증 체계를 사용 하 여 HTTP 요청을 인증할 수 있습니다. HMAC는 해시 기반 메시지 인증 코드를 나타냅니다. 이러한 요청은 TLS를 통해 전송 되어야 합니다.

## <a name="prerequisites"></a>필수 조건

- **증명서** - \<Access Key ID\>
- **비밀** -Base64 디코딩된 액세스 키 값입니다. ``base64_decode(<Access Key Value>)``

자격 증명 (라고도 함 `id` ) 및 비밀 (라고도 함)의 값은 `value` Azure 앱 구성의 인스턴스에서 가져와야 합니다. [Azure Portal](https://portal.azure.com) 또는 [Azure CLI](/cli/azure/)를 사용 하 여이 작업을 수행할 수 있습니다.

인증에 필요한 모든 HTTP 헤더를 각 요청에 제공합니다. 필요한 최소는 다음과 같습니다.

|  요청 헤더 | Description  |
| --------------- | ------------ |
| **Host** | 인터넷 호스트 및 포트 번호입니다. 자세한 내용은  [3.2.2](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.2.2)섹션을 참조 하세요. |
| **날짜** | 요청이 시작 된 날짜 및 시간입니다. 현재 협정 세계시 (그리니치 표준시)에서 15 분을 초과할 수 없습니다. 이 값은 [3.3.1](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.3.1)섹션에 설명 된 것 처럼 HTTP 날짜입니다.
| **x-ms-date** | 위와 동일 ```Date``` 합니다. 에이전트가 요청 헤더에 직접 액세스할 수 없는 경우에는이를 대신 사용할 수 있습니다 ```Date``` . 그렇지 않으면 프록시가이를 수정할 수 있습니다. ```x-ms-date```및 ```Date``` 가 둘 다 제공 되는 경우에는가 ```x-ms-date``` 우선적으로 적용 됩니다. |
| **x-ms-content-sha256** | 요청 본문의 b a s e 64로 인코딩된 SHA256 해시입니다. 본문이 없는 경우에도 제공 되어야 합니다. ```base64_encode(SHA256(body))```|
| **권한 부여** | HMAC-SHA256 체계에서 요구 하는 인증 정보입니다. 형식 및 세부 정보는이 문서의 뒷부분에 설명 되어 있습니다. |

**예제:**

```http
Host: {myconfig}.azconfig.io
Date: Fri, 11 May 2018 18:48:36 GMT
x-ms-content-sha256: {SHA256 hash of the request body}
Authorization: HMAC-SHA256 Credential={Access Key ID}&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature={Signature}
```

## <a name="authorization-header"></a>권한 부여 헤더

### <a name="syntax"></a>Syntax

``Authorization``: **HMAC-SHA256** ```Credential```=\<value\>&```SignedHeaders```=\<value\>&```Signature```=\<value\>

|  인수 | Description  |
| ------ | ------ |
| **HMAC-SHA256** | 권한 부여 체계. _하다_ |
| **자격 증명** | 서명을 계산 하는 데 사용 되는 선택 키의 ID입니다. _하다_ |
| **서명 헤더** | 서명에 추가 된 HTTP 요청 헤더입니다. _하다_ |
| **서명** | 문자열을 서명 하는 b a s e 64로 인코딩된 HMACSHA256. _하다_|

### <a name="credential"></a>자격 증명

서명을 계산 하는 데 사용 되는 액세스 키의 ID입니다.

### <a name="signed-headers"></a>서명 된 헤더

요청에 서명 하는 데 필요한 HTTP 요청 헤더 이름 (세미콜론으로 구분)입니다. 이러한 HTTP 헤더는 요청에도 올바르게 제공 되어야 합니다. 공백은 사용 하지 마십시오.

### <a name="required-http-request-headers"></a>필요한 HTTP 요청 헤더

`x-ms-date`[또는 `Date` ]; `host` ;`x-ms-content-sha256`

다른 모든 HTTP 요청 헤더를 서명에 추가할 수도 있습니다. 인수에 추가 하기만 하면 ```SignedHeaders``` 됩니다.

**예제:**

x-y; host; host-a-sha256; ```Content-Type``` ;```Accept```

### <a name="signature"></a>서명

서명 문자열의 Base64 인코딩 HMACSHA256 해시입니다. 로 식별 된 액세스 키를 사용 `Credential` 합니다. `base64_encode(HMACSHA256(String-To-Sign, Secret))`

### <a name="string-to-sign"></a>서명 문자열

요청에 대 한 정식 표현입니다.

_문자열-서명 =_

**HTTP_METHOD** + ' \n ' + **path_and_query** + ' \n ' + **signed_headers_values**

|  인수 | Description  |
| ------ | ------ |
| **HTTP_METHOD** | 요청에 사용 되는 대문자 HTTP 메서드 이름입니다. 자세한 내용은 [섹션 9](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)를 참조 하세요. |
|**path_and_query** | 요청 절대 URI 경로 및 쿼리 문자열의 연결입니다. 자세한 내용은 [섹션 3.3](https://tools.ietf.org/html/rfc3986#section-3.3)을 참조 하세요.
| **signed_headers_values** | 에 나열 된 모든 HTTP 요청 헤더의 세미콜론으로 구분 된 값입니다 `SignedHeaders` . 형식은 `SignedHeaders` 의미 체계를 따릅니다. |

**예제:**

```js
string-To-Sign=
            "GET" + '\n' +                                                                                  // VERB
            "/kv?fields=*&api-version=1.0" + '\n' +                                                         // path_and_query
            "Fri, 11 May 2018 18:48:36 GMT;{myconfig}.azconfig.io;{value of ms-content-sha256 header}"      // signed_headers_values
```


### <a name="errors"></a>오류

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**이유:** HMAC-SHA256 스키마를 사용 하는 권한 부여 요청 헤더는 제공 되지 않습니다.

**해결 방법:** 유효한 ```Authorization``` HTTP 요청 헤더를 제공합니다.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="The access token has expired", Bearer
```

**이유:** ```Date``` 또는 ```x-ms-date``` 요청 헤더가 현재 협정 세계시 (그리니치 표준시)에서 15 분을 초과 합니다.

**해결 방법:** 올바른 날짜와 시간을 입력 합니다.


```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid access token date", Bearer
```

**이유:** ```Date``` 또는 요청 헤더가 없거나 잘못 되었습니다 ```x-ms-date``` .

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="[Credential][SignedHeaders][Signature] is required", Bearer
```

**이유:** 요청 헤더에서 필수 매개 변수가 누락 되었습니다 ```Authorization``` .

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Credential", Bearer
```

**이유:** 제공 된 [ ```Host``` ]/[액세스 키 ID]를 찾을 수 없습니다.

**해결 방법:** ```Credential``` 요청 헤더의 매개 변수를 확인 ```Authorization``` 합니다. 유효한 액세스 키 ID 인지 확인 하 고 ```Host``` 헤더가 등록 된 계정을 가리키는지 확인 합니다.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature", Bearer
```

**이유:** 제공 된가 ```Signature``` 서버에서 예상 하는 것과 일치 하지 않습니다.

**해결 방법:** ```String-To-Sign``` 이 올바른지 확인 합니다. ```Secret```가 올바르고 제대로 사용 되는지 확인 합니다 (를 사용 하기 전에 base64 디코딩된).

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Signed request header 'xxx' is not provided", Bearer
```

**이유:** ```SignedHeaders``` 헤더의 매개 변수에 요청 헤더가 누락 되어야  ```Authorization``` 합니다.

**해결 방법:** 올바른 값을 사용 하 여 필요한 헤더를 제공 합니다.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="XXX is required as a signed header", Bearer
```

**이유:** 에 매개 변수가 없습니다 ```SignedHeaders``` .

**해결 방법:** 서명 된 헤더 최소 요구 사항을 확인 합니다.

## <a name="code-snippets"></a>코드 조각

### <a name="javascript"></a>JavaScript

*필수 조건*: [암호화-JS](https://code.google.com/archive/p/crypto-js/)

```javascript
function signRequest(host,
                     method,      // GET, PUT, POST, DELETE
                     url,         // path+query
                     body,        // request body (undefined of none)
                     credential,  // access key id
                     secret)      // access key value (base64 encoded)
{
        var verb = method.toUpperCase();
        var utcNow = new Date().toUTCString();
        var contentHash = CryptoJS.SHA256(body).toString(CryptoJS.enc.Base64);

        //
        // SignedHeaders
        var signedHeaders = "x-ms-date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign =
            verb + '\n' +                              // VERB
            url + '\n' +                               // path_and_query
            utcNow + ';' + host + ';' + contentHash;   // Semicolon separated SignedHeaders values

        //
        // Signature
        var signature = CryptoJS.HmacSHA256(stringToSign, CryptoJS.enc.Base64.parse(secret)).toString(CryptoJS.enc.Base64);

        //
        // Result request headers
        return [
            { name: "x-ms-date", value: utcNow },
            { name: "x-ms-content-sha256", value: contentHash },
            { name: "Authorization", value: "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signedHeaders + "&Signature=" + signature }
        ];
}
```

### <a name="c"></a>C#

```csharp
using (var client = new HttpClient())
{
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://{config store name}.azconfig.io/kv?api-version=1.0"),
        Method = HttpMethod.Get
    };

    //
    // Sign the request
    request.Sign(<Credential>, <Secret>);

    await client.SendAsync(request);
}

static class HttpRequestMessageExtensions
{
    public static HttpRequestMessage Sign(this HttpRequestMessage request, string credential, string secret)
    {
        string host = request.RequestUri.Authority;
        string verb = request.Method.ToString().ToUpper();
        DateTimeOffset utcNow = DateTimeOffset.UtcNow;
        string contentHash = Convert.ToBase64String(request.Content.ComputeSha256Hash());

        //
        // SignedHeaders
        string signedHeaders = "date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign = $"{verb}\n{request.RequestUri.PathAndQuery}\n{utcNow.ToString("r")};{host};{contentHash}";

        //
        // Signature
        string signature;

        using (var hmac = new HMACSHA256(Convert.FromBase64String(secret)))
        {
            signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign)));
        }

        //
        // Add headers
        request.Headers.Date = utcNow;
        request.Headers.Add("x-ms-content-sha256", contentHash);
        request.Headers.Authorization = new AuthenticationHeaderValue("HMAC-SHA256", $"Credential={credential}&SignedHeaders={signedHeaders}&Signature={signature}");

        return request;
    }
}

static class HttpContentExtensions
{
    public static byte[] ComputeSha256Hash(this HttpContent content)
    {
        using (var stream = new MemoryStream())
        {
            if (content != null)
            {
                content.CopyToAsync(stream).Wait();
                stream.Seek(0, SeekOrigin.Begin);
            }

            using (var alg = SHA256.Create())
            {
                return alg.ComputeHash(stream.ToArray());
            }
        }
    }
}
```

### <a name="java"></a>Java

```java
public CloseableHttpResponse signRequest(HttpUriRequest request, String credential, String secret)
        throws IOException, URISyntaxException {
    Map<String, String> authHeaders = generateHeader(request, credential, secret);
    authHeaders.forEach(request::setHeader);

    return httpClient.execute(request);
}

private static Map<String, String> generateHeader(HttpUriRequest request, String credential, String secret)
        throws URISyntaxException, IOException {
    String requestTime = GMT_DATE_FORMAT.format(new Date());

    String contentHash = buildContentHash(request);
    // SignedHeaders
    String signedHeaders = "x-ms-date;host;x-ms-content-sha256";

    // Signature
    String methodName = request.getRequestLine().getMethod().toUpperCase();
    URIBuilder uri = new URIBuilder(request.getRequestLine().getUri());
    String scheme = uri.getScheme() + "://";
    String requestPath = uri.toString().substring(scheme.length()).substring(uri.getHost().length());
    String host = new URIBuilder(request.getRequestLine().getUri()).getHost();
    String toSign = String.format("%s\n%s\n%s;%s;%s", methodName, requestPath, requestTime, host, contentHash);

    byte[] decodedKey = Base64.getDecoder().decode(secret);
    String signature = Base64.getEncoder().encodeToString(new HmacUtils(HMAC_SHA_256, decodedKey).hmac(toSign));

    // Compose headers
    Map<String, String> headers = new HashMap<>();
    headers.put("x-ms-date", requestTime);
    headers.put("x-ms-content-sha256", contentHash);

    String authorization = String.format("HMAC-SHA256 Credential=%s, SignedHeaders=%s, Signature=%s",
            credential, signedHeaders, signature);
    headers.put("Authorization", authorization);

    return headers;
}

private static String buildContentHash(HttpUriRequest request) throws IOException {
    String content = "";
    if (request instanceof HttpEntityEnclosingRequest) {
        try {
            StringWriter writer = new StringWriter();
            IOUtils.copy(((HttpEntityEnclosingRequest) request).getEntity().getContent(), writer,
                    StandardCharsets.UTF_8);

            content = writer.toString();
        }
        finally {
            ((HttpEntityEnclosingRequest) request).getEntity().getContent().close();
        }
    }

    byte[] digest = new DigestUtils(SHA_256).digest(content);
    return Base64.getEncoder().encodeToString(digest);
}
```

### <a name="golang"></a>Golang

```golang
import (
    "bytes"
    "crypto/hmac"
    "crypto/sha256"
    "encoding/base64"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

//SignRequest Setup the auth header for accessing Azure App Configuration service
func SignRequest(id string, secret string, req *http.Request) error {
    method := req.Method
    host := req.URL.Host
    pathAndQuery := req.URL.Path
    if req.URL.RawQuery != "" {
        pathAndQuery = pathAndQuery + "?" + req.URL.RawQuery
    }

    content, err := ioutil.ReadAll(req.Body)
    if err != nil {
        return err
    }
    req.Body = ioutil.NopCloser(bytes.NewBuffer(content))

    key, err := base64.StdEncoding.DecodeString(secret)
    if err != nil {
        return err
    }

    timestamp := time.Now().UTC().Format(http.TimeFormat)
    contentHash := getContentHashBase64(content)
    stringToSign := fmt.Sprintf("%s\n%s\n%s;%s;%s", strings.ToUpper(method), pathAndQuery, timestamp, host, contentHash)
    signature := getHmac(stringToSign, key)

    req.Header.Set("x-ms-content-sha256", contentHash)
    req.Header.Set("x-ms-date", timestamp)
    req.Header.Set("Authorization", "HMAC-SHA256 Credential="+id+", SignedHeaders=x-ms-date;host;x-ms-content-sha256, Signature="+signature)

    return nil
}

func getContentHashBase64(content []byte) string {
    hasher := sha256.New()
    hasher.Write(content)
    return base64.StdEncoding.EncodeToString(hasher.Sum(nil))
}

func getHmac(content string, key []byte) string {
    hmac := hmac.New(sha256.New, key)
    hmac.Write([]byte(content))
    return base64.StdEncoding.EncodeToString(hmac.Sum(nil))
}
```

### <a name="python"></a>Python

```python

import base64
import hashlib
import hmac
from datetime import datetime
import six

def sign_request(host,
                method,     # GET, PUT, POST, DELETE
                url,        # Path + Query
                body,       # Request body
                credential, # Access Key ID
                secret):    # Access Key Value
    verb = method.upper()

    utc_now = str(datetime.utcnow().strftime("%b, %d %Y %H:%M:%S ")) + "GMT"

    if six.PY2:
        content_digest = hashlib.sha256(bytes(body)).digest()
    else:
        content_digest = hashlib.sha256(bytes(body, 'utf-8')).digest()

    content_hash = base64.b64encode(content_digest).decode('utf-8')

    # Signed Headers
    signed_headers = "x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names

    # String-To-Sign
    string_to_sign = verb + '\n' + \
                    url + '\n' + \
                    utc_now + ';' + host + ';' + content_hash  # Semicolon separated SignedHeaders values

    # Decode secret
    if six.PY2:
        decoded_secret = base64.b64decode(secret)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign), hashlib.sha256).digest()
    else:
        decoded_secret = base64.b64decode(secret, validate=True)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign, 'utf-8'), hashlib.sha256).digest()

    # Signature
    signature = base64.b64encode(digest).decode('utf-8')

    # Result request headers
    return {
        "x-ms-date": utc_now,
        "x-ms-content-sha256": content_hash,
        "Authorization": "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signed_headers + "&Signature=" + signature
    }
```

### <a name="powershell"></a>PowerShell

```PowerShell
function Sign-Request(
    [string] $hostname,
    [string] $method,      # GET, PUT, POST, DELETE
    [string] $url,         # path+query
    [string] $body,        # request body
    [string] $credential,  # access key id
    [string] $secret       # access key value (base64 encoded)
)
{  
    $verb = $method.ToUpperInvariant()
    $utcNow = (Get-Date).ToUniversalTime().ToString("R", [Globalization.DateTimeFormatInfo]::InvariantInfo)
    $contentHash = Compute-SHA256Hash $body

    $signedHeaders = "x-ms-date;host;x-ms-content-sha256";  # Semicolon separated header names

    $stringToSign = $verb + "`n" +
                    $url + "`n" +
                    $utcNow + ";" + $hostname + ";" + $contentHash  # Semicolon separated signedHeaders values

    $signature = Compute-HMACSHA256Hash $secret $stringToSign

    # Return request headers
    return @{
        "x-ms-date" = $utcNow;
        "x-ms-content-sha256" = $contentHash;
        "Authorization" = "HMAC-SHA256 Credential=" + $credential + "&SignedHeaders=" + $signedHeaders + "&Signature=" + $signature
    }
}

function Compute-SHA256Hash(
    [string] $content
)
{
    $sha256 = [System.Security.Cryptography.SHA256]::Create()
    try {
        return [Convert]::ToBase64String($sha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $sha256.Dispose()
    }
}

function Compute-HMACSHA256Hash(
    [string] $secret,      # base64 encoded
    [string] $content
)
{
    $hmac = [System.Security.Cryptography.HMACSHA256]::new([Convert]::FromBase64String($secret))
    try {
        return [Convert]::ToBase64String($hmac.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $hmac.Dispose()
    }
}

# Stop if any error occurs
$ErrorActionPreference = "Stop"

$uri = [System.Uri]::new("https://{myconfig}.azconfig.io/kv?api-version=1.0")
$method = "GET"
$body = $null
$credential = "<Credential>"
$secret = "<Secret>"

$headers = Sign-Request $uri.Authority $method $uri.PathAndQuery $body $credential $secret
Invoke-RestMethod -Uri $uri -Method $method -Headers $headers -Body $body
```

### <a name="bash"></a>Bash

*필수 구성 요소*:

| 필수 조건 | 명령 | 테스트 된 버전 |
| ------------ | ------- | --------------- |
| [Bash](https://www.gnu.org/software/bash/) | bash | 3.5.27, 4.4.23 |
| [coreutils](https://www.gnu.org/software/coreutils/) | tr | 8.28 |
| [curl](https://curl.haxx.se/) | curl | 7.55.1, 7.58.0 |
| [OpenSSL](https://www.openssl.org/) | openssl | 1.1.0 g, 1.1.1 a |
| [util-linux](https://github.com/karelzak/util-linux/) | hexdump | 2.14.1, 2.31.1 |

```bash
#!/bin/bash

sign_request () {
    local host="$1"
    local method="$2"      # GET, PUT, POST, DELETE
    local url="$3"         # path+query
    local body="$4"        # request body
    local credential="$5"  # access key id
    local secret="$6"      # access key value (base64 encoded)

    local verb=$(printf "$method" | tr '[:lower:]' '[:upper:]')
    local utc_now="$(date -u '+%a, %d %b %Y %H:%M:%S GMT')"
    local content_hash="$(printf "$body" | openssl sha256 -binary | base64)"

    local signed_headers="x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names
    local string_to_sign="$verb\n$url\n$utc_now;$host;$content_hash"  # Semicolon separated signed_headers values

    local decoded_secret="$(printf "$secret" | base64 -d | hexdump -v -e '/1 "%02x"')"
    local signature="$(printf "$string_to_sign" | openssl sha256 -mac HMAC -macopt hexkey:"$decoded_secret" -binary | base64)"

    # Output request headers
    printf '%s\n' \
           "x-ms-date: $utc_now" \
           "x-ms-content-sha256: $content_hash" \
           "Authorization: HMAC-SHA256 Credential=$credential&SignedHeaders=$signed_headers&Signature=$signature"
}

host="{config store name}.azconfig.io"
method="GET"
url="/kv?api-version=1.0"
body=""
credential="<Credential>"
secret="<Secret>"

headers=$(sign_request "$host" "$method" "$url" "$body" "$credential" "$secret")

while IFS= read -r line; do
    header_args+=("-H$line")
done <<< "$headers"
curl -X "$method" -d "$body" "${header_args[@]}" "https://$host$url"
```
