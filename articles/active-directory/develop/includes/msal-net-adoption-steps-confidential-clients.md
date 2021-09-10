---
ms.openlocfilehash: fa43176a945a01651dc768558d14604d0a15b300
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113726837"
---
코드를 업데이트하기 위한 다음 단계는 모든 기밀 클라이언트 시나리오에 적용됩니다.

1. 소스 코드 `using Microsoft.Identity.Client;`에 MSAL.NET 네임스페이스를 추가합니다.
2. `AuthenticationContext`를 인스턴스화하는 대신 `ConfidentialClientApplicationBuilder.Create`를 사용하여 `IConfidentialClientApplication`을 인스턴스화합니다.
3. `resourceId` 문자열 대신 MSAL.NET은 범위를 사용합니다. ADAL.NET을 사용하는 애플리케이션은 사전 인증되므로 항상 `new string[] { $"{resourceId}/.default" }` 범위를 사용할 수 있습니다.
4. `AuthenticationContext.AcquireTokenAsync`에 대한 호출 대신 `IConfidentialClientApplication.AcquireTokenXXX`에 대한 호출로 교체됩니다. 여기서 *XXX* 는 시니리오에 따라 다릅니다.
