---
title: '문제 해결: 알려진 문제'
titleSuffix: Azure Digital Twins
description: Azure Digital Twins의 알려진 문제를 인식하고 완화하는 데 도움을 받으세요.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 10/6/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: b80da6d98a2b70f9c0d8d84922e9043bef114d80
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133617"
---
# <a name="troubleshooting-azure-digital-twins-known-issues"></a>Azure Digital Twins 문제 해결: 알려진 문제

이 문서에서는 Azure Digital Twins와 관련된 알려진 문제에 대한 정보를 제공합니다.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell의 "400 클라이언트 오류: 잘못된 요청"

**문제 설명:** *https://shell.azure.com* 에서 Cloud Shell을 실행하는 명령이 간헐적으로 실패하고 "URL http://localhost:50342/oauth2/token 에 대한 400 클라이언트 오류: 잘못된 요청"과 전체 스택 추적이 표시될 수 있습니다.

| 어떤 영향이 있나요? | 원인 | 해결 방법 |
| --- | --- | --- |
| &nbsp;Azure&nbsp;Digital&nbsp;Twins에서 이 문제는 다음 명령 그룹에 영향을 미칩니다.<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | 이는 Cloud Shell의 알려진 문제, [Cloud Shell에서 토큰 가져오기가 400 클라이언트 오류: 잘못된 요청과 함께 간헐적으로 실패](https://github.com/Azure/azure-cli/issues/11749)로 인한 결과입니다.<br><br>이는 Azure Digital Twins 인스턴스 인증 토큰과 Cloud Shell의 기본 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md) 기반 인증의 문제를 나타냅니다. <br><br>`az dt` 또는 `az dt endpoint` 명령 그룹의 Azure Digital Twins 명령에 영향을 미치지 않는데, Cloud Shell의 관리 ID 인증 관련 문제가 없는 다른 유형의 인증 토큰(Azure Resource Manager 기반)을 사용하기 때문입니다. | 이 문제를 해결하는 한 가지 방법은 Cloud Shell에서 `az login` 명령을 다시 실행하고 다음에 나오는 로그인 단계를 완료하는 것입니다. 이 작업을 통해 관리 ID 인증 외부로 세션이 전환되어 근본적인 문제가 방지됩니다. 그런 다음, 명령을 다시 실행할 수 있습니다.<br><br>그렇지 않으면 Azure Portal에서 Cloud Shell 창을 열고 여기에서 Cloud Shell 작업을 완료합니다.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Azure Portal 아이콘 모음에 있는 Cloud Shell 아이콘의 스크린샷." lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>마지막 해결 방법은 Azure CLI 명령을 로컬로 실행할 수 있도록 컴퓨터에 [Azure CLI를 설치](/cli/azure/install-azure-cli)하는 것입니다. 로컬 CLI에서는 이 문제가 발생하지 않습니다. |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Azure.Identity 1.2.0의 대화형 브라우저 인증 문제

**문제 설명:** [Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) 라이브러리 버전 **1.2.0** 을 사용하여 Azure Digital Twins 애플리케이션에서 인증 코드를 작성할 때 [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) 메서드에 문제가 발생할 수 있습니다. 이 문제는 브라우저 창에서 인증을 시도할 때 "Azure.Identity.AuthenticationFailedException" 오류 응답으로 표시됩니다. 브라우저 창이 완전히 시작되지 않거나 사용자를 성공적으로 인증하는 것처럼 보일 수 있지만 클라이언트 애플리케이션이 오류와 함께 실패합니다.

| 어떤 영향이 있나요? | 원인 | 해결 방법 |
| --- | --- | --- |
| &nbsp;영향을 받는&nbsp;메서드는&nbsp;&nbsp;다음&nbsp;&nbsp;문서에서&nbsp;사용됩니다.<br><br>[클라이언트 앱 코딩](tutorial-code.md)<br><br>[앱 인증 코드 쓰기](how-to-authenticate-client.md)<br><br>[Azure Digital Twins API 및 SDK](concepts-apis-sdks.md) | 일부 사용자는 `Azure.Identity` 라이브러리 버전 **1.2.0** 에서 문제를 겪습니다. | 해결하려면 `Azure.Identity`의 [최신 버전](https://www.nuget.org/packages/Azure.Identity)을 사용하도록 애플리케이션을 업데이트합니다. 라이브러리 버전을 업데이트한 후 브라우저에서 예상대로 로드하고 인증해야 합니다. |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Azure.Identity 1.3.0의 기본 Azure 자격 증명 인증 문제

**문제 설명:** [Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) 라이브러리의 **1.3.0** 버전을 사용하여 인증 코드를 작성할 때 일부 사용자가 이러한 Azure Digital Twins 문서의 많은 샘플에서 사용되는 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) 메서드 관련 문제를 겪습니다. 이 문제는 코드에서 인증을 시도할 때 "Azure.Identity.AuthenticationFailedException: SharedTokenCacheCredential authentication failed" 오류 응답으로 표시됩니다.

| 어떤 영향이 있나요? | 원인 | 해결 방법 |
| --- | --- | --- |
| `DefaultAzureCredential`은 인증을 포함하는 서비스의 대부분 설명서 예제에서 사용됩니다. `Azure.Identity` 라이브러리 버전 1.3.0과 함께 `DefaultAzureCredential`을 사용하여 인증 코드를 작성하고 이 오류 메시지가 표시되는 경우 이 문제가 영향을 미치는 것입니다. | 이는 `Azure.Identity`를 사용한 일부 구성 문제로 인해 발생할 수 있습니다. | 이 문제를 해결하는 한 가지 방법은 현재 `Azure.Identity`에 대해 열려 있는 [DefaultAzureCredential 문제](https://github.com/Azure/azure-sdk/issues/1970)에서 설명하는 것과 같이 자격 증명에서 `SharedTokenCacheCredential`을 제외하는 것입니다.<br>또 다른 옵션은 이전 버전의 `Azure.Identity`(예: [버전 1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3))를 사용하도록 애플리케이션을 변경하는 것입니다. 이전 버전을 사용하면 Azure Digital Twins의 기능에 영향을 주지 않으므로 허용된 해결책입니다. |

## <a name="next-steps"></a>다음 단계

Azure Digital Twins의 보안 및 사용 권한에 대해 자세히 알아보세요.
* [Azure Digital Twins 솔루션에 대한 보안](concepts-security.md)