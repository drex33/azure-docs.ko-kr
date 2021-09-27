---
title: Azure Active Directory를 사용하여 액세스 권한 부여
description: 이 문서에서는 Azure Active Directory를 사용하여 Event Hubs 리소스에 대한 액세스 권한을 부여하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: f00f0ff73762cede60a0f2298875fac057ff0563
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627371"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Event Hubs 리소스에 대한 액세스 권한 부여
Azure Event Hubs는 Azure AD(Azure Active Directory)를 사용하여 Event Hubs 리소스에 대한 요청에 권한을 부여할 수 있도록 지원합니다. Azure AD를 사용하면 Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 사용자 또는 애플리케이션 서비스 주체일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 역할 및 역할 할당에 대한 자세한 내용은 [다양한 역할 이해](../role-based-access-control/overview.md)를 참조하세요.

## <a name="overview"></a>개요
보안 주체(사용자 또는 애플리케이션)가 Event Hubs 리소스에 액세스하려고 시도하는 경우 요청에 권한이 부여되어야 합니다. Azure AD의 경우 리소스에 대한 액세스는 2단계 프로세스입니다. 

 1. 먼저, 보안 주체의 ID가 인증되고 OAuth 2.0 토큰이 반환됩니다. 토큰을 요청하는 리소스 이름은 `https://eventhubs.azure.net/`으로, 모든 클라우드/테넌트에서 동일합니다. Kafka 클라이언트에서 토큰을 요청하는 리소스는 `https://<namespace>.servicebus.windows.net`입니다.
 1. 다음으로, 토큰은 지정한 리소스에 대한 액세스 권한을 부여하기 위해 Event Hubs 서비스에 요청의 일부로 전달됩니다.

인증 단계를 수행하려면 런타임 시 애플리케이션 요청에 OAuth 2.0 액세스 토큰을 포함해야 합니다. Azure VM, 가상 머신 확장 집합 또는 Azure 함수 앱과 같은 Azure 엔터티 내에서 애플리케이션이 실행되는 경우 관리 ID를 사용하여 리소스에 액세스할 수 있습니다. Event Hubs 서비스에 대한 관리 ID의 요청을 인증하는 방법에 관한 자세한 내용은 [Azure Active Directory를 사용하는 Azure Event Hubs 리소스에 대한 액세스와 Azure 리소스에 대한 관리 ID 인증](authenticate-managed-identity.md)을 참조하세요. 

권한 부여 단계에서는 보안 주체에 하나 이상의 Azure 역할을 할당해야 합니다. Azure Event Hubs는 Event Hubs 리소스에 대한 권한 집합을 포함하는 Azure 역할을 제공합니다. 보안 주체에 할당된 역할에 따라 보안 주체가 갖는 권한이 결정됩니다. Azure 역할에 대한 자세한 내용은 [Azure Event Hubs에 대한 Azure 기본 제공 역할](#azure-built-in-roles-for-azure-event-hubs)을 참조하세요. 

Event Hubs에 요청하는 네이티브 애플리케이션 및 웹 애플리케이션도 Azure AD를 사용하여 권한을 부여할 수 있습니다. 액세스 토큰을 요청하고 Event Hubs 리소스에 대한 요청에 권한을 부여하는 데 사용하는 방법을 알아보려면 [애플리케이션에서 Azure AD를 사용하여 Azure Event Hubs에 대한 액세스 인증](authenticate-application.md)을 참조하세요. 

## <a name="assign-azure-roles-for-access-rights"></a>액세스 권한을 위한 Azure 역할 할당
Azure AD(Azure Active Directory)는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 보안 리소스에 대한 액세스 권한을 부여합니다. Azure Event Hubs는 이벤트 허브 데이터에 액세스하는 데 사용되는 일반 권한 세트가 포함된 Azure 기본 제공 역할 세트를 정의하며, 데이터 액세스를 위한 사용자 지정 역할도 정의할 수 있습니다.

Azure AD 보안 주체에 Azure 역할을 할당하면 Azure는 해당 리소스에 대한 액세스 권한을 보안 주체에 부여합니다. 액세스 범위는 구독, 리소스 그룹, Event Hubs 네임스페이스 또는 그 아래에 있는 리소스 수준으로 지정할 수 있습니다. Azure AD 보안 주체는 사용자, 애플리케이션 서비스 사용자 또는 [Azure 리소스의 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

## <a name="azure-built-in-roles-for-azure-event-hubs"></a>Azure Event Hubs에 대한 Azure 기본 제공 역할
Azure는 Azure AD 및 OAuth를 사용하여 Event Hubs 데이터에 대한 액세스 권한을 부여할 수 있도록 다음과 같은 Azure 기본 제공 역할을 제공합니다.

| 역할 | 설명 | 
| ---- | ----------- | 
| [Azure Event Hubs 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) | Event Hubs 리소스에 대한 전체 액세스 권한을 부여하려면 이 역할을 사용합니다. |
| [Azure Event Hubs 데이터 보낸 사람](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) | Event Hubs 리소스에 대한 전송 액세스 권한을 부여하려면 이 역할을 사용합니다. |
| [Azure Event Hubs 데이터 받는 사람](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) | Event Hubs 리소스에 대한 사용/수신 액세스 권한을 부여하려면 이 역할을 사용합니다. |

스키마 레지스트리 기본 제공 역할은 [스키마 레지스트리 역할](schema-registry-overview.md#azure-role-based-access-control)을 참조하세요.

## <a name="resource-scope"></a>리소스 범위 
Azure 역할을 보안 주체에 할당하기 전에 보안 주체에게 부여해야 하는 액세스 범위를 결정합니다. 모범 사례에 따르면 항상 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다.

다음 목록에서는 가장 좁은 범위부터 시작하여 Event Hubs 리소스에 대한 액세스 범위를 지정할 수 있는 수준을 설명합니다.

- **소비자 그룹**: 이 범위에서는 역할 할당이 해당 엔터티에만 적용됩니다. 현재 Azure Portal은 이 수준에서 보안 주체에 대한 Azure 역할 할당을 지원하지 않습니다. 
- **이벤트 허브**: 역할 할당이 Event Hubs 엔터티와 그 아래에 있는 소비자 그룹에 적용됩니다.
- **네임스페이스**: 역할 할당이 네임스페이스 아래에 있는 Event Hubs의 전체 토폴로지 및 연결된 소비자 그룹에 적용됩니다.
- **리소스 그룹**: 역할 할당이 리소스 그룹 아래에 있는 모든 Event Hubs 리소스에 적용됩니다.
- **구독**: 역할 할당이 구독의 모든 리소스 그룹에 있는 모든 Event Hubs 리소스에 적용됩니다.

> [!NOTE]
> - Azure 역할 할당을 전파하는 데 최대 5분이 소요될 수 있습니다. 
> - 이 내용은 Event Hubs와 Apache Kafka용 Event Hubs에 모두 적용됩니다. Kafka용 Event Hubs 지원에 대한 자세한 내용은 [Kafka용 Event Hubs - 보안 및 인증](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)을 참조하세요.


기본 제공 역할이 정의되는 방식에 대한 자세한 내용은 [역할 정의 이해](../role-based-access-control/role-definitions.md#management-and-data-operations)를 참조하세요. Azure 사용자 지정 역할을 만드는 방법에 대한 자세한 내용은 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.



## <a name="samples"></a>샘플
- [Microsoft.Azure.EventHubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    이러한 샘플은 이전 **Microsoft.Azure.EventHubs** 라이브러리를 사용하지만 최신 **Azure.Messaging.EventHubs** 라이브러리를 사용하여 쉽게 업데이트할 수 있습니다. 이전 라이브러리를 사용하는 샘플을 새 라이브러리로 이동하려면 [Microsoft.Azure.EventHubs에서 Azure.Messaging.EventHubs로 마이그레이션하기 위한 가이드](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)를 참조하세요.
- [Azure.Messaging.EventHubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    이 샘플은 최신 **Azure.Messaging.EventHubs** 라이브러리를 사용하도록 업데이트되었습니다.
- [Kafka용 Event Hubs - OAuth 샘플](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) 


## <a name="next-steps"></a>다음 단계
- 보안 주체에 Azure 기본 제공 역할을 할당하는 방법을 알아봅니다. [Azure Active Directory를 사용하여 Event Hubs 리소스에 대한 액세스 인증](authenticate-application.md)을 참조하세요.
- [Azure RBAC를 사용하여 사용자 지정 역할을 만드는 방법](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)을 알아봅니다.
- [EH에서 Azure Active Directory를 사용하는 방법](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)을 알아봅니다.

다음 관련 문서를 참조하세요.

- [Azure Active Directory를 사용하여 애플리케이션에서 Azure Event Hubs에 대한 요청 인증](authenticate-application.md)
- [Azure Active Directory를 사용하여 관리 ID를 인증하여 Event Hubs 리소스에 액세스](authenticate-managed-identity.md)
- [공유 액세스 서명을 사용하여 Azure Event Hubs에 대한 요청 인증](authenticate-shared-access-signature.md)
- [공유 액세스 서명을 사용하여 Event Hubs 리소스에 대한 액세스 권한 부여](authorize-access-shared-access-signature.md)
