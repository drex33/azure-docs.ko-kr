---
title: 인증 및 권한 부여 문제 해결 - Azure Event Hubs
description: 이 문서에서는 Azure Event Hubs와 관련된 인증 및 권한 부여 문제를 해결하는 방법에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: 0561ab94f113bf849d5492b93171c9b14540de44
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656838"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>인증 및 권한 부여 문제 해결 - Azure Event Hubs
[연결 문제 해결](troubleshooting-guide.md) 문서에서는 Azure Event Hubs와의 연결 문제 해결에 대한 팁을 제공합니다. 이 문서에서는 Azure Event Hubs 관련 인증 및 권한 부여 문제를 해결하기 위한 팁과 권장 사항을 제공합니다. 

## <a name="if-you-are-using-azure-active-directory"></a>Azure Active Directory를 사용하는 경우
Azure AD(Azure Active Directory)를 사용하여 Azure Event Hubs를 인증하고 권한을 부여하는 경우 이벤트 허브에 액세스하는 ID가 올바른 **리소스 범위**(소비자 그룹, 이벤트 허브, 네임스페이스, 리소스 그룹 또는 구독)에서 올바른 **Azure 역할** 의 구성원인지 확인합니다.

### <a name="azure-roles"></a>Azure 역할
- [Azure Event Hubs 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Event Hubs 리소스에 대한 전체 액세스 권한을 갖습니다.
- [Azure Event Hubs 데이터 보낸 사람](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): 전송 액세스 권한을 갖습니다.
- [Azure Event Hubs 데이터 받는 사람](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): 수신 액세스 권한을 갖습니다.

스키마 레지스트리 기본 제공 역할은 [스키마 레지스트리 역할](schema-registry-overview.md#azure-role-based-access-control)을 참조하세요.

### <a name="resource-scopes"></a>리소스 범위
- **소비자 그룹**: 이 범위에서 역할 할당은 이 엔터티에만 적용됩니다. 현재 Azure Portal은 이 수준에서 보안 주체에 대한 Azure 역할 할당을 지원하지 않습니다. 
- **이벤트 허브**: 역할 할당이 Event Hubs 엔터티와 그 아래에 있는 소비자 그룹에 적용됩니다.
- **네임스페이스**: 역할 할당이 네임스페이스 아래에 있는 Event Hubs의 전체 토폴로지 및 연결된 소비자 그룹에 적용됩니다.
- **리소스 그룹**: 역할 할당이 리소스 그룹 아래에 있는 모든 Event Hubs 리소스에 적용됩니다.
- **구독**: 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 Event Hubs 리소스에 적용됩니다.

자세한 내용은 다음 문서를 참조하세요.

- [Event Hubs 리소스에 액세스하기 위해 Azure Active Directory를 사용하여 애플리케이션 인증](authenticate-application.md)
- [Azure Active Directory를 사용하여 Event Hubs 리소스에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>SAS(공유 액세스 서명)를 사용하는 경우
[SAS](authenticate-shared-access-signature.md)를 사용하는 경우 다음 단계를 수행합니다. 

- 사용 중인 SAS 키가 올바른지 확인하세요. 그렇지 않으면 올바른 SAS 키를 사용합니다.
- 키에 올바른 사용 권한(보내기, 받기 또는 관리)이 있는지 확인합니다. 그렇지 않은 경우 필요한 권한이 있는 키를 사용합니다. 
- 키가 만료되었는지 확인합니다. 만료되기 전에 SAS를 갱신하는 것이 좋습니다. 클라이언트와 Event Hubs 서비스 노드 사이에 클록 스큐(clock skew)가 있는 경우 클라이언트에서 인식하기 전에 인증 토큰이 만료될 수 있습니다. 현재 구현 계정 클록 스큐는 최대 5분입니다. 즉, 클라이언트는 만료되기 5분 전까지 토큰을 갱신합니다. 따라서 클록 스큐가 5분보다 큰 경우 클라이언트는 일시적인 인증 오류를 관찰할 수 있습니다.
- **SAS 시작 시간** 이 **지금** 으로 설정된 경우 클록 스큐(다른 머신에서 현재 시간의 차이)로 인해 처음 몇 분 동안 일시적인 오류가 표시될 수 있습니다. 시작 시간을 최소 15분 전으로 설정하거나 아예 설정하지 마세요. 이는 만료 시간에도 일반적으로 적용됩니다. 

자세한 내용은 다음 문서를 참조하세요. 

- [SAS(공유 액세스 서명)를 사용하여 인증](authenticate-shared-access-signature.md) 
- [공유 액세스 서명을 사용하여 Event Hubs 리소스에 대한 액세스 권한 부여](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요.

* [연결 문제 해결](troubleshooting-guide.md)
