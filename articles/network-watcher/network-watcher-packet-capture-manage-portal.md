---
title: 패킷 캡처 관리 - Azure Portal
titleSuffix: Azure Network Watcher
description: Azure Portal을 사용하여 Network Watcher의 패킷 캡처 기능을 관리하는 방법을 알아봅니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: d4112bd1d632ec21e2d8cb565027277eeb85452d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018262"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>포털에서 Azure Network Watcher를 사용하여 패킷 캡처 관리

Network Watcher 패킷 캡처를 사용하면 가상 머신 간에 트래픽을 추적하는 캡처 세션을 만들 수 있습니다. 원하는 트래픽만 캡처할 수 있도록 캡처 세션에 대 한 필터가 제공됩니다. 패킷 캡처를 통해 사후 및 사전 대응적으로 네트워크 예외를 진단할 수 있습니다. 또한 네트워크 침입에 대한 정보를 가져오는 네트워크 통계를 수집하는 것을 포함하여 클라이언트 서버 간 통신을 디버그할 수 있습니다. 원격으로 패킷 캡처를 트리거할 수 있으면 원하는 가상 머신에서 수동으로 패킷 캡처를 실행하는 부담이 없어지고 시간이 단축됩니다.

이 문서에서는 패킷 캡처를 시작, 중지, 다운로드 및 삭제하는 방법을 알아봅니다. 

## <a name="before-you-begin"></a>시작하기 전에

패킷 캡처에는 다음과 같은 아웃바운드 TCP 연결이 필요합니다.
- 포트 443을 통해 선택한 스토리지 계정으로
- 포트 80을 통해 169.254.169.254로
- 포트 8037을 통해 168.63.129.16으로

> [!NOTE]
> 위의 두 후자에서 설명된 포트는 Network Watcher 확장을 포함하는 모든 Network Watcher 기능에서 공통적이며 때때로 변경될 수 있습니다.


네트워크 보안 그룹이 네트워크 인터페이스에 연결되거나 네트워크 인터페이스가 포함된 서브넷에 연결되어 있으면 이전 포트를 허용하는 규칙이 있는지 확인합니다. 마찬가지로 네트워크에 사용자 정의 트래픽 경로를 추가하면 위에서 언급한 IP와 포트에 연결하지 못할 수도 있으므로 연결할 수 있는지 확인합니다. 

## <a name="start-a-packet-capture"></a>패킷 캡처 시작

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동하고, **모든 서비스** 를 선택한 후, **네트워킹 섹션** 에서 **Network Watcher** 를 선택합니다.
2. **네트워크 진단 도구** 에서 **패킷 캡처** 를 선택합니다. 기존 패킷 캡처는 상태에 관계없이 나열됩니다.
3. **추가** 를 선택하여 패킷 캡처를 만듭니다. 다음 속성의 값을 선택할 수 있습니다.
   - **구독**: 패킷 캡처를 만들려는 가상 머신이 포함된 구독입니다.
   - **리소스 그룹**: 가상 머신의 리소스 그룹입니다.
   - **대상 가상 머신**: 패킷 캡처를 만들려는 가상 머신입니다.
   - **패킷 캡처 이름**: 패킷 캡처의 이름입니다.
   - **스토리지 계정 또는 파일**: **스토리지 계정**, **파일** 또는 둘 다 선택합니다. **파일** 을 선택하면 캡처가 가상 머신 내의 경로에 기록됩니다.
   - **로컬 파일 경로**: 패킷 캡처가 저장될 가상 머신의 로컬 경로입니다(‘파일’을 선택했을 때만 유효). 경로는 유효한 경로여야 합니다. Linux 가상 머신을 사용하는 경우 경로는 */var/captures* 로 시작해야 합니다.
   - **스토리지 계정**: *스토리지 계정* 을 선택한 경우 기존 스토리지 계정을 선택합니다. **Storage** 를 선택한 경우에만 이 옵션을 사용할 수 있습니다.
   
     > [!NOTE]
     > Premium Storage 계정에서는 패킷 캡처 저장이 현재 지원되지 않습니다.

   - **패킷당 최대 바이트**: 캡처된 각 패킷의 바이트 수입니다. 비워 두면 모든 바이트가 캡처됩니다.
   - **세션당 최대 바이트**: 캡처된 총 바이트 수입니다. 값에 도달하면 패킷 캡처가 중지됩니다.
   - **시간 제한(초)**: 패킷 캡처가 중지되기 전의 시간 제한입니다. 기본값은 18,000초입니다.
   - 필터링(선택 사항). **+ 필터 추가** 를 선택합니다.
     - **프로토콜**: 패킷 캡처에 대해 필터링할 프로토콜입니다. 사용 가능한 값은 TCP, UDP 및 모두입니다.
     - **로컬 IP 주소**: 로컬 IP 주소가 이 필터 값과 일치하는 패킷으로 패킷 캡처를 필터링합니다.
     - **로컬 포트**: 로컬 포트가 이 필터 값과 일치하는 패킷으로 패킷 캡처를 필터링합니다.
     - **원격 IP 주소**: 원격 IP 주소가 이 필터 값과 일치하는 패킷으로 패킷 캡처를 필터링합니다.
     - **원격 포트**: 원격 포트가 이 필터 값과 일치하는 패킷으로 패킷 캡처를 필터링합니다.
    
     > [!NOTE]
     > 포트 및 IP 주소 값은 포트에 대한 단일 값, 값 범위 또는 범위(예: 80-1024)일 수 있습니다. 필요한 만큼 필터를 정의할 수 있습니다.

4. **확인** 을 선택합니다.

패킷 캡처에서 시간 제한 설정이 만료되면 패킷 캡처가 중지되고 검토할 수 있습니다. 패킷 캡처 세션을 수동으로 중지할 수도 있습니다.

> [!NOTE]
> 포털은 자동으로 다음을 수행합니다.
>  * 지역에 아직 Network Watcher가 없는 경우 선택한 가상 머신이 있는 지역과 동일한 지역에 Network Watcher를 만듭니다.
>  * 아직 설치되지 않은 경우 *AzureNetworkWatcherExtension* [Linux](../virtual-machines/extensions/network-watcher-linux.md) 또는 [Windows](../virtual-machines/extensions/network-watcher-windows.md) 가상 머신 확장을 가상 머신에 추가합니다.

## <a name="delete-a-packet-capture"></a>패킷 캡처 삭제

1. 패킷 캡처 보기에서 패킷 캡처의 오른쪽에 있는 **...** 를 선택하거나 기존 패킷 캡처를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.
2. 패킷 캡처를 삭제할지 확인하는 메시지가 표시됩니다. **예** 를 선택합니다.

> [!NOTE]
> 패킷 캡처를 삭제해도 스토리지 계정 또는 가상 머신에서 캡처 파일이 삭제되지는 않습니다.

## <a name="stop-a-packet-capture"></a>패킷 캡처 중지

패킷 캡처 보기에서 패킷 캡처의 오른쪽에 있는 **...** 를 선택하거나 기존 패킷 캡처를 마우스 오른쪽 단추로 클릭하고 **중지** 를 선택합니다.

## <a name="download-a-packet-capture"></a>패킷 캡처 다운로드

패킷 캡처 세션이 완료되면 캡처 파일을 Blob Storage 또는 가상 머신의 로컬 파일에 업로드합니다. 패킷 캡처의 스토리지 위치는 패킷 캡처를 만드는 동안 정의됩니다. 스토리지 계정에 저장되는 캡처 파일에 액세스하는 편리한 도구는 [다운로드](https://storageexplorer.com/)할 수 있는 Microsoft Azure Storage Explorer입니다.

스토리지 계정이 지정되어 있으면 패킷 캡처 파일은 다음 위치에서 스토리지 계정에 저장됩니다.

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

캡처를 만들 때 **파일** 을 선택한 경우 가상 머신에 구성한 경로에서 파일을 보거나 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 가상 머신 경고로 패킷 캡처를 자동화하는 방법을 알아보려면 [경고로 트리거된 패킷 캡처 만들기](network-watcher-alert-triggered-packet-capture.md)를 참조하세요.
- 특정 트래픽이 가상 머신 내부 또는 외부에서 허용되는지 확인하려면 [가상 머신 네트워크 트래픽 필터 문제 진단](diagnose-vm-network-traffic-filtering-problem.md)을 참조하세요.