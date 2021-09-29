---
title: Communication Services에서 Azure Active Directory 사용
titleSuffix: An Azure Communication Services quickstart
description: Azure Active Directory를 사용하여 Azure VM, 함수 앱, 기타 리소스에서 실행되는 애플리케이션에서 Azure Communication Services 액세스 권한을 부여할 수 있습니다.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.subservice: identity
ms.topic: quickstart
ms.date: 06/30/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 39c1be0ddd7e2d80800faae96c42f983220307fb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677248"
---
# <a name="quickstart-authenticate-using-azure-active-directory"></a>빠른 시작: Azure Active Directory를 사용하여 인증

Azure Active Directory를 사용하여 Azure Communication Services를 시작하세요. Communication Services ID 및 SMS SDK는 Azure AD(Azure Active Directory) 인증을 지원합니다.

이 빠른 시작에서는 Active Directory를 지원하는 Azure 환경에서 ID 및 SMS SDK에 대한 액세스 권한을 부여하는 방법을 보여 줍니다. 또한 작업에 대한 서비스 주체를 만들어 개발 환경에서 코드를 테스트하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free)
- 활성 Azure Communication Services 리소스. 없는 경우 [Communication Services 리소스 만들기](../create-communication-resource.md)를 참조하세요.
- SMS를 보내려면 [전화번호](../telephony-sms/get-phone-number.md)가 필요합니다.
- 개발 환경용 서비스 주체에 대해서는 [관리 ID로 액세스 권한 부여](./service-principal-from-cli.md)를 참조하세요

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/active-directory/service-principal-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/active-directory/service-principal-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/active-directory/service-principal-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/active-directory/service-principal-python.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계

- [Azure 역할 기반 액세스 제어에 대해 자세히 알아보기](../../../../articles/role-based-access-control/index.yml)
- [.NET용 Azure ID 라이브러리에 대해 자세히 알아보기](/dotnet/api/overview/azure/identity-readme)
- [사용자 액세스 토큰 만들기](../../quickstarts/access-tokens.md)
- [SMS 메시지 보내기](../../quickstarts/telephony-sms/send.md)
- [SMS에 대한 자세한 정보](../../concepts/telephony-sms/concepts.md)
- [테스트를 위해 빠르게 ID 만들기](./quick-create-identity.md).

