---
title: Azure Functions 보안 설정
description: Azure에서 실행 중인 함수 코드가 일반적인 공격으로부터 더욱 안전하게 보호하는 방법을 알아봅니다.
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: 97a58c38f08e3b22f25ca2834a26692452fcff9e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529183"
---
# <a name="securing-azure-functions"></a>Azure Functions 보안 설정

여러 측면에서 서버리스 함수의 안전한 개발, 배포 및 운영 계획은 웹 기반 또는 클라우드 호스팅 애플리케이션과 유사합니다. [Azure App Service](../app-service/index.yml)는 함수 앱에 대한 호스팅 인프라를 제공합니다. 이 문서에서는 함수 코드를 실행하기 위한 보안 전략과 App Service가 함수를 보호하는 데 어떻게 도움이 되는지 제공합니다. 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

[Azure Security Benchmark](../security/benchmarks/overview.md)를 따르는 보안 권장 사항은 [Azure Functions용 Azure 보안 기준](security-baseline.md)을 참조하세요.

## <a name="secure-operation"></a>보안 작업 

이 섹션에서는 함수 앱을 최대한 안전하게 구성하고 실행하는 방법을 안내합니다. 

### <a name="security-center"></a>Security Center

Security Center는 포털의 함수 앱과 통합됩니다. 잠재적인 구성 관련 보안 취약성에 대한 빠른 평가를 무료로 제공합니다. 전용 계획에서 실행되는 함수 앱은 Security Center의 실시간 보안 기능을 사용할 수 있으며, 이 경우 추가 비용이 듭니다. 자세히 알아보려면 [Azure App Service 웹 앱 및 API 보호](../security-center/defender-for-app-service-introduction.md)를 참조하세요. 

### <a name="log-and-monitor"></a>로그 및 모니터

작업 모니터링과 로깅 분석을 통해 공격을 탐지하는 방법입니다. Functions는 함수 앱에 대한 로그, 성능 및 오류 데이터를 수집하는 Application Insights와 통합됩니다. Application Insights는 성능 이상을 자동으로 감지하며, 문제를 진단하고 함수가 어떻게 사용되는지 이해할 수 있는 강력한 분석 도구를 포함하고 있습니다. 자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요.

Functions는 또한 더욱 쉬운 분석을 위해 함수 앱 로그를 시스템 이벤트와 통합할 수 있도록 Azure Monitor 로그와 통합됩니다. 진단 설정을 사용하여 Logs Analytics 작업 영역 등 사용자가 선택한 대상으로 함수에 대한 플랫폼 로그 및 메트릭의 스트리밍 내보내기를 구성할 수 있습니다. 자세한 내용은 [Azure Monitor 로그를 사용한 Azure Functions 모니터링](functions-monitor-log-analytics.md)을 참조하세요. 

엔터프라이즈 수준 위협 감지 및 응답 자동화의 경우 로그 및 이벤트를 Logs Analytics 작업 영역으로 스트리밍합니다. 그런 다음 이 작업 영역에 Azure Sentinel을 연결할 수 있습니다. 자세한 내용을 알아보려면 [Azure Sentinel이란?](../sentinel/overview.md)을 참조하세요.  

가시성에 대한 보안 권장 사항은 [Azure Functions용 Azure 보안 기준](security-baseline.md#logging-and-monitoring)을 참조하세요. 

### <a name="require-https"></a>HTTPS 필요

기본적으로 클라이언트는 HTTP 또는 HTTPS를 모두 사용하여 함수 엔드포인트에 연결할 수 있습니다. HTTPS는 SSL/TLS 프로토콜을 사용하여 암호화 및 인증된 보안 연결을 제공하므로 HTTP를 HTTPS로 리디렉션해야 합니다. 방법을 알아보려면 [HTTPS 적용](../app-service/configure-ssl-bindings.md#enforce-https)을 참조하세요.

HTTPS가 필요한 경우 최신 TLS 버전도 필요합니다. 방법을 알아보려면 [TLS 버전 적용](../app-service/configure-ssl-bindings.md#enforce-tls-versions)을 참조하세요.

자세한 정보는 [보안 연결(TLS)](../app-service/overview-security.md#https-and-certificates)을 참조하세요.

### <a name="function-access-keys"></a>함수 액세스 키

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>시스템 키 

특정 확장에는 웹후크 엔드포인트에 액세스하도록 시스템 관리 키가 필요할 수 있습니다. 시스템 키는 내부 구성 요소에 의해 호출되는 확장별 함수 엔드포인트용으로 설계되었습니다. 예를 들어 [Event Grid 트리거](functions-bindings-event-grid-trigger.md)의 경우 트리거 엔드포인트 호출 시 구독에서 시스템 키를 사용해야 합니다. Durable Functions는 또한 시스템 키를 사용하여 [Durable Task 확장 API](durable/durable-functions-http-api.md)를 호출합니다. 

시스템 키의 범위는 확장에 따라 결정되지만 일반적으로 전체 함수 앱에 적용됩니다. 시스템 키는 특정 확장에 의해서만 생성될 수 있으며 해당 값을 명시적으로 설정할 수 없습니다. 다른 키와 마찬가지로 포털에서 또는 키 API를 사용하여 키에 대한 새 값을 생성할 수 있습니다.

#### <a name="keys-comparison"></a>키 비교

다음 표에서는 다양한 종류의 액세스 키에 대한 사용을 비교합니다.

| 작업                                        | 범위                    | 유효한 키         |
|-----------------------------------------------|--------------------------|--------------------|
| 함수 실행                            | 특정 함수        | 함수           |
| 함수 실행                            | 모든 함수             | 함수 또는 호스트   |
| 관리자 엔드포인트 호출                        | 함수 앱             | 호스트(마스터 전용) |
| Durable Task 확장 API 호출              | 함수 앱<sup>1</sup> | 시스템<sup>2</sup> |
| 확장별 웹후크 호출(내부) | 함수 앱<sup>1</sup> | 시스템<sup>2</sup> |

<sup>1</sup>범위는 확장에 의해 결정됩니다.  
<sup>2</sup>확장에 따라 특정 이름이 설정됩니다.

액세스 키에 대해 자세히 알아보려면 [HTTP 트리거 바인딩 문서](functions-bindings-http-webhook-trigger.md#obtaining-keys)를 참조하세요.


#### <a name="secret-repositories"></a>비밀 리포지토리

기본적으로 키는 `AzureWebJobsStorage` 설정에서 제공하는 계정의 Blob Storage 컨테이너에 저장됩니다. 특정 애플리케이션 설정을 사용하여 해당 동작을 재정의하고 다른 위치에 키를 저장할 수 있습니다.

|Location  |설정 | 값 | 설명  |
|---------|---------|---------|---------|
|다른 스토리지 계정     |  `AzureWebJobsSecretStorageSas`       | `<BLOB_SAS_URL>` | 제공된 SAS URL에 따라, 두 번째 스토리지 계정의 Blob Storage에 키를 저장합니다. 키는 함수 앱에 고유한 비밀을 사용하여 저장하기 전에 암호화됩니다. |
|파일 시스템   | `AzureWebJobsSecretStorageType`   |  `files`       | 키는 함수 앱에 고유한 비밀을 사용하여 저장 전 암호화된 상태로 파일 시스템에 유지됩니다. |
|Azure Key Vault  | `AzureWebJobsSecretStorageType`<br/>`AzureWebJobsSecretStorageKeyVaultName` | `keyvault`<br/>`<VAULT_NAME>` | 자격 증명 모음에는 반드시 호스팅 리소스의 시스템 할당 관리 ID에 해당하는 액세스 정책이 있어야 합니다. 액세스 정책은 ID에 `Get`,`Set`, `List`, `Delete`의 비밀 권한을 부여하도록 합니다. <br/>로컬로 실행하는 경우, 개발자 ID가 사용되며 설정은 반드시 [local.settings.json 파일](functions-develop-local.md#local-settings-file)에 있어야 합니다. | 
|Kubernetes 비밀  |`AzureWebJobsSecretStorageType`<br/>`AzureWebJobsKubernetesSecretName`(선택 사항) | `kubernetes`<br/>`<SECRETS_RESOURCE>` | Kubernetes에서 함수 런타임을 실행하는 경우에만 지원됩니다. `AzureWebJobsKubernetesSecretName`이 설정되지 않은 경우, 리포지토리는 읽기 전용으로 간주됩니다. 이 경우, 배포 전에 값이 생성되어야 합니다. Azure Functions Core Tools는 Kubernetes에 배포할 때 값을 자동으로 생성합니다.|

### <a name="authenticationauthorization"></a>인증/권한 부여

함수 키가 원치 않는 액세스에 대해 약간의 위험 완화를 제공할 수 있지만 함수 엔드포인트를 실제로 보호하는 유일한 방법은 함수에 액세스하는 클라이언트의 양성 인증을 구현하는 것입니다. 그런 다음 ID를 기준으로 권한 부여 결정을 내릴 수 있습니다.  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>사용 권한

모든 애플리케이션 또는 서비스와 마찬가지로 가능한 가장 낮은 권한으로 함수 앱을 실행하는 것이 목표입니다. 

#### <a name="user-management-permissions"></a>사용자 관리 권한

함수는 기본 제공 [Azure RBAC](../role-based-access-control/overview.md)(역할 기반 액세스 제어)를 지원합니다. Functions는 Azure 역할로서 [Contributor](../role-based-access-control/built-in-roles.md#contributor), [Owner](../role-based-access-control/built-in-roles.md#owner), [Reader](../role-based-access-control/built-in-roles.md#owner)를 지원합니다. 

권한은 함수 앱 수준에서 적용됩니다. 대부분의 함수 앱 수준 작업을 수행하려면 기여자 역할이 필요합니다. 소유자 역할만 함수 앱을 삭제할 수 있습니다. 

#### <a name="organize-functions-by-privilege"></a>권한으로 함수 구성 

애플리케이션 설정에 저장된 연결 문자열 및 기타 자격 증명은 함수 앱의 모든 함수에 연결된 리소스에 대한 동일한 권한 집합을 제공합니다. 이러한 자격 증명을 사용하지 않는 함수를 별도의 함수 앱으로 이동하여 특정 자격 증명에 액세스할 수 있는 함수의 수를 최소화하는 것이 좋습니다. 항상 [함수 체이닝](/learn/modules/chain-azure-functions-data-using-bindings/)과 같은 기술을 사용하여 다른 함수 앱에 있는 함수 사이에서 데이터를 전달할 수 있습니다.  

#### <a name="managed-identities"></a>관리 ID

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

관리 ID는 일부 트리거와 바인딩에서의 연결에 대한 비밀 대신 사용할 수 있습니다. [ID 기반 연결](#identity-based-connections)을 참조하세요.

자세한 내용은 [App Service 및 Azure Functions에 대한 관리 ID를 사용하는 방법](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json)을 참조하세요.

#### <a name="restrict-cors-access"></a>CORS 액세스 제한

[CORS(원본 간 리소스 공유)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)는 다른 도메인에서 실행 중인 웹 앱이 HTTP 트리거 엔드포인트로 요청할 수 있도록 허용하는 방법입니다. App Service는 HTTP 요청에 필요한 CORS 헤더를 제공하기 위한 기본 지원을 제공합니다. CORS 규칙은 함수 앱 수준에서 정의됩니다.  

모든 사이트에서 엔드포인트에 액세스할 수 있도록 하는 와일드카드를 사용하고자 할 수도 있습니다. 하지만 이는 사이트 간 스크립팅 공격을 방지하는 데 도움이 되는 CORS의 목적을 무력화합니다. 대신 엔드포인트에 액세스해야 하는 각 웹 앱의 도메인에 대해 별도의 CORS 항목을 추가합니다. 

### <a name="managing-secrets"></a>암호 관리 

코드를 실행해야 하는 다양한 서비스 및 리소스에 연결할 수 있도록 함수 앱은 연결 문자열 및 서비스 키와 같은 비밀에 액세스할 수 있어야 합니다. 이 섹션에서는 함수에 필요한 암호를 저장하는 방법을 설명합니다.

함수 코드에 비밀을 저장하지 마세요. 

#### <a name="application-settings"></a>애플리케이션 설정

기본적으로 함수 앱 및 바인딩에서 사용하는 연결 문자열과 비밀을 애플리케이션 설정으로 저장합니다. 이렇게 하면 함수 코드 및 함수에서 사용하는 다양한 바인딩에서 이러한 자격 증명을 사용할 수 있습니다. 애플리케이션 설정(키) 이름은 실제 값을 검색하는 데 사용되며, 이 실제 값은 비밀입니다. 

예를 들어 모든 함수 앱에는 런타임에 사용하는 연결된 스토리지 계정이 필요합니다. 기본적으로 이 스토리지 계정에 대한 연결은 `AzureWebJobsStorage`라는 애플리케이션 설정에 저장됩니다.

앱 설정 및 연결 문자열은 Azure에 암호화되어 저장됩니다. 앱이 시작될 때 앱의 프로세스 메모리에 삽입되기 전에만 암호 해독됩니다. 암호화 키는 정기적으로 회전합니다. 대신 비밀의 보안 스토리지를 관리하려면 앱 설정이 Azure Key Vault에 대한 참조여야 합니다. 

로컬 컴퓨터에서 함수를 개발할 때 local.settings.json 파일에서 설정을 기본값으로 암호화할 수도 있습니다. 자세한 사항은 [로컬 설정 파일](functions-develop-local.md#local-settings-file)에서 `IsEncrypted` 속성을 참조하세요.  

#### <a name="key-vault-references"></a>Key Vault 참조

애플리케이션 설정은 대부분의 함수에 충분하지만 여러 서비스에서 동일한 비밀을 공유하고자 할 수 있습니다. 이 경우 비밀의 중복 스토리지로 인해 잠재적인 취약성이 발생합니다. 보다 안전한 방법은 중앙 비밀 스토리지 서비스와 비밀 대신 이 서비스에 대한 참조 사용입니다.      

[Azure Key Vault](../key-vault/general/overview.md)는 액세스 정책 및 감사 기록에 대한 전체 제어와 함께 중앙 집중식 비밀 관리를 제공하는 서비스입니다. 애플리케이션 설정에서 연결 문자열 또는 키 대신 Key Vault 참조를 사용할 수 있습니다. 자세히 알아보려면 [App Service 및 Azure Functions의 Key Vault 참조 사용](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json)을 참조하세요.

### <a name="identity-based-connections"></a>ID 기반 연결

일부 리소스에 연결하기 위해 비밀 대신 ID를 사용할 수 있습니다. 이는 비밀 관리를 요구하지 않는다는 이점이 있으며, 보다 세분화된 액세스 제어 및 감사를 제공합니다. 

[Azure AD 인증을 지원하는 Azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)에 연결하는 코드를 작성하는 경우, 비밀 또는 연결 문자열 대신 ID를 사용하도록 선택할 수 있습니다. 각 서비스의 설명서에 두 연결 방법에 대한 세부 정보가 있습니다.

ID 기반 연결을 사용하여 일부 Azure Functions 트리거와 바인딩 확장을 구성할 수 있습니다. 현재 여기에는 [Azure Blob](./functions-bindings-storage-blob.md) 및 [Azure 큐](./functions-bindings-storage-queue.md) 확장이 포함됩니다. ID 사용을 위해 확장을 구성하는 방법에 대한 자세한 내용은 [Azure Functions에서 ID 기반 연결을 사용하는 방법](./functions-reference.md#configure-an-identity-based-connection)을 참조하세요.

### <a name="set-usage-quotas"></a>사용 할당량 설정

사용 계획에서 실행 중인 함수에 사용 할당량을 설정하는 것이 좋습니다. 함수 앱에서 함수의 총 실행 수에 대해 일일 GB-초 제한을 설정하면 제한에 도달하는 순간 실행이 중지됩니다. 이는 잠재적으로 함수를 실행하는 악성 코드를 완화하는 데 도움이 될 수 있습니다. 함수의 사용량을 예측하는 방법을 알아보려면 [사용 계획 비용 예측](functions-consumption-costs.md)을 참조하세요. 

### <a name="data-validation"></a>데이터 유효성 검사

함수에서 사용하는 트리거 및 바인딩은 추가 데이터 유효성 검사를 제공하지 않습니다. 코드는 트리거 또는 입력 바인딩에서 수신한 데이터의 유효성을 검사해야 합니다. 업스트림 서비스가 손상된 경우 유효성 검사가 완료되지 않은 입력이 함수를 통해 전달되지 않아야 합니다. 예를 들어 함수가 관계형 데이터베이스에 있는 Azure Storage 큐에서 데이터를 저장하는 경우 데이터의 유효성을 검사하고 명령을 매개 변수화하여 SQL 삽입 공격을 방지해야 합니다. 

함수에 들어오는 데이터가 이미 유효성 검사를 받았거나 삭제된 것으로 간주하지 마세요. 또한 출력 바인딩에 기록되는 데이터가 유효한지 확인하는 것이 좋습니다. 

### <a name="handle-errors"></a>오류 처리

기본적인 것으로 보이지만 함수에서 양호한 오류 처리를 작성하는 것이 중요합니다. 처리되지 않은 오류는 호스트에 대해 버블링되며 런타임에 의해 처리됩니다. 바인딩마다 오류를 다르게 처리합니다. 자세한 내용은 [Azure Functions 오류 처리](functions-bindings-error-pages.md)를 참조하세요.

### <a name="disable-remote-debugging"></a>원격 디버깅 사용 안 함

함수를 적극적으로 디버깅하는 경우를 제외하고 원격 디버깅을 사용하지 않도록 설정되어 있는지 확인합니다. 포털에서 함수 앱 **구성** 의 **일반 설정** 에서 원격 디버깅을 사용하지 않도록 설정할 수 있습니다. 

### <a name="restrict-cors-access"></a>CORS 액세스 제한

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

허용된 원본 목록에서 와일드카드를 사용하지 마세요. 대신 요청을 가져올 특정 도메인을 나열합니다.

### <a name="store-data-encrypted"></a>암호화된 데이터 저장

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>안전한 배포

Azure Functions 도구를 통합하면 로컬 함수 프로젝트 코드를 Azure에 쉽게 게시할 수 있습니다. Azure Functions 토폴로지에 대한 보안을 고려할 때 배포가 작동하는 방식을 이해하는 것이 중요합니다.   

### <a name="deployment-credentials"></a>배포 자격 증명

App Service 배포에는 배포 자격 증명 집합이 필요합니다. 이러한 배포 자격 증명은 함수 앱 배포의 보안을 유지하는 데 사용됩니다. 배포 자격 증명은 App Service 플랫폼에서 관리하며, 미사용 시에는 암호화됩니다. 

배포 자격 증명은 두 가지 종류가 있습니다.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

현재 Key Vault는 배포 자격 증명에 대해 지원되지 않습니다. 배포 자격 증명 관리에 대해 자세히 알아보려면 [Azure App Service에 대한 배포 자격 증명 구성](../app-service/deploy-configure-credentials.md)을 참조하세요.

### <a name="disable-ftp"></a>FTP 미사용

기본적으로 각 함수 앱은 FTP 엔드포인트 사용이 설정됩니다. FTP 엔드포인트는 배포 자격 증명을 사용하여 액세스됩니다. 

FTP는 함수 코드 배포에 대해 권장되지 않습니다. FTP 배포는 수동 작업이며, 트리거를 동기화해야 합니다. 자세히 알아보려면 [FTP 배포](functions-deployment-technologies.md#ftp)를 참조하세요. 

FTP 사용을 계획하지 않은 경우 포털에서 사용하지 않도록 설정해야 합니다. FTP 사용을 선택한 경우 [FTPS를 적용](../app-service/deploy-ftp.md#enforce-ftps)해야 합니다.

### <a name="secure-the-scm-endpoint"></a>SCM 엔드포인트 보안

모든 함수 앱에는 배포 및 기타 App Service [사이트 확장](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)에서 사용하는 `scm` 서비스 엔드포인트가 있습니다. 함수 앱에 대한 SCM 엔드포인트는 항상 `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>` 형식의 URL입니다. 네트워크 격리를 사용하여 함수를 보호하는 경우에도 이 엔드포인트를 고려해야 합니다. 

별도의 SCM 엔드포인트를 통해 가상 네트워크에서 격리 또는 실행 중인 함수 앱에 대한 배포 및 기타 고급 도구 기능을 제어할 수 있습니다. SCM 엔드포인트는 기본 인증(배포 자격 증명 사용) 및 Azure Portal 자격 증명을 사용한 SSO(Single Sign-On)을 모두 지원합니다. 자세한 내용은 [Kudu 서비스 액세스](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)를 참조하세요. 

### <a name="continuous-security-validation"></a>지속적인 보안 유효성 검사

개발 프로세스의 모든 단계에서 보안을 고려해야 하므로 지속적인 배포 환경에서도 보안 유효성 검사를 구현하는 것이 좋습니다. 이를 DevSecOps라고도 합니다. 배포 파이프라인에 Azure DevOps를 사용하여 배포 프로세스에 유효성 검사를 통합할 수 있습니다. 자세한 내용은 [CI/CD 파이프라인에 지속적인 보안 유효성 검사를 추가하는 방법](/azure/devops/migrate/security-validation-cicd-pipeline)을 참조하세요.  

## <a name="network-security"></a>네트워크 보안

함수 앱에 대한 네트워크 액세스를 제한하면 함수 엔드포인트에 액세스할 수 있는 사용자를 제어할 수 있습니다. Functions는 App Service 인프라를 활용하여 함수가 인터넷 라우팅 가능한 주소를 사용하지 않으면서 리소스에 액세스하거나 함수 엔드포인트에 대한 인터넷 액세스를 제한할 수 있습니다. 이러한 네트워킹 옵션에 대해 자세히 알아보려면 [Azure Functions 네트워킹 옵션](functions-networking-options.md)을 참조하세요.

### <a name="set-access-restrictions"></a>액세스 제한 설정

액세스 제한을 통해 앱에 대한 트래픽을 제어하는 허용/거부 규칙의 목록을 정의할 수 있습니다. 규칙은 우선 순위에 따라 평가됩니다. 규칙이 정의되어 있지 않은 경우 앱은 모든 주소의 트래픽을 허용합니다. 자세히 알아보려면 [Azure App Service 액세스 제한](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json)을 참조하세요.

### <a name="private-site-access"></a>프라이빗 사이트 액세스

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>격리로 함수 앱 배포

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>게이트웨이 서비스 사용

[Azure Application Gateway](../application-gateway/overview.md) 및 [Azure Front Door](../frontdoor/front-door-overview.md)와 같은 게이트웨이 서비스를 통해 WAF(웹 애플리케이션 방화벽)를 설정할 수 있습니다. WAF 규칙은 함수에 대한 추가 보호 계층을 제공하는 감지된 공격을 모니터링 또는 차단하는 데 사용됩니다. WAF를 설정하려면 함수 앱이 ASE에서 실행되거나 프라이빗 엔드포인트(미리 보기)를 사용해야 합니다. 자세히 알아보려면 [프라이빗 엔드포인트 사용](../app-service/networking/private-endpoint.md)을 참조하세요.    

## <a name="next-steps"></a>다음 단계

+ [Azure Functions에 대한 Azure 보안 기준](security-baseline.md)
+ [Azure Functions 진단](functions-diagnostics.md)
