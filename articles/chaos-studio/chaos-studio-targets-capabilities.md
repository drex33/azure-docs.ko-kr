---
title: Azure 비정상 스튜디오의 대상 및 기능
description: 대상 및 기능을 사용 하 여 Azure 비정상 스튜디오에서 리소스 온 보 딩을 제어 하는 방법을 이해 합니다.
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 74e3db42471fb4322a9c80cd63f30fee730d7a49
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371489"
---
# <a name="targets-and-capabilities-in-azure-chaos-studio"></a>Azure 비정상 스튜디오의 대상 및 기능

Azure 리소스에 대해 오류를 삽입 하려면 먼저 리소스에 해당 대상 및 기능이 활성화 되어 있어야 합니다. 대상 및 기능은 오류 주입에 사용 되는 리소스 및 해당 리소스에 대해 실행할 수 있는 오류를 제어 합니다. [다른 보안 측정값과 함께](chaos-studio-permissions-security.md) 대상과 기능을 사용 하면 비정상 스튜디오에서 실수로 인 한 오류 주입을 방지할 수 있습니다. 예를 들어 대상 및 기능을 사용 하면 kill process 오류가 실행 되지 않도록 하는 동시에 프로덕션 가상 컴퓨터에 대해 CPU 압력 오류를 실행 하도록 허용할 수 있습니다.

## <a name="targets"></a>대상

비정상 **대상** 은 비정상 스튜디오에서 특정 대상 유형에 대 한 리소스와 상호 작용할 수 있도록 합니다. **대상 형식은** 리소스에 대해 오류를 삽입 하는 방법을 나타냅니다. 서비스 직접 오류만 지 원하는 리소스 종류에는 대상 유형 (예: Azure Cosmos DB 형식)이 하나 있습니다 `Microsoft-CosmosDB` . 서비스 직접 및 에이전트 기반 오류를 지 원하는 리소스 종류에는 두 가지 대상 형식이 있습니다. 하나는 서비스 직접 오류 (예: `Microsoft-VirtualMachine` )이 고 다른 하나는 에이전트 기반 오류 (항상)입니다 `Microsoft-Agent` .

대상은 비정상 스튜디오 (예: 가상 머신 또는 네트워크 보안 그룹)에 등록 되는 리소스의 자식으로 만들어진 확장 리소스입니다. 대상은 리소스에 대해 사용 하도록 설정 된 대상 유형을 정의 합니다. 예를 들어이 리소스 ID를 사용 하 여 Cosmos DB 인스턴스를 온 보 딩 하는 경우:

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB
```

Cosmos DB 리소스는 다음과 같이 형식이 지정 된 자식 리소스를 가집니다.

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB/providers/Microsoft.Chaos/targets/Microsoft-CosmosDB
```

외부에서 만들어진 대상이 있는 리소스만 비정상 스튜디오의 오류 주입에 가능 됩니다.

## <a name="capabilities"></a>기능

**기능** 을 사용 하면 비정상 스튜디오에서 가상 컴퓨터를 종료 하는 등의 특정 오류를 리소스에 대해 실행할 수 있습니다. 기능은 대상 유형별로 고유 하며, 사용할 수 있는 오류를 나타냅니다 (예:) `CPUPressure-1.0` . 비정상 [스튜디오 오류 라이브러리를 방문](chaos-studio-fault-library.md) 하 여 사용 가능한 모든 오류 및 해당 기능 이름 및 대상 유형을 이해 하세요.

기능은 대상의 자식으로 만들어진 확장 리소스입니다. 예를 들어, 서비스 직접 대상 ID를 사용 하는 가상 머신에서 종료 오류를 사용 하도록 설정 하는 경우:

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine
```

대상 리소스의 자식 리소스는 다음과 같이 지정 됩니다.

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0
```

실험은 해당 기능이 활성화 된 상태에서 등록 대상에만 오류를 삽입할 수 있습니다. 

## <a name="listing-capability-names-and-parameters"></a>기능 이름 및 매개 변수 나열
참조를 위해 기능 이름, 오류 Urn 및 매개 변수 목록은 [오류 라이브러리에서](chaos-studio-fault-library.md)사용할 수 있지만 HTTP 응답을 사용 하 여 기능을 만들거나 기존 기능에 대해 get을 수행 하 여 요청 시이 정보를 가져올 수 있습니다. 예를 들어 VM 종료 기능에 대해 GET을 수행 합니다.

```bash
az rest --method get --url "https://management.azure.com/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0?api-version=2021-08-11-preview"
```

는 다음과 같은 JSON을 반환 합니다.

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

속성은 비정상 `properties.urn` 실험에서 실행 하려는 오류를 정의 하는 데 사용 됩니다. 이 오류의 매개 변수에 대 한 스키마를 이해 하기 위해에서 참조 하는 스키마를 가져올 수 있습니다 `properties.parametersSchema` .

```bash
az rest --method get --url "https://schema-tc.eastus.chaos-prod.azure.com/targetTypes/Microsoft-VirtualMachine/capabilityTypes/Shutdown-1.0/parametersSchema.json"
```

다음 JSON을 반환 합니다.
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
이제 대상 및 기능을 이해 했으므로 다음을 수행할 준비가 되었습니다.
- [오류 및 작업에 대 한 자세한 정보](chaos-studio-faults-actions.md)
- [첫 번째 실험 만들기 및 실행](chaos-studio-tutorial-service-direct-portal.md)
