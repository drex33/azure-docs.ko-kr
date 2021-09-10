---
title: NSG에 대한 흐름 로깅 소개
titleSuffix: Azure Network Watcher
description: 이 문서에서는 Azure Network Watcher의 NSG 흐름 로그 기능을 사용하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: 23960e112dd03a711027c2364f648f60f23d0c8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567005"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>네트워크 보안 그룹에 대한 흐름 로깅 소개

## <a name="introduction"></a>소개

[NSG](../virtual-network/network-security-groups-overview.md#security-rules)(네트워크 보안 그룹) 흐름 로그는 NSG를 통과하는 IP 트래픽에 대한 정보를 로깅할 수 있는 Azure Network Watcher의 기능입니다. 흐름 데이터는 액세스할 수 있는 위치에서 Azure Storage 계정으로 전송되며 원하는 시각화 도구, SIEM 또는 IDS로 내보낼 수 있습니다.

![흐름 로그 개요](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>흐름 로그를 사용하는 이유

보안, 규정 준수 및 성능이 저하되지 않도록 네트워크를 모니터링하고 관리하고 파악하기 위해 필요합니다. 네트워크를 보호하고 최적화하는 데 있어서 가장 중요한 것은 사용자가 자신의 정확히 아는 것입니다. 네트워크의 현재 상태, 연결 중인 사용자 및 연결되는 위치, 인터넷에 대해 열려 있는 포트, 예상되는 네트워크 동작, 비정상적인 네트워크 동작, 갑작스런 트래픽 증가에 대해 알아야 하는 경우가 종종 있습니다.

흐름 로그는 클라우드 환경에서 모든 네트워크 활동의 진위를 보여 주는 요소입니다. 리소스를 최적화하려는 신생기업이나 침입을 탐지하고자 하는 대기업에게 흐름 로그는 최상의 방법입니다. 네트워크 흐름 최적화, 처리량 모니터링, 규정 준수 확인, 침입 탐지 등에 사용할 수 있습니다.

## <a name="common-use-cases"></a>일반 사용 예

**네트워크 모니터링**: 알 수 없거나 원치 않는 트래픽을 식별합니다. 트래픽 수준과 대역폭 사용량을 모니터링합니다. IP와 포트로 흐름 로그를 필터링하여 애플리케이션 동작을 이해합니다. 흐름 로그를 선택한 분석 및 시각화 도구로 내보내어 모니터링 대시보드를 설정합니다.

**사용량 모니터링 및 최적화:** 네트워크에서 상위 토커를 식별합니다. GeoIP 데이터와 결합하여 하위 지역 간 트래픽을 식별합니다. 용량 예측을 위한 트래픽 증가를 이해합니다. 데이터를 사용하여 명백하게 제한적인 트래픽 규칙을 제거합니다.

**규정 준수**: 흐름 데이터를 사용하여 네트워크 격리 및 엔터프라이즈 액세스 규칙의 준수 상태를 확인합니다.

**네트워크 포렌식 및 보안 분석**: 손상된 IP 및 네트워크 인터페이스에서 네트워크 흐름을 분석합니다. 흐름 로그를 선택한 SIEM 또는 IDS 도구로 내보냅니다.

## <a name="how-logging-works"></a>로깅 작동 방법

**주요 속성**

- 흐름 로그는 [Layer 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer)에서 작동하고 NSG에서 들어오고 나가는 모든 IP 흐름을 기록합니다.
- 로그는 Azure 플랫폼을 통해 **1분 간격** 으로 수집되며 고객 리소스나 네트워크 성능에 영향을 주지 않습니다.
- 로그는 JSON 형식으로 작성되며, NSG 규칙에 따라 아웃바운드와 인바운드 흐름을 표시합니다.
- 각 로그 기록에는 흐름이 적용되는 NIC(네트워크 인터페이스), 5-튜플 정보, 트래픽 결정 및 처리량 정보(버전 2에만 해당)가 포함됩니다. 전체 세부 정보는 아래 _로그 형식_ 을 참조하세요.
- 흐름 로그에는 생성 후 최대 1년까지 로그를 자동으로 삭제할 수 있는 보존 기능이 있습니다. 

> [!NOTE]
> 보존은 [범용 v2 스토리지 계정(GPv2)](../storage/common/storage-account-overview.md#types-of-storage-accounts)을 사용하는 경우에만 가능합니다. 

**핵심 개념**

- 소프트웨어 정의 네트워크는 VNet(가상 네트워크)과 서브넷을 중심으로 구성됩니다. VNet과 서브넷 보안은 NSG를 사용하여 관리할 수 있습니다.
- NSG(네트워크 보안 그룹)에는 연결된 리소스에 대해 네트워크 트래픽을 허용하거나 거부하는 _보안 규칙_ 목록이 포함됩니다. NSG는 서브넷, 개별 VM 또는 VM(Resource Manager)에 연결된 개별 NIC(네트워크 인터페이스)와 연결할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹 개요](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)를 참조하세요.
- 네트워크의 모든 트래픽 흐름은 해당 NSG의 규칙을 사용하여 평가합니다.
- 평가 결과는 NSG 흐름 로그입니다. 흐름 로그는 Azure 플랫폼을 통해 수집하며, 고객 리소스를 변경할 필요가 없습니다.
- 참고: 규칙에는 종료 형식과 미종료 형식이 있으며, 각 형식은 서로 다른 로깅 동작을 가집니다.
- - NSG 거부 규칙은 종료 형식입니다. 트래픽을 거부하는 NSG는 이를 흐름 로그에 기록하고, 모든 NSG가 트래픽을 거부한 후 처리는 중지됩니다. 
- - NSG 허용 규칙은 미종료 형식으로, 하나의 NSG에서 허용하는 경우에도 다음 NSG에서 처리가 계속됩니다. 트래픽을 허용하는 마지막 NSG는 흐름 로그에 대한 트래픽을 기록합니다.
- NSG 흐름 로그는 액세스할 수 있는 위치에서 스토리지 계정에 기록됩니다.
- 트래픽 분석, Splunk, Grafana, Stealthwatch 등의 도구를 사용하여 흐름 로그를 내보내고, 처리하고, 분석하고, 시각화할 수 있습니다.

## <a name="log-format"></a>로그 형식

흐름 로그에는 다음 속성이 포함됩니다.

* **시간** - 이벤트가 로그된 시간
* **systemId** -네트워크 보안 그룹 시스템 ID입니다.
* **범주** - 이벤트의 범주입니다. 범주는 항상 **NetworkSecurityGroupFlowEvent** 입니다.
* **resourceid** - NSG의 리소스 ID
* **operationName** - 항상 NetworkSecurityGroupFlowEvents
* **속성** - 흐름의 속성 컬렉션
    * **버전** - 흐름 로그 이벤트 스키마의 버전 번호
    * **흐름** - 흐름의 컬렉션입니다. 이 속성에는 서로 다른 규칙에 대한 여러 항목이 있습니다.
        * **규칙** - 흐름이 나열된 규칙
            * **흐름** - 흐름의 컬렉션
                * **mac** - 흐름이 수집된 VM에 대한 NIC의 MAC 주소
                * **flowTuples** - 쉼표로 구분된 형식에서 흐름 튜플에 대한 여러 속성을 포함하는 문자열
                    * **타임스탬프** - 흐름이 UNIX epoch 형식으로 일어날 경우에 대한 타임스탬프입니다.
                    * **원본 IP** - 원본 IP
                    * **대상 IP** - 대상 IP
                    * **원본 포트** - 원본 포트
                    * **대상 포트** - 대상 포트
                    * **프로토콜** - 흐름의 프로토콜입니다. 유효한 값은 TCP에 대해서 **T** 이며 UDP에 대해서 **U** 입니다.
                    * **트래픽 흐름** - 트래픽 흐름의 방향입니다. 유효한 값은 인바운드에 대해서 **I** 이며 아웃바운드에 대해서 **O** 입니다.
                    * **트래픽 의사 결정** - 트래픽이 허용되었는지 또는 거부되었는지 여부입니다. 유효한 값은 허용에 대해 **A** 이며 거부에 대해 **D** 입니다.
                    * **흐름 상태 - 버전 2만** - 흐름의 상태를 캡처합니다. 가능한 상태는 다음과 같습니다. **B**: 흐름이 만들어질 때 시작합니다. 통계가 제공되지 않습니다. **C**: 지속적인 흐름에 대해 계속됩니다. 통계가 5분 간격으로 제공됩니다. **E**: 흐름이 종료되면 끝납니다. 통계가 제공됩니다.
                    * **패킷 - 원본에서 대상으로 - 버전 2만** 마지막 업데이트 이후 원본에서 대상으로 전송된 TCP 패킷의 총 수입니다.
                    * **전송된 바이트 - 원본에서 대상으로 - 버전 2만** 마지막 업데이트 이후 원본에서 대상으로 전송된 TCP 패킷 바이트의 총 수입니다. 패킷 바이트에는 패킷 헤더 및 페이로드가 포함됩니다.
                    * **패킷 - 대상에서 원본으로 - 버전 2만** 마지막 업데이트 이후 대상에서 원본으로 전송된 TCP UDP 패킷의 총 수입니다.
                    * **전송된 바이트 - 대상에서 원본으로 - 버전 2만** 마지막 업데이트 이후 대상에서 원본으로 전송된 TCP 패킷 바이트의 총 수입니다. 패킷 바이트에는 패킷 헤더 및 페이로드가 포함됩니다.


**NSG 흐름 로그 버전 2(버전 1과 비교)** 

로그의 버전 2에는 흐름 상태가 추가되었습니다. 수신하는 흐름 로그 버전을 구성할 수 있습니다.

흐름이 시작되면 흐름 상태 _B_ 가 기록됩니다. 흐름 상태 _C_ 및 흐름 상태 _E_ 는 각각 흐름의 연속 및 흐름 종료를 나타내는 상태입니다. _C_ 및 _E_ 상태 둘 다 트래픽 대역폭 정보를 포함합니다.

### <a name="sample-log-records"></a>샘플 로그 레코드

다음 텍스트는 흐름 로그의 예제입니다. 이전 섹션에 설명된 속성 목록을 따르는 여러 레코드를 볼 수 있습니다.

> [!NOTE]
> *flowTuples* 속성의 값은 쉼표로 구분된 목록입니다.
 
**버전 1 NSG 흐름 로그 형식 샘플**
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        
        
```
**버전 2 NSG 흐름 로그 형식 샘플**
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        }
        
```
**로그 튜플 설명**

![흐름 로그 튜플](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**샘플 대역폭 계산**

185.170.185.105:35370과 10.2.0.4:23 간 TCP 대화의 흐름 튜플:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

연속 _C_ 및 종료 _E_ 흐름 상태의 경우, 바이트 및 패킷 수는 이전 흐름 튜플 레코드 시간으로부터의 집계 개수입니다. 이전 예제 대화를 참조할 때 전송된 패킷의 총 수는 1021+52+8005+47 = 9125가 됩니다. 전송된 바이트의 총 수는 588096+29952+4610880+27072 = 5256000입니다.


## <a name="enabling-nsg-flow-logs"></a>NSG 흐름 로그 사용

흐름 로그 사용에 대한 지침을 보려면 아래의 링크를 참조하세요.

- [Azure Portal](./network-watcher-nsg-flow-logging-portal.md)
- [PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [CLI](./network-watcher-nsg-flow-logging-cli.md)
- [REST (영문)](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>매개 변수 업데이트

**Azure Portal**

Azure Portal에서 Network Watcher의 NSG 흐름 로그 섹션으로 이동합니다. 그런 다음 NSG의 이름을 클릭합니다. 그러면 흐름 로그에 대한 설정 창이 표시됩니다. 원하는 매개 변수를 변경하고 **저장** 을 눌러 변경 내용을 배포합니다.

**PS/CLI/REST/ARM**

명령줄 도구를 통해 매개 변수를 업데이트하려면 앞에서 흐름 로그에 사용한 것과 같은 명령을 변경하려는 매개 변수에 사용합니다.

## <a name="working-with-flow-logs"></a>흐름 로그 작업

*흐름 로그 읽기 및 내보내기*

- 포털에서 흐름 로그 [다운로드&amp;하기](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [PowerShell 함수를 사용하여 흐름 로그 읽기](./network-watcher-read-nsg-flow-logs.md)
- [NSG 흐름 로그를 Splunk로 내보내기](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

흐름 로그는 NSG를 대상으로 하지만 다른 로그와 동일하게 표시되지 않습니다. 흐름 로그는 스토리지 계정 내에만 저장되며 다음 예제와 같이 로깅 경로를 따릅니다.

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*흐름 로그 시각화*

- [Azure 트래픽 분석](./traffic-analytics.md)은 흐름 로그를 처리하고, 인사이트를 추출하고, 흐름 로그를 시각화하는 Azure Native Service입니다. 
- [[자습서] Power BI를 사용하여 NSG 흐름 로그 시각화하기](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [[자습서] 탄력적 스택을 사용하여 NSG 흐름 로그 시각화하기](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [[자습서] Grafana를 사용하여 NSG 흐름 로그 관리 및 분석하기](./network-watcher-nsg-grafana.md)
- [[자습서] Graylog를 사용하여 NSG 흐름 로그 관리 및 분석하기](./network-watcher-analyze-nsg-flow-logs-graylog.md)

*흐름 로그를 사용하지 않도록 설정*

흐름 로그를 사용하지 않도록 설정하면 연결된 NSG에 대한 흐름 로깅이 중지됩니다. 그러나 리소스로서의 흐름 로그는 모든 설정 및 연결과 함께 계속 존재합니다. 구성된 NSG에서 흐름 로깅을 시작할 때마다 사용하도록 설정할 수 있습니다. 흐름 로그의 사용/사용 안 함 단계는 [이 가이드](./network-watcher-nsg-flow-logging-powershell.md)에서 찾을 수 있습니다.  

*흐름 로그 삭제*

흐름 로그가 삭제되면 연결된 NSG에 대한 흐름 로깅이 중지될 뿐만 아니라 해당 설정 및 연결을 통해 흐름 로그 리소스도 삭제됩니다. 흐름 로깅을 다시 시작하려면 해당 NSG에 대한 새 흐름 로그 리소스를 만들어야 합니다. 흐름 로그는 [PowerShell](/powershell/module/az.network/remove-aznetworkwatcherflowlog), [CLI](/cli/azure/network/watcher/flow-log#az_network_watcher_flow_log_delete) 또는 [REST API](/rest/api/network-watcher/flowlogs/delete)를 사용하여 삭제할 수 있습니다. Azure Portal에서 흐름 로그 삭제에 대한 지원은 파이프라인에 있습니다.    

또한 NSG가 삭제되면 기본적으로 연결된 흐름 로그 리소스가 삭제됩니다.

> [!NOTE]
> NSG를 다른 리소스 그룹 또는 구독으로 이동하려면 연결된 흐름 로그를 삭제해야 합니다. 흐름 로그를 사용하지 않도록 설정하면 작동하지 않습니다. NSG를 마이그레이션한 후 흐름 로그를 다시 만들어 흐름 로깅을 사용하도록 설정해야 합니다.  

## <a name="nsg-flow-logging-considerations"></a>NSG 흐름 로깅 고려 사항

**Storage 계정 고려 사항**: 

- 위치: 스토리지 계정은 NSG와 동일한 하위 지역에 있어야 합니다.
- 성능 계층: 현재는 표준 계층 스토리지 계정만 지원합니다.
- 키 회전 직접 관리: 스토리지 계정에 대한 액세스 키를 변경하거나 회전하는 경우 NSG 흐름 로그의 작동이 중지됩니다. 문제를 해결하려면 NSG 흐름 로그를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.

**흐름 로깅 비용**: NSG 흐름 로그는 생성된 로그 양에 따라 요금이 청구됩니다. 트래픽 볼륨이 많으면 흐름 로그 볼륨과 관련 비용도 증가할 수 있습니다. NSG 흐름 로그의 가격에는 스토리지의 기본 비용이 포함되지 않습니다. NSG 흐름 로깅에서 보존 정책 기능을 사용하면 해당 기간 동안 별도의 스토리지 비용이 발생됩니다. 데이터를 영구적으로 보존하기 원하지만 보존 정책을 적용하고 싶지는 않은 경우, 보존(일)을 0으로 설정합니다. 추가 세부 정보는 [Network Watcher 가격 책정](https://azure.microsoft.com/pricing/details/network-watcher/) 및 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

**사용자 정의 인바운드 TCP 규칙** 에 대한 문제: [NSG](../virtual-network/network-security-groups-overview.md)(네트워크 보안 그룹)는 [상태 저장 방화벽](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true)으로 구현됩니다. 그러나 현재는 플랫폼 제한으로 인해 인바운드 TCP 흐름에 영향을 주는 사용자 정의 규칙은 상태 비저장 방식으로 구현됩니다. 이로 인해 사용자 정의 인바운드 규칙의 영향을 받는 흐름은 종료되지 않습니다. 해당 흐름에 대해서는 바이트 및 패킷 수가 추가로 기록되지 않습니다. 따라서 NSG 흐름 로그(및 트래픽 분석)에서 보고되는 바이트와 패킷 수는 실제와 다를 수 있습니다. 해당 문제를 해결하는 옵트인 플래그는 늦어도 2021년 6월부터 사용할 수 있습니다. 그러는 사이에 해당 동작으로 인해 심각한 문제를 겪고 있는 고객은 옵트인 지원을 요청할 수 있습니다. Network Watcher > NSG 흐름 로그에서 지원을 요청하세요.  

**인터넷 IP에서 공용 IP가 없는 VM으로 로그인하는 인바운드 흐름**: 인스턴스 수준 공용 IP로서 NIC와 연결된 공용 IP 주소를 통해 할당된 공용 IP 주소가 없거나, 기본 부하 분산 장치 백 엔드 풀의 일부인 VM에 대해서는 [기본 SNAT](../load-balancer/load-balancer-outbound-connections.md)를 사용하고 Azure에서 할당된 IP 주소를 통해 아웃바운드 연결을 지원하도록 합니다. 따라서 SNAT에 할당된 포트 범위의 포트로 흐름을 보내는 경우, 인터넷 IP 주소에서 흐름 로그 항목을 확인할 수 있습니다. Azure는 VM에 대한 흐름을 허용하지 않지만, 해당 시도를 기록하고 Network Watcher의 NSG 흐름 로그에 기본적으로 표시됩니다. NSG를 사용하여 원치 않는 인바운드 인터넷 트래픽을 명시적으로 차단하는 것이 좋습니다.

**ExpressRoute 게이트웨이 서브넷의 NSG** – 트래픽이 ExpressRoute 게이트웨이(예: [FastPath](../expressroute/about-fastpath.md))를 무시할 수 있으므로 ExpressRoute 게이트웨이 서브넷에서 흐름을 기록하지 않는 것이 좋습니다. 따라서 NSG가 ExpressRoute 게이트웨이 서브넷에 연결되어 있고 NSG 흐름 로그가 사용하도록 설정된 경우 가상 머신에 대한 아웃바운드 흐름이 캡처되지 않을 수 있습니다. 이러한 흐름은 VM의 서브넷 또는 NIC에서 캡처해야 합니다. 

**Application Gateway V2 서브넷 NSG에 대한 문제**: 현재는 Application Gateway V2 서브넷 NSG의 흐름 로깅이 [지원되지 않습니다.](../application-gateway/application-gateway-faq.yml#are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet) 해당 문제는 V1 Application Gateway에 영향을 주지 않습니다.

**호환되지 않는 서비스**: 현재는 플랫폼 제한으로 인해 Azure 서비스의 일부가 NSG 흐름 로그에서 지원되지 않습니다. 현재 호환되지 않는 서비스 목록은 다음과 같습니다.
- [ACI(Azure Container Instances)](https://azure.microsoft.com/services/container-instances/)
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>모범 사례

**중요한 VNet/서브넷에 대한 설정**: 흐름 로그는 감사 기능 및 보안 모범 사례로, 모든 중요한 VNet/서브넷에서 사용하도록 설정해야 합니다. 

**리소스에 연결된 모든 NSG에 대해 NSG 흐름 로깅을 사용하도록 설정**: Azure의 흐름 로깅은 NSG 리소스에 구성됩니다. 하나의 흐름은 하나의 NSG 규칙에만 연결됩니다. 여러 NSG를 활용하는 시나리오에서는 리소스의 서브넷 또는 네트워크 인터페이스가 적용된 모든 NSG에서 NSG 흐름 로깅을 사용하도록 설정하여 모든 트래픽을 기록하는 것이 좋습니다. 자세한 내용은 네트워크 보안 그룹에서 [트래픽 평가 방법](../virtual-network/network-security-group-how-it-works.md) 을 참조하세요. 

몇 가지 일반적인 시나리오:
1. **VM의 다중 NIC**: 가상 머신에 여러 NIC를 연결하는 경우 모든 NIC에서 흐름 로깅을 사용하도록 설정해야 합니다.
1. **NIC 및 서브넷 수준에서의 NSG 설정**: NSG가 NIC 및 서브넷 수준에서 구성된 경우에는 두 NSG에서 흐름 로깅을 사용하도록 설정해야 합니다. 
1. **AKS 클러스터 서브넷**: AKS는 클러스터 서브넷에 기본 NSG를 추가합니다. 위의 지점에서 설명한 대로 이 기본 NSG에서 흐름 로깅을 사용하도록 설정해야 합니다.

**스토리지 프로비저닝**: 스토리지는 예상 흐름 로그 볼륨이 포함된 튜닝에서 프로비저닝되어야 합니다.

**이름 지정**: NSG 이름은 80자 이하여야 하고 NSG 규칙 이름은 65자까지 사용할 수 있습니다. 이름이 문자 수 제한을 초과하는 경우 로깅 중에 잘릴 수 있습니다.

## <a name="troubleshooting-common-issues"></a>일반적인 문제 해결

**NSG 흐름 로그를 사용하도록 설정할 수 없습니다.**

- **Microsoft.Insights** 리소스 공급자가 등록되지 않았습니다.

_Authorizationfailed_ 또는 _GatewayAuthenticationFailed_ 오류를 받은 경우 구독에서 Microsoft Insights 리소스 공급자를 사용하도록 설정하지 않았을 수 있습니다. [지침에 따라](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider) Microsoft Insights 공급자를 사용하도록 설정하세요.

**NSG 흐름 로그를 사용하도록 설정했지만 스토리지 계정에 데이터가 표시되지 않습니다.**

- **설치 시간**

스토리지 계정에 NSG 흐름 로그가 표시되는 데 최대 5분이 걸릴 수 있습니다(올바르게 구성된 경우). [여기에 설명된 대로](./network-watcher-nsg-flow-logging-portal.md#download-flow-log) 액세스할 수 있는 PT1H.json이 나타납니다.

- **NSG에 트래픽이 없음**

경우에 따라 VM이 활성화되어 있지 않거나 App Gateway의 업스트림 필터 또는 NSG에 대한 트래픽을 차단하는 기타 디바이스 때문에 로그가 표시되지 않습니다.

**NSG 흐름 로그를 자동화하려고 함**

이제 ARM 템플릿을 통한 자동화 지원을 NSG 흐름 로그에 사용할 수 있습니다. 자세한 내용은 [기능 공지](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) 및 [ARM 템플릿에서 빠른 시작 문서](quickstart-configure-network-security-group-flow-logs-from-arm-template.md)를 참조하세요.

## <a name="faq"></a>FAQ

**NSG 흐름 로그는 무엇을 하나요?**

[NSG(네트워크 보안 그룹)](../virtual-network/network-security-groups-overview.md)를 통해 Azure 네트워크 리소스를 결합하고 관리할 수 있습니다. NSG 흐름 로그를 사용하여 모든 트래픽의 5-튜플 흐름 정보를 기록할 수 있습니다. 원시 흐름 로그는 필요에 따라 추가로 처리, 분석, 쿼리 또는 내보낼 수 있는 Azure Storage 계정에 기록됩니다.

**흐름 로그의 사용은 네트워크 대기 시간 또는 성능에 영향을 미치나요?**

흐름 로그 데이터는 네트워크 트래픽 경로 외부에서 수집되므로 네트워크 처리량 또는 대기 시간에 영향을 주지 않습니다. 네트워크 성능에 영향을 미치지 않고 흐름 로그를 만들거나 삭제할 수 있습니다.

**방화벽이 있는 스토리지 계정에서 NSG 흐름 로그를 어떻게 사용하나요?**

방화벽이 있는 스토리지 계정을 사용하려면 스토리지 계정에 액세스하기 위해 신뢰할 수 있는 Microsoft 서비스에 대한 예외 설정을 해야 합니다.

- 포털의 전체 검색란 또는 [스토리지 계정 페이지](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)에서 스토리지 계정 이름을 입력하여 스토리지 계정으로 이동합니다.
- **설정** 섹션에서 **방화벽 및 가상 네트워크** 를 선택합니다.
- **다음의 액세스를 허용** 에서 **선택한 네트워크** 를 선택합니다. 그런 다음 **예외** 에서 ****신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용합니다**** 옆의 확인란을 선택합니다.
- 이미 선택되어 있는 경우에는 변경할 필요가 없습니다.
- [NSG 흐름 로그 개요 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)에서 대상 NSG를 찾고, 위의 스토리지 계정을 선택하여 NSG 흐름 로그를 사용하도록 설정합니다.

몇 분 후에 스토리지 로그를 확인할 수 있습니다. 그러면 업데이트된 타임스탬프 또는 새로 만든 JSON 파일이 표시됩니다.

**서비스 엔드포인트가 있는 스토리지 계정으로 NSG 흐름 로그를 어떻게 사용하나요?**

NSG 흐름 로그는 별도의 추가 구성 없이 서비스 엔드포인트와 호환됩니다. 가상 네트워크에서 [서비스 엔드포인트를 사용하도록 설정하는 방법에 대한 자습서](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint)를 참조하세요.

**흐름 로그 버전 1과 버전 2의 차이는 무엇인가요?**

흐름 로그 버전 2에서는 _흐름 상태_ 개념이 추가되었고 전송되는 바이트와 패킷 정보를 저장하기도 합니다. [자세히 알아보기](#log-format)

## <a name="pricing"></a>가격 책정

NSG 흐름 로그는 수집된 로그에 대해 GB 당 요금으로 청구되며, 구독 시 월 5GB가 기본 제공됩니다. 현재 해당 하위 지역의 가격은 [Network Watcher 가격 책정 페이지](https://azure.microsoft.com/pricing/details/network-watcher/)를 참조하세요.

로그의 스토리지 비용은 별도로 청구됩니다. 관련 가격은 [Azure Storage 블록 Blob 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.
