---
title: Azure 웹 pubsub의 Azure Active Directory을 사용 하 여 액세스 권한 부여
description: 이 문서에서는 Azure Active Directory를 사용 하 여 Azure 웹 PubSub 서비스 리소스에 대 한 액세스 권한을 부여 하는 방법을 설명 합니다.
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: be8c2aa41f8680df9156d7b86dd8eb2bf24ca514
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482960"
---
# <a name="authorize-access-to-web-pubsub-resources-using-azure-active-directory"></a>Azure Active Directory를 사용 하 여 웹 PubSub 리소스에 대 한 액세스 권한 부여
azure 웹 pubsub 서비스는 azure AD (Azure Active Directory)를 사용 하 여 웹 pubsub 리소스에 대 한 요청에 권한을 부여할 수 있도록 지원 합니다. Azure AD에서는 RBAC (역할 기반 액세스 제어)를 사용 하 여 보안 주체<sup>[<a href="#security-principal">1</a>]</sup>에 대 한 권한을 부여할 수 있습니다. 보안 주체는 OAuth 2.0 토큰을 반환 하는 Azure AD에서 인증 됩니다. 그런 다음 토큰을 사용 하 여 웹 PubSub 리소스에 대 한 요청을 인증할 수 있습니다.

Azure AD를 사용 하 여 웹 PubSub에 대해 요청을 인증 하면 액세스 키 권한 부여를 통해 뛰어난 보안과 사용 편의성을 제공 합니다. 가능 하면 필요한 최소 권한으로 액세스를 보장 하기 위해 웹 PubSub 리소스와 함께 Azure AD 인증을 사용 하는 것이 좋습니다.

<a id="security-principal"></a>
 *[1] 보안 주체: 사용자/리소스 그룹, 응용 프로그램 또는 시스템 할당 id 및 사용자 할당 id와 같은 서비스 사용자입니다.*

## <a name="overview-of-azure-ad-for-web-pubsub"></a>웹 PubSub 용 Azure AD 개요

보안 주체가 웹 PubSub 리소스에 액세스 하려고 하는 경우 요청에 권한이 부여 되어야 합니다. Azure AD를 사용 하면 리소스에 대 한 액세스에 두 단계가 필요 합니다. 

1. 먼저, Azure에서 보안 주체를 인증 해야 하며,이는 OAuth 2.0 토큰을 반환 합니다. 
2. 그런 다음 토큰은 웹 PubSub 리소스에 대 한 요청의 일부로 전달 되 고 서비스에서 지정 된 리소스에 대 한 액세스 권한을 부여 하는 데 사용 됩니다.

### <a name="client-side-authentication-while-using-azure-ad"></a>Azure AD를 사용 하는 동안 클라이언트 쪽 인증

액세스 키를 사용 하는 경우 키가 협상 서버 (또는 함수 앱)와 웹 PubSub 리소스 사이에서 공유 됩니다. 즉, 웹 PubSub 서비스에서 공유 키를 사용 하 여 클라이언트 연결 요청을 인증할 수 있습니다. 그러나 Azure AD를 사용 하 여 권한을 부여 하는 경우에는 공유 키가 없습니다. 

이 문제를 해결 하기 위해 Azure 웹 PubSub 서비스에 연결 하는 데 사용할 수 있는 클라이언트 토큰을 생성 하는 REST API을 제공 했습니다.

1. 먼저 협상 서버는 자신을 인증 하기 위해 Azure의 **Aad 토큰이** 필요 합니다.
1. 둘째로, 협상 서버는 **Aad 토큰** 을 사용 하 여 웹 Pubsub Auth API를 호출 하 여 **클라이언트 토큰** 을 가져오고 클라이언트에 반환 합니다.
1. 마지막으로 클라이언트는 **클라이언트 토큰** 을 사용 하 여 Azure 웹 pubsub 서비스에 연결 합니다.

지원 되는 프로그래밍 언어에 대 한 도우미 함수 (예: ' GenerateClientAccessUri ')를 제공 했습니다.

## <a name="assign-azure-roles-for-access-rights"></a>액세스 권한에 대한 Azure 역할 할당

azure AD (Azure Active Directory)는 [azure 역할 기반 액세스 제어](../role-based-access-control/overview.md)를 통해 보안 리소스에 대 한 액세스 권한을 부여 합니다. Azure 웹 PubSub는 웹 PubSub 리소스에 액세스 하는 데 사용 되는 일반적인 권한 집합을 포함 하는 Azure 기본 제공 역할 집합을 정의 합니다. 웹 PubSub 리소스에 액세스 하기 위한 사용자 지정 역할을 정의할 수도 있습니다.

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

## <a name="azure-built-in-roles-for-web-pubsub-resources"></a>웹 PubSub 리소스에 대 한 Azure 기본 제공 역할입니다.

- `Web PubSub Service Owner`

    읽기/쓰기 REST Api 및 인증 Api를 비롯 한 데이터 평면 사용 권한에 대 한 모든 권한

    업스트림 서버를 빌드하기 위해 가장 일반적으로 사용 되는 역할입니다.

- `Web PubSub Service Reader`

    을 사용 하 여 웹 PubSub 리소스에 대 한 읽기 전용 REST Api 권한을 부여 합니다.

    일반적으로 웹 PubSub 데이터-평면 **읽기** 전용 REST api **만** 호출 하는 모니터링 도구를 작성 하려는 경우에 사용 됩니다.

## <a name="next-steps"></a>다음 단계

Azure 응용 프로그램을 만들고 AAD Auth를 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
- [Azure 응용 프로그램에서 Azure AD를 사용 하 여 웹 PubSub 리소스에 대 한 요청 권한 부여](howto-authorize-from-application.md)

관리 id를 구성 하 고 AAD Auth를 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
- [관리 id에서 Azure AD를 사용 하 여 웹 PubSub 리소스에 대 한 요청 권한 부여](howto-authorize-from-managed-identity.md)

역할 및 역할 할당에 대 한 자세한 내용은 다음을 참조 하세요. 
- [Azure 역할 기반 액세스 제어 란?](../role-based-access-control/overview.md)

사용자 지정 역할을 만드는 방법에 대 한 자세한 내용은 다음을 참조 하세요. 
- [사용자 지정 역할을 만드는 단계](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)