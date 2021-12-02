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
ms.openlocfilehash: 896885e92f8a18adf5347794db87f1c6cccc990f
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133436516"
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

## <a name="using-identity-for-monitoring-and-metrics"></a>모니터링 및 메트릭에 ID 사용

사용자 ID는 Azure Monitor 통해 수집된 로그 및 메트릭의 기본 키 역할을 하기 위한 것입니다. 예를 들어 특정 사용자의 모든 호출을 보려면 특정 Azure Communication Services ID(또는 ID)를 단일 사용자에게 매핑하는 방식으로 인증을 설정해야 합니다. [인증 개념,](../concepts/authentication.md) [로그 분석을](../concepts/analytics/log-analytics.md)통한 진단 호출 및 사용 가능한 메트릭에 대해 자세히 [알아봅니다.](../concepts/metrics.md)

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
