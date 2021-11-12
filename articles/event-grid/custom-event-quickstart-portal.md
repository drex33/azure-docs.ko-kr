---
title: 웹 엔드포인트에 사용자 지정 이벤트 보내기 - Event Grid, Azure Portal
description: '빠른 시작: Azure Event Grid 및 Azure Portal을 사용하여 사용자 지정 토픽을 게시하고 해당 토픽에 대한 이벤트를 구독합니다. 이벤트는 웹 애플리케이션에서 처리합니다.'
ms.date: 07/01/2021
ms.topic: quickstart
ms.openlocfilehash: 461d43e2a0210dfd5844beb844c7ae2a48e936f3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310075"
---
# <a name="route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>Azure Portal 및 Event Grid를 사용하여 사용자 지정 이벤트를 웹 엔드포인트로 라우팅
Event Grid는 다양한 Azure 서비스 및 애플리케이션에서 이벤트를 쉽게 관리할 수 있는 완전 관리형 서비스입니다. 이벤트 기반 및 서버리스 애플리케이션 빌드를 간소화합니다. 서비스에 대한 개요는 [Event Grid 개요](overview.md)를 참조하세요.

이 문서에서는 Azure Portal을 사용하여 다음 작업을 수행합니다. 

1. 사용자 지정 토픽 만들기
1. 사용자 지정 토픽 구독
1. 이벤트 트리거
1. 결과 보기 일반적으로 이벤트 데이터를 처리하고 작업을 수행하는 엔드포인트에 이벤트를 보냅니다. 그러나 이 문서를 간소화하기 위해 메시지를 수집하고 표시하는 웹앱에 이벤트를 보냅니다.


## <a name="prerequisites"></a>필수 구성 요소
[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>사용자 지정 토픽 만들기
Event Grid 항목은 이벤트를 게시하는 사용자 정의 엔드포인트를 제공합니다. 

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 토픽의 검색 창에 **Event Grid 토픽** 을 입력한 다음, 드롭다운 목록에서 **Event Grid 토픽** 을 선택합니다. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Event Grid 토픽 검색 및 선택":::
3. **Event Grid 항목** 페이지의 도구 모음에 있는 **+ 만들기** 를 선택합니다. 
4. **토픽 만들기** 페이지에서 다음 단계를 수행합니다.
    1. Azure **구독** 을 선택합니다.
    2. 기존 리소스 그룹을 선택하거나 새 **새로 만들기** 를 선택하고 **리소스 그룹** 의 **이름** 을 입력합니다.
    3. 사용자 지정 항목의 고유한 **이름** 을 입력합니다. DNS 항목으로 표시되기 때문에 토픽 이름은 고유해야 합니다. 이미지에 표시된 이름은 사용하지 마십시오. 대신 고유한 이름을 만듭니다. 3~50자 사이로 a-z, A-Z, 0-9 및 “-” 값만 포함해야 합니다.
    4. Event Grid 항목의 **위치** 를 선택합니다.
    5. 페이지 아래쪽에서 **검토 + 만들기** 를 선택합니다. 

        :::image type="content" source="./media/custom-event-quickstart-portal/create-custom-topic.png" alt-text="토픽 만들기 페이지":::
    6. **토픽 만들기** 페이지의 **검토 + 만들기** 탭에서 **만들기** 를 선택합니다. 
    
        :::image type="content" source="./media/custom-event-quickstart-portal/review-create-page.png" alt-text="설정 검토 및 만들기":::
5. 배포에 성공하면 **리소스로 이동** 을 선택하여 토픽에 대한 **Event Grid 토픽** 페이지로 이동합니다. 이 페이지를 열린 상태로 유지합니다. 나중에 빠른 시작에서 사용할 예정입니다. 

    :::image type="content" source="./media/custom-event-quickstart-portal/event-grid-topic-home-page.png" alt-text="Event Grid 토픽 홈페이지를 보여 주는 스크린샷":::

## <a name="create-a-message-endpoint"></a>메시지 엔드포인트 만들기
사용자 지정 항목에 대한 구독을 만들기 전에 이벤트 메시지에 대한 엔드포인트를 만듭니다. 일반적으로 엔드포인트는 이벤트 데이터를 기반으로 작업을 수행합니다. 이 빠른 시작을 간소화하기 위해 이벤트 메시지를 표시하는 [미리 작성된 웹앱](https://github.com/Azure-Samples/azure-event-grid-viewer)을 배포합니다. 배포된 솔루션은 App Service 계획, App Service 웹앱 및 GitHub의 소스 코드를 포함합니다.

1. 문서 페이지에서 **Azure에 배포** 를 선택하여 구독에 솔루션을 배포합니다. Azure Portal에서 매개 변수에 대한 값을 제공합니다.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to deploy to Azure."></a>
2. **사용자 지정 배포** 페이지에서 다음 단계를 수행합니다. 
    1. **리소스 그룹** 에서 스토리지 계정을 만들 때 생성된 것과 동일한 리소스 그룹을 선택합니다. 자습서를 완료한 후 리소스 그룹을 삭제하면 더 쉽게 정리할 수 있습니다.  
    2. **사이트 이름** 에 웹앱의 이름을 입력합니다.
    3. **호스팅 계획 이름** 에 웹앱을 호스팅하는 데 사용할 App Service 계획의 이름을 입력합니다.
    5. **검토 + 만들기** 를 선택합니다. 

        :::image type="content" source="./media/blob-event-quickstart-portal/template-deploy-parameters.png" alt-text="사용자 지정 배포 페이지를 보여주는 스크린샷.":::
1. **리뷰 + 생성** 페이지에서 **생성** 를 선택합니다. 
1. 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 포털에서 경고(벨 아이콘)를 선택하고 **리소스 그룹으로 이동** 을 선택합니다. 

    ![경고 - 리소스 그룹으로 이동합니다.](./media/blob-event-quickstart-portal/navigate-resource-group.png)
4. **리소스 그룹** 페이지의 리소스 목록에서 자신이 만든 웹앱을 선택합니다. 이 목록에는 App Service 계획과 스토리지 계정도 표시됩니다. 

    ![웹 사이트를 선택합니다.](./media/blob-event-quickstart-portal/resource-group-resources.png)
5. 웹앱의 **App Service** 페이지에서 URL을 선택하여 웹 사이트로 이동합니다. URL은 `https://<your-site-name>.azurewebsites.net` 형식이어야 합니다.
    
    ![웹 사이트로 이동합니다.](./media/blob-event-quickstart-portal/web-site.png)

6. 사이트가 보이지만 사이트에 이벤트가 아직 게시되지 않았음을 확인합니다.

   ![새 사이트를 봅니다.](./media/blob-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>사용자 지정 항목 구독

Event Grid 항목을 구독하여 Event Grid에 추적하려는 이벤트와 이벤트를 보낼 위치를 알립니다.

1. 이제 사용자 지정 항목에 대한 **Event Grid 항목** 페이지의 도구 모음에서 **+ 이벤트 구독** 을 선택합니다.

    :::image type="content" source="./media/custom-event-quickstart-portal/new-event-subscription.png" alt-text="이벤트 구독 추가 단추":::
2. **이벤트 구독 만들기** 페이지에서 다음 단계를 수행합니다.
    1. 이벤트 구독에 대한 이벤트 **이름** 을 입력합니다.
    3. **엔드포인트 유형** 으로 **웹후크** 를 선택합니다. 
    4. **엔드포인트 선택** 을 선택합니다. 

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-subscription-values.png" alt-text="이벤트 구독 값 입력":::
    5. 웹 후크 엔드포인트의 경우 웹앱의 URL을 제공하고 `api/updates`를 홈 페이지 URL에 추가합니다. **선택 확인** 을 선택합니다.

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-endpoint.png" alt-text="엔드포인트 URL 제공":::
    6. **이벤트 구독 만들기** 페이지에서 **만들기** 를 선택합니다.

3. 웹앱을 다시 확인하고, 구독 유효성 검사 이벤트를 보냈음을 확인합니다. 눈 모양 아이콘을 선택하여 이벤트 데이터를 확장합니다. Event Grid는 유효성 검사 이벤트를 보내므로 엔드포인트는 이벤트 데이터를 수신하려는 것을 확인할 수 있습니다. 웹앱은 구독의 유효성을 검사하는 코드를 포함합니다.

    ![구독 이벤트 보기](./media/custom-event-quickstart-portal/view-subscription-event.png)

## <a name="send-an-event-to-your-topic"></a>토픽에 이벤트 보내기

이제 이벤트를 트리거하여 Event Grid가 메시지를 사용자 엔드포인트에 어떻게 배포하는지 살펴 보겠습니다. Azure CLI 또는 PowerShell을 사용하여 사용자 지정 항목에 테스트 이벤트를 보냅니다. 일반적으로 애플리케이션 또는 Azure 서비스는 이벤트 데이터를 보냅니다.

첫 번째 예제에서는 Azure CLI를 사용합니다. 사용자 지정 항목에 대한 URL 및 키와 샘플 이벤트 데이터를 가져옵니다. `<topic name>`의 사용자 지정 항목 이름을 사용합니다. 샘플 이벤트 데이터가 생성됩니다. JSON의 `data` 요소는 이벤트의 페이로드입니다. 모든 잘 구성된(Well-Formed) JSON은 이 필드에 배치될 수 있습니다. 또한 고급 라우팅 및 필터링을 위해 제목 필드를 사용할 수 있습니다. CURL은 HTTP 요청을 보내는 유틸리티입니다.


### <a name="azure-cli"></a>Azure CLI
1. Azure Portal에서 **Cloud Shell** 을 선택합니다. Cloud Shell이 웹 브라우저의 아래쪽 창에 열립니다. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Cloud Shell 선택 아이콘":::
1. Cloud Shell 창의 왼쪽 위 모서리에서 **Bash** 를 선택합니다. 

    ![Cloud Shell - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. 다음 명령을 실행하여 항목에 대한 **엔드포인트** 를 가져옵니다. 명령을 복사하고 붙여넣은 후, 명령을 실행하기 전에 **항목 이름** 및 **리소스 그룹 이름** 을 업데이트합니다. 이 토픽 엔드포인트에 샘플 이벤트를 게시합니다. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. 다음 명령을 실행하여 사용자 지정 항목에 대한 **키** 를 가져옵니다. 명령을 복사하고 붙여넣은 후, 명령을 실행하기 전에 **항목 이름** 및 **리소스 그룹** 이름을 업데이트합니다. Event Grid 토픽의 기본 키입니다. Azure Portal에서 이 키를 가져오려면 **Event Grid 토픽** 페이지의 **액세스 키** 탭으로 전환합니다. 사용자 지정 토픽에 이벤트를 게시하려면 액세스 키가 필요합니다. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. 이벤트 정의와 함께 다음 명령문을 복사하고 **ENTER** 를 누릅니다. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. 다음 **Curl** 명령을 실행하여 이벤트를 게시합니다. 명령에서 `aeg-sas-key` 헤더는 이전에 가져온 액세스 키로 설정됩니다. 

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
두 번째 예제에서는 PowerShell을 사용하여 비슷한 단계를 수행합니다.

1. Azure Portal에서 **Cloud Shell** 을 선택하거나 `https://shell.azure.com/`으로 이동합니다. Cloud Shell이 웹 브라우저의 아래쪽 창에 열립니다. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Cloud Shell 선택 아이콘":::
1. **Cloud Shell** 에서 Cloud Shell 창의 왼쪽 위 모서리에서 **PowerShell** 을 선택합니다. Azure CLI 섹션에서 샘플 **Cloud Shell** 창 이미지를 확인합니다.
2. 다음 변수를 설정합니다. 각 명령을 복사하고 붙여넣은 후, 명령을 실행하기 전에 **항목 이름** 및 **리소스 그룹 이름** 을 업데이트합니다.

    **리소스 그룹**:
    ```powershell
    $resourceGroupName = "<resource group name>"
    ```

    **Event Grid 토픽 이름**:    
    ```powershell
    $topicName = "<topic name>"
    ```
3. 다음 명령을 실행하여 항목에 대한 **엔드포인트** 및 **키** 를 가져옵니다.

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. 이벤트를 준비합니다. Cloud Shell 창에서 명령문을 복사하고 실행합니다. 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. **Invoke-WebRequest** cmdlet을 사용하여 이벤트를 보냅니다. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Event Grid 뷰어에서 확인
이벤트를 트리거했고 Event Grid가 구독할 때 구성한 엔드포인트로 메시지를 보냈습니다. 웹앱을 확인하여 방금 전송한 이벤트를 봅니다.

:::image type="content" source="./media/custom-event-quickstart-portal/event-grid-viewer-end.png" alt-text="Event Grid 뷰어":::

## <a name="clean-up-resources"></a>리소스 정리
이 이벤트로 작업을 계속하려는 경우 이 문서에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 이 문서에서 만든 리소스를 삭제합니다.

1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다. 왼쪽 메뉴에 보이지 않으면, 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **리소스 그룹** 을 선택합니다. 

    ![리소스 그룹](./media/custom-event-quickstart-portal/delete-resource-groups.png)
1. 리소스 그룹을 선택하여 **리소스 그룹** 페이지를 시작합니다. 
1. 도구 모음에서 **리소스 그룹 삭제** 를 선택합니다. 
1. 리소스 그룹의 이름을 입력하여 삭제를 확인하고 **삭제** 를 선택합니다. 

    이미지에 보이는 다른 리소스 그룹은 Cloud Shell 창에서 생성되어 사용되었습니다. 나중에 Cloud Shell 창에서 사용하지 않으려면 삭제합니다. 

## <a name="next-steps"></a>다음 단계

이제 사용자 지정 항목 및 이벤트 구독을 만드는 방법에 대해 알아보았습니다. 다음으로 어떤 Event Grid가 도움이 되는지 자세히 알아보세요.

- [Event Grid 정보](overview.md)
- [Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid 및 Logic Apps를 사용하여 가상 머신 변경 모니터링](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [데이터 웨어하우스로 빅 데이터 스트림](event-grid-event-hubs-integration.md)

서로 다른 프로그래밍 언어를 사용하여 Event Grid에서 이벤트를 게시하고 사용하는 방법을 알아보려면 다음 샘플을 참조하세요. 

- [.NET에 대한 Azure Event Grid 샘플](/samples/azure/azure-sdk-for-net/azure-event-grid-sdk-samples/)
- [Java에 대한 Azure Event Grid 샘플](/samples/azure/azure-sdk-for-java/eventgrid-samples/)
- [Python에 대한 Azure Event Grid 샘플](/samples/azure/azure-sdk-for-python/eventgrid-samples/)
- [JavaScript에 대한 Azure Event Grid 샘플](/samples/azure/azure-sdk-for-js/eventgrid-javascript/)
- [TypeScript에 대한 Azure Event Grid 샘플](/samples/azure/azure-sdk-for-js/eventgrid-typescript/)
