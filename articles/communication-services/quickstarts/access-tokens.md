---
title: 빠른 시작 - 액세스 토큰 만들기 및 관리
titleSuffix: An Azure Communication Services quickstart
description: Azure Communication Services ID SDK를 사용하여 ID 및 액세스 토큰을 관리하는 방법을 알아봅니다.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 11/17/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: identity
zone_pivot_groups: acs-js-csharp-java-python
ms.custom: mode-other
ms.openlocfilehash: 2b163db12209df247d5a6cbd32b9e76fcc50b7ff
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133039360"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>빠른 시작: 액세스 토큰 만들기 및 관리

액세스 토큰을 통해 ACS SDK가 Azure Communication Services에 대해 특정 ID로 직접 [인증](../concepts/authentication.md)할 수 있습니다. 사용자가 애플리케이션 내에서 호출 또는 채팅 스레드에 조인하도록 하려면 일부를 만들어야 합니다. 

ACS SDK를 사용하여 ID를 만들고 액세스 토큰을 관리할 수도 있습니다. 이 빠른 시작에서는 이 작업을 수행하는 방법을 알아봅니다. 프로덕션 사용 사례의 경우 [서버 쪽 서비스](../concepts/client-and-server-architecture.md)에서 액세스 토큰을 생성하는 것이 좋습니다.

이 자습서 전체에서 이미지에 표시되는 모든 가격은 데모용일 뿐입니다.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/access-tokens/access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/access-tokens/access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/access-tokens/access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/access-tokens/access-token-java.md)]
::: zone-end

앱의 출력은 완료된 각 작업을 설명합니다.
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-1ce9-31b4-54b7-a43a0d006a52

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Successfully revoked all access tokens for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](./create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음과 같은 방법을 배웠습니다.

> [!div class="checklist"]
> * ID 관리
> * 액세스 토큰 발급
> * Communication Services ID SDK 사용


> [!div class="nextstepaction"]
> [앱에 음성 통화 추가](./voice-video-calling/getting-started-with-calling.md)

다음을 수행할 수도 있습니다.

 - [인증에 대한 자세한 정보](../concepts/authentication.md)
 - [앱에 채팅 추가](./chat/get-started.md)
 - [클라이언트 및 서버 아키텍처에 대한 자세한 정보](../concepts/client-and-server-architecture.md)
