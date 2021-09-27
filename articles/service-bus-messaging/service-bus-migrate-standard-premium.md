---
title: Azure Service Bus 네임스페이스를 표준에서 프리미엄으로 마이그레이션
description: 기존 Azure Service Bus 표준 네임스페이스를 프리미엄으로 마이그레이션하도록 허용하기 위한 가이드
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: eea34edddf641e3ee1c07bea92b20364e7aeaf34
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602137"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>기존 Azure Service Bus 표준 네임스페이스를 프리미엄 계층으로 마이그레이션

이전에는 Azure Service Bus가 표준 계층에서만 네임스페이스를 제공했습니다. 네임스페이스는 낮은 처리량 및 개발자 환경에 최적화된 다중 테넌트 설정입니다. 프리미엄 계층은 고정 가격으로 예측 가능한 대기 시간에 대한 네임스페이스당 전용 리소스 및 증가된 처리량을 제공합니다. 프리미엄 계층은 높은 처리량 및 추가적인 엔터프라이즈 기능이 필요한 프로덕션 환경에 최적화되어 있습니다.

이 문서에서는 기존 표준 계층 네임스페이스를 프리미엄 계층으로 마이그레이션하는 방법을 설명합니다.  

>[!WARNING]
> 마이그레이션은 프리미엄 계층으로 업그레이드될 Service Bus 표준 네임스페이스를 위한 것입니다. 마이그레이션 도구는 다운그레이드를 지원하지 않습니다.

다음 몇 가지 사항을 주의해야 합니다.

- 해당 마이그레이션은 같은 위치에서 진행되며, 이는 기존 보낸 사람 및 받는 사람 애플리케이션에서 **코드 또는 구성을 변경할 필요가 없음** 을 의미합니다. 기존 연결 문자열이 자동으로 새 프리미엄 네임스페이스를 가리킵니다.
- 마이그레이션을 성공적으로 수행하려면 **프리미엄** 네임스페이스에 **엔터티가 없어야** 합니다.
- 표준 네임스페이스의 모든 **엔터티** 는 마이그레이션 프로세스 중에 프리미엄 네임스페이스로 **복사** 됩니다.
- 마이그레이션은 프리미엄 계층에서 **메시징 단위당 1,000개의 엔터티** 를 지원합니다. 필요한 메시징 단위 수를 식별하려면 현재 표준 네임스페이스에 있는 엔터티 수로 시작합니다.
- **기본 계층** 에서 **프리미엄 계층** 으로 직접 마이그레이션할 수는 없지만 먼저 기본 단계에서 표준으로, 그런 다음 표준에서 프리미엄으로 마이그레이션하여 간접적으로 해당 작업을 수행할 수 있습니다.

## <a name="migration-steps"></a>마이그레이션 단계

일부 조건은 마이그레이션 프로세스와 관련이 있습니다. 오류 발생 가능성을 줄이기 위해 다음 단계를 숙지하세요. 이러한 단계에서는 마이그레이션 프로세스를 간략하게 설명하고 다음 섹션에는 단계별 세부 정보가 나열되어 있습니다.

1. 새 프리미엄 네임스페이스를 만듭니다.
1. 표준 및 프리미엄 네임스페이스를 서로 쌍으로 연결합니다.
1. 표준에서 프리미엄 네임스페이스로 엔터티를 동기화(복사)합니다.
1. 마이그레이션을 커밋합니다.
1. 네임스페이스의 마이그레이션 후 이름을 사용하여 표준 네임스페이스의 엔터티를 드레이닝합니다.
1. 표준 네임스페이스를 삭제합니다.

>[!IMPORTANT]
> 마이그레이션이 커밋된 다음 이전 표준 네임스페이스에 액세스하여 큐 및 구독을 드레이닝합니다. 메시지가 드레이닝되면 받는 사람 애플리케이션에서 처리하기 위해 새 프리미엄 네임스페이스로 전송될 수 있습니다. 큐 및 구독이 드레이닝되면 이전 표준 네임스페이스를 삭제하는 것이 좋습니다.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Azure CLI 또는 PowerShell을 사용한 마이그레이션

Azure CLI 또는 PowerShell 도구를 사용하여 Service Bus 표준 네임스페이스를 프리미엄으로 마이그레이션하려면 다음 단계를 수행합니다.

1. 새 Service Bus 프리미엄 네임스페이스를 만듭니다. [Azure Resource Manager 템플릿](service-bus-resource-manager-namespace.md)을 참조하거나 [Azure Portal을 사용](service-bus-create-namespace-portal.md)할 수 있습니다. **serviceBusSku** 매개 변수에 대해 **프리미엄** 을 선택해야 합니다.

1. 마이그레이션 명령을 간소화하기 위해 다음 환경 변수를 설정합니다.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > 마이그레이션 후 별칭/이름(post_migration_dns_name)은 마이그레이션 후 이전 표준 네임스페이스에 액세스하는 데 사용됩니다. 이를 사용하여 큐 및 구독을 드레이닝한 다음 네임스페이스를 삭제합니다.

1. 표준 및 프리미엄 네임스페이스를 쌍으로 연결하고 다음 명령을 사용하여 동기화를 시작합니다.

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. 다음 명령을 사용하여 마이그레이션 상태를 확인합니다.

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    다음 값이 표시되면 마이그레이션이 완료된 것으로 간주합니다.

    * MigrationState = “Active”
    * pendingReplicationsOperationsCount = 0
    * provisioningState = “Succeeded”

    해당 명령은 마이그레이션 구성도 표시합니다. 값이 올바르게 설정되었는지 확인합니다. 또한 포털에서 프리미엄 네임스페이스를 확인하여 모든 큐 및 토픽이 만들어졌으며 표준 네임스페이스에 있던 항목과 일치하는지 확인합니다.

1. 다음 전체 명령을 실행하여 마이그레이션을 커밋합니다.

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Azure Portal을 사용한 마이그레이션

Azure Portal을 사용한 마이그레이션은 명령을 사용한 마이그레이션과 동일한 논리 흐름을 사용합니다. Azure Portal을 사용하여 마이그레이션하려면 다음 단계를 수행합니다.

1. 왼쪽 창의 **탐색** 메뉴에서 **프리미엄으로 마이그레이션** 을 선택합니다. **시작** 단추를 클릭하여 다음 페이지로 이동합니다.
    :::image type="content" source="./media/service-bus-standard-premium-migration/migrate-premium-page.png" alt-text="프리미엄으로 마이그레이션 페이지를 보여주는 이미지.":::
1. 다음 설치 **네임스페이스 페이지가 표시됩니다.**

    :::image type="content" source="./media/service-bus-standard-premium-migration/setup-namespaces-page.png" alt-text="네임스페이스 설정 페이지를 보여 줍니다.":::
1. 설치 **네임스페이스** 페이지에서 다음 단계 중 하나를 수행합니다. 
    1. **새 프리미엄 네임스페이스 만들기를** 선택하는 경우:
        1. **네임스페이스 만들기** 페이지에서 네임스페이스의 이름을 입력하고 **검토 + 만들기를** 선택합니다.
        1. **리뷰 + 생성** 페이지에서 **생성** 를 선택합니다.

            :::image type="content" source="./media/service-bus-standard-premium-migration/create-premium-namespace.png" alt-text="네임스페이스 만들기 페이지를 보여주는 이미지":::
    1. **기존 빈 프리미엄 네임스페이스 선택을 선택하는** 경우:
        1. 네임스페이스가 있는 Azure 구독 및 리소스 그룹을 선택합니다.
        1. 그런 다음 프리미엄 네임스페이스를 선택합니다.
        1. 그런 다음 **선택** 을 클릭합니다.
        
            :::image type="content" source="./media/service-bus-standard-premium-migration/select-existing-namespace.png" alt-text="기존 프리미엄 네임스페이스의 선택을 보여주는 이미지.":::
1. 마이그레이션 **후 이름** 를 입력한 후 **다음을** 선택합니다. 마이그레이션을 완료한 후 표준 네임스페이스에 액세스하는 경우 해당 이름을 사용합니다.

    :::image type="content" source="./media/service-bus-standard-premium-migration/enter-post-migration-name.png" alt-text="표준 네임스페이스의 마이그레이션 후 이름을 보여주는 이미지입니다.":::
1. **동기화 시작을** 선택하여 표준 네임스페이스와 프리미엄 네임스페이스 간에 엔터티를 동기화합니다.

    :::image type="content" source="./media/service-bus-standard-premium-migration/start-sync-button.png" alt-text="동기화 시작 단추를 보여주는 이미지입니다.":::
1. 대화 상자에서 **예를** 선택하여 동기화를 확인하고 시작합니다. 동기화가 완료될 때까지 기다립니다. 그다음에 **다음** 을 선택합니다.

    >[!IMPORTANT]
    > 어떤 이유로든 마이그레이션을 중단해야 하는 경우 이 문서의 FAQ 섹션에서 흐름 중단을 검토하세요.    
1. **전환** 페이지에서 **마이그레이션 완료를** 선택합니다. 

    :::image type="content" source="./media/service-bus-standard-premium-migration/complete-migration.png" alt-text="마이그레이션 마법사의 **전환** 페이지를 보여주는 이미지.":::
1. **예를** 선택하여 표준 네임스페이스를 프리미엄으로 전환합니다. 스위치가 완료되면 표준 네임스페이스의 DNS 이름이 프리미엄 네임스페이스를 가리킵니다. 이 작업은 실행 취소할 수 없습니다. 마이그레이션이 완료되면 **성공** 페이지가 표시됩니다.

    :::image type="content" source="./media/service-bus-standard-premium-migration/success-page.png" alt-text="성공 페이지를 보여주는 이미지입니다.":::

## <a name="caveats"></a>제한 사항

Azure Service Bus 표준 계층에서 제공하는 일부 기능은 Azure Service Bus 프리미엄 계층에서 지원되지 않습니다. 프리미엄 계층은 예측 가능한 처리량 및 대기 시간에 대한 전용 리소스를 제공하므로 이러한 방식으로 설계되었습니다.

다음은 프리미엄 및 마이그레이션 작업에서 지원되지 않는 기능 목록입니다.

### <a name="express-entities"></a>Express 엔터티

   스토리지로 메시지 데이터를 커밋하지 않는 Express 엔터티는 프리미엄에서 지원되지 않습니다. 모든 엔터프라이즈 메시징 시스템에서 예상한 대로 전용 리소스는 데이터 유지를 보장하면서 처리량을 상당히 개선했습니다.

   마이그레이션하는 동안 표준 네임스페이스의 모든 Express 엔터티는 비 Express 엔터티로 프리미엄 네임스페이스에 만들어집니다.

   ARM(Azure Resource Manager) 템플릿을 활용하는 경우 자동화된 워크플로가 오류 없이 실행되도록 배포 구성에서 ‘enableExpress’ 플래그를 제거해야 합니다.

### <a name="partitioned-entities"></a>분할된 엔터티

   분할된 엔터티는 다중 테넌트 설정에서 더 나은 가용성을 제공하기 위해 표준 계층에서 지원되었습니다. 프리미엄 계층에서 네임스페이스당 전용 리소스를 프로비저닝하는 경우 이것이 더 이상 필요하지 않습니다.

   마이그레이션하는 동안 표준 네임스페이스에 있는 모든 분할된 엔터티는 분할되지 않은 엔터티로 프리미엄 네임스페이스에 만들어집니다.

   ARM 템플릿에서 특정 큐 또는 토픽에 대해 ‘enablePartitioning’을 ‘true’로 설정하는 경우 브로커는 이를 무시합니다.

## <a name="faqs"></a>FAQ

### <a name="what-happens-when-the-migration-is-committed"></a>마이그레이션이 커밋되면 어떻게 되나요?

마이그레이션이 커밋되면 표준 네임스페이스를 가리키는 연결 문자열이 프리미엄 네임스페이스를 가리킵니다.

보낸 사람 및 받는 사람 애플리케이션은 표준 네임스페이스와 연결이 끊어지고 프리미엄 네임스페이스에 자동으로 다시 연결됩니다.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>표준에서 프리미엄으로의 마이그레이션이 완료된 후에는 무엇을 하나요?

표준에서 프리미엄으로 마이그레이션하면 토픽, 구독, 필터와 같은 엔터티 메타데이터가 표준 네임스페이스에서 프리미엄 네임스페이스로 복사됩니다. 표준 네임스페이스에 커밋된 메시지 데이터는 표준 네임스페이스에서 프리미엄 네임스페이스로 복사되지 않습니다.

마이그레이션이 진행되는 동안에는 표준 네임스페이스에 전송되고 커밋된 일부 메시지가 있을 수 있습니다. 표준 네임스페이스에서 해당 메시지를 수동으로 드레이닝하고 프리미엄 네임스페이스에 수동으로 보냅니다. 메시지를 수동으로 드레이닝하려면 마이그레이션 명령에서 지정된 마이그레이션 후 DNS 이름을 사용하여 표준 네임스페이스 엔터티를 드레이닝하는 콘솔 앱 또는 스크립트를 사용합니다. 받는 사람이 처리할 수 있도록 해당 메시지를 프리미엄 네임스페이스로 보냅니다.

메시지가 드레이닝되면 표준 네임스페이스를 삭제합니다.

>[!IMPORTANT]
> 표준 네임스페이스의 메시지가 드레이닝되면 표준 네임스페이스를 삭제합니다. 처음에 표준 네임스페이스를 나타냈던 연결 문자열은 이제 프리미엄 네임스페이스를 나타내므로 이러한 작업이 중요합니다. 표준 네임스페이스는 더 이상 필요하지 않습니다. 마이그레이션한 표준 네임스페이스를 삭제하면 나중에 혼동을 줄일 수 있습니다.

### <a name="how-much-downtime-do-i-expect"></a>가동 중지 시간이 얼마나 될까요?

마이그레이션 프로세스는 애플리케이션의 예상 가동 중지 시간을 줄이기 위한 것입니다. 보낸 사람 및 받는 사람 애플리케이션은 새 프리미엄 네임스페이스를 가리키는 데 사용하는 연결 문자열을 사용하여 가동 중지 시간을 줄일 수 있습니다.

애플리케이션에서 발생하는 가동 중지 시간은 프리미엄 네임스페이스를 가리키도록 DNS 항목을 업데이트하는 데 걸리는 시간으로 제한됩니다. 가동 중지 시간은 약 5분입니다.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>마이그레이션을 수행하는 동안 구성을 변경해야 하나요?

아니요, 마이그레이션을 수행하는 데 코드 또는 구성을 변경할 필요가 없습니다. 보낸 사람 및 받는 사람 애플리케이션이 표준 네임스페이스에 액세스하는 데 사용하는 연결 문자열은 프리미엄 네임스페이스에 대한 별칭 역할을 하도록 자동으로 매핑됩니다.

### <a name="what-happens-when-i-abort-the-migration"></a>마이그레이션을 중단하면 어떻게 되나요?

`Abort` 명령을 사용하거나 Azure Portal을 사용하여 마이그레이션을 중단할 수 있습니다.

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure Portal

**엔터티 동기화** 페이지에서 **중단** 을 선택 합니다. 

:::image type="content" source="./media/service-bus-standard-premium-migration/abort1.png" alt-text="중단 페이지를 표시 하는 이미지입니다.":::

완료 되 면 다음 페이지가 표시 됩니다. 

:::image type="content" source="./media/service-bus-standard-premium-migration/abort3.png" alt-text="중단 완료 페이지를 표시 하는 이미지입니다.":::

마이그레이션 프로세스가 중단되면 표준에서 프리미엄 네임스페이스로 엔터티(토픽, 구독, 필터)를 복사하는 프로세스가 중단되고 연결이 끊깁니다.

연결 문자열이 프리미엄 네임스페이스를 가리키도록 업데이트되지 않습니다. 기존 애플리케이션은 마이그레이션을 시작하기 전과 마찬가지로 계속 작동합니다.

그러나 프리미엄 네임스페이스의 엔터티를 삭제하거나 프리미엄 네임스페이스를 삭제하지는 않습니다. 마이그레이션을 사용하여 진행하지 않기로 한 경우 엔터티를 수동으로 삭제합니다.

>[!IMPORTANT]
> 마이그레이션을 중단하기로 한 경우 마이그레이션에 대해 프로비저닝한 프리미엄 네임스페이스를 삭제하여 리소스에 대한 요금이 부과되지 않도록 합니다.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>메시지를 드레이닝하길 원하지 않습니다. 어떻게 해야 합니까?

마이그레이션이 진행되는 동안 그리고 마이그레이션이 커밋되기 직전까지 보낸 사람 애플리케이션에서 전송되고 표준 네임스페이스의 스토리지에 커밋된 메시지가 있을 수 있습니다.

마이그레이션하는 동안 실제 메시지 데이터/페이로드가 표준 네임스페이스에서 프리미엄 네임스페이스로 복사되지 않습니다. 메시지는 수동으로 드레이닝된 다음 프리미엄 네임스페이스로 전송되어야 합니다.

그러나 계획된 유지 관리/하우스키핑 기간 동안 마이그레이션할 수 있고 메시지를 수동으로 드레이닝하고 메시지를 전송하지 않으려는 경우 다음 단계를 수행합니다.

1. 보낸 사람 애플리케이션을 중지합니다. 받는 사람 애플리케이션은 현재 표준 네임스페이스에 있는 메시지를 처리하고 큐를 드레이닝합니다.
1. 표준 네임스페이스의 큐 및 구독이 비게 되면 앞에서 설명한 절차에 따라 표준에서 프리미엄 네임스페이스로 마이그레이션을 실행합니다.
1. 마이그레이션이 완료되면 보낸 사람 애플리케이션을 다시 시작할 수 있습니다.
1. 이제 보낸 사람 및 받는 사람이 프리미엄 네임스페이스와 자동으로 연결됩니다.

    >[!NOTE]
    > 마이그레이션을 위해 받는 사람 애플리케이션을 중지할 필요는 없습니다.
    >
    > 마이그레이션이 완료되면 받는 사람 애플리케이션은 표준 네임스페이스와의 연결을 끊고 프리미엄 네임스페이스에 자동으로 연결됩니다.

## <a name="next-steps"></a>다음 단계

* [표준 및 프리미엄 메시징의 차이점](./service-bus-premium-messaging.md)에 대해 자세히 알아보세요.
* [Service Bus 프리미엄에 대한 고가용성 및 지리적 재해 복구 측면](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)에 대해 알아보세요.

