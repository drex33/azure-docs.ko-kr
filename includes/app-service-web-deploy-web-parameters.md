---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: af26bc94e96c9d4bd1fdf59d5479bdc7181221f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298522"
---
Azure 리소스 관리자와 함께 템플릿을 배포할 때 지정하고자 하는 값으로 매개 변수를 정의합니다. 템플릿은 모든 매개 변수 값이 포함 된 매개 변수라는 섹션을 포함합니다. 배포 하는 프로젝트 또는 배포 중인 환경에 따라 달라 지는 값에 대 한 매개 변수를 정의 해야 합니다. 상수 값에 대 한 매개 변수를 정의 하지 마십시오. 각 매개 변수 값은 배포되는 리소스를 정의하는 템플릿에 사용됩니다.

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

```json
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

템플릿은이 매개 변수에 허용 되는 값을 정의 하 고, `S1` 값이 지정 되지 않은 경우 기본값을 할당 합니다.

### <a name="workersize"></a>workerSize

호스팅 계획의 인스턴스 크기입니다(소, 중, 대).

```json
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

템플릿은이 매개 변수에 허용 되는 값 ( `0` , `1` 또는 `2` )을 정의 하 고, `0` 값이 지정 되지 않은 경우에는 기본값을 할당 합니다. 값은 small, medium 및 large에 해당 합니다.
