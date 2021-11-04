---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 9727c1e2733bb3922a388e4408fde7dbe33db1f1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131571006"
---
Azure 리소스 관리자와 함께 템플릿을 배포할 때 지정하고자 하는 값으로 매개 변수를 정의합니다. 템플릿은 모든 매개 변수 값이 포함 된 매개 변수라는 섹션을 포함합니다. 배포하는 프로젝트에 따라 또는 환경에 따라 달라지는 이러한 값에 대한 매개 변수를 정의해야 합니다. 항상 동일하게 유지되는 값으로 매개 변수를 정의하지 마세요. 각 매개 변수 값은 배포되는 리소스를 정의하는 템플릿에 사용됩니다. 

매개 변수를 정의할 때는 **allowedValues** 필드를 사용하여 배포 중에 사용자가 제공할 수 있는 값을 지정합니다. 배포 중에 값이 제공되지 않으면 **defaultValue** 필드를 사용하여 매개 변수에 값을 할당합니다.

템플릿에서 각 매개 변수에 대해 설명할 것입니다.

### <a name="sitename"></a>siteName
만들려는 웹앱의 이름입니다.

```config
"siteName":{
  "type":"string"
}
```

### <a name="hostingplanname"></a>hostingPlanName
웹앱을 호스팅하는 데 사용하는 App Service 계획의 이름입니다.

```config
"hostingPlanName":{
  "type":"string"
}
```

### <a name="sku"></a>sku
호스팅 계획에 대한 가격 책정 계층입니다.

```config
"sku": {
  "type": "string",
  "allowedValues": [
    "F1",
    "D1",
    "B1",
    "B2",
    "B3",
    "S1",
    "S2",
    "S3",
    "P1",
    "P2",
    "P3",
    "P4"
  ],
  "defaultValue": "S1",
  "metadata": {
    "description": "The pricing tier for the hosting plan."
  }
}
```

이 매개 변수에 허용되는 값을 정의하고, 값이 지정되지 않은 경우에는 기본값(S1)을 할당하는 템플릿입니다.

### <a name="workersize"></a>workerSize
호스팅 계획의 인스턴스 크기입니다(소, 중, 대).

```config
"workerSize":{
  "type":"string",
  "allowedValues":[
    "0",
    "1",
    "2"
  ],
  "defaultValue":"0"
}
```

이 매개 변수에 허용되는 값(0, 1, 2)을 정의하고, 값이 지정되지 않은 경우에는 기본값(0)을 할당하는 템플릿입니다. 값은 작은 규모, 중간 규모 및 대규모에 해당합니다.
