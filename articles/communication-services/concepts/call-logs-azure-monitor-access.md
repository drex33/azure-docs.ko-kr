---
title: Azure Communication Services - 호출 요약 및 호출 진단 로그 사용 및 액세스
titleSuffix: An Azure Communication Services concept document
description: Azure Monitor에서 호출 요약 및 호출 진단 로그에 액세스하는 방법
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 07/22/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: b5c2da9842ba21b63c7b36d5b7377f74a25a1e90
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672280"
---
# <a name="enable-and-access-call-summary-and-call-diagnostic-logs"></a>호출 요약 및 호출 진단 로그 사용 및 액세스

[!INCLUDE [Private Preview Notice](../includes/private-preview-include.md)]

Azure Communication Services Voice 및 Video 리소스에 대한 원격 분석 데이터에 액세스하려면 다음 단계를 수행합니다.

## <a name="enable-logging"></a>로깅 사용
1. 먼저 로그에 대한 스토리지 계정을 만들어야 합니다. 이 단계를 완료하기 위한 지침은 [스토리지 계정 만들기](../../storage/common/storage-account-create.md?tabs=azure-portal)로 이동합니다. 또한 다른 스토리지 옵션의 유형 및 기능에 대한 더 많은 정보는 [스토리지 계정 개요](../../storage/common/storage-account-overview.md)를 참조하세요. Azure 스토리지 계정이 이미 있는 경우 2단계로 이동합니다.
 
1. 스토리지 계정을 만들 때 [리소스에서 진단 로그 사용](./logging-and-diagnostics.md#enable-diagnostic-logs-in-your-resource)의 지침에 따라 로깅을 사용해야 합니다. "CallSummaryPRIVATEPREVIEW" 및 "CallDiagnosticPRIVATEPREVIEW" 로그에 대한 확인란을 선택합니다. 

1. 다음으로 "스토리지 계정에 보관" 상자를 선택한 다음, 아래 드롭다운 메뉴에서 로그에 대한 스토리지 계정을 선택합니다. "분석 작업 영역으로 보내기" 옵션은 현재 이 기능의 프라이빗 미리 보기에 사용할 수 없지만 이 기능이 공개되면 사용할 수 있습니다.

:::image type="content" source="media\call-logs-images\call-logs-access-diagnostic-setting.png" alt-text="Azure Monitor 진단 설정":::



## <a name="access-your-logs"></a>로그에 액세스

로그에 액세스하려면 Azure Portal에서 [Storage 계정](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)으로 이동하여 위의 3단계에서 지정한 스토리지 계정으로 이동합니다. 

:::image type="content" source="media\call-logs-images\call-logs-access-storage.png" alt-text="Azure Portal Storage":::

여기에서 모든 로그 또는 개별 로그를 다운로드할 수 있습니다.

:::image type="content" source="media\call-logs-images\call-logs-access-storage-resource.png" alt-text="Azure Portal Storage 다운로드":::

## <a name="next-steps"></a>다음 단계

- [로깅 및 진단](./logging-and-diagnostics.md)에 대해 자세히 알아보세요.