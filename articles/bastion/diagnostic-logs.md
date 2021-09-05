---
title: Azure Bastion 리소스 로그 사용 및 작업
description: Azure Bastion 진단 로그를 사용하도록 설정하고 작업하는 방법을 알아봅니다.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: ba31710e13ef786f2e4ede68541f1a5884306669
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535843"
---
# <a name="enable-and-work-with-bastion-resource-logs"></a>Bastion 리소스 로그 사용 및 작업

사용자가 Azure Bastion을 사용하여 워크로드에 연결하면 Bastion에서 원격 세션 진단을 로깅할 수 있습니다. 그런 다음 진단을 사용하여 어떤 사용자가 어디에서 언제 어떤 워크로드에 연결되었는지 및 관련된 로깅 정보 등을 볼 수 있습니다. 진단을 사용하려면 Azure Bastion에서 진단 로그를 사용하도록 설정해야 합니다. 이 문서는 진단 로그를 사용하도록 설정하고 로그를 확인하는 데 도움이 됩니다.

## <a name="enable-the-resource-log"></a><a name="enable"></a>리소스 로그 사용

1. [Azure Portal](https://portal.azure.com)에서 Azure Bastion 리소스로 이동한 다음, Azure Bastion 페이지에서 **진단 설정** 을 선택합니다.

   ![“진단 설정” 페이지 스크린샷](./media/diagnostic-logs/1diagnostics-settings.png)
2. **진단 설정** 을 선택한 다음 **+ 진단 설정 추가** 를 선택하여 로그 대상을 추가합니다.

   ![“진단 설정 추가” 단추가 선택된 “진단 설정” 페이지 스크린샷](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. **진단 설정** 페이지에서 진단 로그를 저장하는 데 사용할 스토리지 계정 유형을 선택합니다.

   ![스토리지 위치 선택 섹션이 강조 표시된 “진단 설정” 페이지 스크린샷](./media/diagnostic-logs/3add-storage-account.png)
4. 설정을 완료하면 다음 예제와 같이 표시됩니다.

   ![예제 설정](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>진단 로그 보기

진단 로그에 액세스하려면 진단 설정을 사용하도록 설정하는 동안 지정한 스토리지 계정을 직접 사용할 수 있습니다.

1. 스토리지 계정 리소스, **컨테이너** 로 차례로 이동합니다. 스토리지 계정 Blob 컨테이너에 **insights-logs-bastionauditlogs** Blob이 생성된 것을 확인할 수 있습니다.

   ![진단 설정](./media/diagnostic-logs/1-navigate-to-logs.png)
2. 컨테이너 내부로 이동하면 Blob에 다양한 폴더가 표시됩니다. 해당 폴더는 Azure Bastion 리소스의 리소스 계층 구조를 나타냅니다.

   ![진단 설정 추가](./media/diagnostic-logs/2-resource-h.png)
3. 진단 로그를 액세스/확인하려는 Azure Bastion 리소스의 전체 계층 구조로 이동합니다. ‘y=’, ‘m=’, ‘d=’, ‘h=’, ‘m=’은 리소스 로그의 연도, 월, 일, 시간, 분을 각각 나타냅니다.

   ![스토리지 위치 선택](./media/diagnostic-logs/3-resource-location.png)
4. 탐색한 기간의 진단 로그 데이터가 포함된, Azure Bastion에서 만든 json 파일을 찾습니다.

5. 스토리지 Blob 컨테이너에서 json 파일을 다운로드합니다. 아래에는 json 파일의 예제 항목이 참조용으로 나와 있습니다.

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.
