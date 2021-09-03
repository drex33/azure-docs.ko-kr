---
title: Azure Active Directory를 사용하여 관리 ID 인증
description: 이 문서에서는 Azure Event Hubs 리소스에 액세스하기 위해 Azure Active Directory를 사용하여 관리 ID를 인증하는 방법을 설명합니다
ms.topic: conceptual
ms.date: 06/14/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 85648a5448420f3f25061142bf1f23e06de492b2
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119965"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Azure Active Directory를 사용해 관리 ID를 인증하여 Event Hubs 리소스에 액세스
Azure Event Hubs는 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하는 Azure Active Directory (Azure AD) 인증을 지원합니다. Azure 리소스에 대한 관리 ID는 Azure Virtual Machines(VM), 함수 앱, Virtual Machine Scale Sets 및 기타 서비스에서 실행되는 응용 프로그램의 Azure AD 자격 증명을 사용하여 Event Hubs 리소스에 대한 액세스 권한을 부여할 수 있습니다. Azure AD 인증과 함께 Azure 리소스에 대한 관리 ID를 사용하면 클라우드에서 실행되는 응용 프로그램에 자격 증명을 저장할 필요가 없습니다.

이 문서에서는 Azure VM에서 관리 ID를 사용하여 이벤트 허브에 대한 액세스 권한을 부여하는 방법을 설명합니다.

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용
Azure 리소스에 관리 ID를 사용하여 VM에서 Event Hubs 리소스에 권한을 부여하려면 먼저 VM에서 Azure 리소스에 대한 관리 ID를 사용하도록 설정해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD에서 관리 ID에 권한 부여
응용 프로그램의 관리 ID에서 Event Hubs 서비스의 요청에 권한을 부여하려면 먼저 해당 관리 ID에 대한 Azure RBAC(역할 기반 액세스 제어) 설정을 구성합니다. Azure Event Hubs는 Event Hubs에서 전송 및 읽기 권한을 포함하는 Azure 역할을 정의합니다. Azure 역할이 관리 ID에 할당되면 관리 ID에 적절한 범위에서 Event Hubs 데이터에 대한 액세스 권한이 부여됩니다.

Azure 역할을 할당하는 방법에 대한 자세한 내용은 [Event Hubs 리소스에 대한 액세스를 위한 Azure Active Directory 인증](authorize-access-azure-active-directory.md)을 참조하세요.

## <a name="use-event-hubs-with-managed-identities"></a>Event Hubs에서 관리 ID 사용
관리 ID에 Event Hubs를 사용하려면 ID에 역할 및 적절한 범위를 할당해야 합니다. 이 섹션의 절차에서는 관리 ID로 실행되고 Event Hubs 리소스에 액세스하는 간단한 응용 프로그램을 사용합니다.

여기서는 [Azure App Service](https://azure.microsoft.com/services/app-service/)에서 호스팅되는 샘플 웹 응용 프로그램을 사용합니다. 웹 애플리케이션을 만드는 방법에 대한 단계별 지침은 [Azure에서 ASP.NET Core 웹앱 만들기](../app-service/quickstart-dotnetcore.md)를 참조하세요.

애플리케이션이 생성되면 다음 단계를 수행합니다. 

1. **설정** 으로 이동하고 **ID** 를 선택합니다. 
1. **상태** 를 **켜짐** 으로 선택합니다. 
1. **저장** 을 선택하여 설정을 저장합니다. 

    :::image type="content" source="./media/authenticate-managed-identity/identity-web-app.png" alt-text="웹앱의 관리 ID":::
4. 확인 메시지에서 **예** 를 선택합니다. 

    이 설정을 사용하도록 설정하면 새 서비스 ID가 Azure AD(Azure Active Directory)에서 생성되고 App Service 호스트로 구성됩니다.

    이제 Event Hubs 리소스에서 필요한 범위의 역할에 이 서비스 ID를 할당합니다.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 역할을 할당하려면
원하는 범위(Event Hubs 네임스페이스, 리소스 그룹, 구독)에서 관리 ID에 [Event Hubs 역할](authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) 중 하나를 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

> [!NOTE]
> 관리 ID를 지원하는 서비스 목록은 [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)를 참조하세요.

### <a name="test-the-web-application"></a>웹 애플리케이션 테스트
1. Event Hubs 네임스페이스 및 이벤트 허브 만들기 
2. Azure에 웹앱 배포 GitHub에서 웹 응용 프로그램에 대한 링크는 다음 탭 섹션을 참조하세요. 
3. SendReceive.aspx가 웹앱에 대한 기본 문서로 설정되었는지 확인합니다. 
3. 웹앱의 **ID를** 사용하도록 설정합니다. 
4. 이 ID를 네임스페이스 수준이나 이벤트 허브 수준에서 **Event Hubs 데이터 소유자** 역할에 할당합니다. 
5. 웹 응용 프로그램을 실행하고, 네임스페이스 이름 및 이벤트 허브 이름과 메시지를 입력하고, **전송** 을 선택합니다. 이벤트를 받으려면 **수신** 을 선택합니다. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.Messaging.EventHubs(latest)](#tab/latest)
이제 웹 응용 프로그램을 시작하고 브라우저가 샘플 aspx 페이지를 가리키도록 할 수 있습니다. [GitHub 리포지토리의](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp) Event Hubs 리소스에서 데이터를 보내고 받는 샘플 웹 응용 프로그램을 찾을 수 있습니다.

[NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)에서 최신 패키지를 설치하고, **EventHubProducerClient** 를 사용해 Event Hubs로 이벤트를 보내기 시작하며 **EventHubConsumerClient** 로 이벤트를 수신합니다. 

> [!NOTE]
> 관리 ID를 사용하여 이벤트 허브에 이벤트를 게시하는 Java 샘플은 [GitHub에서 Azure ID 샘플을 사용하여 이벤트 게시](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)를 참조하세요.

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs(레거시)](#tab/old)
이제 웹 응용 프로그램을 시작하고 브라우저가 샘플 aspx 페이지를 가리키도록 할 수 있습니다. [GitHub 리포지토리의](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp) Event Hubs 리소스에서 데이터를 보내고 받는 샘플 웹 응용 프로그램을 찾을 수 있습니다.

최신 패키지를 [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)에서 설치하고 다음 코드와 같이 EventHubClient를 사용하여 Event Hubs로 데이터를 보내고 받기 시작합니다. 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Kafka용 Event Hubs
Apache Kafka 응용 프로그램을 사용하여 관리 ID OAuth를 사용하여 Azure Event Hubs에서 메시지를 보내고 받을 수 있습니다. GitHub에서 [Kafka용 Event Hubs - 관리 ID OAuth를 사용하여 메시지 보내기 및 받기](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)샘플을 참조하세요.

## <a name="samples"></a>샘플
- **Azure.Messaging.EventHubs** 샘플
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Microsoft.Azure.EventHubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    이러한 샘플은 이전 **Microsoft.Azure.EventHubs** 라이브러리를 사용하지만 최신 **Azure.Messaging.EventHubs** 라이브러리를 사용하여 쉽게 업데이트할 수 있습니다. 이전 라이브러리를 사용하는 샘플을 새 라이브러리로 이동하려면 [Microsoft.Azure.EventHubs에서 Azure.Messaging.EventHubs로 마이그레이션하기 위한 가이드](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)를 참조하세요.
    이 샘플은 최신 **Azure.Messaging.EventHubs** 라이브러리를 사용하도록 업데이트되었습니다.
- [Kafka용 Event Hubs - 관리 ID OAuth를 사용하여 메시지 보내기 및 받기](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>다음 단계
- Azure 리소스용 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md) 을 참조하세요
- 다음 관련 문서를 참조하세요.
    - [Azure Active Directory를 사용하여 응용 프로그램에서 Azure Event Hubs에 대한 요청 인증](authenticate-application.md)
    - [공유 액세스 서명을 사용하여 Azure Event Hubs에 대한 요청 인증](authenticate-shared-access-signature.md)
    - [Azure Active Directory를 사용하여 Event Hubs 리소스에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)
    - [공유 액세스 서명을 사용하여 Event Hubs 리소스에 대한 액세스 권한 부여](authorize-access-shared-access-signature.md)
