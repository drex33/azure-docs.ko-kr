---
title: Azure 통합 리소스, Azure 독일을 글로벌 Azure로 마이그레이션
description: 이 문서에서는 Azure 독일에서 글로벌 Azure로 Azure 통합 리소스를 마이그레이션하는 방법에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurepowershell
ms.openlocfilehash: 0a4ea6393741e15d8dce94869213c62c08537c7e
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "122528070"
---
# <a name="migrate-integration-resources-to-global-azure"></a>글로벌 Azure로 통합 리소스 마이그레이션

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

이 문서에는 Azure 독일에서 글로벌 Azure로 Azure 통합 리소스를 마이그레이션하는 데 도움이 되는 정보가 있습니다.

## <a name="service-bus"></a>Service Bus

Azure Service Bus 서비스에는 데이터 내보내기 또는 가져오기 기능이 없습니다. Azure 독일에서 글로벌 Azure로 Service Bus 리소스를 마이그레이션하려면 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/export-template-portal.md)으로 리소스를 내보낼 수 있습니다. 그런 다음, 글로벌 Azure 지역에 대해 내보낸 템플릿을 조정하고 리소스를 다시 만듭니다.

> [!NOTE]
> Resource Manager 템플릿 내보내기는 데이터(예: 메시지)를 복사하지 않습니다. 템플릿을 내보내면 메타데이터만 다시 생성됩니다.

> [!IMPORTANT]
> 새 지역과 일치하도록 위치, Azure Key Vault 비밀, 인증서 및 기타 GUID를 변경합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="service-bus-metadata"></a>Service Bus 메타데이터

Resource Manager 템플릿을 내보낼 때 다음 Service Bus 메타데이터 요소가 다시 생성됩니다.

- 네임스페이스
- 큐
- 토픽
- Subscriptions
- 규칙
- 권한 부여 규칙

### <a name="keys"></a>구성

내보내기 및 다시 만들기에 대한 이전 단계에서는 권한 부여 규칙과 연결된 공유 액세스 서명 키를 복사하지 않습니다. 공유 액세스 서명 키를 유지해야 하는 경우 선택적 매개 변수 `-Keyvalue`와 함께 `New-AzServiceBuskey` cmdlet을 사용하여 키를 문자열로 수락합니다. 업데이트된 cmdlet은 [Azure PowerShell Az 모듈](/powershell/azure/install-az-ps)에서 사용할 수 있습니다.

### <a name="usage-example"></a>사용 예제

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Queue <queuename> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Topic <topicname> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

> [!NOTE]
> 키를 유지하는 경우에도 새 연결 문자열을 사용하도록 애플리케이션을 업데이트해야 합니다. DNS 호스트 이름은 Azure 독일 및 글로벌 Azure에서 다릅니다.

### <a name="sample-connection-strings"></a>샘플 연결 문자열

**Azure 독일**

```cmd
Endpoint=sb://myBFProdnamespaceName.**servicebus.cloudapi.de**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

**글로벌 Azure**

```cmd
Endpoint=sb://myProdnamespaceName.**servicebus.windows.net**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

추가 정보는 다음 항목을 참조하세요.

- [Service Bus 자습서](../service-bus-messaging/index.yml)를 완료하여 지식을 새로 고칩니다.
- [Resource Manager 템플릿 내보내기](../azure-resource-manager/templates/export-template-portal.md) 방법을 숙지하거나 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 개요를 읽어봅니다.
- [Service Bus 개요](../service-bus-messaging/service-bus-messaging-overview.md)를 검토하세요.

## <a name="logic-apps"></a>Logic Apps

Azure Logic Apps는 Azure 독일에서 사용할 수 없지만 대신 글로벌 Azure의 Logic Apps를 사용하여 예약 작업을 만들 수 있습니다. 이전에는 Azure 독일에서 사용할 수 있었지만 Azure Scheduler는 사용이 중지됩니다.

추가 정보는 다음 항목을 참조하세요.

- [Azure Logic Apps 자습서](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)를 완료하여 자세히 알아봅니다.
- [Azure Logic Apps 개요](../logic-apps/logic-apps-overview.md)를 검토합니다.

## <a name="next-steps"></a>다음 단계

다음 서비스 범주에서 리소스 마이그레이션을 위한 도구, 기술, 권장 사항에 대해 알아봅니다.

- [컴퓨팅](./germany-migration-compute.md)
- [네트워킹](./germany-migration-networking.md)
- [스토리지](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [데이터베이스](./germany-migration-databases.md)
- [분석](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [ID](./germany-migration-identity.md)
- [보안](./germany-migration-security.md)
- [관리 도구](./germany-migration-management-tools.md)
- [미디어](./germany-migration-media.md)
