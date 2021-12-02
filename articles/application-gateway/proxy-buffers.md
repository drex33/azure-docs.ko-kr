---
title: 요청 및 응답 버퍼 구성
description: Azure Application Gateway 대한 요청 및 응답 버퍼를 구성하는 방법을 알아봅니다.
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: how-to
ms.date: 12/01/2021
ms.author: jaysoni
ms.openlocfilehash: ab3301c35fdd8b39280db62417eebb40ace1ffdd
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133481996"
---
# <a name="configure-request-and-response-proxy-buffers"></a>요청 및 응답 프록시 버퍼 구성

Azure Application Gateway Standard v2 및 WAF v2 S SKU는 버퍼링 요청(클라이언트에서) 또는 응답(백 엔드 서버에서)을 지원합니다. Application Gateway 상호 작용하는 클라이언트의 처리 기능에 따라 이러한 버퍼를 사용하여 패킷 배달 속도를 구성할 수 있습니다.
 
## <a name="response-buffer"></a>응답 버퍼 

Application Gateway 응답 버퍼는 백 엔드 서버에서 보낸 응답 패킷의 전부 또는 일부를 수집한 후 클라이언트에 전달할 수 있습니다. 기본적으로 응답 버퍼링은 느린 클라이언트를 수용하는 데 유용한 Application Gateway 사용하도록 설정됩니다. 이 설정을 사용하면 Application Gateway 완전한 응답을 받고 클라이언트의 처리 속도에 따라 작동하면 백 엔드 TCP 연결을 닫을 수 있기 때문에 이를 절약할 수 있습니다. 이러한 방식으로 Application Gateway 클라이언트의 속도에 따라 응답을 계속 전달합니다. 

 
## <a name="request-buffer"></a>요청 버퍼 

이와 비슷한 방식으로 Application Gateway 요청 버퍼는 요청 본문의 전체 또는 일부를 임시로 저장한 다음 한 번에 더 큰 업로드 요청을 백 엔드 서버에 전달할 수 있습니다. 기본적으로 요청 버퍼링 설정은 Application Gateway 사용하도록 설정되며 백 엔드 서버에서 더 작은 데이터 패킷을 다시 어셈블하는 처리 함수를 오프로드하는 데 유용합니다.


>[!NOTE]
>기본적으로 요청 및 응답 버퍼는 모두 Application Gateway 리소스에서 사용하도록 설정되지만 별도로 구성할 수 있습니다. 또한 설정은 리소스 수준에서 적용되며 각 수신기에 대해 별도로 관리될 수 없습니다.
 
</br>

요구 사항 및/또는 Application Gateway 통신하는 클라이언트 시스템의 관찰된 성능에 따라 요청 또는 응답 버퍼를 사용하거나 사용하지 않도록 설정할 수 있습니다. 

</br>

> [!WARNING]
>프로덕션 게이트웨이에서 롤아웃하기 전에 성능을 테스트하고 평가하는 것이 좋습니다. 

## <a name="how-to-change-the-buffer-setting"></a>버퍼 설정을 변경하는 방법 

아래와 같이 ARM 템플릿에서 GlobalConfiguration을 사용하여 이 설정을 변경할 수 있습니다.

```json
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{      
   },
   "variables":{      
   },
   "resources":[
      {
         "type":"Microsoft.Network/applicationGateways",
         "apiVersion":"xxx-xx-xx",
         "name":"[parameters('applicationGateways_xxxx_x_xx_name')]",
         "location":"eastus",
         "tags":{            
         },
         "identity":{      
         },
         "properties":{
            "globalConfiguration":{
               "enableRequestBuffering":false,
               "enableResponseBuffering":false
            }
         }
      }
   ]
} 
```
참조는 [.NET용 Azure SDK를](/dotnet/api/microsoft.azure.management.network.models.applicationgatewayglobalconfiguration) 참조하세요.
