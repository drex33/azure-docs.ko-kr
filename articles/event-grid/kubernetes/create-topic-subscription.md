---
title: Kubernetes의 Azure Event Grid - 이벤트 처리기로 Webhook
description: 이 문서에서는 Azure Arc에 연결된 Kubernetes 클러스터에서 이벤트 그리드 토픽을 만든 다음, 해당 토픽에 대한 구독을 만드는 방법을 설명합니다.
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 06/17/2021
ms.topic: quickstart
ms.openlocfilehash: aa4c83d5e702c8f39a88c285c716a84ba2716d45
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337569"
---
# <a name="route-cloud-events-to-webhooks-with-azure-event-grid-on-kubernetes"></a>Kubernetes의 Azure Event Grid를 사용하여 클라우드 이벤트를 웹후크로 라우팅
이 빠른 시작에서는 Kubernetes의 Event Grid에서 토픽을 만들고 토픽에 대한 구독을 만든 다음, 토픽에 샘플 이벤트를 보내 시나리오를 테스트합니다. 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]


## <a name="prerequisites"></a>필수 구성 요소

1. [Kubernetes 클러스터를 Azure Arc에 연결합니다](../../azure-arc/kubernetes/quickstart-connect-cluster.md).
1. [Kubernetes 클러스터에 Event Grid 확장을 설치합니다](install-k8s-extension.md). 이 확장은 Kubernetes 클러스터에 Event Grid를 배포합니다. 


## <a name="create-a-custom-location"></a>사용자 지정 위치 만들기
Azure 위치 확장인 사용자 지정 위치를 사용하면 Azure Arc 지원 Kubernetes 클러스터를 Event Grid 토픽과 같은 리소스를 배포하기 위한 대상 위치로 사용할 수 있습니다. 사용자 지정 위치는 클러스터의 네임스페이스를 나타내며 토픽 및 이벤트 구독이 배포되는 위치입니다. 이 섹션에서는 사용자 지정 위치를 만듭니다. 

1. Azure Arc 클러스터, 리소스 그룹 및 사용자 지정 위치 이름의 값을 보유하도록 다음 변수를 선언합니다. 이러한 명령문을 편집기로 복사하고 값을 바꾼 다음, bash 창에 복사/붙여넣습니다.  

    ```azurecli-interactive
    resourcegroupname="<AZURE RESOURCE GROUP NAME>"
    arcclustername="<AZURE ARC CLUSTER NAME>"
    customlocationname="<CUSTOM LOCATION NAME>"
    ```
1. Azure Arc 연결 클러스터의 리소스 ID를 가져옵니다. 명령을 실행하기 전에 Azure Arc 클러스터 이름 및 리소스 그룹 매개 변수에 대한 값을 업데이트합니다. 

    ```azurecli-interactive
    hostresourceid=$(az connectedk8s show -n $arcclustername -g $resourcegroupname --query id -o tsv)    
    ```
1. Event Grid 확장 리소스 ID를 가져옵니다. 이 단계에서는 Event Grid 확장에 대해 지정한 이름이 **eventgrid-ext** 라고 가정합니다. 명령을 실행하기 전에 Azure Arc 클러스터 및 리소스 그룹 이름을 업데이트합니다. 

    ```azurecli-interactive
    clusterextensionid=$(az k8s-extension show --name eventgrid-ext --cluster-type connectedClusters -c $arcclustername -g $resourcegroupname  --query id -o tsv)    
    ```
1. 위의 두 값을 사용하여 사용자 지정 위치를 만듭니다. 명령을 실행하기 전에 사용자 지정 위치 및 리소스 그룹 이름을 업데이트합니다. 

    ```azurecli-interactive
    az customlocation create -n $customlocationname -g $resourcegroupname --namespace arc --host-resource-id $hostresourceid --cluster-extension-ids $clusterextensionid    
    ```
1. 사용자 지정 위치의 리소스 ID를 가져옵니다. 명령을 실행하기 전에 사용자 지정 위치 이름을 업데이트합니다. 

    ```azurecli-interactive
    customlocationid=$(az customlocation show -n $customlocationname -g $resourcegroupname --query id -o tsv)    
    ```

    사용자 지정 위치 생성에 대한 자세한 내용은 [Azure Arc 지원 Kubernetes에서 사용자 지정 위치 만들기 및 관리](../../azure-arc/kubernetes/custom-locations.md)를 참조하세요. 

## <a name="create-a-topic"></a>토픽 만들기
이 섹션에서는 이전 단계에서 만든 사용자 지정 위치에 항목을 만듭니다. 명령을 실행하기 전에 리소스 그룹 및 이벤트 그리드 토픽 이름을 업데이트합니다. 미국 동부 이외의 위치를 사용하는 경우 위치를 업데이트합니다. 

1. 토픽 이름을 보유할 변수를 선언합니다. 

    ```azurecli-interactive
    topicname="<TOPIC NAME>"
    ```
4. 다음 명령을 실행하여 토픽을 만듭니다. 

    ```azurecli-interactive
    az eventgrid topic create -g $resourcegroupname --name $topicname --kind azurearc --extended-location-name $customlocationid --extended-location-type customlocation --input-schema CloudEventSchemaV1_0 --location $region    
    ```

    CLI 명령에 대한 자세한 내용은 [`az eventgrid topic create`](/cli/azure/eventgrid/topic#az_eventgrid_topic_create)를 참조하세요.

## <a name="create-a-message-endpoint"></a>메시지 엔드포인트 만들기

사용자 지정 항목에 대한 구독을 만들기 전에 이벤트 메시지에 대한 엔드포인트를 만듭니다. 일반적으로 엔드포인트는 이벤트 데이터를 기반으로 작업을 수행합니다. 이 빠른 시작을 간소화하기 위해 이벤트 메시지를 표시하는 [미리 작성된 웹앱](https://github.com/Azure-Samples/azure-event-grid-viewer)을 배포합니다. 배포된 솔루션은 App Service 계획, App Service 웹앱 및 GitHub의 소스 코드를 포함합니다.

1. 문서 페이지에서 **Azure에 배포** 를 선택하여 구독에 솔루션을 배포합니다. Azure Portal에서 매개 변수에 대한 값을 제공합니다.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to deploy to Azure."></a>
1. 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 성공된 후 실행하는지 확인하려면 웹앱을 봅니다. 웹 브라우저에서 `https://<your-site-name>.azurewebsites.net`으로 이동합니다.

    배포에 실패하면 오류 메시지를 확인합니다. 웹 사이트 이름이 이미 사용되었기 때문일 수 있습니다. 템플릿을 다시 배포하고 사이트에 대해 다른 이름을 선택합니다. 
1. 참조하는 사이트에 이벤트가 아직 게시되지 않았습니다.

   ![새 사이트 보기](../media/custom-event-quickstart-portal/view-site.png)

## <a name="create-a-subscription"></a>구독 만들기
구독자는 토픽에 게시된 이벤트를 등록할 수 있습니다. 이벤트를 수신하려면 관심 있는 토픽에 대한 Event Grid 구독을 만들어야 합니다. 이벤트 구독은 이러한 이벤트가 전송되는 대상을 정의합니다. 지원되는 모든 대상 또는 처리기에 대해 알아보려면 [이벤트 처리기](event-handlers.md)를 참조하세요.

WebHook(HTTPS 엔드포인트) 대상을 사용하여 이벤트 구독을 만들려면 이벤트 구독의 이름을 입력하고 웹 사이트의 이름을 업데이트하고 다음 명령을 실행합니다.

```azurecli-interactive
topicid=$(az eventgrid topic show --name $topicname --resource-group $resourcegroupname --query id -o tsv)
az eventgrid event-subscription create --name <EVENT SUBSCRIPTION NAME> --source-resource-id $topicid --endpoint https://<SITE NAME>.azurewebsites.net/api/updates
```


CLI 명령에 대한 자세한 내용은 [`az eventgrid event-subscription create`](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create)를 참조하세요.

## <a name="send-events-to-the-topic"></a>토픽에 이벤트 보내기
1. 다음 명령을 실행하여 항목에 대한 **엔드포인트** 를 가져옵니다. 명령을 복사하고 붙여넣은 후, 명령을 실행하기 전에 **항목 이름** 및 **리소스 그룹 이름** 을 업데이트합니다. 이 토픽 엔드포인트에 샘플 이벤트를 게시합니다. 

    ```azurecli
    az eventgrid topic show --name $topicname -g $resourcegroupname --query "endpoint" --output tsv
    ```
2. 다음 명령을 실행하여 사용자 지정 항목에 대한 **키** 를 가져옵니다. 명령을 복사하고 붙여넣은 후, 명령을 실행하기 전에 **항목 이름** 및 **리소스 그룹** 이름을 업데이트합니다. 토픽의 기본 키입니다. Azure Portal에서 이 키를 가져오려면 **Event Grid 토픽** 페이지의 **액세스 키** 탭으로 전환합니다. 사용자 지정 토픽에 이벤트를 게시하려면 액세스 키가 필요합니다. 

    ```azurecli
    az eventgrid topic key list --name $topicname -g $resourcegroupname --query "key1" --output tsv
    ```
1. 다음 **Curl** 명령을 실행하여 이벤트를 게시합니다. 명령을 실행하기 전에 1단계와 2단계에서 엔드포인트 URL 및 키를 지정합니다. 

    ```bash
    curl  -k -X POST -H "Content-Type: application/cloudevents-batch+json" -H "aeg-sas-key: <KEY_FROM_STEP_2>" -g <ENDPOINT_URL_FROM_STEP_1> \
    -d  '[{ 
          "specversion": "1.0",
          "type" : "orderCreated",
          "source": "myCompanyName/us/webCommerceChannel/myOnlineCommerceSiteBrandName",
          "id" : "eventId-n",
          "time" : "2020-12-25T20:54:07+00:00",
          "subject" : "account/acct-123224/order/o-123456",
          "dataSchema" : "1.0",
          "data" : {
             "orderId" : "123",
             "orderType" : "PO",
             "reference" : "https://www.myCompanyName.com/orders/123"
          }
    }]'
    ```
    
    1단계의 토픽 엔드포인트 URL이 개인 IP 주소이면(예: Event Grid 브로커의 서비스 유형이 ClusterIP 인 경우) 클러스터의 다른 pod 내에서 **Curl** 을 실행하여 해당 IP 주소에 액세스할 수 있습니다. 예를 들어, 다음 단계를 수행해야 합니다.

    1. 다음 구성을 사용하여 매니페스트 파일을 만듭니다. 필요에 따라 ``dnsPolicy``를 조정할 수 있습니다. 자세한 내용은 [서비스 및 pod용 DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)를 참조하세요.
    
        ```yml
        apiVersion: v1
        kind: Pod
        metadata:
            name: test-pod2
        spec:
            containers:
              - name: nginx
                image: nginx
            hostNetwork: true
            dnsPolicy: ClusterFirstWithHostNet       
        ```
    1. pod를 만듭니다.
        ```bash
            kubectl apply -f <name_of_your_yaml_manifest_file>
        ```
    1. pod가 실행 중인지 확인합니다.
        ```bash
            kubectl get pod test-pod
        ```
    1. 컨테이너에서 셸 세션 시작
        ```bash
            kubectl exec --stdin --tty test-pod -- /bin/bash
        ```

    이제 클러스터의 실행 중인 컨테이너에 위의 이전 단계에 설명된 **Curl** 명령을 실행할 수 있는 셸 세션이 있습니다.

    > [!NOTE]
    > 프로그래밍 언어를 사용하여 클라우드 이벤트를 보내는 방법을 알아보려면 다음 샘플을 참조하세요. 
    > - [C#](/samples/azure/azure-sdk-for-net/azure-event-grid-sdk-samples/)의 검사 샘플을 참조하세요.
    > - [Java](/samples/azure/azure-sdk-for-java/eventgrid-samples/)
    > - [JavaScript](/samples/azure/azure-sdk-for-js/eventgrid-javascript/) 및 [TypeScript](/samples/azure/azure-sdk-for-js/eventgrid-typescript/)
    > - [Python](/samples/azure/azure-sdk-for-python/eventgrid-samples/)

### <a name="verify-in-the-event-grid-viewer"></a>Event Grid 뷰어에서 확인
이벤트를 트리거했고 Event Grid가 구독할 때 구성한 엔드포인트로 메시지를 보냈습니다. 웹앱을 확인하여 방금 전송한 이벤트를 봅니다.

:::image type="content" source="./media/create-topic-subscription/viewer-received-event.png" alt-text="Event Grid Viewer에서 수신된 이벤트 보기":::

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 
- [이벤트 처리기 및 대상](event-handlers.md) - Kubernetes의 Event Grid에서 지원하는 모든 이벤트 처리기 및 대상에 대한 정보를 제공합니다. 
- [이벤트 필터링](filter-events.md) - 이벤트 구독의 이벤트 필터링에 대한 정보를 제공합니다. 
