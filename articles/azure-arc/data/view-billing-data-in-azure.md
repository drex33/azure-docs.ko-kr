---
title: 청구 데이터를 Azure에 업로드하고 Azure Portal에서 보기
description: 청구 데이터를 Azure에 업로드하고 Azure Portal에서 보기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 2c4e25aebf46ea13b69b8ca24d1336c4ba5521ad
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529418"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>청구 데이터를 Azure에 업로드하고 Azure Portal에서 보기

> [!IMPORTANT] 
>  미리 보기 기간 동안 Azure Arc 지원 데이터 서비스를 사용하는 데 비용이 들지 않습니다. 청구 시스템이 엔드투엔드로 작동하지만 청구 미터는 $0으로 설정됩니다.  이 시나리오를 따르면 현재 **하이브리드 데이터 서비스** 라는 서비스와 **Microsoft.AzureArcData/`<resource type>`** 라는 유형의 리소스에 대한 항목이 청구서에 표시됩니다. 만든 Azure Arc의 각 데이터 서비스에 대한 레코드를 볼 수 있지만 각 레코드에 대한 요금은 0달러입니다.


## <a name="connectivity-modes---implications-for-billing-data"></a>연결 모드 - 청구 데이터에 대한 영향

앞으로 Azure Arc 지원 데이터 서비스를 실행할 수 있는 두 가지 모드가 제공됩니다.

- **간접적으로 연결됨** - Azure에 직접 연결되지 않습니다. 데이터는 내보내기/업로드 프로세스를 통해서만 Azure로 전송됩니다.
- **직접 연결됨** - 이 모드에서는 Azure Arc 지원 데이터 서비스가 실행 중인 Kubernetes 클러스터와 Azure 간의 직접 연결을 제공하기 위해 Azure Arc 지원 Kubernetes 서비스에 종속됩니다. 이렇게 하면 더 많은 기능을 사용할 수 있으며 Azure PaaS에서 데이터 서비스를 관리하는 것처럼 Azure Portal 및 Azure CLI를 사용하여 Azure Arc 지원 데이터 서비스를 관리할 수도 있습니다.  이 연결 모드는 아직 미리 보기에서 사용할 수 없지만 곧 제공될 예정입니다.

[연결 모드](./connectivity.md)의 차이점에 대해 자세히 알아볼 수 있습니다.

간접 연결 모드에서 청구 데이터는 Azure Arc 데이터 컨트롤러에서 보안 파일로 주기적으로 내보낸 다음 Azure에 업로드되어 처리됩니다.  곧 출시될 직접 연결 모드에서는 서비스 비용을 거의 실시간으로 볼 수 있도록 청구 데이터가 약 1시간 동안 Azure로 자동 전송됩니다. 간접적으로 연결된 모드에서 데이터를 내보내고 업로드하는 프로세스는 스크립트를 사용하여 자동화할 수도 있고, 이를 수행할 서비스를 구축할 수도 있습니다.

## <a name="upload-billing-data-to-azure"></a>Azure에 청구 데이터 업로드

청구 데이터를 Azure에 업로드하려면 먼저 다음이 수행되어야 합니다.

1. 아직 없는 경우 Azure Arc 지원 데이터 서비스를 만듭니다. 예를 들어 다음 중 하나를 만듭니다.
   - [Azure Arc에서 Azure SQL Managed Instance 만들기](create-sql-managed-instance.md)
   - [Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)
1. 아직 업로드하지 않은 경우 [Azure Monitor에 리소스 인벤토리, 사용량 데이터, 메트릭 및 로그를 업로드](upload-metrics-and-logs-to-azure-monitor.md)합니다.
1. 청구 원격 측정 수집 프로세스에서 일부 청구 데이터를 수집할 수 있도록 데이터 서비스를 만든 후 최소 2시간 동안 기다립니다.

청구 데이터를 내보내려면 다음 명령을 실행합니다.

```azurecli
az arcdata dc export -t usage -p usage.json --k8s-namespace <namespace> --use-k8s
```

현재 파일은 내용을 볼 수 있도록 암호화되어 있지 않습니다. 텍스트 편집기에서 자유롭게 열어 내용이 어떻게 보이는지 확인합니다.

`resources` 및 `data`의 두 가지 데이터 세트가 있음을 알 수 있습니다. `resources`는 데이터 컨트롤러, PostgreSQL 하이퍼스케일 서버 그룹 및 SQL Managed Instances입니다. 데이터의 `resources` 레코드는 리소스 기록의 관련 이벤트(만들기, 업데이트, 삭제 시기)를 캡처합니다. `data` 레코드는 시간당 지정된 인스턴스에서 사용할 수 있는 코어 수를 캡처합니다.

`resource` 항목의 예:

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

`data` 항목의 예:

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

다음 명령을 실행하여 usage.json 파일을 Azure에 업로드합니다.

```azurecli
az arcdata dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>Azure Portal에서 청구 데이터 보기

Azure Portal에서 청구 데이터를 보려면 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 화면 상단의 검색창에 **Cost Management** 를 입력하고 Cost Management 서비스를 클릭합니다.
1. **Cost Management 개요** 에서 **Cost Management** 탭을 클릭합니다.
1. 왼쪽에서 **비용 분석** 탭을 클릭합니다.
1. 보기 상단에서 **리소스별 비용** 단추를 클릭합니다.
1. 범위가 데이터 서비스 리소스가 만들어진 구독으로 설정되어 있는지 확인합니다.
1. 보기 상단 근처의 범위 선택기 옆에 있는 보기 드롭다운에서 **리소스별 비용** 을 선택합니다.
1. 날짜 필터가 **이번 달** 또는 데이터 서비스 리소스를 만든 시점을 감안할 때 적절한 다른 시간 범위로 설정되어 있는지 확인합니다.
1. 한 가지 유형의 Azure Arc 지원 데이터 서비스로만 필터링하려면 **필터 추가** 를 클릭하여 **리소스 종류** = `Microsoft.AzureArcData/<data service type>`별로 필터를 추가합니다.
1. 이제 만들어져 Azure에 업로드된 모든 리소스 목록이 표시됩니다. 청구 미터가 $0이므로 비용이 항상 $0임을 알 수 있습니다.

## <a name="download-billing-data"></a>청구 데이터 다운로드

Azure Portal에서 직접 청구 요약 데이터를 다운로드할 수 있습니다.

1. 위의 지침에 따라 도달한 동일한 **비용 분석 -> 리소스 종류별 보기** 보기를 보려면 상단 근처에 있는 다운로드 단추를 클릭합니다.
1. 다운로드 파일 형식(Excel 또는 CSV)을 선택하고 **데이터 다운로드** 단추를 클릭합니다.
1. 선택한 파일 유형에 따라 적절한 편집기에서 파일을 엽니다.

## <a name="export-billing-data"></a>청구 데이터 내보내기

청구 내보내기 작업을 만들어 주기적으로 자동으로 **자세한** 사용량 및 청구 데이터를 Azure Storage 컨테이너로 내보낼 수 있습니다. 이는 청구 기간 동안 지정된 인스턴스가 청구된 시간과 같은 청구 세부 정보를 확인하려는 경우에 유용합니다.

청구 내보내기 작업을 설정하려면 다음 단계를 따릅니다.

1. 왼쪽에서 **내보내기** 를 클릭합니다.
1. **추가** 를 클릭합니다.
1. 이름과 내보내기 빈도를 입력하고 다음을 클릭합니다.
1. 새 스토리지 계정을 만들거나 기존 계정을 사용하도록 선택하고 청구 데이터 파일을 내보낼 스토리지 계정, 컨테이너 및 디렉터리 경로를 지정하는 양식을 작성하고 다음을 클릭합니다.
1. **만들기** 를 클릭합니다.

청구 데이터 내보내기 파일은 약 4시간 후에 사용할 수 있으며 청구 내보내기 작업을 만들 때 지정한 일정에 따라 내보내집니다.

내보낸 청구 데이터 파일을 보려면 다음 단계를 따릅니다.

Azure Portal에서 청구 데이터 파일의 유효성을 검사할 수 있습니다. 

> [!IMPORTANT]
> 청구 내보내기 작업을 만든 후 다음 단계를 진행하기 전에 4시간 동안 기다립니다.

1. 포털 상단의 검색창에 **스토리지 계정** 을 입력하고 **스토리지 계정** 을 클릭합니다.
3. 위에서 청구 내보내기 작업을 만들 때 지정한 스토리지 계정을 클릭합니다.
4. 왼쪽에서 컨테이너를 클릭합니다.
5. 위에서 청구 내보내기 작업을 만들 때 지정한 컨테이너를 클릭합니다.
6. 위에서 청구 내보내기 작업을 만들 때 지정한 폴더를 클릭합니다.
7. 생성된 폴더 및 파일을 드릴다운하고 생성된 .csv 파일 중 하나를 클릭합니다.
8. 파일을 로컬 다운로드 폴더에 저장하는 **다운로드** 단추를 클릭합니다.
9. Excel과 같은 .csv 파일 뷰어를 사용하여 파일을 엽니다.
10. **리소스 유형** = `Microsoft.AzureArcData/<data service resource type`이 있는 행만 표시하도록 결과를 필터링합니다.
11. UsageQuantity 열에서 현재 24시간 동안 인스턴스가 사용된 시간을 볼 수 있습니다.
