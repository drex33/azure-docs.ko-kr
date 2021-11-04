---
title: 예약된 리소스 이름 오류
description: 예약어가 포함된 리소스 이름을 제공할 때 발생하는 오류를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: 28376ed34e2007c3f6cfb7f57f08199e5a1ade8a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458839"
---
# <a name="resolve-reserved-resource-name-errors"></a>예약된 리소스 이름 오류 해결

이 문서에서는 이름에 예약어를 포함하는 리소스를 배포할 때 발생할 수 있는 오류에 대해 설명합니다.

## <a name="symptom"></a>증상

공용 엔드포인트를 통해 사용할 수 있는 리소스를 배포할 때 다음 오류가 표시될 수 있습니다.

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>원인

퍼블릭 엔드포인트가 있는 리소스는 이름에 예약어 또는 상표를 사용할 수 없습니다.

예약어는 다음과 같습니다.

* ACCESS
* APP_CODE
* APP_THEMES
* APP_DATA
* APP_GLOBALRESOURCES
* APP_LOCALRESOURCES
* APP_WEBREFERENCES
* APP_BROWSERS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

다음 단어는 이름에 전체 단어 또는 부분 단어로 사용할 수 없습니다.

* MICROSOFT
* WINDOWS

## <a name="solution"></a>솔루션

예약어 중 하나를 사용하지 않는 이름을 제공합니다.
