---
title: 만들기 또는 업데이트 작업에 대한 PUT 호출
description: 컴퓨팅 리소스의 만들기 또는 업데이트 작업에 대한 PUT 호출
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: 07d22c4535bc10012ea5df6cfb222f5a53eb0a81
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697288"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>컴퓨팅 리소스의 만들기 또는 업데이트에 대한 PUT 호출

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합


`Microsoft.Compute` 리소스는 *HTTP PUT* 의미 체계의 기본 정의를 지원하지 않습니다. 대신 이러한 리소스는 PUT 및 PATCH 동사 모두에 대해 PATCH 의미 체계를 사용합니다.

**만들기** 작업은 해당하는 경우 기본값을 적용합니다. 그러나 PUT 또는 PATCH를 통해 수행된 리소스 **업데이트** 는 기본 속성을 적용하지 않습니다. **업데이트** 작업은 엄격한 PATCH 의미 체계를 적용합니다.

예를 들어, 가상 머신의 디스크 `caching` 속성은 리소스가 OS 디스크인 경우 기본적으로 `ReadWrite`입니다.

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

그러나 **업데이트** 작업의 경우 속성이 제외되거나 *null* 값이 전달되면 변경되지 않은 상태로 유지되며 기본값은 없습니다.

이러한 특성은 연결 제거의 목적으로 업데이트 작업을 리소스로 보내는 경우에 중요합니다. 해당 리소스가 `Microsoft.Compute` 리소스인 경우 제거할 해당 속성을 명시적으로 호출하고 값을 할당해야 합니다. 이를 위해 사용자는 **" "** 와 같은 빈 문자열을 전달할 수 있습니다. 이 경우 해당 연결을 제거하도록 플랫폼에 지시됩니다.

> [!IMPORTANT]
> 배열 요소 "패치"를 지원하지 않습니다. 대신 클라이언트는 업데이트된 배열의 전체 콘텐츠를 사용하여 PUT 또는 PATCH 요청을 수행해야 합니다. 예를 들어, VM에서 데이터 디스크를 분리하려면 GET 요청을 수행하여 현재 VM 모델을 가져오고, `properties.storageProfile.dataDisks`에서 분리할 디스크를 제거하고, 업데이트된 VM 엔터티를 사용하여 PUT 요청을 수행합니다.

## <a name="examples"></a>예제

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>근접 배치 그룹 연결을 제거하기 위한 올바른 페이로드

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>근접 배치 그룹 연결을 제거하기 위한 잘못된 페이로드

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>다음 단계
[Virtual Machines](/rest/api/compute/virtualmachines/createorupdate) 및 [Virtual Machine Scale Sets](/rest/api/compute/virtualmachinescalesets/createorupdate)에 대한 만들기 또는 업데이트 호출에 대해 자세히 알아보기