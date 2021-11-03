---
title: 예약된 리소스 이름 오류
description: 예약어가 포함된 리소스 이름을 제공할 때 발생하는 오류를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102977"
---
# <a name="resolve-reserved-resource-name-errors"></a>예약된 리소스 이름 오류 해결

이 문서에서는 이름에 예약어가 포함된 리소스를 배포할 때 발생하는 오류에 대해 설명합니다.

## <a name="symptom"></a>증상

공용 엔드포인트를 통해 사용할 수 있는 리소스를 배포할 때 다음 오류가 표시될 수 있습니다.

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>원인

공용 엔드포인트가 있는 리소스의 이름에는 예약어 또는 상표를 사용할 수 없습니다.

예약어는 다음과 같습니다.

* ACCESS
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

전체 단어 또는 이름의 하위 문자열로 사용할 수 없는 단어는 다음과 같습니다.

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>솔루션

예약어 중 하나를 사용하지 않는 이름을 제공합니다.