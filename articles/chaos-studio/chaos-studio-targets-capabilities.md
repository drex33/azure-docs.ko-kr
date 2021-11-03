---
title: Azure Chaos Studio의 대상 및 기능
description: 대상 및 기능을 사용하여 Azure Chaos Studio에서 리소스 온보딩을 제어하는 방법을 이해합니다.
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: cccfcfa60ed0d527fe3bd0dac9db0a6206a95c2f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102760"
---
# <a name="targets-and-capabilities-in-azure-chaos-studio"></a>Azure Chaos Studio의 대상 및 기능

Azure 리소스에 오류를 삽입하려면 먼저 해당 대상 및 기능을 사용하도록 설정해야 합니다. 대상 및 기능은 오류 주입에 사용할 수 있는 리소스와 해당 리소스에 대해 실행할 수 있는 오류를 제어합니다. [다른 보안 조치와 함께](chaos-studio-permissions-security.md) 대상 및 기능을 사용하면 Chaos Studio에서 실수로 또는 악의적인 오류 주입을 방지할 수 있습니다. 예를 들어 대상 및 기능을 사용하면 프로덕션 가상 머신에 대해 CPU 압력 오류를 실행하도록 허용하면서 kill process 오류가 실행되지 않도록 할 수 있습니다.

## <a name="targets"></a>대상

**비정상** 상황에서는 Chaos Studio가 특정 대상 유형에 대한 리소스와 상호 작용할 수 있습니다. **대상 형식은** 리소스에 대해 오류를 삽입하는 방법을 나타냅니다. 서비스 직접 오류만 지원하는 리소스 종류에는 하나의 대상 유형(예: `Microsoft-CosmosDB` Azure Cosmos DB의 유형)이 있습니다. 서비스 다이렉트 및 에이전트 기반 오류를 지원하는 리소스 종류에는 서비스 직접 오류(예: `Microsoft-VirtualMachine` )와 에이전트 기반 오류(항상 )의 두 가지 대상 유형이 `Microsoft-Agent` 있습니다.

대상은 Chaos Studio에 온보딩되는 리소스의 자식으로 만든 확장 리소스입니다(예: Virtual Machine 또는 네트워크 보안 그룹). 대상은 리소스에서 사용하도록 설정된 대상 유형을 정의합니다. 예를 들어 이 리소스 ID를 Cosmos DB 인스턴스를 온보딩하는 경우:

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB
```

Cosmos DB 리소스에는 다음과 같은 형식의 자식 리소스가 있습니다.

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB/providers/Microsoft.Chaos/targets/Microsoft-CosmosDB
```

대상이 생성된 리소스만 Chaos Studio를 통해 오류 주입을 대상으로 할 수 있습니다.

## <a name="capabilities"></a>기능

**기능을** 사용하면 Chaos Studio에서 가상 머신 종료와 같은 리소스에 대해 특정 오류를 실행할 수 있습니다. 기능은 대상 유형별로 고유하며 사용하도록 설정하는 오류를 나타냅니다(예: `CPUPressure-1.0` ). [Chaos Studio 오류 라이브러리를 방문하여](chaos-studio-fault-library.md) 사용 가능한 모든 오류와 해당 기능 이름 및 대상 유형을 이해합니다.

기능은 대상의 자식으로 만든 확장 리소스입니다. 예를 들어 서비스 직접 대상 ID를 사용하여 Virtual Machine에서 종료 오류를 사용하도록 설정하는 경우:

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine
```

대상 리소스에는 다음과 같은 형식의 자식 리소스가 있습니다.

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0
```

실험은 해당 기능을 사용하도록 설정된 온보딩된 대상에만 오류를 삽입할 수 있습니다. 

## <a name="listing-capability-names-and-parameters"></a>기능 이름 및 매개 변수 나열
참고로 기능 이름, 오류 URL 및 매개 변수 목록은 [오류 라이브러리 에서](chaos-studio-fault-library.md)사용할 수 있지만 HTTP 응답을 사용하여 기능을 만들거나 기존 기능에서 GET을 수행하여 요청 시 이 정보를 얻을 수 있습니다. 예를 들어 VM 종료 기능에서 GET을 수행합니다.

```bash
az rest --method get --url "https://management.azure.com/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0?api-version=2021-08-11-preview"
```

다음 JSON을 반환합니다.

```JSON
{
  "id": "/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0",
  "name": "shutdown-1.0",
  "properties": {
    "description": null,
    "name": "shutdown-1.0",
    "parametersSchema": "https://schema-tc.eastus.chaos-prod.azure.com/targetTypes/Microsoft-VirtualMachine/capabilityTypes/Shutdown-1.0/parametersSchema.json",
    "publisher": "Microsoft",
    "targetType": "VirtualMachine",
    "type": "shutdown",
    "urn": "urn:csci:microsoft:virtualMachine:shutdown/1.0",
    "version": "1.0"
  },
  "resourceGroup": "myRG",
  "systemData": {
    "createdAt": "2021-09-15T23:00:00.826575+00:00",
    "lastModifiedAt": "2021-09-15T23:00:00.826575+00:00"
  },
  "type": "Microsoft.Chaos/targets/capabilities"
}
```

`properties.urn`속성은 비정형 실험에서 실행하려는 오류를 정의하는 데 사용됩니다. 이 오류의 매개 변수에 대한 스키마를 이해하려면 에서 참조하는 스키마를 얻을 수 `properties.parametersSchema` 있습니다.

```bash
az rest --method get --url "https://schema-tc.eastus.chaos-prod.azure.com/targetTypes/Microsoft-VirtualMachine/capabilityTypes/Shutdown-1.0/parametersSchema.json"
```

는 다음 JSON을 반환합니다.
```JSON
{
  "$schema": "https://json-schema.org/draft-07/schema",
  "properties": {
    "abruptShutdown": {
      "type": "boolean"
    },
    "restartWhenComplete": {
      "type": "boolean"
    }
  },
  "type": "object"
}
```

## <a name="next-steps"></a>다음 단계
이제 대상과 기능을 이해하게 됐으므로 다음을 수행할 준비가 되었습니다.
- [오류 및 작업에 대해 알아보기](chaos-studio-faults-actions.md)
- [첫 번째 실험 만들기 및 실행](chaos-studio-tutorial-service-direct.md)
