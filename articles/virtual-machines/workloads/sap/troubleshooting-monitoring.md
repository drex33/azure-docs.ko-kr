---
title: Azure의 SAP HANA(대규모 인스턴스) 모니터링 | Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스) 모니터링에 관해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/23/2021
ms.author: madhukan
ms.custom:
- H1Hack27Feb2017
- contperf-fy21q4
ms.openlocfilehash: 4d0977cd408c0a8fdcff16ad01c4c72203e72c40
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969717"
---
# <a name="monitor-sap-hana-large-instances-on-azure"></a>Azure의 SAP HANA(대규모 인스턴스) 모니터링

이 문서에서는 Azure의 SAP HANA(대규모 인스턴스)(BareMetal Infrastructure라고도 함)를 모니터링하는 방법을 살펴봅니다.

Azure의 SAP HANA(대규모 인스턴스)는 다른 IaaS 배포와 다르지 않습니다. 운영 체제와 애플리케이션을 모니터링하는 것이 중요합니다. 애플리케이션에서 다음 리소스를 사용하는 방법을 알아 두세요.

- CPU
- 메모리
- 네트워크 대역폭
- 디스크 공간

SAP HANA(대규모 인스턴스)를 모니터링하여 위 리소스가 충분한지 또는 고갈되었는지 여부를 확인합니다. 다음 섹션에서는 이러한 리소스를 각각 자세히 설명합니다.

## <a name="cpu-resource-consumption"></a>CPU 리소스 사용량

SAP는 SAP HANA 워크로드의 최대 CPU 사용 임계값을 정의합니다. 이 임계값 내에서 유지하면 메모리에 저장된 데이터를 사용하는 데 충분한 CPU 리소스가 확보됩니다. 높은 CPU 사용량은 SAP HANA 서비스에서 쿼리를 실행할 때 누락된 인덱스나 비슷한 문제로 인해 발생할 수 있습니다. 따라서 HANA(대규모 인스턴스)의 CPU 사용량과 특정 HANA 서비스의 CPU 사용량을 모니터링하는 것이 중요합니다.

## <a name="memory-consumption"></a>메모리 사용량 

HANA 내에서 및 SAP HANA(대규모 인스턴스)의 HANA 외부에서 둘 다 메모리 사용량을 모니터링하는 것이 중요합니다. SAP의 크기 조정 지침 내에서 유지할 수 있도록 데이터가 사용하는 HANA 할당 메모리 양을 모니터링합니다. 대규모 인스턴스에서 메모리 사용량을 모니터링하여 HANA 이외 소프트웨어에서 너무 많은 메모리를 사용하지 않는지 확인합니다. HANA 이외 소프트웨어가 메모리를 사용하기 위해 HANA와 경쟁하지 않기를 원합니다.

## <a name="network-bandwidth"></a>네트워크 대역폭 

Azure VNet(Virtual Network) 게이트웨이의 대역폭은 제한되어 있습니다. 제한된 데이터만 Azure VNet으로 이동할 수 있습니다. VNet 내의 모든 Azure VM이 수신한 데이터를 모니터링합니다. 이렇게 하면 선택한 Azure 게이트웨이 SKU의 한도에 가까울 때 이를 알 수 있습니다. 또한 HANA(대규모 인스턴스)에서 들어오고 나가는 네트워크 트래픽을 모니터링하여 시간이 지남에 따라 처리된 볼륨을 추적하는 것이 좋습니다.

## <a name="disk-space"></a>디스크 공간

디스크 공간 사용량은 일반적으로 시간이 지남에 따라 증가합니다. 일반적인 원인은 다음과 같습니다.
- 시간이 지남에 따라 데이터 볼륨이 증가함
- 트랜잭션 로그 백업 실행
- 추적 파일 저장
- 스토리지 스냅샷 만들기 

따라서 디스크 공간 사용량을 모니터링하고 HANA(대규모 인스턴스)와 연결된 디스크 공간을 관리하는 것이 중요합니다.

## <a name="preloaded-system-diagnostic-tools"></a>미리 로드된 시스템 진단 도구

HANA 대규모 인스턴스의 **유형 II SKU** 에서 서버에는 미리 로드된 시스템 진단 도구가 포함됩니다. 해당 진단 도구를 사용하여 시스템 상태 검사를 수행할 수 있습니다.
 
다음 명령을 실행하여 /var/log/health_check에서 상태 검사 로그 파일을 생성합니다.

```
/opt/sgi/health_check/microsoft_tdi.sh
```
Microsoft 지원 팀과 함께 문제를 해결할 경우 이 진단 도구를 사용하여 로그 파일을 제공하도록 요청될 수 있습니다. 다음 명령을 사용하여 파일을 zip으로 압축할 수 있습니다.

```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

## <a name="azure-monitor-for-sap-solutions"></a>SAP 솔루션을 위한 Azure Monitor

SAP 솔루션을 위한 Azure Monitor를 사용하여 위에 나열된 모든 리소스 등을 모니터링할 수 있습니다. SAP 솔루션을 위한 Azure Monitor는 Azure에 기본 제공됩니다. 이를 사용하여 Azure 인프라 및 데이터베이스에서 단일 위치로 데이터를 수집하고 더 빠른 문제 해결을 위해 시각적으로 데이터에 상관 관계를 지정할 수 있습니다. 자세한 내용은 [Azure의 SAP 모니터링](../../../virtual-machines/workloads/sap/monitor-sap-on-azure.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

SAP HANA 내에서 모니터링하고 문제를 해결하는 방법에 관해 알아봅니다.

> [!div class="nextstepaction"]
> [HANA 쪽에서 모니터링 및 문제 해결](hana-monitor-troubleshoot.md)
