---
title: Azure Relay 인증 및 권한 부여 | Microsoft Docs
description: 이 문서에서는 Azure Relay 서비스를 사용하는 SAS(공유 액세스 서명) 인증에 대한 개요를 제공합니다.
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: dbabee7f49e4f905c34a91dcb6095e7eab3faa0f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566975"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay 인증 및 권한 부여
Azure AD(Azure Active Directory) 및 SAS(공유 액세스 서명)라는 두 가지 방법으로 Azure Relay 리소스에 대한 액세스를 인증하고 권한을 부여할 수 있습니다. 이 문서에서는 이 두 가지 유형의 보안 메커니즘을 사용하는 방법에 관해 자세히 설명합니다.

## <a name="azure-active-directory-preview"></a>Azure Active Directory(미리 보기)
Azure Relay 리소스에 대한 Azure AD 통합은 클라이언트의 리소스 액세스를 세부적으로 제어하기 위해 Azure RBAC(Azure 역할 기반 액세스 제어)를 제공합니다. Azure RBAC를 사용하여 사용자, 그룹 또는 애플리케이션 서비스 주체일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 보안 주체는 Azure AD에 의해 인증되어 OAuth 2.0 토큰을 반환합니다. 토큰은 Azure Relay 리소스에 대한 액세스 요청에 권한을 부여하는 데 사용할 수 있습니다.

Azure AD 인증에 관한 자세한 내용은 다음 문서를 참조하세요.
- [관리되는 ID를 사용하여 인증](authenticate-managed-identity.md)
- [Azure Active Directory 애플리케이션에서 인증](authenticate-application.md)

> [!IMPORTANT]
> Azure AD에서 반환된 OAuth 2.0 토큰을 사용하여 사용자 또는 애플리케이션에 권한을 부여하면 SAS(공유 액세스 서명)를 통해 뛰어난 보안과 사용 편의성이 제공됩니다. Azure AD를 사용하면 코드에 토큰을 저장하고 잠재적인 보안 취약점을 감수할 필요가 없습니다. 가능하면 Azure Relay 애플리케이션에서 Azure AD를 사용하는 것이 좋습니다.

### <a name="built-in-roles"></a>기본 제공 역할
Azure Relay의 경우 Azure Portal 및 Azure 리소스 관리 API를 통한 네임스페이스 및 관련된 모든 리소스의 관리는 이미 Azure RBAC 모델을 사용하여 보호되고 있습니다. Azure는 Relay 네임스페이스에 대한 액세스 권한을 부여하기 위해 아래와 같은 Azure 기본 제공 역할을 제공합니다.

| 역할 | 설명 | 
| ---- | ----------- | 
| [Azure Relay 소유자](../role-based-access-control/built-in-roles.md#azure-relay-owner) | 이 역할을 사용하여 Azure Relay 리소스에 대한 **전체** 액세스 권한을 부여합니다. |
| [Azure Relay 수신자](../role-based-access-control/built-in-roles.md#azure-relay-listener) | 이 역할을 사용하여 Azure Relay 리소스에 대한 **수신 대기 및 엔터티 읽기** 액세스 권한을 부여합니다. |
| [Azure Relay 발신자](../role-based-access-control/built-in-roles.md#azure-relay-sender) | 이 역할을 사용하여 Azure Relay 리소스에 대한 **보내기 및 엔터티 읽기** 액세스 권한을 부여합니다. | 


## <a name="shared-access-signature"></a>공유 액세스 서명
애플리케이션은 SAS(공유 액세스 서명) 인증을 사용하여 Azure Relay에 인증할 수 있습니다. SAS 인증을 사용하면 애플리케이션이 Relay 네임스페이스에 구성된 액세스 키를 통해 Azure Relay 서비스에 인증할 수 있습니다. 그런 다음 이 키를 사용하여 클라이언트가 Relay 서비스를 인증하는 데 사용할 수 있는 공유 액세스 서명 토큰을 생성할 수 있습니다.

[SAS 인증](../service-bus-messaging/service-bus-sas.md)을 사용하면 특정 권한으로 Azure Relay 리소스에 액세스하는 권한을 사용자에게 부여할 수 있습니다. SAS 인증은 리소스에 연결된 권한이 있는 암호화 키의 구성을 포함합니다. 클라이언트는 액세스된 리소스 URI 및 구성된 키로 서명된 만료로 구성된 SAS 토큰을 제공하여 해당 리소스에 대한 액세스를 얻을 수 있습니다.

Relay 네임스페이스에서 SAS에 대한 키를 구성할 수 있습니다. Service Bus 메시징과 달리 [Relay 하이브리드 연결](relay-hybrid-connections-protocol.md)은 무단 또는 익명 발신자를 지원합니다. 포털에서 다음 스크린샷에 표시된 것처럼 만들 때 엔터티에 대한 익명 액세스를 활성화할 수 있습니다.

![“하이브리드 연결 만들기” 대화 상자에 “이름” 텍스트 상자와 “클라이언트 인증 필요” 레이블이 지정되고 선택된 확인란이 있습니다.][0]

SAS를 사용하려면 다음 속성을 구성하는 Relay 네임스페이스에서 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 개체를 구성할 수 있습니다.

* *KeyName* 입니다.
* *PrimaryKey* 는 SAS 토큰을 서명/확인하는 데 사용되는 암호화 키입니다.
* *SecondaryKey* 는 SAS 토큰을 서명/확인하는 데 사용되는 암호화 키입니다.
* *권한* 입니다.

네임스페이스 수준에서 구성된 권한 부여 규칙은 해당 키를 사용하여 서명된 토큰으로 클라이언트에 대한 네임스페이스의 모든 relay 연결에 액세스를 부여할 수 있습니다. 권한 부여 규칙은 Relay 네임스페이스에서 최대 12개까지 구성할 수 있습니다. 기본적으로 모든 권한이 있는 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 은 처음으로 프로비전될 때 모든 네임스페이스에 대해 구성됩니다.

엔터티에 액세스하려면 클라이언트는 특정 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)을 사용하여 생성된 SAS 토큰이 필요합니다. 액세스를 요청하는 리소스 URI로 구성된 리소스 문자열의 HMAC-SHA256 및 권한 부여 규칙에 연결된 암호화 키가 있는 만료를 사용하여 SAS 토큰을 생성합니다.

Azure Relay에 대한 SAS 인증 지원은 Azure.NET SDK 버전 2.0 이상에 포함됩니다. SAS는 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)에 대한 지원을 포함합니다. 연결 문자열을 매개 변수로 허용하는 모든 API는 SAS 연결 문자열에 대한 지원을 포함합니다.

## <a name="next-steps"></a>다음 단계

- SAS에 대한 자세한 내용은 [공유 액세스 서명을 사용한 Service Bus 인증](../service-bus-messaging/service-bus-sas.md)을 계속 읽으세요.
- 하이브리드 연결 기능에 대한 자세한 내용은 [Azure Relay 하이브리드 연결 프로토콜 가이드](relay-hybrid-connections-protocol.md)를 참조하세요.
- Service Bus 메시징 인증 및 권한 부여에 관한 해당 정보는 [Service Bus 인증 및 권한 부여](../service-bus-messaging/service-bus-authentication-and-authorization.md)를 참조하세요. 

[0]: ./media/relay-authentication-and-authorization/hcanon.png