---
title: 변경 피드를 사용하여 DICOM 변경 내용 끌어오기
description: 이 방법 가이드에서는 Azure Healthcare API용 DICOM 변경 피드를 사용하여 DICOM 변경 내용을 끌어오는 방법을 설명합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: fa0237a57f5ebb8df0a69fa715a36d963ea0748f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122434697"
---
# <a name="pull-dicom-changes-using-the-change-feed"></a>변경 피드를 사용하여 DICOM 변경 내용 끌어오기

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

DICOM 변경 피드는 고객에게 DICOM 서비스의 기록을 거치고 서비스에서 이벤트 만들기 및 삭제에 대한 조치를 할 수 있는 기능을 제공합니다. 이 방법 가이드에서는 변경 피드를 사용하는 방법을 설명합니다.

변경 피드는 REST API를 사용하여 액세스됩니다. 변경 피드의 샘플 사용과 함께 이러한 API는 [DICOM 변경 피드 개요에](dicom-change-feed-overview.md)설명되어 있습니다. REST API 버전은 [DICOM 서비스 설명서에 대한 API 버전 지정에서](api-versioning-dicom-service.md)설명한 대로 요청 URL에 명시적으로 지정해야 합니다.

## <a name="consume-change-feed"></a>변경 피드 사용

다음 C# 코드 예제에서는 DICOM 클라이언트 패키지를 사용하여 변경 피드를 사용하는 방법을 보여줍니다.

```csharp
const int limit = 10;
 
using HttpClient httpClient = new HttpClient { BaseAddress = new Uri("<URL>") };
using CancellationTokenSource tokenSource = new CancellationTokenSource();
 
int read;
List<ChangeFeedEntry> entries = new List<ChangeFeedEntry>();
DicomWebClient client = new DicomWebClient(httpClient);
do
{
    read = 0;
    DicomWebAsyncEnumerableResponse<ChangeFeedEntry> result = await client.GetChangeFeed(
        $"?offset={entries.Count}&limit={limit}&includeMetadata={true}",
        tokenSource.Token);
 
    await foreach (ChangeFeedEntry entry in result)
    {
        read++;
        entries.Add(entry);
    }
} while (read > 0);
```

**ChangeFeedRetrieveService.cs** 코드 예제를 보고 액세스하려면 [변경 피드 사용](https://github.com/microsoft/dicom-server/blob/main/converter/dicom-cast/src/Microsoft.Health.DicomCast.Core/Features/DicomWeb/Service/ChangeFeedRetrieveService.cs)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 변경 피드를 사용하는 방법을 설명합니다. 변경 피드를 사용하면 DICOM 서비스의 기록을 모니터링할 수 있습니다. DICOM 서비스에 대한 자세한 내용은 다음을 참조하세요.

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)
