---
title: 제품 만들기 또는 수정 | Microsoft Docs
description: 새 제품을 만들거나 기존 제품을 업데이트하기 위한 API입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: 9ea679864d722306b9a7375e732a8ce90d1eed9f
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069903"
---
# <a name="create-or-modify-an-offer"></a>제안 만들기 또는 수정

> [!NOTE]
> Cloud 파트너 포털 API는 파트너 센터와 통합되었으며 앞으로도 계속 작동합니다. 이 전환으로 인해 몇 가지 사소한 사항이 변경되었습니다. 파트너 센터로 전환한 후에도 코드가 계속 작동하도록 [Cloud 파트너 포털 API 참조](./cloud-partner-portal-api-overview.md)에 나열된 변경 내용을 검토합니다. CPP API는 파트너 센터로 전환하기 전에 이미 통합된 기존 제품에만 사용해야 합니다. 새 제품은 파트너 센터 제출 API를 사용해야 합니다.

이 호출은 게시자 네임스페이스 내에서 특정 제안을 업데이트하거나 새 제안을 만듭니다.

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 매개 변수

|  **이름**         |  **설명**                      |  **데이터 형식**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  게시자 식별자입니다(예: `contoso`). |   String |
| offerId           |  제안 식별자입니다.                     |   String        |
| api-version       |  최신 버전의 API입니다.            |   Date           |
|  |  |  |

## <a name="header"></a>헤더

|  **이름**        |  **값**               |
|  ---------       |  ----------              | 
| 콘텐츠 형식     | `application/json`       |
| 권한 부여    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>본문 예제

다음 예제에서는 offerID가 `contosovirtualmachine`인 제안을 만듭니다.

### <a name="request"></a>요청

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
          "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
          "microsoft-azure-marketplace.termsOfUse": "Terms of use",
          "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
          "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.supportContactName": "Jon Doe",
          "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.publicAzureSupportUrl": "",
          "microsoft-azure-marketplace.fairfaxSupportUrl": ""
      },
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>응답

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.screenshots": [],
             "microsoft-azure-marketplace.videos": [],
             "microsoft-azure-marketplace.leadDestination": "None",
             "microsoft-azure-marketplace.tableLeadConfiguration": {},
             "microsoft-azure-marketplace.blobLeadConfiguration": {},
             "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
             "microsoft-azure-marketplace.crmLeadConfiguration": {},
             "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
             "microsoft-azure-marketplace.marketoLeadConfiguration": {},
             "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
             "microsoft-azure-marketplace.termsOfUse": "Terms of use",
             "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
             "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.supportContactName": "Jon Doe",
             "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.publicAzureSupportUrl": "",
             "microsoft-azure-marketplace.fairfaxSupportUrl": ""
         },
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> 이 제안을 수정하려면 위 요청에 **If-Match** 헤더 집합을 추가합니다. 위와 동일한 요청 본문을 사용하지만 필요에 따라 값을 수정합니다. 

### <a name="response-status-codes"></a>응답 상태 코드

| ‘코드’  |  **설명**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. 요청이 성공적으로 처리되었으며 제안이 수정되었습니다.           |
|  201      | `Created`. 요청이 성공적으로 처리되었으며 제안이 만들어졌습니다.   |
|  400      | `Bad/Malformed request`. 오류 응답 본문이 자세한 정보를 제공할 수 있습니다.            |
|  403      | `Forbidden`. 클라이언트에서 요청된 네임스페이스에 액세스할 수 없습니다.                     |
|  404      | `Not found`. 클라이언트에서 참조한 엔터티가 존재하지 않습니다.                           |
|  412      | 요청자가 요청에 지정한 사전 조건 중 하나를 서버가 충족하지 않습니다. 클라이언트가 요청과 함께 전송된 ETAG를 확인해야 합니다. |
|  |  |

## <a name="uploading-artifacts"></a>아티팩트 업로드 중

위 예제와 같이 이미지, 로고 등의 아티팩트를 웹의 액세스 가능 위치에 업로드한 후 각각을 PUT 요청에 URI로 포함하여 공유해야 합니다. 시스템은 이러한 파일이 Azure Marketplace 스토리지에 없는 것을 감지하고 다운로드합니다. 결과적으로, 후속 GET 요청은 이러한 파일의 Azure Marketplace 서비스 URL을 반환한다는 것을 알 수 있습니다.

## <a name="categories-and-industries"></a>범주 및 산업

새 제품을 만들 때 마켓플레이스에서 제품에 대한 범주를 지정해야 합니다. 필요에 따라 일부 제품 유형에 대해 산업을 지정할 수도 있습니다. 제품 유형에 따라 다음 표의 특정 키 값을 사용하여 제품에 적용 가능한 범주/산업을 제공합니다.

### <a name="azure-marketplace-categories"></a>Azure Marketplace 범주

이러한 범주 및 해당 키는 Azure 앱, 가상 머신, 핵심 가상 머신, 컨테이너, 컨테이너 앱, IoT Edge 모듈 및 SaaS 제품 유형에 적용됩니다. 굵은 기울임꼴 항목(예: ***분석***)은 범주이며 표준 텍스트 항목(예: data-insights)은 하위 범주입니다. 공백 또는 대/소문자를 변경하지 말고 정확한 키 값을 사용합니다.

| 범주 | SaaS 키 | Azure 앱 키 | 가상 머신, 컨테이너, 컨테이너 앱, IoT Edge 모듈, 핵심 가상 머신 키 |
| --- | --- | --- | --- |
| ***분석** _ | _*_analytics_*_ | _*_analytics-azure-apps_*_ | _ *_analytics-amp_** |
| 데이터 인사이트 | data-insights | data-insights | data-insights |
| 데이터 분석 | data-analytics | data-analytics | data-analytics |
| 빅 데이터 | big-data | bigData | big-data |
| 예측 분석 | predictive-analytics | predictive-analytics | predictive-analytics |
| 실시간/스트리밍 분석 | real-time-streaming-analytics | real-time-streaming-analytics | real-time-streaming-analytics |
| 기타 | 기타 | other-analytics | 기타 |
| ***AI + 기계 학습** _ | _*_ArtificialIntelligence_*_ | _*_ai-plus-machine-learning_*_ | _ *_ai-plus-machine-learning_** |
| Bot Services | bot-services | bot-services | bot-services |
| Cognitive Services | cognitive-services | cognitive-services | cognitive-services |
| ML 서비스 | ml-service | ml-service | ml-service |
| 자동화된 ML | automated-ml | automated-ml | automated-ml |
| 비즈니스/로봇 프로세스 자동화 | business-robotic-process-automation | business-robotic-process-automation | business-robotic-process-automation |
| 데이터 레이블 지정 | data-labelling | data-labelling | data-labelling |
| 데이터 준비 | data-preparation | data-preparation | data-preparation |
| 정보 마이닝 | knowledge-mining | knowledge-mining | knowledge-mining |
| ML 작업 | ml-operations | ml-operations | ml-operations |
| 기타 | other-AI-plus-machine-learning | 기타 | 기타 |
| ***블록체인** _ | _*_blockchain_*_ | _*_blockchain_*_ | _ *_blockchain_** |
| 앱 가속기 | app-accelerators | app-accelerators | app-accelerators |
| 단일 노드 원장 | single-node-ledger | single-node-ledger | single-node-ledger |
| 다중 노드 원장 | multi-node-ledger | multi-node-ledger | multi-node-ledger |
| 도구 | 도구 | 도구 | 도구 |
| 기타 | 기타 | 기타 | 기타 |
| ***컴퓨팅** _ | _*_compute-saas_*_ | _*_compute-azure-apps_*_ | _ *_compute_** |
| 애플리케이션 인프라 | appInfra | appInfrastructure | application-infrastructure |
| 운영 체제 | clientOS | clientOS | operating-systems |
| 캐시 | 캐시 | 캐시 | 캐시 |
| 기타 | other-compute | other-compute | 기타 |
| ***컨테이너** _ | _*_containers_*_ | _*_containers_*_ | _ *_containers_** |
| 컨테이너 앱 | container-apps | container-apps | container-apps |
| 컨테이너 이미지 | container-images | container-images | container-images |
| 컨테이너 시작 | get-started-with-containers | get-started-with-containers | get-started-with-containers |
| 기타 | 기타 | 기타 | 기타 |
| ***데이터베이스** _ | _*_databases-saas_*_ | _*_database_*_ | _ *_databases_** |
| NoSQL 데이터베이스 | nosql-databases | nosql-databases | nosql-databases |
| 관계형 데이터베이스 | relational-databases | relational-databases | relational-databases |
| 원장/블록체인 데이터베이스 | ledger-blockchain-databases | ledger-blockchain-databases | ledger-blockchain-databases |
| 데이터 레이크 | data-lakes | data-lakes | data-lakes |
| 데이터 웨어하우스 | data-warehouse | data-warehouse | data-warehouse |
| 기타 | other-databases | other-databases | 기타 |
| ***개발자 도구** _ | _*_developer-tools-saas_*_ | _*_developer-tools-azure-apps_*_ | _ *_developer-tools_** |
| 도구 | tools-developer-tools | tools-developer-tools | tools-developer-tools |
| 스크립트 | 스크립트 | 스크립트 | 스크립트 |
| 개발자 서비스 | devService | devService | developer-service |
| 기타 | other-developer-tools | other-developer-tools | 기타 |
| ***DevOps** _ | _*_devops_*_ | _*_devops_*_ | _ *_devops_** |
| 기타 | 기타 | 기타 | 기타 |
| ***ID** _ | _*_identity_*_ | _*_identity_*_ | _ *_identity_** |
| 액세스 관리 | access-management | access-management | access-management |
| 기타 | 기타 | 기타 | 기타 |
| ***통합** _ | _*_integration_*_ | _*_integration_*_ | _ *_integration_** |
| 메시징 | 메시징 | 메시징 | 메시징 |
| 기타 | 기타 | 기타 | 기타 |
| ***사물 인터넷** _ | _*_IoT_*_ | _*_internet-of-things-azure-apps_*_ | _ *_internet-of-things_** |
| IoT 핵심 서비스 | 해당 없음 | iot-core-services | iot-core-services |
| IoT Edge 모듈 | 해당 없음 | iot-edge-modules | iot-edge-modules |
| IoT 솔루션 | iot-solutions | iot-solutions | iot-solutions |
| 데이터 분석 및 시각화 | data-analytics-and-visualization | data-analytics-and-visualization | data-analytics-and-visualization |
| IoT 연결 | iot-connectivity | iot-connectivity | iot-connectivity |
| 기타 | other-internet-of-things | other-internet-of-things | 기타 |
| ***IT 및 관리 도구** _ | _*_ITandAdministration_*_ | _*_it-and-management-tools-azure-apps_*_ | _ *_it-and-management-tools_** |
| 관리 솔루션 | management-solutions | management-solutions | management-solutions |
| 비즈니스 애플리케이션 | businessApplication | businessApplication | business-applications |
| 기타 | other-it-management-tools | other-it-management-tools | 기타 |
| ***모니터링 및 진단** _ | _*_monitoring-and-diagnostics_*_ | _*_monitoring-and-diagnostics_*_ | _ *_monitoring-and-diagnostics_** |
| 기타 | 기타 | 기타 | 기타 |
| ***미디어** _ | _*_media_*_ | _*_media_*_ | _ *_media_** |
| Media Services | media-services | media-services | media-services |
| Content Protection | content-protection | content-protection | content-protection |
| 라이브 및 주문형 스트리밍 | live-and-on-demand-streaming | live-and-on-demand-streaming | live-and-on-demand-streaming |
| 기타 | 기타 | 기타 | 기타 |
| ***마이그레이션** _ | _*_migration_*_ | _*_migration_*_ | _ *_migration_** |
| 데이터 마이그레이션 | data-migration | data-migration | data-migration |
| 기타 | 기타 | 기타 | 기타 |
| ***혼합 현실** _ | _*_mixed-reality_*_ | _*_mixed-reality_*_ | _ *_mixed-reality_** |
| 기타 | 기타 | 기타 | 기타 |
| ***네트워킹** _ | _*_networking_*_ | _*_networking_*_ | _ *_networking_** |
| 어플라이언스 관리자 | appliance-managers | appliance-managers | appliance-managers |
| 연결 | 연결 | 연결 | 연결 |
| 방화벽 | 방화벽 | 방화벽 | 방화벽 |
| 부하 분산 장치 | load-balancers | load-balancers | load-balancers |
| 기타 | 기타 | 기타 | 기타 |
| ***보안** _ | _*_security_*_ | _*_security_*_ | _ *_security_** |
| ID 및 액세스 관리 | identity-and-access-management | identity-and-access-management | identity-and-access-management |
| Threat Protection | threat-protection | threat-protection | threat-protection |
| Information Protection | information-protection | information-protection | information-protection |
| 기타 | 기타 | 기타 | 기타 |
| ***스토리지** _ | _*_storage-saas_*_ | _*_storage-azure-apps_*_ | _ *_storage_** |
| 백업 및 복구 | 백업(backup) | 백업(backup) | backup-and-recovery |
| 엔터프라이즈 하이브리드 스토리지 | enterprise-hybrid-storage | enterprise-hybrid-storage | enterprise-hybrid-storage |
| 파일 공유 | file-sharing | file-sharing | file-sharing |
| 데이터 수명 주기 관리 | data-lifecycle-management | data-lifecycle-management | data-lifecycle-management |
| 기타 | other-storage | other-storage | 기타 |
| ***웹** _ | _*_web_*_ | _*_web_*_ | _ *_web_** |
| 블로그 및 CMS | blogs-and-cmss | blogs-and-cmss | blogs-and-cmss |
| 시작 웹앱 | starter-web-apps | starter-web-apps | starter-web-apps |
| 전자 상거래 | ecommerce | ecommerce | ecommerce |
| Web Apps 프레임워크 | web-apps-frameworks | web-apps-frameworks | web-apps-frameworks |
| Web Apps | web-apps | web-apps | web-apps |
| 기타 | 기타 | 기타 | 기타 |
||||

### <a name="microsoft-appsource-categories"></a>Microsoft AppSource 범주

이러한 범주 및 해당 키는 SaaS, Power BI app, dynamics 365 business central, dynamics 365 for customer engagement 및 작업 제공 유형에 대 한 dynamics 365에 적용 됩니다. 굵은 기울임꼴 항목(예: ***분석***)은 범주이며 표준 텍스트 항목(예: advanced-analytics)은 하위 범주입니다. 공백 또는 대/소문자를 변경하지 말고 정확한 키 값을 사용합니다.

| 범주 | SaaS 키 | Dynamics 365 business central, Dynamics 365 for customer engagement, Dynamics 365 for operation 키 | 앱 키 Power BI |
| --- | --- | --- | --- |
| ***분석** _ | _*_analytics_*_ | _*_분석_*_ | _ *_Analytics_** |
| 고급 분석 | advanced-analytics | advanced-analytics | advanced-analytics |
| 시각화 및 보고 | visualization-reporting | visualization-reporting | visualization-reporting |
| 기타 | 기타 | other-analytics | other-analytics |
| ***AI + 기계 학습** _ | _*_ArtificialIntelligence_*_ | _*_ai-plus-machine-learning-dynamics_*_ | _ *_ai-plus-machine-learning-appsource_** |
| 비즈니스용 AI | ai-for-business | ai-for-business | ai-for-business |
| 봇 앱 | bot-apps | bot-apps | bot-apps |
| 기타 | other-ai-plus-machine-learning | other-ai-plus-machine-learning | other-ai-plus-machine-learning |
| **협업*** | _*_협업_*_ | _*_협업_*_ | _ *_collaboration_** |
| 연락처 및 사람 | contact-people | contact-people | contact-and-people |
| 모임 관리 | meeting-management | meeting-management | meeting-management |
| 사이트 디자인 및 관리 | site-design-management | site-design-management | site-design-and-management |
| 작업 및 프로젝트 관리 | task-project-management | task-project-management | task-and-project-management |
| 음성 및 화상 회의 | voice-video-conferencing | voice-video-conferencing | voice-and-video-conferencing |
| 기타 | other-collaboration | other-collaboration | 기타 |
| ***규정 준수 및 법률** _ | _*_compliance_*_ | _*_compliance_*_ | _ *_compliance-and-legal_** |
| 세금 및 감사 | tax-audit | tax-audit | tax-and-audit |
| Legal | Legal | Legal | legal |
| 데이터, 거버넌스 및 개인 정보 | data-governance-privacy | data-governance-privacy | data-governance-and-privacy |
| 건강 및 안전 | health-safety | health-safety | health-and-safety |
| 기타 | other-compliance-legal | other-compliance-legal | 기타 |
| ***고객 서비스** _ | _*_CustomerService_*_ | _*_CustomerService_*_ | _ *_customer-service_** |
| 연락 센터 | contact-center | contact-center | contact-center |
| 대면 서비스 | face-to-face-service | face-to-face-service | face-to-face-service |
| 백오피스 및 직원 서비스 | back-office-employee-service | back-office-employee-service | back-office-and-employee-service |
| 지식 및 사례 관리 | knowledge-case-management | knowledge-case-management | knowledge-and-case-management |
| 소셜 미디어 및 옴니채널 참여 | social-media-omnichannel-engagement | social-media-omnichannel-engagement | social-media-and-omnichannel-engagement |
| 기타 | other-customer-service | other-customer-service | 기타 |
| ***재무** _ | _*_Finance_*_ | _*_Finance_*_ | _ *_finance_** |
| 회계 | 회계 | 회계 | 회계 |
| 자산 관리 | asset-management | asset-management | asset-management |
| 분석, 통합 및 보고 | analytics-consolidation-reporting | analytics-consolidation-reporting | analytics-consolidation-and-reporting |
| 크레딧 및 컬렉션 | credit-collections | credit-collections | credit-and-collections |
| 규정 준수 및 위험 관리 | compliance-risk-management | compliance-risk-management | compliance-and-risk-management |
| 기타 | other-finance | other-finance | 기타 |
| ***인사 관리** _ | _*_HumanResources_*_ | _*_HumanResources_*_ | _ *_human-resources_** |
| 인재 획득 | talent-acquisition | talent-acquisition | talent-acquisition |
| 인재 관리 | talent-management | talent-management | talent-management |
| HR 운영 | hr-operations | hr-operations | hr-operations |
| 인력 계획 및 분석 | workforce-planning-analytics | workforce-planning-analytics | workforce-planning-and-analytics |
| 기타 | other-human-resources | other-human-resources | 기타 |
| ***사물 인터넷** _ | _*_IoT_*_ | _*_internet-of-things-dynamics_*_ | _ *_internet-of-things-appsource_** |
| 자산 관리 및 운영 | asset-management-operations | asset-management-operations | asset-management-and-operations |
| 연결된 제품 | connected-products | connected-products | connected-products |
| 지능형 공급망 | intelligent-supply-chain | intelligent-supply-chain | intelligent-supply-chain |
| 예측 유지 관리 | predictive-maintenance | predictive-maintenance | predictive-maintenance |
| 원격 모니터링 | remote-monitoring | remote-monitoring | remote-monitoring |
| 안전 및 보안 | safety-security | safety-security | safety-and-security |
| 스마트 인프라 및 리소스 | smart-infrastructure-resources | smart-infrastructure-resources | smart-infrastructure-and-resources |
| 차량 및 이동성 | vehicles-mobility | vehicles-mobility | vehicles-and-mobility |
| 기타 | other-internet-of-things | other-internet-of-things | 기타 |
| ***IT 및 관리 도구** _ | _*_ITandAdministration_*_ | _*_ITandAdministration_*_ | _ *_it-and-management-tools_** |
| 관리 솔루션 | management-solutions | management-solutions | management-solutions |
| 비즈니스 애플리케이션 | businessApplication | businessApplication | business-applications |
| 기타 | other-it-management-tools | other-it-management-tools | 기타 |
| ***마케팅** _ | _*_Marketing_*_ | _*_Marketing_*_ | _ *_marketing_** |
| 광고 | advertisement | advertisement | advertisement |
| 분석 | analytics-marketing | analytics-marketing | analytics-marketing |
| 캠페인 관리 및 자동화 | campaign-management-automation | campaign-management-automation | campaign-management-and-automation |
| 메일 마케팅 | email-marketing | email-marketing | email-marketing |
| L2 - 이벤트 및 리소스 관리 | events-resource-management | events-resource-management | events-and-resource-management |
| 리서치 및 분석 | research-analytics | research-analytics | research-and-analysis |
| 소셜 미디어 | social-media | social-media | social-media |
| 기타 | other-marketing | other-marketing | 기타 |
| ***운영 및 공급망** _ | _*_OperationsSupplyChain_*_ | _*_OperationsSupplyChain_*_ | _ *_operations-and-supply-chain_** |
| 자산 및 프로덕션 관리 | asset-production-management | asset-production-management | asset-and-production-management |
| 수요 예측 | demand-forecasting | demand-forecasting | demand-forecasting |
| 정보 관리 및 연결 | information-management-connectivity | information-management-connectivity | information-management-and-connectivity |
| 계획, 구매 및 보고 | planning-purchasing-reporting | planning-purchasing-reporting | planning-purchasing-and-reporting |
| 품질 및 서비스 관리 | quality-service-management | quality-service-management | quality-and-service-management |
| 판매 및 주문 관리 | sales-order-management | sales-order-management | sales-and-order-management |
| 교통 및 웨어하우스 관리 | transportation-warehouse-management | transportation-warehouse-management | transportation-and-warehouse-management |
| 기타 | other-operations-supply-chain | other-operations-supply-chain | 기타 |
| ***생산성** _ | _*_생산성_*_ | _*_생산성_*_ | _ *_productivity_** |
| 콘텐츠 만들기 및 관리 | content-creation-management | content-creation-management | content-creation-and-management |
| 언어 및 번역 | language-translation | language-translation | language-and-translation |
| 문서 관리 | document-management | document-management | document-management |
| 전자 메일 관리 | email-management | email-management | email-management |
| 검색 및 참조 | search-reference | search-reference | search-and-reference |
| 기타 | other-productivity | other-productivity | 기타 |
| 게이미피케이션 | 게이미피케이션 | 게이미피케이션 | gamification |
| ***판매** _ | _*_매출_*_ | _*_매출_*_ | _ *_Sales_** |
| 통신 판매 | telesales | telesales | telesales |
| CPQ(구성, 가격, 견적) | configure-price-quote | configure-price-quote | configure-price-quote |
| 계약 관리 | contract-management | contract-management | contract-management |
| CRM | crm | crm | crm |
| 전자 상거래 | e-commerce | e-commerce | e-commerce |
| 비즈니스 데이터 보강 | business-data-enrichment | business-data-enrichment | business-data-enrichment |
| 판매 활성화 지원 | sales-enablement | sales-enablement | sales-enablement |
| 기타 | other-sales | other-sales | other-sales |
| ***지리적 위치** _ | _*_geolocation_*_ | _*_geolocation_*_ | _ *_geolocation_** |
| 지도 | maps | maps | maps |
| 뉴스 및 날씨 | news-and-weather | news-and-weather | news-and-weather |
| 기타 | other-geolocation | other-geolocation | other-geolocation |
||||

### <a name="microsoft-appsource-industries"></a>Microsoft AppSource 산업

이러한 산업 및 해당 키는 SaaS, Power BI app, dynamics 365 business central, dynamics 365 for customer engagement 및 작업 제공 유형에 대 한 dynamics 365에 적용 됩니다. 굵은 기울임꼴 항목(예: ***자동차***)은 범주이며 표준 텍스트 항목(예: AutomotiveL2)은 하위 범주입니다. 공백 또는 대/소문자를 변경하지 말고 정확한 키 값을 사용합니다.

| 업종 | SaaS, Dynamics 365 business central, Dynamics 365 for customer engagement, Dynamics 365 for operation 키 | Power BI apps 키 |
| --- | --- | --- |
| ***자동차** _ | _*_자동차_*_ | _ *_automotive_** |
| 자동차 | AutomotiveL2 | AutomotiveL2 |
| ***농업** _ | _*_농업_*_ | _ *_agriculture_** |
| 기타 - 분류되지 않음 | Agriculture\_OtherUnsegmented | other-unsegmented |
| ***유통** _ | _*_배포_*_ | _ *_distribution_** |
| 도매 | 도매 | wholesale |
| 소포 및 택배 배달 | ParcelAndPackageShipping | parcel-and-package-shipping |
| ***교육** _ | _*_Education_*_ | _ *_education_** |
| 고등 교육 | HigherEducation | higher-education |
| 초등 및 중등 교육/K-12 | PrimaryAndSecondaryEducationK12 | primary-and-secondary-education |
| 도서관 및 박물관 | LibrariesAndMuseums | libraries-and-museums |
| ***금융 서비스** _ | _*_FinancialServices_*_ | _ *_financial-services_** |
| 뱅킹 및 자금 시장 | BankingAndCapitalMarkets | banking-and-capital-markets |
| 보험 | 보험 | 보험 |
| ***정부** _ | _*_정부_*_ | _ *_government_** |
| 군사 및 정보 | DefenseAndIntelligence | defense-and-intelligence |
| 공공 안전 및 사법 | PublicSafetyAndJustice | public-safety-and-justice |
| 민간 정부 | CivilianGovernment | civilian-government |
| ***의료** _ | _*_HealthCareandLifeSciences_*_ | _ *_healthcare_** |
| 의료 서비스 보험사 | HealthPayor | health-payor |
| 의료 서비스 제공자 | HealthProvider | health-provider |
| 제약 | 제약 | 제약 회사 |
| ***제조 및 자원** _ | _*_제조업_*_ | _ *_manufacturing-and-resources_** |
| 화학 및 농화학 | ChemicalAndAgrochemical | chemical-and-agrochemical |
| 조립 제조 | DiscreteManufacturing | discrete-manufacturing |
| 에너지 | 에너지 | energy |
| ***소매 및 소비재** _ | _*_RetailandConsumerGoods_*_ | _ *_retail-and-consumer-goods_** |
| 소비재 | ConsumerGoods | consumer-goods |
| 소매점 | 소매점 | retailers |
| ***미디어 및 커뮤니케이션** _ | _*_MediaAndCommunications_*_ | _ *_media-and-communications_** |
| 미디어 및 엔터테인먼트 | MediaandEntertainment | media-and-entertainment |
| 전자 통신 | 전자 통신 | telecommunications |
| ***전문 서비스** _ | _*_ProfessionalServices_*_ | _ *_professional-services_** |
| Legal | Legal | legal |
| 파트너 전문 서비스 | PartnerProfessionalServices | partner-professional-services |
| ***건축 및 설계** _ | _*_ArchitectureAndConstruction_*_ | _ *_architecture-and-construction_** |
| 기타 - 분류되지 않음 | ArchitectureAndConstruction\_OtherUnsegmented | other-unsegmented |
| ***숙박 및 여행** _ | _*_HospitalityandTravel_*_ | _ *_hospitality-and-travel_** |
|    호텔 및 레저 | HotelsAndLeisure | hotels-and-leisure |
| 여행 및 교통 | TravelAndTransportation | travel-and-transportation |
| 식당 및 식품 서비스 | RestaurantsAndFoodServices | restaurants-and-food-services |
| ***기타 공공 부문 산업** _ | _*_OtherPublicSectorIndustries_*_ | _ *_other-public-sector-industries_** |
| 임업 및 어업 | ForestryAndFishing | forestry-and-fishing |
| 비영리 | Nonprofits | nonprofits |
| ***부동산** _ | _*_RealEstate_*_ | _ *_real-estate_** |
| 기타 - 분류되지 않음 | RealEstate\_OtherUnsegmented | other-unsegmented |
|||
