---
title: 비정상 스튜디오 오류 및 동작 라이브러리
description: 모든 필수 구성 요소 및 매개 변수를 포함 하 여 비정상 스튜디오에서 사용할 수 있는 작업을 이해 합니다.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 80d399ecb2b3d73b8014e3eebf8dbb8ea1de0436
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053089"
---
# <a name="chaos-studio-fault-and-action-library"></a>비정상 스튜디오 오류 및 동작 라이브러리

현재 사용할 수 있는 오류는 다음과 같습니다. [오류 공급자](./chaos-studio-fault-providers.md) 페이지를 방문 하 여 지원 되는 리소스 종류를 파악 합니다.

## <a name="time-delay"></a>지연 시간

| 속성 | 값 |
|-|-|
| 오류 공급자 | 해당 없음 |
| 지원 되는 OS 유형 | 해당 없음 |
| 설명 | 다른 작업 전후에 지연 시간을 추가 합니다. 오류가 서비스에 나타나거나 실험 외부의 활동이 완료 되기를 기다리는 데 유용 합니다 (예: 다른 오류를 삽입 하기 전에 autohealing가 발생 하기를 기다리는 경우). |
| 사전 요구 사항 | 해당 없음 |
| Urn | urn: 공급자: Azure-chaosStudio:. |
| duration | ISO 8601 형식의 지연 시간 (예: PT10M) |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [ 
    {
      "type": "delay",
      "name": "urn:provider:Azure-chaosStudio:Microsoft.Azure.Chaos.Delay.Timed",
      "duration": "PT10M"
    }
  ] 
}
```

## <a name="cpu-pressure"></a>CPU 압력

| 속성 | 값 |
|-|-|
| 기능 이름 | Cpupress-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원 되는 OS 유형 | Windows, Linux |
| 설명 | 오류 동작 기간 동안이 오류가 삽입 되는 VM의 지정 된 값까지 CPU 압력을 추가 합니다. 인공 CPU 압력은 기간이 종료 되거나 실험이 취소 되 면 제거 됩니다. |
| 사전 요구 사항 | **Linux:** Linux VM에서 오류를 실행 하려면 **스트레스 기능** 유틸리티를 설치 해야 합니다. Linux 배포판 패키지 관리자를 사용 하 여 설치할 수 있습니다. </br> 스트레스를 설치 하는 APT 명령: *sudo APT-업데이트 && sudo APT-y 압축 압축 풀기 && sudo APT-y 설치 스트레스를* 가져옵니다. </br> 스트레스를 설치 하는 YUM 명령: *sudo disjunctive normal form-y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo YUM-y 설치 스트레스*  |
| | **Windows:** 없음을. |
| Urn | urn: csci: microsoft: agent: Cpupress/1.0 |
| 매개 변수 (키, 값)  |
| pressureLevel | VM에 적용 되는 CPU 압력 (%)을 나타내는 1에서 99 사이의 정수입니다. |
| virtualMachineScaleSetInstances | 필드 가상 머신 확장 집합에이 오류를 적용 하는 경우 인스턴스 Id의 배열입니다. |

### <a name="sample-json"></a>JSON 샘플
```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="notes"></a>참고
Linux의 알려진 문제:
1. AzureChaosAgent가 예기치 않게 종료 되 면 스트레스 효과가 제대로 종료 되지 않을 수 있습니다.
2. Linux CPU 오류는 Ubuntu 16.04-LTS 및 Ubuntu 18.04-LTS 에서만 테스트 됩니다.

## <a name="physical-memory-pressure"></a>실제 메모리 부족

| 속성 | 값 |
|-|-|
| 기능 이름 | PhysicalMemoryPressure-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원 되는 OS 유형 | Windows, Linux |
| 설명 | 오류 동작 기간 동안이 오류가 삽입 되는 VM의 지정 된 값까지 실제 메모리 압력을 추가 합니다. 이 기간이 종료 되거나 실험이 취소 되 면 인공 물리적 메모리 압력이 제거 됩니다. |
| 사전 요구 사항 | **Linux:** Linux VM에서 오류를 실행 하려면 **스트레스 기능** 유틸리티를 설치 해야 합니다. Linux 배포판 패키지 관리자를 사용 하 여 설치할 수 있습니다. </br> 스트레스를 설치 하는 APT 명령: *sudo APT-업데이트 && sudo APT-y 압축 압축 풀기 && sudo APT-y 설치 스트레스를* 가져옵니다. </br> 스트레스를 설치 하는 YUM 명령: *sudo disjunctive normal form-y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo YUM-y 설치 스트레스*  |
| | **Windows:** 없음을. |
| Urn | urn: csci: microsoft: agent: physicalMemoryPressure/1.0 |
| 매개 변수 (키, 값) |  |
| pressureLevel | VM에 적용 되는 실제 메모리 압력 (%)의 양을 나타내는 1에서 99 사이의 정수입니다. |
| virtualMachineScaleSetInstances | 필드 가상 머신 확장 집합에이 오류를 적용 하는 경우 인스턴스 Id의 배열입니다. |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:physicalMemoryPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="virtual-memory-pressure"></a>가상 메모리 압력

| 속성 | 값 |
|-|-|
| 기능 이름 | VirtualMemoryPressure-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원 되는 OS 유형 | Windows |
| 설명 | 오류 동작 기간 동안이 오류가 주입 되는 VM의 지정 된 값까지 가상 메모리 압력을 추가 합니다. 인공 가상 메모리 압력은 기간이 종료 되거나 실험이 취소 되 면 제거 됩니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn: csci: microsoft: agent: virtualMemoryPressure/1.0 |
| 매개 변수 (키, 값) |  |
| pressureLevel | VM에 적용 되는 실제 메모리 압력 (%)의 양을 나타내는 1에서 99 사이의 정수입니다. |
| virtualMachineScaleSetInstances | 필드 가상 머신 확장 집합에이 오류를 적용 하는 경우 인스턴스 Id의 배열입니다. |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:virtualMemoryPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="disk-io-pressure-windows"></a>디스크 I/O 압력(Windows)

| 속성 | 값 |
|-|-|
| 기능 이름 | DiskIOPressure-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원되는 OS 유형 | Windows |
| 설명 | [diskspd 유틸리티를](https://github.com/Microsoft/diskspd/wiki) 사용하여 오류 작업 기간 동안 삽입되는 VM의 기본 스토리지에 디스크 압력을 추가합니다. 이 오류에는 5가지 실행 모드가 있습니다. 인공 디스크 압력은 기간이 끝날 때 또는 실험이 취소된 경우 제거됩니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn:csci:microsoft:agent:diskIOPressure/1.0 |
| 매개 변수(키, 값) |  |
| pressureMode | VM의 기본 스토리지에 추가할 디스크 압력의 미리 설정된 모드입니다. 아래 표의 PressureModes 중 하나여야 합니다. |
| virtualMachineScaleSetInstances | (선택 사항) 가상 머신 확장 집합에 이 오류를 적용할 때의 인스턴스 ID 배열 |

### <a name="pressure-modes"></a>압력 모드

| PressureMode | 설명 |
| -- | -- |
| PremiumStorageP10IOPS | numberOfThreads = 1<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 25<br/>sizeOfBlocksInKB = 8<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 2<br/>percentOfWriteActions = 50 |
| PremiumStorageP10Throttling |<br/>numberOfThreads = 2<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 25<br/>sizeOfBlocksInKB = 64<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 1<br/>percentOfWriteActions = 50 |
| PremiumStorageP50IOPS | numberOfThreads = 32<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 32<br/>sizeOfBlocksInKB = 8<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 1<br/>percentOfWriteActions = 50 |
| PremiumStorageP50Throttling | numberOfThreads = 2<br/>randomBlockSizeInKB = 1024<br/>randomSeed = 10<br/>numberOfIOperThread = 2<br/>sizeOfBlocksInKB = 1024<br/>sizeOfWriteBufferInKB = 1024<br/>fileSizeInGB = 20<br/>percentOfWriteActions = 50|
| 기본값 | numberOfThreads = 2<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 2<br/>sizeOfBlocksInKB = 64<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 1<br/>percentOfWriteActions = 50 |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:diskIOPressure/1.0",
      "parameters": [
        {
          "key": "pressureMode",
          "value": "PremiumStorageP10IOPS"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="disk-io-pressure-linux"></a>디스크 I/O 압력(Linux)

| 속성 | 값 |
|-|-|
| 기능 이름 | LinuxDiskIOPressure-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원되는 OS 유형 | Linux |
| 설명 | stress-ng를 사용하여 디스크에 압력을 적용합니다. 임시 파일로 I/O 프로세스를 수행하는 하나 이상의 작업자 프로세스가 생성됩니다. 압력을 적용하는 방법에 대한 자세한 내용은 https://wiki.ubuntu.com/Kernel/Reference/stress-ng 을 참조하세요. |
| 사전 요구 사항 | Linux VM에서 오류를 실행하려면 **stress-ng** 유틸리티를 설치해야 합니다. Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. </br> stress-ng를 설치하는 APT 명령: *sudo apt-get 업데이트 && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> stress-ng를 설치하는 YUM 명령: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng* |
| Urn | urn:csci:microsoft:agent:linuxDiskIOPressure/1.0 |
| 매개 변수(키, 값) |  |
| workerCount | 실행할 작업자 프로세스 수입니다. 이를 0으로 설정하면 프로세서 수만큼 작업자 프로세스가 생성됩니다. |
| fileSizePerWorker | 작업자가 I/O 작업을 수행할 임시 파일의 크기입니다. 정수 + 단위(b), 킬로바이트(k), 메가바이트(m) 또는 기가바이트(g) (예: 4MB의 경우 4m, 256GB의 경우 256g) |
| blockSize | 디스크 I/O 작업에 사용할 블록 크기로, 4MB로 제한됩니다. 정수 + 단위(b), 킬로바이트(k) 또는 메가바이트(m)(예: 512K(512K)) |
| virtualMachineScaleSetInstances | (선택 사항) 가상 머신 확장 집합에 이 오류를 적용할 때의 인스턴스 ID 배열 |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:linuxDiskIOPressure/1.0",
      "parameters": [
        {
          "key": "workerCount",
          "value": "0"
        },
        {
          "key": "fileSizePerWorker",
          "value": "512m"
        },
        {
          "key": "blockSize",
          "value": "256k"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arbitrary-stress-ng-stress"></a>임의의 Stress-ng stress

| 속성 | 값 |
|-|-|
| 기능 이름 | StressNg-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원되는 OS 유형 | Linux |
| 설명 | 인수를 stress-ng에 직접 전달하여 stress-ng 명령을 실행합니다. stress-ng에 대해 미리 정의된 오류 중 하나가 요구 사항을 충족하지 않는 경우에 유용합니다. |
| 사전 요구 사항 | Linux VM에서 오류를 실행하려면 **stress-ng** 유틸리티를 설치해야 합니다. Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. </br> stress-ng를 설치하는 APT 명령: *sudo apt-get 업데이트 && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> stress-ng를 설치하는 YUM 명령: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng* |
| Urn | urn:csci:microsoft:agent:stressNg/1.0 |
| 매개 변수(키, 값) |  |
| stressNgArguments | stress-ng 프로세스에 전달할 하나 이상의 인수입니다. 가능한 stress-ng 인수에 대한 자세한 내용은 다음을 참조하세요. https://wiki.ubuntu.com/Kernel/Reference/stress-ng |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:stressNg/1.0",
      "parameters": [
        {
          "key": "stressNgArguments",
          "value": "--random 64"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="stop-windows-service"></a>Windows 서비스 중지

| 속성 | 값 |
|-|-|
| 기능 이름 | StopService-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원되는 OS 유형 | Windows |
| 설명 | Windows 서비스 컨트롤러 API를 사용하여 오류 기간 동안 Windows 서비스를 중지하고, 기간이 끝날 때 또는 실험이 취소된 경우 서비스를 다시 시작합니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn:csci:microsoft:agent:stopService/1.0 |
| 매개 변수(키, 값) |  |
| serviceName | 중지하려는 Windows 서비스의 이름입니다. `sc.exe query`명령 프롬프트에서 를 실행하여 서비스 이름을 탐색할 수 Windows 서비스 이름은 지원되지 않습니다. |
| virtualMachineScaleSetInstances | (선택 사항) 가상 머신 확장 집합에 이 오류를 적용할 때의 인스턴스 ID 배열 |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:stopService/1.0",
      "parameters": [
        {
          "key": "serviceName",
          "value": "nvagent"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="time-change"></a>시간 변경

| 속성 | 값 |
|-|-|
| 기능 이름 | TimeChange-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원되는 OS 유형 | Windows |
| 설명 | 삽입된 VM의 시스템 시간을 변경하고 기간이 끝날 때 또는 실험이 취소된 경우 다시 설정합니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn:csci:microsoft:agent:timeChange/1.0 |
| 매개 변수(키, 값) |  |
| dateTime | [ISO8601 형식의](https://www.cryptosys.net/pki/manpki/pki_iso8601datetime.html)DateTime 문자열입니다. YYYY-MM-DD 값이 누락된 경우 실험이 실행되는 현재 일로 기본 설정됩니다. Thh:mm:ss 값이 누락된 경우 기본값은 오전 12:00:00입니다. 2자리 연도가 제공된 경우(YYY) 현재 연도를 기준으로 4자리 연도(YYYY)로 변환됩니다. 이 누락된 경우 \<Z\> 현지 시간대의 오프셋으로 기본 설정됩니다. \<Z\> 는 항상 부호 기호(음수 또는 양수)를 포함해야 합니다. |
| virtualMachineScaleSetInstances | (선택 사항) 가상 머신 확장 집합에 이 오류를 적용할 때의 인스턴스 ID 배열 |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:timeChange/1.0",
      "parameters": [
        {
          "key": "dateTime",
          "value": "2038-01-01T03:14:07"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="kill-process"></a>프로세스 킬(Kill process)

| 속성 | 값 |
|-|-|
| 기능 이름 | KillProcess-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원되는 OS 유형 | Windows, Linux |
| 설명 | 오류 매개 변수에 전송된 프로세스 이름과 일치하는 프로세스의 실행 중인 인스턴스를 모두 끊습니다. 오류 동작에 대해 설정된 기간 내에서 지정된 kill 간격의 값에 따라 프로세스가 반복적으로 완료됩니다. 이 오류는 자동 복구가 구성된 경우 시스템 관리자가 프로세스를 수동으로 복구해야 하는 파괴적 오류입니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn:csci:microsoft:agent:killProcess/1.0 |
| 매개 변수(키, 값) |  |
| processName | VM에서 실행되는 프로세스의 이름(.exe 제외) |
| killIntervalInMilliseconds | 연속 킬 시도 사이에 오류가 대기하는 시간(밀리초)입니다. |
| virtualMachineScaleSetInstances | 필드 가상 머신 확장 집합에이 오류를 적용 하는 경우 인스턴스 Id의 배열입니다. |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:killProcess/1.0",
      "parameters": [
        {
          "key": "processName",
          "value": "myapp"
        },
        {
          "key": "killIntervalInMilliseconds",
          "value": "1000"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="dns-failure"></a>DNS 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | DnsFailure-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원 되는 OS 유형 | Windows |
| 설명 | 지정 된 오류 코드를 사용 하 여 DNS 조회 요청에 대 한 응답을 대체 합니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn: csci: microsoft: agent: dnsFailure/1.0 |
| 매개 변수 (키, 값) |  |
| 호스트 | 장애 조치 (failover)에 대 한 DNS 조회 요청에 대 한 호스트 이름의 구분 된 JSON 배열입니다. |
| dnsFailureReturnCode | 조회 오류 (FormErr, ServFail, NXDomain, No p, 거부 됨, XDomain, YXRRSet, NXRRSet, NotAuth, Notauth)를 위해 클라이언트에 반환 되는 DNS 오류 코드입니다. DNS 반환 코드에 대 한 자세한 내용은 [IANA 웹 사이트](https://www.iana.org/assignments/dns-parameters/dns-parameters.xml#dns-parameters-6) 를 참조 하세요. |
| virtualMachineScaleSetInstances | 필드 가상 머신 확장 집합에이 오류를 적용 하는 경우 인스턴스 Id의 배열입니다. |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:dnsFailure/1.0",
      "parameters": [
        {
          "key": "hosts",
          "value": "[ \"www.bing.com\", \"msdn.microsoft.com\" ]"
        },
        {
          "key": "dnsFailureReturnCode",
          "value": "ServFail"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="limitations"></a>제한 사항

* DNS 오류 오류에 Windows 2019 RS5 이상이 필요 합니다.
* 오류에 정의 된 호스트 이름에 대 한 오류 기간 동안 DNS 캐시가 무시 됩니다.

## <a name="network-latency"></a>네트워크 대기 시간

| 속성 | 값 |
|-|-|
| 기능 이름 | NetworkLatency-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원 되는 OS 유형 | Windows |
| 설명 | 지정 된 포트 범위와 네트워크 블록에 대 한 네트워크 대기 시간이 증가 합니다. |
| 사전 요구 사항 | 에이전트를 관리자 권한으로 실행 해야 합니다. 에이전트가 VM 확장으로 설치 된 경우 기본적으로 관리자 권한으로 실행 됩니다. |
| Urn | urn: csci: microsoft: agent: networkLatency/1.0 |
| 매개 변수 (키, 값) |  |
| latencyInMilliseconds | 적용 될 대기 시간 (밀리초)입니다. |
| destinationFilters | 오류 주입을 위해 대상으로 할 아웃 바운드 패킷을 정의 하는 패킷 필터의 구분 된 JSON 배열입니다. 최대값은 3입니다. |
| address | IP 범위의 시작을 나타내는 IP 주소입니다. |
| subnetMask | IP 주소 범위에 대 한 서브넷 마스크입니다. |
| portLow | 필드 포트 범위 시작의 포트 번호입니다. |
| portHigh | 필드 포트 범위의 끝에 대 한 포트 번호입니다. |
| virtualMachineScaleSetInstances | 필드 가상 머신 확장 집합에이 오류를 적용 하는 경우 인스턴스 Id의 배열입니다. |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkLatency/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"address\": \"23.45.229.97\", \"subnetMask\": \"255.255.255.224\", \"portLow\": \"5000\", \"portHigh\": \"5200\" } ]"
        },
        {
          "key": "latencyInMilliseconds",
          "value": "100",
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="network-disconnect"></a>네트워크 연결 끊기

| 속성 | 값 |
|-|-|
| 기능 이름 | NetworkDisconnect-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원 되는 OS 유형 | Windows |
| 설명 | 지정 된 포트 범위 및 네트워크 블록에 대 한 아웃 바운드 네트워크 트래픽을 차단 합니다. |
| 사전 요구 사항 | 에이전트를 관리자 권한으로 실행 해야 합니다. 에이전트가 VM 확장으로 설치 된 경우 기본적으로 관리자 권한으로 실행 됩니다. |
| Urn | urn: csci: microsoft: agent: networkDisconnect/1.0 |
| 매개 변수 (키, 값) |  |
| destinationFilters | 오류 주입을 위해 대상으로 할 아웃 바운드 패킷을 정의 하는 패킷 필터의 구분 된 JSON 배열입니다. 최대값은 3입니다. |
| address | IP 범위의 시작을 나타내는 IP 주소입니다. |
| subnetMask | IP 주소 범위에 대 한 서브넷 마스크입니다. |
| portLow | 필드 포트 범위 시작의 포트 번호입니다. |
| portHigh | 필드 포트 범위의 끝에 대 한 포트 번호입니다. |
| virtualMachineScaleSetInstances | 필드 가상 머신 확장 집합에이 오류를 적용 하는 경우 인스턴스 Id의 배열입니다. |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkDisconnect/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"address\": \"23.45.229.97\", \"subnetMask\": \"255.255.255.224\", \"portLow\": \"5000\", \"portHigh\": \"5200\" } ]"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

> [!WARNING]
> 네트워크 연결 끊기 오류는 새 연결에만 영향을 줍니다. 기존 **활성** 연결은 계속 유지 됩니다. 서비스 또는 프로세스를 다시 시작 하 여 연결을 강제로 중단할 수 있습니다.

## <a name="network-disconnect-with-firewall-rule"></a>방화벽 규칙을 사용 하 여 네트워크 연결 끊기

| 속성 | 값 |
|-|-|
| 기능 이름 | NetworkDisconnectViaFirewall-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원 되는 OS 유형 | Windows |
| 설명 | 지정 된 포트 범위 및 네트워크 블록의 아웃 바운드 트래픽을 차단 하는 Windows 방화벽 규칙을 적용 합니다. |
| 사전 요구 사항 | 에이전트를 관리자 권한으로 실행 해야 합니다. 에이전트가 VM 확장으로 설치 된 경우 기본적으로 관리자 권한으로 실행 됩니다. |
| Urn | urn: csci: microsoft: agent: networkDisconnectViaFirewall/1.0 |
| 매개 변수 (키, 값) |  |
| destinationFilters | 오류 주입을 위해 대상으로 할 아웃 바운드 패킷을 정의 하는 패킷 필터의 구분 된 JSON 배열입니다. 최대값은 3입니다. |
| address | IP 범위의 시작을 나타내는 IP 주소입니다. |
| subnetMask | IP 주소 범위에 대 한 서브넷 마스크입니다. |
| portLow | 필드 포트 범위 시작의 포트 번호입니다. |
| portHigh | 필드 포트 범위의 끝에 대 한 포트 번호입니다. |
| virtualMachineScaleSetInstances | 필드 가상 머신 확장 집합에이 오류를 적용 하는 경우 인스턴스 Id의 배열입니다. |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkDisconnectViaFirewall/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"Address\": \"23.45.229.97\", \"SubnetMask\": \"255.255.255.224\", \"PortLow\": \"5000\", \"PortHigh\": \"5200\" } ]"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arm-virtual-machine-shutdown"></a>ARM 가상 머신 종료
| 속성 | 값 |
|-|-|
| 기능 이름 | 종료-1.0 |
| 대상 형식 | Microsoft-VirtualMachine |
| 지원 되는 OS 유형 | Windows, Linux |
| 설명 | 오류 기간 동안 VM을 종료 하 고, 필요에 따라 오류 기간이 끝날 때 또는 실험을 취소 한 경우 VM을 다시 시작 합니다. Azure Resource Manager Vm만 지원 됩니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn: csci: microsoft: virtualMachine: shutdown/1.0 |
| 매개 변수 (키, 값) |  |
| abruptShutdown | 필드 VM을 정상적으로 종료 해야 하는지 (소거식)를 나타내는 부울 값입니다. |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:virtualMachine:shutdown/1.0",
      "parameters": [
        {
          "key": "abruptShutdown",
          "value": "false"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arm-virtual-machine-scale-set-instance-shutdown"></a>ARM 가상 머신 확장 집합 인스턴스 종료

| 속성 | 값 |
|-|-|
| 기능 이름 | 종료-1.0 |
| 대상 형식 | Microsoft-VirtualMachineScaleSet |
| 지원 되는 OS 유형 | Windows, Linux |
| 설명 | 오류 기간 동안 가상 machinse 확장 집합 인스턴스를 종료 하거나 중지 하 고, 필요에 따라 오류 기간이 끝날 때 또는 실험을 취소 한 경우 VM을 다시 시작 합니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn: csci: microsoft: virtualMachineScaleSet: shutdown/1.0 |
| 매개 변수 (키, 값) |  |
| abruptShutdown | 필드 가상 머신 확장 집합 인스턴스를 정상적으로 종료 해야 하는지 (소거식)를 나타내는 부울 값입니다. |
| 인스턴스 | 오류가 적용 되는 가상 머신 확장 집합 인스턴스 Id의 구분 된 배열인 문자열입니다. |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:virtualMachineScaleSet:shutdown/1.0",
      "parameters": [
        {
          "key": "abruptShutdown",
          "value": "true"
        },
        {
          "key": "instances",
          "value": "[\"1\",\"3\"]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="cosmos-db-failover"></a>Cosmos DB 장애 조치

| 속성 | 값 |
|-|-|
| 기능 이름 | 장애 조치-1.0 |
| 대상 형식 | Microsoft-CosmosDB |
| 설명 | 단일 쓰기 지역이 있는 Cosmos DB 계정에서 지정 된 읽기 지역으로 장애 조치 (failover) 하 여 [쓰기 지역 가동 중단](../cosmos-db/high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage) 을 시뮬레이션 합니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn: csci: microsoft: cosmosDB: failover/1.0 |
| 매개 변수 (키, 값) |  |
| readRegion | 장애 조치 (failover) 중에 쓰기 지역으로 승격 되어야 하는 읽기 지역 (예: "미국 동부 2") |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:cosmosDB:failover/1.0",
      "parameters": [
        {
          "key": "readRegion",
          "value": "West US 2"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-network-faults"></a>AKS 비정상 메시 네트워크 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | NetworkChaos-1.0 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| 설명 | 비정상 [메시](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/networkchaos_experiment) 를 통해 사용 가능한 네트워크 오류가 AKS 클러스터에 대해 실행 되도록 합니다. 네트워크 중단, 지연, 중복, 손실 및 손상으로 인해 발생 하는 AKS 인시던트를 다시 만드는 데 유용 합니다. |
| 사전 요구 사항 | AKS 클러스터는 [1.2.3 이전 버전의 비정상 메시를 배포 하 고 AKS 관리 AAD 사용 하지 않도록 설정](chaos-studio-tutorial-aks.md)해야 합니다. |
| Urn | urn: csci: microsoft: azureKubernetesServiceChaosMesh: networkChaos/1.0 |
| 매개 변수 (키, 값) |  |
| jsonSpec | [NetworkChaos 종류를](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/networkchaos_experiment)사용하는 JSON 형식 및 이스케이프된 [Chaos Mesh 사양입니다.](https://chaos-mesh.org/docs/user_guides/run_chaos_experiment#step-2-define-the-experiment-configuration-file) 이와 같은 [YAML-JSON 변환기를](https://www.convertjson.com/yaml-to-json.htm) 사용하여 Chaos Mesh YAML을 JSON으로 변환하고 축소하고 [이와 같은 JSON 문자열 이스케이프 도구를](https://www.freeformatter.com/json-escape.html) 사용하여 JSON 사양을 이스케이프할 수 있습니다. "jsonSpec" 속성 아래에만 YAML을 포함합니다(메타데이터, 종류 등은 포함하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:networkChaos/1.0",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"delay\",\"delay\":{\"latency\":\"30s\"},\"duration\":\"30s\",\"mode\":\"one\",\"scheduler\":{\"cron\":\"@every 60s\"},\"selector\":{\"labelSelectors\":{\"app\":\"web-show\"}}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-pod-faults"></a>AKS Chaos Mesh Pod 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | PodChaos-1.0 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| 설명 | [Chaos Mesh를](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/podchaos_experiment) 통해 사용할 수 있는 Pod 오류가 AKS 클러스터에 대해 실행되도록 합니다. Pod 오류 또는 컨테이너 문제로 인해 AKS 인시던트 다시 만드는 데 유용합니다. |
| 사전 요구 사항 | AKS [클러스터에는 버전 1.2.3 이하의 Chaos Mesh가 배포되어 있어야 하며 AKS 관리 AAD 사용하지 않도록 설정해야](chaos-studio-tutorial-aks.md)합니다. |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/1.0 |
| 매개 변수(키, 값) |  |
| jsonSpec | [PodChaos 종류](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/podchaos_experiment#pod-failure-configuration-file)를 사용하는 JSON 형식의 [Chaos Mesh 사양입니다.](https://chaos-mesh.org/docs/user_guides/run_chaos_experiment#step-2-define-the-experiment-configuration-file) 이와 같은 [YAML-JSON 변환기를](https://www.convertjson.com/yaml-to-json.htm) 사용하여 Chaos Mesh YAML을 JSON으로 변환하고 축소하고, [이와 같은 JSON 문자열 이스케이프 도구를](https://www.freeformatter.com/json-escape.html) 사용하여 JSON 사양을 이스케이프할 수 있습니다. "jsonSpec" 속성 아래에만 YAML을 포함합니다(메타데이터, 종류 등은 포함하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/1.0",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"pod-failure\",\"mode\":\"one\",\"value\":\"\",\"duration\":\"30s\",\"selector\":{\"labelSelectors\":{\"app.kubernetes.io\/component\":\"tikv\"}},\"scheduler\":{\"cron\":\"@every 2m\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-stress-faults"></a>AKS Chaos Mesh 스트레스 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | StressChaos-1.0 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| 설명 | [Chaos Mesh를](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/stresschaos_experiment) 통해 사용할 수 있는 스트레스 오류가 AKS 클러스터에 대해 실행되도록 합니다. Pod 컬렉션에 대한 스트레스로 인해(예: 높은 CPU 또는 메모리 사용으로 인해) AKS 인시던트 다시 만들기에 유용합니다. |
| 사전 요구 사항 | AKS [클러스터에는 버전 1.2.3 이하의 Chaos Mesh가 배포되어 있어야 하며 AKS 관리 AAD 사용하지 않도록 설정해야](chaos-studio-tutorial-aks.md)합니다. |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:stressChaos/1.0 |
| 매개 변수(키, 값) |  |
| jsonSpec | [StressChaos 종류를](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/stresschaos_experiment#configuration)사용하는 JSON 형식 및 이스케이프된 [Chaos Mesh 사양입니다.](https://chaos-mesh.org/docs/user_guides/run_chaos_experiment#step-2-define-the-experiment-configuration-file) 이와 같은 [YAML-JSON 변환기를](https://www.convertjson.com/yaml-to-json.htm) 사용하여 Chaos Mesh YAML을 JSON으로 변환하고 축소하고, [이와 같은 JSON 문자열 이스케이프 도구를](https://www.freeformatter.com/json-escape.html) 사용하여 JSON 사양을 이스케이프할 수 있습니다. "jsonSpec" 속성 아래에만 YAML을 포함합니다(메타데이터, 종류 등은 포함하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:stressChaos/1.0",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"namespaces\":[\"tidb-cluster-demo\"]},\"stressors\":{\"cpu\":{\"workers\":1}},\"duration\":\"30s\",\"scheduler\":{\"cron\":\"@every 2m\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-io-faults"></a>AKS Chaos Mesh IO 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | IOChaos-1.0 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| 설명 | [Chaos Mesh를](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/iochaos_experiment) 통해 사용할 수 있는 IO 오류가 AKS 클러스터에 대해 실행되도록 합니다. , 및 와 같은 IO 시스템 호출을 사용할 때 IO 지연 및 읽기/쓰기 오류로 인해 AKS 인시던트를 다시 만들 때 `open` `read` `write` 유용합니다. |
| 사전 요구 사항 | AKS [클러스터에는 버전 1.2.3 이하의 Chaos Mesh가 배포되어 있어야 하며 AKS 관리 AAD 사용하지 않도록 설정해야](chaos-studio-tutorial-aks.md)합니다. |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:IOChaos/1.0 |
| 매개 변수(키, 값) |  |
| jsonSpec | [IOChaos 종류](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/iochaos_experiment#configuration-file)를 사용하는 JSON 형식의 [Chaos Mesh 사양입니다.](https://chaos-mesh.org/docs/user_guides/run_chaos_experiment#step-2-define-the-experiment-configuration-file) 이와 같은 [YAML-JSON 변환기를](https://www.convertjson.com/yaml-to-json.htm) 사용하여 Chaos Mesh YAML을 JSON으로 변환하고 축소하고, [이와 같은 JSON 문자열 이스케이프 도구를](https://www.freeformatter.com/json-escape.html) 사용하여 JSON 사양을 이스케이프할 수 있습니다. "jsonSpec" 속성 아래에만 YAML을 포함합니다(메타데이터, 종류 등은 포함하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:IOChaos/1.0",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"latency\",\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"etcd\"}},\"volumePath\":\"\/var\/run\/etcd\",\"path\":\"\/var\/run\/etcd\/**\/*\",\"delay\":\"100ms\",\"percent\":50,\"duration\":\"400s\",\"scheduler\":{\"cron\":\"@every 10m\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-time-faults"></a>AKS Chaos Mesh 시간 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | TimeChaos-1.0 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| 설명 | [Chaos Mesh를](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/timechaos_experiment)사용하여 AKS 클러스터의 시스템 클록이 변경되도록 합니다. 분산 시스템이 동기화되지 않음, 누락/잘못된 윤년/윤초 논리 등으로 인해 발생하는 AKS 인시던트 다시 만드는 데 유용합니다. |
| 사전 요구 사항 | AKS [클러스터에는 버전 1.2.3 이하의 Chaos Mesh가 배포되어 있어야 하며 AKS 관리 AAD 사용하지 않도록 설정해야](chaos-studio-tutorial-aks.md)합니다. |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:timeChaos/1.0 |
| 매개 변수(키, 값) |  |
| jsonSpec | [TimeChaos 종류를](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/timechaos_experiment#configuration-file)사용하는 JSON 형식의 [Chaos Mesh 사양입니다.](https://chaos-mesh.org/docs/user_guides/run_chaos_experiment#step-2-define-the-experiment-configuration-file) 이와 같은 [YAML-JSON 변환기를](https://www.convertjson.com/yaml-to-json.htm) 사용하여 Chaos Mesh YAML을 JSON으로 변환하고 축소하고, [이와 같은 JSON 문자열 이스케이프 도구를](https://www.freeformatter.com/json-escape.html) 사용하여 JSON 사양을 이스케이프할 수 있습니다. "jsonSpec" 속성 아래에만 YAML을 포함합니다(메타데이터, 종류 등은 포함하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:timeChaos/1.0",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app.kubernetes.io\/component\":\"pd\"}},\"timeOffset\":\"-10m100ns\",\"clockIds\":[\"CLOCK_REALTIME\"],\"containerNames\":[\"pd\"],\"duration\":\"10s\",\"scheduler\":{\"cron\":\"@every 15s\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-kernel-faults"></a>AKS Chaos Mesh 커널 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | KernelChaos-1.0 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| 설명 | [Chaos Mesh를](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/kernelchaos_experiment) 통해 사용할 수 있는 커널 오류가 AKS 클러스터에 대해 실행되도록 합니다. 탑재 실패 또는 할당되지 않은 메모리와 같은 Linux 커널 수준 오류로 인해 AKS 인시던트 다시 만들기에 유용합니다. |
| 사전 요구 사항 | AKS [클러스터에는 버전 1.2.3 이하의 Chaos Mesh가 배포되어 있어야 하며 AKS 관리 AAD 사용하지 않도록 설정해야](chaos-studio-tutorial-aks.md)합니다. |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:kernelChaos/1.0 |
| 매개 변수(키, 값) |  |
| jsonSpec | [KernelChaos 종류를](https://chaos-mesh.org/docs/1.2.3/chaos_experiments/kernelchaos_experiment#configuration-file)사용하는 JSON 형식의 [Chaos Mesh 사양입니다.](https://chaos-mesh.org/docs/user_guides/run_chaos_experiment#step-2-define-the-experiment-configuration-file) 이와 같은 [YAML-JSON 변환기를](https://www.convertjson.com/yaml-to-json.htm) 사용하여 Chaos Mesh YAML을 JSON으로 변환하고 축소하고, [이와 같은 JSON 문자열 이스케이프 도구를](https://www.freeformatter.com/json-escape.html) 사용하여 JSON 사양을 이스케이프할 수 있습니다. "jsonSpec" 속성 아래에만 YAML을 포함합니다(메타데이터, 종류 등은 포함하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:kernelChaos/1.0",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"namespaces\":[\"chaos-mount\"]},\"failKernRequest\":{\"callchain\":[{\"funcname\":\"__x64_sys_mount\"}],\"failtype\":0}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="network-security-group-set-rules"></a>네트워크 보안 그룹(규칙 설정)

| 속성 | 값 |
|-|-|
| 기능 이름 | SecurityRule-1.0 |
| 대상 형식 | Microsoft-NetworkSecurityGroup |
| 설명 | 기존 Azure 네트워크 보안 그룹 또는 Azure 네트워크 보안 그룹 집합에서 규칙을 조작 하거나 만들 수 있습니다 (규칙 정의가 적용 되는 보안 그룹에 해당 하는 경우). 다운스트림 또는 지역 간 종속성/비 종속성의 중단을 시뮬레이션 하거나, 서비스 장애 조치 (failover)를 강제로 수행 하는 논리를 트리거할 것으로 예상 되는 이벤트를 시뮬레이션 하거나, 모니터링 또는 상태 관리 서비스에서 작업을 트리거할 것으로 예상 되는 이벤트를 시뮬레이션 하는 데 유용 하거나, 비정상 상태 에이전트를 배포할 수 없는 네트워크 트래픽을 차단 하거나 허용 하는 대신 사용할 수 있습니다 |
| 사전 요구 사항 | 없음 |
| Urn | urn: csci: microsoft: networkSecurityGroup: securityRule/1.0 |
| 매개 변수 (키, 값) |  |
| name | 생성 될 보안 규칙에 대 한 고유 이름입니다. 동일한 이름의 NSG에 다른 규칙이 이미 있으면 오류가 실패 합니다. 문자 또는 숫자로 시작 하 고 문자, 숫자 또는 밑줄로 끝나야 하며 문자, 숫자, 밑줄, 마침표 또는 하이픈만 포함할 수 있습니다. |
| protocol | 보안 규칙에 대 한 프로토콜입니다. 모든, TCP, UDP 또는 ICMP 여야 합니다. |
| sourceAddresses | Json으로 구분 된 CIDR 형식의 IP 주소 배열을 나타내는 문자열입니다. 는 인바운드 규칙의 서비스 태그 이름일 수도 있습니다 (예: "AppService"). 별표 ' * '를 사용 하 여 모든 원본 Ip를 일치 시킬 수도 있습니다. |
| destinationAddresses | Json으로 구분 된 CIDR 형식의 IP 주소 배열을 나타내는 문자열입니다. 는 아웃 바운드 규칙의 서비스 태그 이름일 수도 있습니다 (예: "AppService"). 별표 ' * '를 사용 하 여 모든 대상 Ip를 일치 시킬 수도 있습니다. |
| action | 보안 그룹 액세스 유형입니다. 허용 또는 거부 여야 합니다. |
| destinationPortRanges | 80 또는 1024-65535와 같이 json으로 구분 되는 단일 포트 및/또는 포트 범위의 배열을 나타내는 문자열입니다. |
| sourcePortRanges | Json으로 구분 된 배열 단일 포트 및/또는 포트 범위 (예: 80 또는 1024-65535)를 나타내는 문자열입니다. |
| priority | 네트워크 보안 그룹 내의 모든 보안 규칙에 대해 고유한 100에서 4096 사이의 값입니다. 동일한 우선 순위를 사용 하 여 다른 규칙이 NSG에 이미 있는 경우에는 오류가 발생 하지 않습니다. |
| direction | 보안 규칙의 영향을 받는 트래픽의 방향입니다. 는 인바운드 또는 아웃 바운드 여야 합니다. |

### <a name="sample-json"></a>JSON 샘플

```json
{ 
  "name": "branchOne", 
  "actions": [ 
    { 
      "type": "continuous", 
      "name": "urn:csci:microsoft:networkSecurityGroup:securityRule/1.0", 
      "parameters": [ 
          { 
              "key": "name", 
              "value": "Block_SingleHost_to_Networks" 

          }, 
          { 
              "key": "protocol", 
              "value": "Any" 
          }, 
          { 
              "key": "sourceAddresses", 
              "value": "[\"10.1.1.128/32\"]"
          }, 
          { 
              "key": "destinationAddresses", 
              "value": "[\"10.20.0.0/16\",\"10.30.0.0/16\"]"
          }, 
          { 
              "key": "access", 
              "value": "Deny" 
          }, 
          { 
              "key": "destinationPortRanges", 
              "value": "[\"80-8080\"]"
          }, 
          { 
              "key": "sourcePortRanges", 
              "value": "[\"*\"]"
          }, 
          { 
              "key": "priority", 
              "value": "100" 
          }, 
          { 
              "key": "direction", 
              "value": "Outbound" 
          } 
      ], 
      "duration": "PT10M", 
      "selectorid": "myResources" 
    } 
  ] 
} 
```

### <a name="limitations"></a>제한 사항

* 이 오류는 기존 네트워크 보안 그룹에만 적용할 수 있습니다.
* 트래픽을 거부 하는 NSG 규칙이 적용 되는 경우 기존 연결은 4 분 동안 **유휴 상태가** 될 때까지 중단 되지 않습니다. 한 가지 해결 방법은 NSG 오류가 적용 될 때 기존 연결을 중단 시키는 오류를 사용 하는 동일한 단계에서 다른 분기를 추가 하는 것입니다. 예를 들어 프로세스를 종료 하거나 일시적으로 서비스를 중지 하거나 VM을 다시 시작 하면 연결이 다시 설정 됩니다.
* 규칙은 동작의 시작 부분에서 적용 됩니다. 작업 기간 중에 규칙에 대 한 외부 변경 내용으로 인해 실험이 실패 합니다.
* 응용 프로그램 보안 그룹 규칙을 만들거나 수정 하는 것은 지원 되지 않습니다.
* 우선 순위 값은 대상으로 지정 된 각 NSG에서 고유 해야 합니다. 우선 순위 값이 다른 새 규칙을 만들려고 하면 실험에 실패 합니다.

## <a name="azure-cache-for-redis-reboot"></a>Redis 다시 부팅에 대 한 Azure 캐시

| 속성 | 값 |
|-|-|
| 기능 이름 | 다시 부팅-1.0 |
| 대상 형식 | Microsoft-AzureClusteredCacheForRedis |
| 설명 | 강제 다시 부팅 작업이 대상에서 발생 하 여 잠시 중단을 시뮬레이션 합니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn: csci: microsoft: azureClusteredCacheForRedis: reboot/1.0 |
| 오류 유형 | 불연속 |
| 매개 변수 (키, 값) |  |
| rebootType | 다시 부팅 작업을 수행할 노드 형식으로, PrimaryNode, SecondaryNode 또는 AllNodes로 지정할 수 있습니다.  |
| shardId | 재부팅할 분할 된의 ID입니다.  |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "discrete",
      "name": "urn:csci:microsoft:azureClusteredCacheForRedis:reboot/1.0",
      "parameters": [
        {
          "key": "RebootType",
          "value": "AllNodes"
        },
        {
          "key": "ShardId",
          "value": "0"
        }
      ],
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="limitations"></a>제한 사항

* 다시 부팅 오류가 발생 하면 강제 다시 부팅이 중단 이벤트를 더 잘 시뮬레이션 하 여 데이터가 손실 될 가능성이 있음을 의미 합니다.
* 재부팅 오류는 **불연속** 오류 유형입니다. 연속 오류와 달리 일회성 작업 이므로 기간이 없습니다.
