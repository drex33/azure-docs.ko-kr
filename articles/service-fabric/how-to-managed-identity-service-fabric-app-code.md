---
title: 응용 프로그램에서 관리 ID 사용
description: Azure Service Fabric 응용 프로그램 코드에서 관리 ID를 사용하여 Azure 서비스에 액세스하는 방법입니다.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: cc7eff8119e6b79ca991543cdc09cfe106989fd3
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867060"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Service Fabric 응용 프로그램의 관리 ID를 활용하여 Azure 서비스에 액세스하는 방법

Service Fabric 응용 프로그램은 관리 ID를 활용하여 Azure Active Directory 기반 인증을 지원하는 다른 Azure 리소스에 액세스할 수 있습니다. 애플리케이션은 시스템에서 할당하거나 사용자가 할당할 수 있는 ID를 나타내는 [액세스 토큰](../active-directory/develop/developer-glossary.md#access-token)을 가져오고, 이를 '전달자' 토큰으로 사용하여 [보호된 리소스 서버](../active-directory/develop/developer-glossary.md#resource-server)라고도 하는 해당 애플리케이션 자체를 다른 서비스에 인증할 수 있습니다. 토큰은 Service Fabric 애플리케이션에 할당된 ID를 나타내며, 해당 ID를 공유하는 Azure 리소스(SF 애플리케이션 포함)에만 발급됩니다. 관리 ID에 대한 자세한 설명 및 시스템이 할당한 ID와 사용자가 할당한 ID의 차이점은 [관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md) 문서를 참조하세요. 이 문서 전체에서 관리 ID 사용 Service Fabric 애플리케이션을 [클라이언트 애플리케이션](../active-directory/develop/developer-glossary.md#client-application)이라고 합니다.

Reliable Services 및 컨테이너를 통해 시스템 할당 및 사용자 할당 [Service Fabric 애플리케이션 관리 ID](https://github.com/Azure-Samples/service-fabric-managed-identity)를 사용하는 방법을 보여 주는 도우미 애플리케이션 예제를 참조하세요.

> [!IMPORTANT]
> 관리 ID는 리소스를 포함하는 구독과 연결된 해당 Azure AD 테넌트에서 Azure 리소스와 서비스 주체 간의 연결을 나타냅니다. 따라서 Service Fabric 컨텍스트에서 관리 ID는 Azure 리소스로 배포된 애플리케이션에 대해서만 지원됩니다. 

> [!IMPORTANT]
> Service Fabric 애플리케이션의 관리 ID를 사용하기 전에 보호된 리소스에 대한 액세스 권한을 클라이언트 애플리케이션에 부여해야 합니다. [Azure AD 인증을 지원하는 Azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) 목록을 참조하여 참조한 지원을 확인한 다음, 관심 있는 리소스에 대한 ID 액세스 권한을 부여하는 특정 단계에 대한 해당 서비스의 설명서를 참조하세요. 
 

## <a name="leverage-a-managed-identity-using-azureidentity"></a>Azure.Identity를 사용하여 관리 ID 활용

Azure ID SDK는 이제 Service Fabric을 지원합니다. Azure.Identity를 사용하면 토큰 가져오기, 토큰 캐싱 및 서버 인증을 처리하므로 Service Fabric 앱 관리 ID를 사용하는 코드를 더 쉽게 작성할 수 있습니다. 대부분의 Azure 리소스에 액세스하는 동안 토큰의 개념은 숨겨집니다.

이러한 언어에 Service Fabric 지원은 대한 다음 버전에서 사용할 수 있습니다. 
- [버전 1.3.0의 C#](https://www.nuget.org/packages/Azure.Identity). [C# 샘플](https://github.com/Azure-Samples/service-fabric-managed-identity)을 참조하세요.
- [버전 1.5.0의 Python](https://pypi.org/project/azure-identity/). [Python 샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/identity/azure-identity/tests/managed-identity-live/service-fabric/service_fabric.md)을 참조하세요.
- [버전 1.2.0의 Java](/java/api/overview/azure/identity-readme)

자격 증명을 초기화하고 자격 증명을 사용하여 Azure Key Vault에서 비밀을 가져오는 C# 샘플:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace MyMIService
{
    internal sealed class MyMIService : StatelessService
    {
        protected override async Task RunAsync(CancellationToken cancellationToken)
        {
            try
            {
                // Load the service fabric application managed identity assigned to the service
                ManagedIdentityCredential creds = new ManagedIdentityCredential();

                // Create a client to keyvault using that identity
                SecretClient client = new SecretClient(new Uri("https://mykv.vault.azure.net/"), creds);

                // Fetch a secret
                KeyVaultSecret secret = (await client.GetSecretAsync("mysecret", cancellationToken: cancellationToken)).Value;
            }
            catch (CredentialUnavailableException e)
            {
                // Handle errors with loading the Managed Identity
            }
            catch (RequestFailedException)
            {
                // Handle errors with fetching the secret
            }
            catch (Exception e)
            {
                // Handle generic errors
            }
        }
    }
}

```

## <a name="acquiring-an-access-token-using-rest-api"></a>REST API를 사용하여 액세스 토큰 획득
관리 ID를 사용하도록 설정된 클러스터에서 Service Fabric 런타임은 애플리케이션에서 액세스 토큰을 가져오는 데 사용할 수 있는 localhost 엔드포인트를 공개합니다. 엔드포인트는 클러스터의 모든 노드에서 사용할 수 있으며, 해당 노드의 모든 엔터티에 액세스할 수 있습니다. 권한 있는 호출자는 이 엔드포인트를 호출하고 인증 코드를 제공하여 액세스 토큰을 가져올 수 있습니다. 코드는 Service Fabric 런타임에서 각각의 개별 서비스 코드 패키지 활성화에 대해 생성되며, 해당 서비스 코드 패키지를 호스트하는 프로세스의 수명에 바인딩됩니다.

특히 관리 ID 사용 Service Fabric 서비스의 환경은 다음 변수를 사용하여 시드됩니다.
- 'IDENTITY_ENDPOINT': 서비스의 관리 ID에 해당하는 localhost 엔드포인트
- 'IDENTITY_HEADER': 현재 노드의 서비스를 나타내는 고유 인증 코드
- 'IDENTITY_SERVER_THUMBPRINT': Service Fabric 관리 ID 서버의 지문

> [!IMPORTANT]
> 애플리케이션 코드는 'IDENTITY_HEADER' 환경 변수의 값을 중요한 데이터로 간주해야 합니다. 이는 기록되지 않으며, 그렇지 않고 기록되는 경우에는 전파됩니다. 인증 코드는 로컬 노드 외부에 있거나 서비스를 호스트하는 프로세스가 종료되면 값이 없지만 Service Fabric 서비스의 ID를 나타내므로 액세스 토큰 자체와 동일한 예방 조치로 처리해야 합니다.

토큰을 얻으려면 클라이언트에서 다음 단계를 수행합니다.
- 관리 ID 엔드포인트(IDENTITY_ENDPOINT 값)를 토큰에 필요한 API 버전 및 리소스(대상 그룹)와 연결하여 URI를 구성합니다.
- 지정된 URI에 대한 GET http(s) 요청을 만듭니다.
- 적절한 서버 인증서 유효성 검사 논리를 추가합니다.
- 인증 코드(IDENTITY_HEADER 값)를 헤더로 요청에 추가합니다.
- 요청을 제출합니다.

성공적인 응답에는 결과 액세스 토큰을 나타내는 JSON 페이로드와 이를 설명하는 메타데이터가 포함됩니다. 실패한 응답에는 실패에 대한 설명도 포함됩니다. 오류 처리에 대한 자세한 내용은 아래를 참조하세요.

액세스 토큰은 다양한 수준(노드, 클러스터, 리소스 공급자 서비스)에서 Service Fabric을 통해 캐시되므로 성공적인 응답이 반드시 사용자 애플리케이션의 요청에 대한 응답으로 토큰이 직접 발급되었음을 의미하지는 않습니다. 토큰은 수명보다 짧은 기간 동안 캐시되므로 애플리케이션에서 유효한 토큰을 받도록 보장됩니다. 애플리케이션 코드는 획득한 액세스 토큰을 자체적으로 캐시하는 것이 좋습니다. 캐싱 키는 대상 그룹(의 파생)을 포함해야 합니다. 

샘플 요청:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
여기서

| 요소 | Description |
| ------- | ----------- |
| `GET` | HTTP 동사는 엔드포인트에서 데이터를 검색한다는 것을 나타냅니다. 이 경우에는 OAuth 액세스 토큰입니다. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | Service Fabric 애플리케이션에 대한 관리 ID 엔드포인트이며, IDENTITY_ENDPOINT 환경 변수를 통해 제공됩니다. |
| `api-version` | 관리 ID 토큰 서비스의 API 버전을 지정하는 쿼리 문자열 매개 변수입니다. 현재 유일하게 허용되는 값은 `2019-07-01-preview`이며 변경될 수 있습니다. |
| `resource` | 쿼리 문자열 매개 변수는 대상 리소스의 앱 ID URI를 나타냅니다. 이는 발급된 토큰의 `aud`(대상 그룹) 클레임으로 반영됩니다. 다음 예제에서는 앱 ID URI가 https:\//vault.azure.net/인 Azure Key Vault에 액세스하기 위한 토큰을 요청합니다. |
| `Secret` | Service Fabric 관리 ID 토큰 서비스에서 호출자를 인증하는 Service Fabric 서비스에 대해 요청하는 HTTP 요청 헤더 필드입니다. 이 값은 IDENTITY_HEADER 환경 변수를 통해 SF 런타임에서 제공됩니다. |


샘플 응답:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
여기서

| 요소 | Description |
| ------- | ----------- |
| `token_type` | 토큰 형식입니다. 이 경우 이 토큰의 발표자('전달자')가 토큰의 의도한 주체임을 의미하는 "전달자" 액세스 토큰입니다. |
| `access_token` | 요청된 액세스 토큰입니다. 보안이 설정된 REST API를 호출할 때 토큰은 호출자를 인증하는 API를 허용하는 "전달자" 토큰으로 `Authorization` 요청 헤더 필드에 포함됩니다. | 
| `expires_on` | 액세스 토큰이 만료되는 타임스탬프입니다. "1970-01-01T0:0:0Z UTC"의 초 수로 표시되며 토큰의 `exp` 클레임에 해당합니다. 이 경우 토큰은 2019-08-08T06:10:11+00:00(RFC 3339 표준)에 만료됩니다.|
| `resource` | 액세스 토큰이 발급된 리소스이며, 요청에 대한 `resource` 쿼리 문자열 매개 변수를 통해 지정됩니다. 이는 토큰의 'aud' 클레임에 해당합니다. |


## <a name="acquiring-an-access-token-using-c"></a>C#을 사용하여 액세스 토큰 획득
위의 내용은 C#에서 다음과 같이 바뀝니다.

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>관리 ID를 사용하여 Service Fabric 애플리케이션에서 Key Vault에 액세스
다음 샘플에서는 위의 내용을 기반으로 하여 관리 ID를 사용하여 Key Vault에 저장된 비밀에 액세스하는 방법을 보여 줍니다.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>오류 처리
HTTP 응답 헤더의 '상태 코드' 필드는 요청의 성공 상태를 나타냅니다. '200 OK' 상태는 성공을 나타내며, 응답은 위에서 설명한 대로 액세스 토큰을 포함합니다. 다음은 가능한 오류 응답을 열거한 간단한 목록입니다.

| 상태 코드 | 오류 원인 | 처리 방법 |
| ----------- | ------------ | ------------- |
| 404 찾을 수 없습니다. | 알 수 없는 인증 코드이거나 애플리케이션에 관리 ID가 할당되지 않았습니다. | 애플리케이션 설정 또는 토큰 획득 코드를 수정합니다. |
| 429 요청이 너무 많음. |  AAD 또는 SF에서 설정한 제한에 도달했습니다. | 지수 백오프를 통해 다시 시도. 아래 지침을 참조하세요. |
| 요청의 4xx 오류입니다. | 요청 매개 변수 중 하나 이상이 올바르지 않습니다. | 다시 시도하지 마세요.  자세한 내용은 오류 세부 정보를 확인하세요.  4xx 오류는 디자인 타임 오류입니다.|
| 5xx 서비스 오류입니다. | 관리 ID 하위 시스템 또는 Azure Active Directory에서 일시적 오류를 반환했습니다. | 잠시 후에 다시 시도하는 것이 안전합니다. 다시 시도하면 제한 조건(429)에 도달할 수 있습니다.|

오류가 발생하면 해당 HTTP 응답 본문에는 다음과 같은 오류 세부 정보가 있는 JSON 개체가 포함됩니다.

| 요소 | 설명 |
| ------- | ----------- |
| code | 오류 코드 |
| correlationId | 디버깅에 사용할 수 있는 상관 관계 ID입니다. |
| message | 오류의 자세한 설명입니다. **오류 설명은 언제든지 변경될 수 있습니다. 오류 메시지 자체에 의존하지 마세요.**|

샘플 오류:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

다음은 관리 ID와 관련된 일반적인 Service Fabric 오류 목록입니다.

| 코드 | 메시지 | Description | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | 요청 헤더에서 비밀을 찾을 수 없습니다. | 인증 코드가 요청과 함께 제공되지 않았습니다. | 
| ManagedIdentityNotFound | 지정된 애플리케이션 호스트에 대한 관리 ID를 찾을 수 없습니다. | 애플리케이션에 ID가 없거나 인증 코드를 알 수 없습니다. |
| ArgumentNullOrEmpty | 'resource' 매개 변수는 Null 또는 빈 문자열이 아니어야 합니다. | 리소스(대상 그룹)가 요청에 제공되지 않았습니다. |
| InvalidApiVersion | api-version ''이(가) 지원되지 않습니다. 지원되는 버전은 '2019-07-01-preview'입니다. | 요청 URI에 지정된 API 버전이 없거나 지원되지 않습니다. |
| InternalServerError | 오류가 발생했습니다. | Service Fabric 스택 외부에 있는 관리 ID 하위 시스템에서 오류가 발생했습니다. 가장 가능성이 높은 원인은 리소스에 대해 잘못된 값이 지정되었기 때문입니다(후행 '/' 확인?) | 

## <a name="retry-guidance"></a>다시 시도 지침 

일반적으로 다시 시도할 수 있는 유일한 오류 코드는 429(요청이 너무 많음)입니다. 내부 서버 오류/5xx 오류 코드는 다시 시도할 수 있지만 원인은 영구적일 수 있습니다. 

대역폭 제한은 관리 ID 하위 시스템, 특히 '업스트림' 종속성(관리 ID Azure 서비스 또는 보안 토큰 서비스)에 대한 호출 수에 적용됩니다. Service Fabric은 파이프라인의 다양한 수준에서 토큰을 캐시하지만 관련 구성 요소의 분산 특성을 고려할 때 호출자는 일관되지 않은 제한 응답을 경험할 수 있습니다(예: 애플리케이션의 한 노드/인스턴스에서 제한되지만, 요청하는 동안 다른 노드에서는 제한되지 않음). 제한 조건이 설정되면 조건이 지워질 때까지 동일한 애플리케이션의 후속 요청이 429 HTTP 상태 코드(요청이 너무 많음)로 인해 실패할 수 있습니다.  

제한으로 인해 실패한 요청은 다음과 같이 지수 백오프를 사용하여 다시 시도하는 것이 좋습니다. 

| 호출 인덱스 | 429 수신에 대한 작업 | 
| --- | --- | 
| 1 | 1초 대기 후 다시 시도 |
| 2 | 2초 대기 후 다시 시도 |
| 3 | 4초 대기 후 다시 시도 |
| 4 | 8초 대기 후 다시 시도 |
| 4 | 8초 대기 후 다시 시도 |
| 5 | 16초 대기 후 다시 시도 |

## <a name="resource-ids-for-azure-services"></a>Azure 서비스의 리소스 ID
Azure AD를 지원하는 리소스 및 해당 리소스 ID에 대한 목록은 [Azure AD 인증을 지원하는 Azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [관리 ID가 있는 Service Fabric 애플리케이션을 관리 클러스터에 배포](how-to-managed-cluster-application-managed-identity.md)
* [시스템 할당 관리 ID가 있는 Service Fabric 애플리케이션을 기본 클러스터에 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [기본 클러스터에 사용자 할당 관리 ID가 있는 Service Fabric 애플리케이션 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure 리소스에 대한 Service Fabric 애플리케이션의 관리 ID 액세스 권한 부여](./how-to-grant-access-other-resources.md)
* [Service Fabric 관리 ID를 사용하여 샘플 애플리케이션 검색](https://github.com/Azure-Samples/service-fabric-managed-identity)