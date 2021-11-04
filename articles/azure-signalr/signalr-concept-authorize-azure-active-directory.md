---
title: Azure SignalR Service에 대 한 Azure Active Directory를 사용 하 여 액세스 권한 부여
description: 이 문서에서는 Azure Active Directory를 사용하여 Azure SignalR Service 리소스에 대한 액세스 권한을 부여하는 방법을 설명합니다.
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 0e644f792e47243f3c6924cd8546efb5661a9193
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482907"
---
# <a name="authorize-access-with-azure-active-directory-for-azure-signalr-service"></a>Azure SignalR Service에 대 한 Azure Active Directory를 사용 하 여 액세스 권한 부여

azure SignalR Service는 azure AD (Azure Active Directory) 사용을 지원 하 여 SignalR 리소스에 대 한 요청에 권한을 부여 합니다. Azure AD에서는 RBAC (역할 기반 액세스 제어)를 사용 하 여 보안 주체<sup>[<a href="#security-principal">1</a>]</sup>에 대 한 권한을 부여할 수 있습니다. 보안 주체는 OAuth 2.0 토큰을 반환 하는 Azure AD에서 인증 됩니다. 그런 다음 토큰을 사용 하 여 SignalR 리소스에 대 한 요청을 인증할 수 있습니다.

Azure AD를 사용 하 여 SignalR에 대 한 요청을 승인 하면 액세스 키 권한 부여를 통해 뛰어난 보안과 사용 편의성을 제공 합니다. 가능 하면 필요한 최소 권한으로 액세스를 보장 하기 위해 SignalR 리소스와 함께 Azure AD 인증을 사용 하는 것이 좋습니다.

<a id="security-principal"></a>
 *[1] 보안 주체: 사용자/리소스 그룹, 응용 프로그램 또는 시스템 할당 id 및 사용자 할당 id와 같은 서비스 사용자입니다.*

## <a name="overview-of-azure-ad-for-signalr"></a>SignalR 용 Azure AD 개요

보안 주체가 SignalR 리소스에 대 한 액세스를 시도 하는 경우 요청에 권한이 부여 되어야 합니다. Azure AD를 사용 하 여 리소스에 액세스 하려면 2 단계를 수행 해야 합니다. 

1. 먼저, Azure에서 보안 주체를 인증 해야 하며,이는 OAuth 2.0 토큰을 반환 합니다. 
2. 그런 다음 토큰은 SignalR 리소스에 대 한 요청의 일부로 전달 되 고 서비스에서 지정 된 리소스에 대 한 액세스 권한을 부여 하는 데 사용 됩니다.

### <a name="client-side-authentication-while-using-azure-ad"></a>Azure AD를 사용 하는 동안 클라이언트 쪽 인증

액세스 키를 사용 하는 경우 키가 앱 서버 (또는 함수 앱)와 SignalR 리소스 간에 공유 되므로 SignalR 서비스에서 공유 키를 사용 하 여 클라이언트 연결 요청을 인증할 수 있습니다. 그러나 Azure AD를 사용 하 여 권한을 부여 하는 경우에는 공유 키가 없습니다. 

이 문제를 해결 하기 위해 클라이언트 연결에 대 한 토큰에 서명 하는 데 사용할 수 있는 **임시 액세스 키** 를 도입 했습니다. 워크플로에는 네 단계가 포함 되어 있습니다.

1. 첫째, 보안 주체는 자신을 인증 하기 위해 Azure에서 OAuth 2.0 토큰이 필요 합니다.
2. 두 번째로, 보안 주체는 SignalR Auth API를 호출 하 여 **임시 액세스 키** 를 가져옵니다.
3. 그런 다음 보안 주체는 협상 하는 동안 클라이언트 연결에 대 한 **임시 액세스 키** 를 사용 하 여 클라이언트 토큰에 서명 합니다.
4. 마지막으로 클라이언트는 클라이언트 토큰을 사용 하 여 Azure SignalR 리소스에 연결 합니다.

**임시 액세스 키는** 90 분 후에 만료 됩니다. 즉, 새 키를 가져와서 1 시간에 한 번 이전에 다시 회전 하는 것이 좋습니다. 

워크플로는 [서버 SDK](https://github.com/Azure/azure-signalr)에 빌드됩니다.

## <a name="assign-azure-roles-for-access-rights"></a>액세스 권한에 대한 Azure 역할 할당

azure AD (Azure Active Directory)는 [azure 역할 기반 액세스 제어](../role-based-access-control/overview.md)를 통해 보안 리소스에 대 한 액세스 권한을 부여 합니다. Azure SignalR는 SignalR 리소스에 액세스 하는 데 사용 되는 일반적인 권한 집합을 포함 하는 Azure 기본 제공 역할 집합을 정의 합니다. SignalR 리소스에 액세스 하기 위한 사용자 지정 역할을 정의할 수도 있습니다.

### <a name="resource-scope"></a>리소스 범위

보안 주체에 게 Azure RBAC 역할을 할당 하기 전에 보안 주체가 가져야 하는 액세스 범위를 확인 해야 할 수 있습니다. 가능한 가장 좁은 범위만 부여 하는 것이 좋습니다. 더 광범위한 범위에서 정의된 Azure RBAC 역할은 그 아래에 있는 리소스에 상속됩니다.

가장 좁은 범위에서 시작 하 여 다음 수준에서 Azure SignalR 리소스에 대 한 액세스 범위를 지정할 수 있습니다.

- **개별 리소스입니다.** 

  이 범위에서 역할 할당은 대상 리소스에만 적용 됩니다.

- **리소스 그룹입니다.** 

  이 범위에서 역할 할당은 리소스 그룹의 모든 리소스에 적용 됩니다.

- **구독.**

  이 범위에서 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 리소스에 적용 됩니다.

- **관리 그룹.** 
  
  이 범위에서 역할 할당은 관리 그룹의 모든 구독에 있는 모든 리소스 그룹의 모든 리소스에 적용 됩니다.

## <a name="azure-built-in-roles-for-signalr-resources"></a>SignalR 리소스에 대 한 Azure 기본 제공 역할

- [SignalR 앱 서버](../role-based-access-control/built-in-roles.md#signalr-app-server-preview)

    Websocket 연결 만들기 API 및 인증 Api에 액세스 합니다.
    
    이는 앱 서버에 가장 일반적으로 사용 되는 역할입니다.

- [SignalR Service 소유자](../role-based-access-control/built-in-roles.md#signalr-service-owner)

    모든 REST Api, WebSocket 연결 만들기 API 및 인증 Api를 포함 하 여 모든 데이터 평면 Api에 대 한 모든 권한을 가집니다.

    **서버** 를 사용 하지 않는 모드에서는 REST Api 권한과 인증 API 권한이 모두 필요 하므로 Azure AD를 사용 하 여 권한 부여를 지원 하기 위해이 역할을 사용 해야 합니다.

- [SignalR REST API 소유자](../role-based-access-control/built-in-roles.md#signalr-rest-api-owner)

    데이터 평면 REST Api에 대 한 모든 권한

    일반적으로 연결 및 그룹을 관리 하지만 연결을 설정 하거나 인증 Api를 호출 **하지** 않는 관리 도구를 작성 하려는 경우에 사용 됩니다.

- [SignalR REST API 판독기](../role-based-access-control/built-in-roles.md#signalr-rest-api-reader)

    데이터 평면 REST Api에 대 한 읽기 전용 액세스입니다.

    SignalR 데이터 평면 **읽기** 전용 REST api **만** 호출 하는 모니터링 도구를 작성 하려는 경우에 일반적으로 사용 됩니다.

## <a name="next-steps"></a>다음 단계

Azure 응용 프로그램을 만들고 Azure AD 인증을 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
- [Azure 응용 프로그램에서 Azure AD를 사용 하 여 리소스를 SignalR 하는 요청 권한 부여](signalr-howto-authorize-application.md)

관리 id를 구성 하 고 Azure AD 인증을 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
- [관리 id에서 Azure AD를 사용 하 여 리소스를 SignalR 하는 요청 권한 부여](signalr-howto-authorize-managed-identity.md)

역할 및 역할 할당에 대 한 자세한 내용은 다음을 참조 하세요. 
- [AZURE RBAC (역할 기반 액세스 제어) 란?](../role-based-access-control/overview.md)

사용자 지정 역할을 만드는 방법에 대 한 자세한 내용은 다음을 참조 하세요. 
- [사용자 지정 역할을 만드는 단계](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)