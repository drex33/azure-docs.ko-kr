---
title: Azure Cognitive Services 보안
titleSuffix: Azure Cognitive Services
description: Cognitive Services 사용에 대한 다양한 보안 고려 사항을 알아봅니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: erhopf
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: b77eadcbd46c4cac442ed26af94d5781d225806a
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475785"
---
# <a name="azure-cognitive-services-security"></a>Azure Cognitive Services 보안

모든 애플리케이션을 개발할 때는 보안을 최우선 순위로 고려해야 합니다. AI를 사용하는 애플리케이션의 경우 보안은 더 중요합니다. 이 문서에서는 전송 계층 보안, 인증, 중요 데이터의 안전한 구성 및 고객 데이터 액세스를 위한 고객 Lockbox 등 Azure Cognitive Services 보안의 다양한 측면을 간략하게 설명합니다.

## <a name="transport-layer-security-tls"></a>TLS(전송 계층 보안)

모든 Cognitive Services 엔드포인트는 HTTP 적용 TLS 1.2를 통해 공개됩니다. 적용되는 보안 프로토콜을 사용하여 Cognitive Services 엔드포인트를 호출하려는 소비자는 다음 지침을 준수해야 합니다.

* 클라이언트 OS(운영 체제)는 TLS 1.2를 지원해야 합니다.
* HTTP 호출에 사용되는 언어(및 플랫폼)는 요청의 일환으로 TLS 1.2를 지정해야 합니다.
  * TLS 지정은 언어 및 플랫폼에 따라 암시적 또는 명시적으로 수행됩니다.

.NET 사용자의 경우 <a href="/dotnet/framework/network-programming/tls" target="_blank">전송 계층 보안 모범 사례</a>를 고려하세요.

## <a name="authentication"></a>인증

인증에 대해 논의할 때 몇 가지 일반적인 오해가 있습니다. 인증 및 권한 부여는 서로 혼동되는 경우가 많습니다. 또한 ID는 보안의 주요 구성 요소입니다. ID는 <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">보안 주체</a>에 대한 정보 컬렉션입니다. IdP(ID 공급자)는 인증 서비스에 ID를 제공합니다. 인증은 사용자의 ID를 확인하는 동작입니다. 권한 부여는 지정된 ID에 대한 리소스에 액세스 권리 및 권한을 지정하는 것입니다. 몇 가지 Cognitive Services 제공 사항에는 Azure 역할 기반 액세스 제어(Azure RBAC)가 포함되어 있습니다. Azure RBAC는 수동으로 보안 주체를 관리하는 것과 관련된 일부 공식 절차를 간소화하는 데 사용할 수 있습니다. 자세한 내용은 [Azure 리소스에 대한 Azure 역할 기반 액세스 제어](../role-based-access-control/overview.md)를 참조하세요.

구독 키, 액세스 토큰 및 Azure Active Directory(AAD)를 사용한 인증에 대한 자세한 내용은 <a href="/azure/cognitive-services/authentication" target="_blank">Azure Cognitive Services에 대한 요청 인증</a>을 참조하세요.

## <a name="environment-variables-and-application-configuration"></a>환경 변수 및 애플리케이션 구성

환경 변수는 특정 환경 내에 저장된 이름-값 쌍입니다. 중요한 데이터에 대해 하드 코드된 값을 사용하는 대신 환경 변수를 사용하는 것이 더 안전합니다. 하드 코드된 값은 안전하지 않으므로 피해야 합니다.

> [!CAUTION]
> 중요한 데이터에 대해 하드 코드된 값을 사용하지 **마세요**. 사용하면 중대한 보안 취약점이 됩니다.

> [!NOTE]
> 환경 변수는 일반 텍스트로 저장되지만 환경에 격리됩니다. 환경이 손상되는 경우 환경의 변수 또한 손상됩니다.

### <a name="set-environment-variable"></a>환경 변수 설정

환경 변수를 설정하려면 다음 명령 중 하나를 사용합니다. 여기서 `ENVIRONMENT_VARIABLE_KEY`는 명명된 키이고 `value`는 환경 변수에 저장된 값입니다.

# <a name="command-line"></a>[명령줄](#tab/command-line)

값이 지정되면 지속형 환경 변수를 만들고 할당합니다.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

명령 프롬프트의 ‘새’ 인스턴스에서 환경 변수를 읽습니다.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

값이 지정되면 지속형 환경 변수를 만들고 할당합니다.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

**Windows PowerShell** 의 새 인스턴스에서 환경 변수를 읽습니다.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

값이 지정되면 지속형 환경 변수를 만들고 할당합니다.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

**Bash** 의 새 인스턴스에서 환경 변수를 읽습니다.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> 환경 변수를 설정한 후에는 IDE(통합 개발 환경)를 다시 시작하여 새로 추가된 환경 변수를 사용할 수 있는지 확인하세요.

### <a name="get-environment-variable"></a>환경 변수 가져오기

환경 변수를 가져오려면 메모리로 읽어야 합니다. 사용 중인 언어에 따라 다음 코드 조각을 고려합니다. 이러한 코드 조각은 `ENVIRONMENT_VARIABLE_KEY`에 따라 환경 변수를 가져오고 `value`라는 값에 할당하는 방법을 보여줍니다.

# <a name="c"></a>[C#](#tab/csharp)

자세한 내용은 <a href="/dotnet/api/system.environment.getenvironmentvariable" target="_blank">`Environment.GetEnvironmentVariable` </a>을 참조하세요.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

자세한 내용은 <a href="/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank">`getenv` </a>를 참조하세요.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

자세한 내용은 <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank">`System.getenv` </a>를 참조하세요.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.JS](#tab/node-js)

자세한 내용은 <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank">`process.env` </a>를 참조하세요.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

자세한 내용은 <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank">`os.environ` </a>을 참조하세요.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

자세한 내용은 <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank">`environment` </a>를 참조하세요.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>고객 Lockbox

[Microsoft Azure에 대한 고객 Lockbox](../security/fundamentals/customer-lockbox-overview.md)는 고객이 고객 데이터 액세스 요청을 검토하고 승인하거나 거부할 수 있는 인터페이스를 제공합니다. 지원 요청 시 Microsoft 엔지니어가 고객 데이터에 액세스해야 하는 경우에 사용됩니다. 이후 검토 및 감사를 위해 고객 Lockbox 요청이 시작, 추적 및 저장되는 방법에 대한 자세한 내용은 [고객 Lockbox](../security/fundamentals/customer-lockbox-overview.md)를 참조하세요. 

고객 Lockbox는 이 서비스에 대해 사용 가능합니다.

* Translator

다음 서비스의 경우 Microsoft 엔지니어가 E0 계층의 고객 데이터에 액세스하지 않습니다. 

* 언어 이해
* Face
* Content Moderator
* Personalizer

E0 SKU를 사용하는 기능을 요청하려면 이  [요청 양식](https://aka.ms/cogsvc-cmk)을 작성하고 제출합니다. 요청 상태에 대한 답변을 받는 데는 영업일 기준 약 3~5일이 소요됩니다. 요청에 따라 공간이 확보되면 큐에 배치되고 승인될 수 있습니다. LUIS와 함께 E0 SKU를 사용하도록 승인되면 Azure Portal에서 새 리소스를 만들고 해당 가격 책정 계층으로 E0를 선택해야 합니다. 사용자는 F0에서 새 E0 SKU로 업그레이드할 수 없습니다.

Speech Service는 현재 고객 Lockbox를 지원하지 않습니다. 그러나 고객 데이터는 BYOS(사용자 고유의 스토리지 가져오기)를 사용하여 저장할 수 있으므로 고객 Lockbox와 유사한 데이터 제어를 수행할 수 있습니다. Speech Service 데이터는 Speech 리소스가 생성된 지역에서 유지되고 처리됩니다. 이는 미사용 데이터와 전송 중인 데이터에 적용됩니다. Custom Speech 및 Custom Voice와 같은 사용자 지정 기능을 사용할 때 모든 고객 데이터는 BYOS(사용된 경우) 및 Speech Service 리소스가 있는 동일한 지역에서 전송, 저장 및 처리됩니다.

> [!IMPORTANT]
> Microsoft는 고객 데이터를 사용하여 음성 모델을 개선하지 **않습니다**. 또한 엔드포인트 로깅이 사용하지 않도록 설정되고 사용자 지정이 사용되지 않은 경우에는 고객 데이터가 저장되지 않습니다. 

## <a name="next-steps"></a>다음 단계

* 다양한 [Cognitive Services](./what-are-cognitive-services.md) 살펴보기
* [Cognitive Services 가상 네트워크](cognitive-services-virtual-networks.md) 자세히 알아보기