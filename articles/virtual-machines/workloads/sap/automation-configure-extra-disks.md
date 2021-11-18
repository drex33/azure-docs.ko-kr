---
title: SAP deployment automation 구성에 디스크 추가
description: Azure의 SAP 배포 자동화 프레임 워크에서 시스템에 대 한 더 많은 디스크를 구성 합니다. 새 시스템 또는 기존 시스템에 추가 디스크를 추가 합니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 5b3f900de7c9e03ddf5dda8001ce5932cc7fbaac
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730455"
---
# <a name="change-the-disk-configuration-for-the-sap-deployment-automation"></a>SAP 배포 자동화에 대 한 디스크 구성 변경

기본적으로 [Azure의 sap 배포 자동화 프레임 워크](automation-deployment-framework.md) 는 sap 시스템에 대 한 디스크 구성을 정의 합니다. 필요에 따라 사용자 지정 디스크 구성 json 파일을 제공 하 여 기본 구성을 변경할 수 있습니다.

> [!TIP]
> 가능 하면 디스크를 더 추가 하는 대신 디스크 크기를 늘리는 것이 가장 좋습니다.


### <a name="hana-databases"></a>HANA 데이터베이스

다음 표에서는 HANA 시스템의 기본 디스크 구성을 보여 줍니다. 

| 크기      | VM SKU              | OS 디스크       | 데이터 디스크       | 로그 디스크        | Hana 공유    | 사용자 SAP     | Backup          |
|-----------|---------------------|---------------|------------------|------------------|----------------|--------------|-----------------|
| 기본값   | Standard_D8s_v3     | E6 (64 G B)    | P20 (512 G B)     | P20 (512 G B)     | E20 (512 G B)   | E6 (64 G B)   | E20 (512 G B)    |  
| S4DEMO    | Standard_E32ds_v4   | P10 (128 G B)  | 4 P10 (128 G B)   | 3 P10 (128 G B)   |                | P20 (512 G B) | P20 (512 G B)    |
| M32ts     | Standard_M32ts      | P6 (64 G B)    | 4 P6 (64 G B)     | 3 P10 (128 G B)   | P20 (512 G B)   | P6 (64 G B)   | P20 (512 G B)    |
| M32ls     | Standard_M32ls      | P6 (64 G B)    | 4 P6 (64 G B)     | 3 P10 (128 G B)   | P20 (512 G B)   | P6 (64 G B)   | P20 (512 G B)    |
| M64ls     | Standard_M64ls      | P6 (64 G B)    | 4 P10 (128 G B)   | 3 P10 (128 G B)   | P20 (512 G B)   | P6 (64 G B)   | P30 (1024 G B)   |
| M64s      | Standard_M64s       | P10 (128 G B)  | 4 P15 (256 G B)   | 3 P15 (256 G B)   | P30 (1024 G B)  | P6 (64 G B)   | P30 (1024 G B)   |
| M64ms     | Standard_M64ms      | P6 (64 G B)    | 4 P20 (512 G B)   | 3 P15 (256 G B)   | P30 (1024 G B)  | P6 (64 G B)   | 2 P30 (1024 G B) |
| M128s     | Standard_M128s      | P10 (128 G B)  | 4 P20 (512 G B)   | 3 P15 (256 G B)   | P30 (1024 G B)  | P6 (64 G B)   | 2 P30 (1024 G B) |
| M128ms    | Standard_M128m      | P10 (128 G B)  | 4 P30 (1024 G B)  | 3 P15 (256 G B)   | P30 (1024 G B)  | P6 (64 G B)   | 4 P30 (1024 G B) |
| M208s_v2  | Standard_M208s_v2   | P10 (128 G B)  | 4 P30 (1024 G B)  | 3 P15 (256 G B)   | P30 (1024 G B)  | P6 (64 G B)   | 3 P40 (2048 G B) |
| M208ms_v2 | Standard_M208ms_v2  | P10 (128 G B)  | 4 P40 (2048 G B)  | 3 P15 (256 G B)   | P30 (1024 G B)  | P6 (64 G B)   | 3 P40 (2048 G B) |
| M416s_v2  | Standard_M416s_v2   | P10 (128 G B)  | 4 P40 (2048 G B)  | 3 P15 (256 G B)   | P30 (1024 G B)  | P6 (64 G B)   | 3 P40 (2048 G B) |
| M416ms_v2 | Standard_M416m_v2   | P10 (128 G B)  | 4 P50 (4096 G B)  | 3 P15 (256 G B)   | P30 (1024 G B)  | P6 (64 G B)   | 4 P50 (4096 G B) |

### <a name="anydb-databases"></a>AnyDB 데이터베이스

다음 표에서는 AnyDB 시스템의 기본 디스크 구성을 보여 줍니다. 

| 크기    | VM SKU           | OS 디스크     | 데이터 디스크       | 로그 디스크       |
|---------|------------------|-------------|------------------|-----------------|
| 기본값 | Standard_E4s_v3  | P6 (64 G B)  | P15 (256 G B)     | P10 (128 G B)    |
| 200GB  | Standard_E4s_v3  | P6 (64 G B)  | P15 (256 G B)     | P10 (128 G B)    |
| 500GB  | Standard_E8s_v3  | P6 (64 G B)  | P20 (512 G B)     | P15 (256 G B)    |
| 1TB  | Standard_E16s_v3 | P10 (128GB) | 2 P20 (512 G B)   | 2 P15 (256 G B)  |
| 2TB  | Standard_E32s_v3 | P10 (128GB) | 2 P30 (1024 G B)  | 2 P20 (512 G B)  |
| 5TB  | Standard_M64ls   | P10 (128GB) | 5 P30 (1024 G B)  | 2 P20 (512 G B)  |
| 10TB  | Standard_M64s    | P10 (128GB) | 5 P40 (2048 G B)  | 2 P20 (512 G B)  |
| 15tb  | Standard_M64s    | P10 (128GB) | 4 P50 (4096 G B)  | 2 P20 (512 G B)  |
| 20tb  | Standard_M64s    | P10 (128GB) | 5 P50 (4096 G B)  | 2 P20 (512 G B)  |
| 30tb  | Standard_M128s   | P10 (128GB) | 8 P50 (4096 G B)  | 2 P40 (2048 G B) |
| 40 TB  | Standard_M128s   | P10 (128GB) | 10 P50 (4096 G B) | 2 P40 (2048 G B) |
| 50 TB  | Standard_M128s   | P10 (128GB) | 13 P50 (4096 G B) | 2 P40 (2048 G B) |


## <a name="custom-sizing-file"></a>사용자 지정 크기 조정 파일

사용자 지정 크기 조정 파일을 사용 하 여 SAP 시스템의 디스크 크기를 정의할 수 있습니다. 

아래 표시 된 구조를 사용 하 여 파일을 만들고 파일을 시스템에 대 한 매개 변수 파일과 동일한 폴더에 저장 합니다 (예를 들어, ' XO1_db_sizes. json ').  그런 다음 `db_disk_sizes_filename` 데이터베이스 계층에 대 한 매개 변수 파일에서 매개 변수를 정의 합니다. 예들 들어 `db_disk_sizes_filename = "XO1_db_sizes.json"`입니다.

다음 예제 코드는 데이터베이스 계층에 대 한 구성의 예입니다. 3 개의 데이터 디스크 (Lun 0, 1 및 2), 로그 디스크 (LSKU 사용) 및 백업 디스크 (LUN 13, 표준 SSDN SKU 사용)를 정의 합니다.

```json
{
  "db" : {
    "Default": {
      "compute": {
        "vm_size"                 : "Standard_D4s_v3",
        "swap_size_gb"            : 2
      },
      "storage": [
        {
          "name"                  : "os",
          "count"                 : 1,
          "disk_type"             : "Premium_LRS",
          "size_gb"               : 128,
          "caching"               : "ReadWrite"
        },
        {
          "name"                  : "data",
          "count"                 : 3,
          "disk_type"             : "Premium_LRS",
          "size_gb"               : 256,
          "caching"               : "ReadWrite",
          "write_accelerator"     : false,
          "start_lun"             : 0
        },
        {
          "name"                  : "log",
          "count"                 : 1,
          "disk_type"             : "UltraSSD_LRS",
          "size_gb": 512,
          "disk-iops-read-write"  : 2048,
          "disk-mbps-read-write"  : 8,
          "caching"               : "None",
          "write_accelerator"     : false,
          "start_lun"             : 9
        },
        {
          "name"                  : "backup",
          "count"                 : 1,
          "disk_type"             : "StandardSSD_LRS",
          "size_gb"               : 256,
          "caching"               : "ReadWrite",
          "write_accelerator"     : false,
          "start_lun"             : 13
        }

      ]
    }
  }
}
```

## <a name="add-extra-disks-to-existing-system"></a>기존 시스템에 추가 디스크 추가

이미 배포 된 시스템에 디스크를 추가 해야 하는 경우 JSON 구조에 새 블록을 추가할 수 있습니다. 이 블록에 특성을 포함 하 `append` 고 값을로 설정 `true` 합니다. 예를 들어 다음 샘플 코드에서 마지막 블록에는 특성이 포함 되어 있습니다 `"append" : true,` . 마지막 블록은 코드의 첫 번째 블록에 이미 구성 된 새 디스크를 데이터베이스 계층에 추가 합니다 `"data"` .

```json
{
  "db" : {
    "Default": {
      "compute": {
        "vm_size"                 : "Standard_D4s_v3",
        "swap_size_gb"            : 2
      },
      "storage": [
        {
          "name"                  : "os",
          "count"                 : 1,
          "disk_type"             : "Premium_LRS",
          "size_gb"               : 128,
          "caching"               : "ReadWrite"
        },
        {
          "name"                  : "data",
          "count"                 : 3,
          "disk_type"             : "Premium_LRS",
          "size_gb"               : 256,
          "caching"               : "ReadWrite",
          "write_accelerator"     : false,
          "start_lun"             : 0
        },
        {
          "name"                  : "log",
          "count"                 : 1,
          "disk_type"             : "UltraSSD_LRS",
          "size_gb": 512,
          "disk-iops-read-write"  : 2048,
          "disk-mbps-read-write"  : 8,
          "caching"               : "None",
          "write_accelerator"     : false,
          "start_lun"             : 9
        },
        {
          "name"                  : "backup",
          "count"                 : 1,
          "disk_type"             : "StandardSSD_LRS",
          "size_gb"               : 256,
          "caching"               : "ReadWrite",
          "write_accelerator"     : false,
          "start_lun"             : 13
        }
        ,
        {
          "name"                  : "data",
          "count"                 : 1,
          "disk_type"             : "Premium_LRS",
          "size_gb"               : 256,
          "caching"               : "ReadWrite",
          "write_accelerator"     : false,
          "append"                : true,
          "start_lun"             : 4
        }

      ]
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 명명 모듈 구성](automation-naming-module.md)

