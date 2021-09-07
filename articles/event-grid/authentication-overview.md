---
title: Event Grid 사용자 지정 토픽, 도메인 파트너 네임스페이스에 이벤트를 게시하는 클라이언트를 인증합니다.
description: 이 문서에서는 Event Grid 사용자 지정 토픽, 도메인 및 파트너 네임스페이스에 이벤트를 게시하는 클라이언트를 인증하는 다양한 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 9e9718d6b39bd41cf91e610a01ce5f95bd0d5a18
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528995"
---
# <a name="client-authentication-when-publishing-events-to-event-grid"></a>Event Grid에 이벤트를 게시할 때 클라이언트 인증
Event Grid에 이벤트를 게시하는 클라이언트에 대한 인증은 다음 방법을 사용하여 지원됩니다.

- Azure AD(Azure Active Directory)
- 액세스 키 또는 SAS(공유 액세스 서명)

## <a name="authenticate-using-azure-active-directory-preview"></a>Azure Active Directory를 사용하여 인증(미리 보기)
Event Grid 리소스에 대한 Azure AD 통합은 클라이언트의 리소스 액세스를 세부적으로 제어하기 위해 Azure RBAC(역할 기반 액세스 제어)를 제공합니다. Azure RBAC를 사용하여 사용자, 그룹 또는 애플리케이션 서비스 주체일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 보안 주체는 Azure AD에 의해 인증되어 OAuth 2.0 토큰을 반환합니다. 토큰을 사용하여 Event Grid 리소스(토픽, 도메인 또는 파트너 네임스페이스)에 액세스하기 위한 요청을 허가할 수 있습니다. 자세한 내용은 [Microsoft ID 플랫폼을 사용하여 인증 및 권한 부여](authenticate-with-active-directory.md)를 참조하세요.


> [!IMPORTANT]
> Azure AD ID를 사용하여 사용자 또는 애플리케이션을 인증하고 권한을 부여하면 키 기반 및 SAS(공유 액세스 서명) 인증보다 뛰어난 보안과 사용 편의성을 제공합니다. Azure AD를 사용하면 인증에 사용되는 비밀을 코드에 저장할 필요가 없으며 잠재적인 보안 취약성의 위험이 있습니다. Azure Event Grid 이벤트 게시 애플리케이션에서 Azure AD를 사용하는 것이 좋습니다.

> [!NOTE]
> Azure Event Grid의 Azure AD 인증 지원이 미리 보기로 릴리스되었습니다. Kubernetes의 Azure Event Grid는 Azure AD 인증을 아직 지원하지 않습니다. 

## <a name="authenticate-using-access-keys-and-shared-access-signatures"></a>액세스 키 및 공유 액세스 서명을 사용하여 인증
**액세스 키** 또는 **SAS(공유 액세스 서명)** 토큰을 사용하여 Azure Event Grid 토픽 또는 도메인에 이벤트를 게시하는 클라이언트를 인증할 수 있습니다. 자세한 내용은 [액세스 키 사용 또는 SAS(공유 액세스 서명) 사용](authenticate-with-access-keys-shared-access-signatures.md)을 참조하세요. 
   

## <a name="next-steps"></a>다음 단계
이 문서에서는 Event Grid에 이벤트를 **게시**(이벤트 수신)할 때의 인증을 다룹니다. 이벤트를 **전달**(이벤트 송신)할 때의 인증에 대한 자세한 내용은 [이벤트 처리기에 이벤트 전달 인증](security-authentication.md)을 참조하세요. 

