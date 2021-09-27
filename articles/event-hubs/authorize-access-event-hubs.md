---
title: Azure Event Hubs에 대한 액세스 권한 부여
description: 이 문서에서는 Azure Event Hubs 리소스에 대한 액세스 권한을 부여하는 여러 옵션에 대한 정보를 제공합니다.
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: spelluru
ms.openlocfilehash: df4498de020b2e389622fb5f495e5f42f69f186d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561028"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Azure Event Hubs에 대한 액세스 권한 부여
Event Hubs에서 이벤트/데이터를 게시하거나 이용할 때마다 클라이언트가 Event Hubs 리소스에 액세스하려고 합니다. 안전한 리소스에 대한 모든 요청은 서비스에서 클라이언트가 데이터를 게시/이용하는 데 필요한 권한을 가지고 있는지 확인할 수 있도록 권한을 부여해야 합니다. 

Azure Event Hubs는 안전한 리소스에 대한 액세스 권한을 부여하는 다음과 같은 옵션을 제공합니다.

- Azure Active Directory
- 공유 액세스 서명

> [!NOTE]
> 이 문서는 Event Hubs 및 [Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) 시나리오에 모두 적용됩니다. 

## <a name="azure-active-directory"></a>Azure Active Directory
Event Hubs 리소스에 대한 Azure AD(Azure Active Directory) 통합은 클라이언트에서 리소스에 대한 액세스를 세부적으로 제어할 수 있도록 Azure RBAC(역할 기반 액세스 제어)를 제공합니다. Azure RBAC를 사용하여 사용자, 그룹 또는 애플리케이션 서비스 주체일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 보안 주체는 Azure AD에 의해 인증되어 OAuth 2.0 토큰을 반환합니다. 토큰은 Event Hubs 리소스에 대한 액세스 요청에 권한을 부여하는 데 사용할 수 있습니다.

Azure AD 인증에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Active Directory를 사용하여 Azure Event Hubs에 대한 요청 인증](authenticate-application.md)
- [Azure Active Directory를 사용하여 Event Hubs 리소스에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)

## <a name="shared-access-signatures"></a>공유 액세스 서명 
Event Hubs 리소스에 대한 SAS(공유 액세스 서명)는 Event Hubs 리소스에 대해 위임된 액세스를 제한적으로 제공합니다. 서명이 유효한 시간 간격 또는 부여되는 권한에 제약 조건을 추가하면 리소스를 유연하게 관리할 수 있습니다. 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 인증](authenticate-shared-access-signature.md)을 참조하세요. 

Azure AD에서 반환된 OAuth 2.0 토큰을 사용하는 사용자 또는 애플리케이션에 대한 권한 부여는 SAS(공유 액세스 서명)를 통해 뛰어난 보안과 사용 편의성을 제공합니다. Azure AD를 사용하면 잠재적인 보안 취약성 위험이 있는 코드와 함께 액세스 토큰을 저장할 필요가 없습니다. SAS(공유 액세스 서명)를 계속 사용하여 Event Hubs 리소스에 대한 세분화된 액세스 권한을 부여할 수 있습니다. 하지만 Azure AD에서는 SAS 토큰을 관리하거나 손상된 SAS를 해지할 필요 없이 유사한 기능을 제공합니다. 

기본적으로 모든 Event Hubs 리소스는 보안을 유지하며 계정 소유자만 사용할 수 있습니다. 위에서 설명한 권한 부여 전략 중 하나를 사용하여 클라이언트에 Event Hub 리소스에 대한 액세스 권한을 부여할 수 있습니다. 최대 수준의 보안과 사용 편의성을 위해 가능한 경우 Azure AD를 사용하는 것이 좋습니다.

SAS를 사용한 권한 부여에 대한 자세한 내용은 [공유 액세스 서명을 사용하여 Event Hubs 리소스에 대한 액세스 권한 부여](authorize-access-shared-access-signature.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- GitHub 리포지토리에 게시된 [Azure RBAC 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)을 검토합니다. 
- 다음 문서를 참조하세요.
    - [Azure Active Directory를 사용하여 애플리케이션에서 Azure Event Hubs에 대한 요청 인증](authenticate-application.md)
    - [Azure Active Directory를 사용하여 관리 ID를 인증하여 Event Hubs 리소스에 액세스](authenticate-managed-identity.md)
    - [공유 액세스 서명을 사용하여 Azure Event Hubs에 대한 요청 인증](authenticate-shared-access-signature.md)
    - [Azure Active Directory를 사용하여 Event Hubs 리소스에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)
    - [공유 액세스 서명을 사용하여 Event Hubs 리소스에 대한 액세스 권한 부여](authorize-access-shared-access-signature.md)

