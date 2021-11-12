---
title: 비정상 스튜디오 오류 및 동작 라이브러리
description: 모든 필수 구성 요소 및 매개 변수를 포함 하 여 비정상 스튜디오에서 사용할 수 있는 작업을 이해 합니다.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 60e9dd18d8c9357f2696ec79be08925cdd7e97da
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371014"
---
# <a name="chaos-studio-fault-and-action-library"></a>비정상 스튜디오 오류 및 동작 라이브러리

현재 사용할 수 있는 오류는 다음과 같습니다. [오류 공급자](./chaos-studio-fault-providers.md) 페이지를 방문 하 여 지원 되는 리소스 종류를 파악 합니다.

## <a name="time-delay"></a>지연 시간

| 속성 | 값 |
|-|-|
| 오류 공급자 | 해당 없음 |
| 지원 되는 OS 유형 | 해당 없음 |
| Description | 다른 작업 전후에 지연 시간을 추가 합니다. 오류가 서비스에 나타나거나 실험 외부의 활동이 완료 되기를 기다리는 데 유용 합니다 (예: 다른 오류를 삽입 하기 전에 autohealing가 발생 하기를 기다리는 경우). |
| 필수 조건 | 해당 없음 |
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
| Description | 오류 동작 기간 동안이 오류가 삽입 되는 VM의 지정 된 값까지 CPU 압력을 추가 합니다. 인공 CPU 압력은 기간이 종료 되거나 실험이 취소 되 면 제거 됩니다. |
| 필수 조건 | **Linux:** Linux VM에서 오류를 실행 하려면 **스트레스 기능** 유틸리티를 설치 해야 합니다. Linux 배포판 패키지 관리자를 사용 하 여 설치할 수 있습니다. </br> 스트레스를 설치 하는 APT 명령: *sudo APT-업데이트 && sudo APT-y 압축 압축 풀기 && sudo APT-y 설치 스트레스를* 가져옵니다. </br> 스트레스를 설치 하는 YUM 명령: *sudo disjunctive normal form-y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo YUM-y 설치 스트레스*  |
| | **Windows:** 없음. |
| Urn | urn: csci: microsoft: agent: Cpupress/1.0 |
| 매개 변수 (키, 값)  |
| pressureLevel | VM에 적용 되는 CPU 압력 (%)을 나타내는 1에서 99 사이의 정수입니다. |
| virtualMachineScaleSetInstances | 가상 머신 확장 집합에이 오류를 적용 하는 경우 인스턴스 Id의 배열입니다. 가상 머신 확장 집합에 필요 합니다. |

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
| Description | 오류 동작 기간 동안이 오류가 삽입 되는 VM의 지정 된 값까지 실제 메모리 압력을 추가 합니다. 이 기간이 종료 되거나 실험이 취소 되 면 인공 물리적 메모리 압력이 제거 됩니다. |
| 필수 조건 | **Linux:** Linux VM에서 오류를 실행 하려면 **스트레스 기능** 유틸리티를 설치 해야 합니다. Linux 배포판 패키지 관리자를 사용 하 여 설치할 수 있습니다. </br> 스트레스를 설치 하는 APT 명령: *sudo APT-업데이트 && sudo APT-y 압축 압축 풀기 && sudo APT-y 설치 스트레스를* 가져옵니다. </br> 스트레스를 설치 하는 YUM 명령: *sudo disjunctive normal form-y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo YUM-y 설치 스트레스*  |
| | **Windows:** 없음. |
| Urn | urn: csci: microsoft: agent: physicalMemoryPressure/1.0 |
| 매개 변수 (키, 값) |  |
| pressureLevel | VM에 적용 되는 실제 메모리 압력 (%)의 양을 나타내는 1에서 99 사이의 정수입니다. |
| virtualMachineScaleSetInstances | 가상 머신 확장 집합에이 오류를 적용 하는 경우 인스턴스 Id의 배열입니다. 가상 머신 확장 집합에 필요 합니다. |

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
| Description | 오류 동작 기간 동안이 오류가 주입 되는 VM의 지정 된 값까지 가상 메모리 압력을 추가 합니다. 인공 가상 메모리 압력은 기간이 종료 되거나 실험이 취소 되 면 제거 됩니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn: csci: microsoft: agent: virtualMemoryPressure/1.0 |
| 매개 변수 (키, 값) |  |
| pressureLevel | VM에 적용 되는 실제 메모리 압력 (%)의 양을 나타내는 1에서 99 사이의 정수입니다. |
| virtualMachineScaleSetInstances | 가상 머신 확장 집합에 이 오류를 적용할 때 인스턴스 ID의 배열입니다. 가상 머신 확장 집합에 필요합니다. |

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
| Description | [diskspd 유틸리티를](https://github.com/Microsoft/diskspd/wiki) 사용하여 오류 작업 기간 동안 삽입되는 VM의 기본 스토리지에 디스크 압력을 추가합니다. 이 오류에는 5가지 실행 모드가 있습니다. 인공 디스크 압력은 기간이 끝날 때 또는 실험이 취소된 경우 제거됩니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn:csci:microsoft:agent:diskIOPressure/1.0 |
| 매개 변수(키, 값) |  |
| pressureMode | VM의 기본 스토리지에 추가할 디스크 압력의 미리 설정된 모드입니다. 아래 표의 PressureModes 중 하나여야 합니다. |
| virtualMachineScaleSetInstances | 가상 머신 확장 집합에 이 오류를 적용할 때 인스턴스 ID의 배열입니다. 가상 머신 확장 집합에 필요합니다. |

### <a name="pressure-modes"></a>압력 모드

| PressureMode | Description |
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
| Description | stress-ng를 사용하여 디스크에 압력을 적용합니다. 임시 파일로 I/O 프로세스를 수행하는 하나 이상의 작업자 프로세스가 생성됩니다. 압력을 적용하는 방법에 대한 자세한 내용은 https://wiki.ubuntu.com/Kernel/Reference/stress-ng 을 참조하세요. |
| 필수 조건 | Linux VM에서 오류를 실행하려면 **stress-ng** 유틸리티를 설치해야 합니다. Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. </br> stress-ng를 설치하는 APT 명령: *sudo apt-get 업데이트 && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> stress-ng를 설치하는 YUM 명령: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng* |
| Urn | urn:csci:microsoft:agent:linuxDiskIOPressure/1.0 |
| 매개 변수(키, 값) |  |
| workerCount | 실행할 작업자 프로세스 수입니다. 이 를 0으로 설정하면 프로세서 수만큼 작업자 프로세스가 생성됩니다. |
| fileSizePerWorker | 작업자가 I/O 작업을 수행할 임시 파일의 크기입니다. 정수 + 단위(b), 킬로바이트(k), 메가바이트(m) 또는 기가바이트(g)(예: 4MB의 경우 4m, 256GB의 경우 256g) |
| blockSize | 디스크 I/O 작업에 사용할 블록 크기로, 4MB로 제한됩니다. 정수 + 단위(b), 킬로바이트(k) 또는 메가바이트(m)(예: 512K(512K)) |
| virtualMachineScaleSetInstances | 가상 머신 확장 집합에 이 오류를 적용할 때 인스턴스 ID의 배열입니다. 가상 머신 확장 집합에 필요합니다. |

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

## <a name="arbitrary-stress-ng-stress"></a>임의 Stress-ng stress

| 속성 | 값 |
|-|-|
| 기능 이름 | StressNg-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원되는 OS 유형 | Linux |
| Description | 인수를 stress-ng에 직접 전달하여 stress-ng 명령을 실행합니다. stress-ng에 대해 미리 정의된 오류 중 하나가 요구 사항을 충족하지 않는 경우에 유용합니다. |
| 필수 조건 | Linux VM에서 오류를 실행하려면 **stress-ng** 유틸리티를 설치해야 합니다. Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. </br> stress-ng를 설치하는 APT 명령: *sudo apt-get 업데이트 && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> stress-ng를 설치하는 YUM 명령: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng* |
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
| Description | Windows 서비스 컨트롤러 API를 사용하여 오류 기간 동안 Windows 서비스를 중지하고, 기간이 끝날 때 다시 시작하거나, 실험이 취소된 경우 서비스를 다시 시작합니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn:csci:microsoft:agent:stopService/1.0 |
| 매개 변수(키, 값) |  |
| serviceName | 중지하려는 Windows 서비스의 이름입니다. `sc.exe query`명령 프롬프트에서 를 실행하여 서비스 이름을 탐색할 수 Windows 서비스 이름은 지원되지 않습니다. |
| virtualMachineScaleSetInstances | 가상 머신 확장 집합에 이 오류를 적용할 때 인스턴스 ID의 배열입니다. 가상 머신 확장 집합에 필요합니다. |

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
| Description | 삽입된 VM의 시스템 시간을 변경하고 기간이 끝날 때 또는 실험이 취소된 경우 다시 설정합니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn:csci:microsoft:agent:timeChange/1.0 |
| 매개 변수(키, 값) |  |
| dateTime | [ISO8601 형식의](https://www.cryptosys.net/pki/manpki/pki_iso8601datetime.html)DateTime 문자열입니다. YYYY-MM-DD 값이 누락된 경우 실험이 실행되는 현재 일로 기본 설정됩니다. Thh:mm:ss 값이 누락된 경우 기본값은 오전 12:00:00입니다. 2자리 연도가 제공된 경우(YYY) 현재 연도를 기준으로 4자리 연도(YYYY)로 변환됩니다. 이 누락된 경우 \<Z\> 현지 시간대의 오프셋으로 기본 설정됩니다. \<Z\> 는 항상 부호 기호(음수 또는 양수)를 포함해야 합니다. |
| virtualMachineScaleSetInstances | 가상 머신 확장 집합에 이 오류를 적용할 때 인스턴스 ID의 배열입니다. 가상 머신 확장 집합에 필요합니다. |

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

## <a name="kill-process"></a>프로세스 완료

| 속성 | 값 |
|-|-|
| 기능 이름 | KillProcess-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원되는 OS 유형 | Windows, Linux |
| Description | 오류 매개 변수에 전송된 프로세스 이름과 일치하는 프로세스의 실행 중인 인스턴스를 모두 끊습니다. 오류 동작에 대해 설정된 기간 내에서 지정된 kill 간격의 값에 따라 프로세스가 반복적으로 완료됩니다. 이 오류는 자동 복구가 구성된 경우 시스템 관리자가 프로세스를 수동으로 복구해야 하는 파괴적 오류입니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn: csci: microsoft: agent: killProcess/1.0 |
| 매개 변수 (키, 값) |  |
| processName | VM에서 실행 중인 프로세스의 이름 (.exe 없음) |
| killIntervalInMilliseconds | 오류가 연속 중지 시도 사이에서 대기 하는 시간 (밀리초)입니다. |
| virtualMachineScaleSetInstances | 가상 머신 확장 집합에이 오류를 적용 하는 경우 인스턴스 Id의 배열입니다. 가상 머신 확장 집합에 필요 합니다. |

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
| Description | 지정 된 오류 코드를 사용 하 여 DNS 조회 요청에 대 한 응답을 대체 합니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn: csci: microsoft: agent: dnsFailure/1.0 |
| 매개 변수 (키, 값) |  |
| 호스트 | 장애 조치 (failover)에 대 한 DNS 조회 요청에 대 한 호스트 이름의 구분 된 JSON 배열입니다. |
| dnsFailureReturnCode | 조회 오류 (FormErr, ServFail, NXDomain, No p, 거부 됨, XDomain, YXRRSet, NXRRSet, NotAuth, Notauth)를 위해 클라이언트에 반환 되는 DNS 오류 코드입니다. DNS 반환 코드에 대 한 자세한 내용은 [IANA 웹 사이트](https://www.iana.org/assignments/dns-parameters/dns-parameters.xml#dns-parameters-6) 를 참조 하세요. |
| virtualMachineScaleSetInstances | 가상 머신 확장 집합에이 오류를 적용 하는 경우 인스턴스 Id의 배열입니다. 가상 머신 확장 집합에 필요 합니다. |

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
| Description | 지정 된 포트 범위와 네트워크 블록에 대 한 네트워크 대기 시간이 증가 합니다. |
| 필수 조건 | 에이전트를 관리자 권한으로 실행 해야 합니다. 에이전트가 VM 확장으로 설치 된 경우 기본적으로 관리자 권한으로 실행 됩니다. |
| Urn | urn: csci: microsoft: agent: networkLatency/1.0 |
| 매개 변수 (키, 값) |  |
| latencyInMilliseconds | 적용 될 대기 시간 (밀리초)입니다. |
| destinationFilters | 오류 주입을 위해 대상으로 할 아웃 바운드 패킷을 정의 하는 패킷 필터의 구분 된 JSON 배열입니다. 최대값은 3입니다. |
| address | IP 범위의 시작을 나타내는 IP 주소입니다. |
| subnetMask | IP 주소 범위에 대 한 서브넷 마스크입니다. |
| portLow | 필드 포트 범위 시작의 포트 번호입니다. |
| portHigh | 필드 포트 범위의 끝에 대 한 포트 번호입니다. |
| virtualMachineScaleSetInstances | 가상 머신 확장 집합에이 오류를 적용 하는 경우 인스턴스 Id의 배열입니다. 가상 머신 확장 집합에 필요 합니다. |

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
| Description | 지정 된 포트 범위 및 네트워크 블록에 대 한 아웃 바운드 네트워크 트래픽을 차단 합니다. |
| 필수 조건 | 에이전트를 관리자 권한으로 실행 해야 합니다. 에이전트가 VM 확장으로 설치 된 경우 기본적으로 관리자 권한으로 실행 됩니다. |
| Urn | urn: csci: microsoft: agent: networkDisconnect/1.0 |
| 매개 변수 (키, 값) |  |
| destinationFilters | 오류 주입을 위해 대상으로 할 아웃 바운드 패킷을 정의 하는 패킷 필터의 구분 된 JSON 배열입니다. 최대값은 3입니다. |
| address | IP 범위의 시작을 나타내는 IP 주소입니다. |
| subnetMask | IP 주소 범위에 대 한 서브넷 마스크입니다. |
| portLow | 필드 포트 범위 시작의 포트 번호입니다. |
| portHigh | 필드 포트 범위의 끝에 대 한 포트 번호입니다. |
| virtualMachineScaleSetInstances | 가상 머신 확장 집합에 이 오류를 적용할 때 인스턴스 ID의 배열입니다. 가상 머신 확장 집합에 필요합니다. |

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
> 네트워크 연결 끊기 오류는 새 연결에만 영향을 미칩니다. 기존 **활성** 연결은 계속 유지됩니다. 서비스 또는 프로세스를 다시 시작하여 연결을 강제로 끊을 수 있습니다.

## <a name="network-disconnect-with-firewall-rule"></a>방화벽 규칙을 사용하여 네트워크 연결 끊기

| 속성 | 값 |
|-|-|
| 기능 이름 | NetworkDisconnectViaFirewall-1.0 |
| 대상 형식 | Microsoft-Agent |
| 지원되는 OS 유형 | Windows |
| Description | 지정된 포트 범위 및 네트워크 블록에 대한 아웃바운드 트래픽을 차단하는 Windows 방화벽 규칙을 적용합니다. |
| 필수 조건 | 에이전트는 관리자 권한으로 실행해야 합니다. 에이전트가 VM 확장으로 설치된 경우 기본적으로 관리자 권한으로 실행됩니다. |
| Urn | urn:csci:microsoft:agent:networkDisconnectViaFirewall/1.0 |
| 매개 변수(키, 값) |  |
| destinationFilters | 오류 주입을 대상으로 지정할 아웃바운드 패킷을 정의하는 패킷 필터의 구분된 JSON 배열입니다. 최대 3입니다. |
| address | IP 범위의 시작을 나타내는 IP 주소입니다. |
| subnetMask | IP 주소 범위에 대한 서브넷 마스크입니다. |
| portLow | (선택 사항) 포트 범위 시작의 포트 번호입니다. |
| portHigh | (선택 사항) 포트 범위 끝의 포트 번호입니다. |
| virtualMachineScaleSetInstances | 가상 머신 확장 집합에 이 오류를 적용할 때 인스턴스 ID의 배열입니다. 가상 머신 확장 집합에 필요합니다. |

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
| 기능 이름 | Shutdown-1.0 |
| 대상 형식 | Microsoft-VirtualMachine |
| 지원되는 OS 유형 | Windows, Linux |
| Description | 오류 기간 동안 VM을 종료하고 필요에 따라 오류 기간이 끝날 때 또는 실험이 취소된 경우 VM을 다시 시작합니다. Azure Resource Manager VM만 지원됩니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn:csci:microsoft:virtualMachine:shutdown/1.0 |
| 매개 변수(키, 값) |  |
| androidShutdown | (선택 사항) VM을 정상적으로 또는 갑자기 종료해야 하는지(파괴적)해야 하는지를 나타내는 부울입니다. |

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
| 기능 이름 | Shutdown-1.0 |
| 대상 형식 | Microsoft-VirtualMachineScaleSet |
| 지원되는 OS 유형 | Windows, Linux |
| Description | 오류 기간 동안 가상 머신 확장 집합 인스턴스를 종료하거나 종료하고, 필요에 따라 오류 기간이 끝날 때 또는 실험이 취소된 경우 VM을 다시 시작합니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn:csci:microsoft:virtualMachineScaleSet:shutdown/1.0 |
| 매개 변수(키, 값) |  |
| androidShutdown | (선택 사항) 가상 머신 확장 집합 인스턴스를 정상적으로 또는 갑자기(파괴적) 종료해야 하는지를 나타내는 부울입니다. |
| 인스턴스 | 오류가 적용될 가상 머신 확장 집합 인스턴스의 구분된 배열인 문자열입니다. |

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

## <a name="cosmos-db-failover"></a>Cosmos DB 장애 조치(failover)

| 속성 | 값 |
|-|-|
| 기능 이름 | 장애 조치(failover) -1.0 |
| 대상 형식 | Microsoft-CosmosDB |
| Description | 단일 쓰기 지역이 있는 Cosmos DB 계정이 지정된 읽기 지역으로 장애 조치(fail over)하여 [쓰기 지역 중단을](../cosmos-db/high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage) 시뮬레이트합니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn:csci:microsoft:cosmosDB:failover/1.0 |
| 매개 변수(키, 값) |  |
| readRegion | 장애 조치(failover) 중에 쓰기 지역으로 승격해야 하는 읽기 지역입니다(예: "미국 동부 2"). |

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

## <a name="aks-chaos-mesh-network-faults"></a>AKS Chaos Mesh 네트워크 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | NetworkChaos-2.1 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | 비정상 [메시](https://chaos-mesh.org/docs/simulate-network-chaos-on-kubernetes/) 를 통해 사용 가능한 네트워크 오류가 AKS 클러스터에 대해 실행 되도록 합니다. 네트워크 중단, 지연, 중복, 손실 및 손상으로 인해 발생 하는 AKS 인시던트를 다시 만드는 데 유용 합니다. |
| 필수 조건 | AKS 클러스터는 비정상 [메시를 배포](chaos-studio-tutorial-aks-portal.md)해야 합니다. |
| Urn | urn: csci: microsoft: azureKubernetesServiceChaosMesh: networkChaos/2.1 |
| 매개 변수 (키, 값) |  |
| jsonSpec | JSON 형식으로 지정 되 고, ARM 템플릿을 통해 생성 된 경우, REST API 또는 Azure CLI는 [networkchaos 종류](https://chaos-mesh.org/docs/simulate-network-chaos-on-kubernetes/#create-experiments-using-the-yaml-files)를 사용 하는 json 이스케이프 비정상 메시 사양을 사용 합니다. [이와 같은 Yaml-json 변환기](https://www.convertjson.com/yaml-to-json.htm) 를 사용 하 여 비정상 메시 YAML을 json으로 변환 하 고,이를 사용 하 여이를 처리 하 고 json [문자열 이스케이프 도구 (예](https://www.freeformatter.com/json-escape.html) : json)를 사용 하 여 json 사양을 이스케이프할 수 있습니다. "JsonSpec" 속성에는 YAML만 포함 됩니다 (메타 데이터, 종류 등은 포함 하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:networkChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"delay\",\"mode\":\"one\",\"selector\":{\"namespaces\":[\"default\"],\"labelSelectors\":{\"app\":\"web-show\"}},\"delay\":{\"latency\":\"10ms\",\"correlation\":\"100\",\"jitter\":\"0ms\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-pod-faults"></a>AKS 비정상 메시 pod 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | PodChaos-2.1 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | 비정상 [메시](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/) 를 통해 사용 가능한 POD 오류가 AKS 클러스터에 대해 실행 되도록 합니다. Pod 실패 또는 컨테이너 문제로 인 한 AKS 인시던트를 다시 만드는 데 유용 합니다. |
| 필수 조건 | AKS 클러스터는 비정상 [메시를 배포](chaos-studio-tutorial-aks-portal.md)해야 합니다. |
| Urn | urn: csci: microsoft: azureKubernetesServiceChaosMesh: podChaos/2.1 |
| 매개 변수 (키, 값) |  |
| jsonSpec | [PodChaos kind](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/#create-experiments-using-yaml-configuration-files)를 사용 하는 json으로 형식이 지정 되 고, ARM 템플릿, REST API 또는 Azure CLI를 통해 생성 된 경우 json 이스케이프 된 비정상 메시 사양을 사용 하 여 생성 됩니다. 이와 [같은 Yaml-json 변환기](https://www.convertjson.com/yaml-to-json.htm) 를 사용 하 여 비정상 메시 YAML을 json으로 변환 하 고, 미니 클래스를 표시 하 고, [이와 같은 json 문자열 이스케이프 도구](https://www.freeformatter.com/json-escape.html) 를 사용 하 여 json 사양을 이스케이프할 수 있습니다. "JsonSpec" 속성에는 YAML만 포함 됩니다 (메타 데이터, 종류 등은 포함 하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"pod-failure\",\"mode\":\"one\",\"duration\":\"30s\",\"selector\":{\"labelSelectors\":{\"app.kubernetes.io\/component\":\"tikv\"}}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-stress-faults"></a>비정상 상태의 메시 스트레스 오류 AKS

| 속성 | 값 |
|-|-|
| 기능 이름 | StressChaos-2.1 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | 비정상 [메시](https://chaos-mesh.org/docs/simulate-heavy-stress-on-kubernetes/) 를 통해 AKS 클러스터에 대해 실행할 수 있는 스트레스 오류가 발생 합니다. 높은 CPU 또는 메모리 사용으로 인 한 경우와 같이 pod 컬렉션을 과부하 하 여 AKS 인시던트를 다시 만드는 데 유용 합니다. |
| 필수 조건 | AKS 클러스터는 비정상 [메시를 배포](chaos-studio-tutorial-aks-portal.md)해야 합니다. |
| Urn | urn: csci: microsoft: azureKubernetesServiceChaosMesh: stressChaos/2.1 |
| 매개 변수 (키, 값) |  |
| jsonSpec | [StressChaos kind](https://chaos-mesh.org/docs/simulate-heavy-stress-on-kubernetes/#create-experiments-using-the-yaml-file)를 사용 하는 json으로 형식이 지정 되 고, ARM 템플릿, REST API 또는 Azure CLI를 통해 생성 된 경우 json 이스케이프 된 비정상 메시 사양을 사용 하 여 생성 됩니다. 이와 [같은 Yaml-json 변환기](https://www.convertjson.com/yaml-to-json.htm) 를 사용 하 여 비정상 메시 YAML을 json으로 변환 하 고, 미니 클래스를 표시 하 고, [이와 같은 json 문자열 이스케이프 도구](https://www.freeformatter.com/json-escape.html) 를 사용 하 여 json 사양을 이스케이프할 수 있습니다. "JsonSpec" 속성에는 YAML만 포함 됩니다 (메타 데이터, 종류 등은 포함 하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:stressChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"app1\"}},\"stressors\":{\"memory\":{\"workers\":4,\"size\":\"256MB\"}}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-io-faults"></a>AKS 비정상 메시 IO 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | IOChaos-2.1 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | 비정상 [메시](https://chaos-mesh.org/docs/simulate-io-chaos-on-kubernetes/) 를 통해 사용할 수 있는 IO 오류가 AKS 클러스터에 대해 실행 되도록 합니다. , 및와 같은 IO 시스템 호출을 사용 하는 경우 IO 지연 및 읽기/쓰기 오류로 인해 AKS 인시던트를 다시 만드는 데 유용 합니다 `open` `read` `write` . |
| 필수 조건 | AKS 클러스터는 비정상 [메시를 배포](chaos-studio-tutorial-aks-portal.md)해야 합니다. |
| Urn | urn: csci: microsoft: azureKubernetesServiceChaosMesh: IOChaos/2.1 |
| 매개 변수 (키, 값) |  |
| jsonSpec | JSON 형식으로 지정 되 고, ARM 템플릿을 통해 만든 경우, REST API 또는 Azure CLI는 [iochaos 종류](https://chaos-mesh.org/docs/simulate-io-chaos-on-kubernetes/#create-experiments-using-the-yaml-files)를 사용 하는 json 이스케이프 비정상 메시 사양을 사용 합니다. 이와 [같은 Yaml-json 변환기](https://www.convertjson.com/yaml-to-json.htm) 를 사용 하 여 비정상 메시 YAML을 json으로 변환 하 고, 미니 클래스를 표시 하 고, [이와 같은 json 문자열 이스케이프 도구](https://www.freeformatter.com/json-escape.html) 를 사용 하 여 json 사양을 이스케이프할 수 있습니다. "JsonSpec" 속성에는 YAML만 포함 됩니다 (메타 데이터, 종류 등은 포함 하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:IOChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"latency\",\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"etcd\"}},\"volumePath\":\"\/var\/run\/etcd\",\"path\":\"\/var\/run\/etcd\/**\/*\",\"delay\":\"100ms\",\"percent\":50,\"duration\":\"400s\"}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-time-faults"></a>AKS 비정상 메시 시간 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | TimeChaos-2.1 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | 비정상  [메시](https://chaos-mesh.org/docs/simulate-time-chaos-on-kubernetes/)를 사용 하 여 AKS 클러스터의 시스템 클록 변경을 발생 시킵니다. 분산 시스템에서 동기화가 발생 하는 AKS 인시던트, 누락/잘못 된 윤년/윤 초 논리 등의 결과를 다시 만드는 데 유용 합니다. |
| 필수 조건 | AKS 클러스터는 비정상 [메시를 배포](chaos-studio-tutorial-aks-portal.md)해야 합니다. |
| Urn | urn: csci: microsoft: azureKubernetesServiceChaosMesh: timeChaos/2.1 |
| 매개 변수 (키, 값) |  |
| jsonSpec | [TimeChaos kind](https://chaos-mesh.org/docs/simulate-time-chaos-on-kubernetes/#create-experiments-using-the-yaml-file)를 사용 하는 json으로 형식이 지정 되 고, ARM 템플릿, REST API 또는 Azure CLI를 통해 생성 된 경우 json 이스케이프 된 비정상 메시 사양을 사용 하 여 생성 됩니다. 이와 [같은 Yaml-json 변환기](https://www.convertjson.com/yaml-to-json.htm) 를 사용 하 여 비정상 메시 YAML을 json으로 변환 하 고, 미니 클래스를 표시 하 고, [이와 같은 json 문자열 이스케이프 도구](https://www.freeformatter.com/json-escape.html) 를 사용 하 여 json 사양을 이스케이프할 수 있습니다. "JsonSpec" 속성에는 YAML만 포함 됩니다 (메타 데이터, 종류 등은 포함 하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:timeChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"app1\"}},\"timeOffset\":\"-10m100ns\"}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-kernel-faults"></a>AKS 비정상 메시 커널 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | KernelChaos-2.1 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | [Chaos Mesh를](https://chaos-mesh.org/docs/simulate-kernel-chaos-on-kubernetes/) 통해 사용할 수 있는 커널 오류가 AKS 클러스터에 대해 실행되도록 합니다. 탑재 실패 또는 할당되지 않은 메모리와 같은 Linux 커널 수준 오류로 인해 AKS 인시던트 다시 만들기에 유용합니다. |
| 필수 조건 | AKS [클러스터에는 Chaos Mesh가 배포되어](chaos-studio-tutorial-aks-portal.md)있어야 합니다. |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:kernelChaos/2.1 |
| 매개 변수(키, 값) |  |
| jsonSpec | ARM 템플릿, REST API 또는 Azure CLI 통해 만들어진 JSON 형식의 JSON 이스케이프 Chaos Mesh 사양으로, [KernelChaos 종류를](https://chaos-mesh.org/docs/simulate-kernel-chaos-on-kubernetes/#configuration-file)사용합니다. 이와 같은 [YAML-JSON 변환기를](https://www.convertjson.com/yaml-to-json.htm) 사용하여 Chaos Mesh YAML을 JSON으로 변환하고 축소하고, [이와 같은 JSON 문자열 이스케이프 도구를](https://www.freeformatter.com/json-escape.html) 사용하여 JSON 사양을 이스케이프할 수 있습니다. "jsonSpec" 속성 아래에만 YAML을 포함합니다(메타데이터, 종류 등은 포함하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:kernelChaos/2.1",
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

## <a name="aks-chaos-mesh-http-faults"></a>AKS Chaos Mesh HTTP 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | HTTPChaos-2.1 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | [Chaos Mesh를](https://chaos-mesh.org/docs/simulate-http-chaos-on-kubernetes/) 통해 사용할 수 있는 HTTP 오류가 AKS 클러스터에 대해 실행되도록 합니다. 지연되거나 잘못된 응답과 같은 HTTP 요청 및 응답 처리 실패로 인한 인시던트 다시 만들기에 유용합니다. |
| 필수 조건 | AKS [클러스터에는 Chaos Mesh가 배포되어](chaos-studio-tutorial-aks-portal.md)있어야 합니다. |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:httpChaos/2.1 |
| 매개 변수(키, 값) |  |
| jsonSpec | ARM 템플릿, REST API 또는 Azure CLI 통해 만들어진 JSON 형식의 JSON 형식이며 [HTTPChaos 종류를](https://chaos-mesh.org/docs/simulate-http-chaos-on-kubernetes/#create-experiments)사용하는 JSON 이스케이프 Chaos Mesh 사양입니다. 이와 같은 [YAML-JSON 변환기를](https://www.convertjson.com/yaml-to-json.htm) 사용하여 Chaos Mesh YAML을 JSON으로 변환하고 축소하고, [이와 같은 JSON 문자열 이스케이프 도구를](https://www.freeformatter.com/json-escape.html) 사용하여 JSON 사양을 이스케이프할 수 있습니다. "jsonSpec" 속성 아래에만 YAML을 포함합니다(메타데이터, 종류 등은 포함하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:httpChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"all\",\"selector\":{\"labelSelectors\":{\"app\":\"nginx\"}},\"target\":\"Request\",\"port\":80,\"method\":\"GET\",\"path\":\"\/api\",\"abort\":true,\"duration\":\"5m\",\"scheduler\":{\"cron\":\"@every 10m\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-dns-faults"></a>AKS Chaos Mesh DNS 오류

| 속성 | 값 |
|-|-|
| 기능 이름 | DNSChaos-2.1 |
| 대상 형식 | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | [Chaos Mesh를](https://chaos-mesh.org/docs/simulate-dns-chaos-on-kubernetes/) 통해 사용할 수 있는 DNS 오류가 AKS 클러스터에 대해 실행되도록 합니다. DNS 오류로 인한 인시던트 다시 만들기에 유용합니다. |
| 필수 조건 | AKS [클러스터에는 Chaos Mesh가 배포되어](chaos-studio-tutorial-aks-portal.md) 있어야 하며 [DNS 서비스가 설치되어 있어야 합니다.](https://chaos-mesh.org/docs/simulate-dns-chaos-on-kubernetes/#deploy-chaos-dns-service) |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:dnsChaos/2.1 |
| 매개 변수(키, 값) |  |
| jsonSpec | ARM 템플릿, REST API 또는 Azure CLI 통해 만들어진 JSON 형식의 JSON 이스케이프 Chaos Mesh 사양으로, [DNSChaos 종류를](https://chaos-mesh.org/docs/simulate-dns-chaos-on-kubernetes/#create-experiments-using-the-yaml-file)사용합니다. 이와 같은 [YAML-JSON 변환기를](https://www.convertjson.com/yaml-to-json.htm) 사용하여 Chaos Mesh YAML을 JSON으로 변환하고 축소하고, [이와 같은 JSON 문자열 이스케이프 도구를](https://www.freeformatter.com/json-escape.html) 사용하여 JSON 사양을 이스케이프할 수 있습니다. "jsonSpec" 속성 아래에만 YAML을 포함합니다(메타데이터, 종류 등은 포함하지 않음). |

### <a name="sample-json"></a>JSON 샘플

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:dnsChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"random\",\"mode\":\"all\",\"patterns\":[\"google.com\",\"chaos-mesh.*\",\"github.?om\"],\"selector\":{\"namespaces\":[\"busybox\"]}}"
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
| Description | 기존 Azure 네트워크 보안 그룹 또는 Azure 네트워크 보안 그룹 집합에서 규칙을 조작하거나 만들 수 있습니다(규칙 정의가 보안 그룹 간 적용 가능하다고 가정). 다운스트림 또는 지역 간 종속성/비 종속성의 중단을 시뮬레이션하거나, 서비스 장애 조치(failover)를 강제하는 논리를 트리거해야 하는 이벤트를 시뮬레이션하거나, 모니터링 또는 상태 관리 서비스에서 작업을 트리거할 것으로 예상되는 이벤트를 시뮬레이션하거나, Chaos Agent를 배포할 수 없는 네트워크 트래픽을 차단하거나 허용하는 대안으로 유용합니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn:csci:microsoft:networkSecurityGroup:securityRule/1.0 |
| 매개 변수(키, 값) |  |
| name | 만들 보안 규칙의 고유한 이름입니다. 이름이 같은 NSG에 다른 규칙이 이미 있는 경우 오류가 실패합니다. 문자 또는 숫자로 시작하고, 문자, 숫자 또는 밑호로 끝나야 하며, 문자, 숫자, 밑호, 마침표 또는 하이픈만 포함할 수 있습니다. |
| protocol | 보안 규칙에 대한 프로토콜입니다. Any, TCP, UDP 또는 ICMP여야 합니다. |
| sourceAddresses | CIDR 형식 IP 주소의 json으로 구분된 배열을 나타내는 문자열입니다. 인바운드 규칙의 서비스 태그 이름(예: "AppService")일 수도 있습니다. 별표 '*'를 사용하여 모든 원본 IP를 일치시킬 수도 있습니다. |
| destinationAddresses | CIDR 형식 IP 주소의 json으로 구분된 배열을 나타내는 문자열입니다. 아웃바운드 규칙의 서비스 태그 이름(예: "AppService")일 수도 있습니다. 별표 '*'를 사용하여 모든 대상 IP를 일치시킬 수도 있습니다. |
| action | 보안 그룹 액세스 유형입니다. 허용 또는 거부여야 합니다. |
| destinationPortRanges | 80 또는 1024-65535와 같은 단일 포트 및/또는 포트 범위의 json으로 구분된 배열을 나타내는 문자열입니다. |
| sourcePortRanges | json으로 구분된 배열 단일 포트 및/또는 포트 범위(예: 80 또는 1024-65535)를 나타내는 문자열입니다. |
| priority | 네트워크 보안 그룹 내의 모든 보안 규칙에 대해 고유한 100에서 4096 사이의 값입니다. 우선 순위가 동일한 NSG에 다른 규칙이 이미 있는 경우 오류가 실패합니다. |
| direction | 보안 규칙의 영향을 받는 트래픽의 방향입니다. 인바운드 또는 아웃바운드여야 합니다. |

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

* 오류는 기존 네트워크 보안 그룹에만 적용할 수 있습니다.
* 트래픽을 거부하려는 NSG 규칙이 적용되는 경우 기존 연결은 4분 동안 **유휴 상태가** 될 때까지 끊어지지 않습니다. 한 가지 해결 방법은 NSG 오류가 적용될 때 기존 연결이 끊어지는 오류를 사용하는 동일한 단계에서 다른 분기를 추가하는 것입니다. 예를 들어 프로세스를 중지하거나, 서비스를 일시적으로 중지하거나, VM을 다시 시작하면 연결이 다시 설정됩니다.
* 규칙은 작업을 시작할 때 적용됩니다. 작업 기간 동안 규칙을 외부적으로 변경하면 실험이 실패합니다.
* 애플리케이션 보안 그룹 규칙을 만들거나 수정하는 것은 지원되지 않습니다.
* 우선 순위 값은 대상으로 지정된 각 NSG에서 고유해야 합니다. 우선 순위 값이 다른 규칙과 동일한 새 규칙을 만들려고 하면 실험이 실패합니다.

## <a name="azure-cache-for-redis-reboot"></a>다시 부팅 Azure Cache for Redis

| 속성 | 값 |
|-|-|
| 기능 이름 | Reboot-1.0 |
| 대상 형식 | Microsoft-AzureClusteredCacheForRedis |
| Description | 대상에서 강제 다시 부팅 작업이 수행되도록 하여 짧은 중단을 시뮬레이션합니다. |
| 사전 요구 사항 | 없음 |
| Urn | urn:csci:microsoft:azureClusteredCacheForRedis:reboot/1.0 |
| 오류 유형 | 불연속 |
| 매개 변수(키, 값) |  |
| rebootType | 다시 부팅 작업을 수행할 노드 유형으로 PrimaryNode, SecondaryNode 또는 AllNodes로 지정할 수 있습니다.  |
| shardId | 다시 부팅할 분할된 데이터베이스의 ID입니다.  |

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

* 다시 부팅 오류로 인해 강제 재부팅이 중단 이벤트를 더 잘 시뮬레이트합니다. 즉, 데이터 손실이 발생할 가능성이 있습니다.
* 재부팅 오류는 **불연속** 오류 유형입니다. 연속 오류와 달리 일회성 작업이므로 기간이 없습니다.
