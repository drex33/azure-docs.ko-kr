---
title: Azure NetApp Files의 SMB 성능 모범 사례 | Microsoft Docs
description: Azure NetApp Files의 SMB 성능 및 모범 사례를 이해하는 데 도움이 됩니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: b-hchen
ms.openlocfilehash: d4a6b1d9cc49e25a0a30c0432b30617cb2bdf7e2
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133481654"
---
# <a name="smb-performance-best-practices-for-azure-netapp-files"></a>Azure NetApp Files의 SMB 성능 모범 사례

이 문서는 Azure NetApp Files의 SMB 성능 및 모범 사례를 이해하는 데 도움이 됩니다.

## <a name="smb-multichannel"></a>SMB 다중 채널

SMB 다중 채널은 SMB 공유에서 기본적으로 사용할 수 있습니다. 기존 SMB 볼륨 이전의 모든 SMB 공유에는 이 기능이 사용되도록 설정되었으며 새로 생성된 모든 볼륨에도 생성 시 이 기능이 사용되도록 설정됩니다. 

SMB 다중 채널 기능을 이용하려면 기능을 활성화하기 전에 설정된 SMB 연결을 재설정해야 합니다. 재설정하려면 SMB 공유 연결을 끊었다가 다시 연결할 수 있습니다.

Windows는 최상의 성능을 사용할 수 있도록 Windows 2012 이후 SMB 다중 채널을 지원합니다.  자세한 내용은 [SMB 다중 채널 배포](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) 및 [SMB 다중 채널의 기본 사항](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0)을 참조하세요. 

### <a name="benefits-of-smb-multichannel"></a>SMB 다중 채널의 혜택

SMB 다중 채널 기능을 사용하면 SMB3 클라이언트가 단일 NIC(네트워크 인터페이스 카드) 또는 여러 NIC를 통해 연결 풀을 설정하고 이를 사용하여 단일 SMB 세션에 대한 요청을 보낼 수 있습니다. 반면, 설계상 SMB1과 SMB2는 클라이언트가 하나의 연결을 설정하고 해당 연결을 통해 특정 세션에 대한 모든 SMB 트래픽을 전송하도록 요구합니다. 이 단일 연결은 단일 클라이언트에서 달성할 수 있는 전체 프로토콜 성능을 제한합니다.

### <a name="performance-for-smb-multichannel"></a>SMB 다중 채널의 성능

다음 테스트 및 그래프는 단일 인스턴스 워크로드에서 SMB 다중 채널의 기능을 보여줍니다.

#### <a name="random-io"></a>임의 I/O  

클라이언트에서 SMB 다중 채널을 사용하지 않도록 설정한 상태에서 FIO와 40GiB 작업 집합을 사용하여 순수 4KiB 읽기 및 쓰기 테스트가 수행되었습니다.  각 테스트 간에 SMB 공유가 분리되었으며, `1`,`4`,`8`,`16`, `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`의 RSS 네트워크 인터페이스 설정에 따라 SMB 클라이언트 연결 수가 증가했습니다. 테스트 결과 I/O 집약적인 작업에는 기본 설정인 `4`가 충분하며, `8`과 `16`으로 증가하면 무시해도 될 정도의 효과가 있었습니다. 

`netstat -na | findstr 445` 명령은 `1`에서 `4`, `8` 및 `16`으로 증가하여 추가 연결이 설정되었음을 입증했습니다.  perfmon `Per Processor Network Activity Cycles` 통계(이 문서에는 포함되지 않음)로 확인되었듯이 각 테스트에서 4개의 CPU 코어가 SMB에 완전히 사용되었습니다.

![SMB 다중 채널의 임의 I/O 비교를 보여주는 차트.](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Azure 가상 머신은 SMB(또는 NFS) 스토리지 I/O 제한에 영향을 주지 않습니다.  다음 차트에 표시된 것처럼 D32ds 인스턴스 종류는 캐시된 스토리지 IOPS의 경우 308,000개, 캐시되지 않은 스토리지 IOPS의 경우 51,200개로 제한됩니다.  하지만 위의 그래프는 SMB를 통해 훨씬 더 많은 I/O를 보여줍니다.

![임의 I/O 비교 테스트를 보여주는 차트.](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

#### <a name="sequential-io"></a>순차 I/O 

앞에서 설명한 임의 I/O 테스트와 유사한 테스트는 64KiB 순차 I/O로 수행되었습니다. RSS 네트워크 인터페이스당 클라이언트 연결 수가 4'를 넘어서면 임의 I/O에는 큰 영향을 미치지 않았지만 순차 I/O에는 동일하게 적용되지 않습니다. 다음 그래프에서 볼 수 있듯이 각 증가량은 읽기 처리량의 증가와 관련이 있습니다. Azure가 각 인스턴스 종류/크기에 대해 배치한 네트워크 대역폭 제한으로 인해 쓰기 처리량이 기복 없이 유지되었습니다. 

![처리량 테스트 비교를 보여주는 차트.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure는 각 가상 머신 유형/크기에 네트워크 속도 제한을 둡니다. 속도 제한은 아웃바운드 트래픽에만 적용됩니다. 가상 머신에 있는 NIC 수는 컴퓨터에서 사용할 수 있는 총 대역폭 양과 관련이 없습니다.  예를 들어, D32ds 인스턴스 종류의 네트워크 제한은 16,000Mbps(2,000MiB/s)입니다.  위의 순차 그래프에서 볼 수 있듯이, 제한은 아웃바운드 트래픽(쓰기)에 영향을 미치지만 다중 채널 읽기에는 영향을 미치지 않습니다.

![순차 I/O 비교 테스트를 보여주는 차트.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="smb-signing"></a>SMB 서명

SMB(서버 메시지 블록) 프로토콜은 원격 Windows 관리와 같은 파일 및 인쇄 공유 그리고 기타 네트워킹 작업에 대한 기초를 제공합니다. 전송 중인 SMB 패킷을 수정하는 중간자(man-in-the-middle) 공격을 방지하기 위해 SMB 프로토콜은 SMB 패킷의 디지털 서명을 지원합니다. 

SMB 서명은 Azure NetApp Files에서 지원하는 모든 SMB 프로토콜 버전에 대해 지원됩니다. 

### <a name="performance-impact-of-smb-signing"></a>SMB 서명이 성능에 미치는 영향  

SMB 서명은 SMB 성능에 부정적 영향을 미칩니다. 성능 저하가 발생할 수 있는 다른 잠재적인 원인들 중에서, 각 패킷의 디지털 서명은 아래 Perfmon 출력과 같이 추가적인 클라이언트측 CPU를 사용합니다. 이 경우 코어 0은 SMB 서명을 포함하여 SMB를 담당하는 것으로 보입니다.  이전 섹션의 비 다중 채널 순차 읽기 처리량 수와 비교한 결과, SMB 서명은 전체 처리량을 875MiB/s에서 약 250MiB/s로 줄였습니다. 

![SMB 서명 성능 영향을 보여주는 차트.](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="performance-for-a-single-instance-with-a-1-tb-dataset"></a>1TB 데이터 세트가 포함된 단일 인스턴스에 대한 성능

읽기/쓰기 조합을 통해 워크로드에 대한 자세한 정보를 제공하기 위해 다음 두 차트는 1TB 데이터 세트와 4개의 SMB 다중 채널을 통해 50TB의 단일 Ultra 서비스 수준 클라우드 볼륨의 성능을 보여줍니다. 최적의 IODepth 16이 사용되었으며, 네트워크 대역폭(`numjobs=16`)의 완전한 사용을 보장하기 위해 FIO(Flexible IO) 매개 변수가 사용되었습니다.

다음 차트는 단일 VM 인스턴스와 10% 간격의 읽기/쓰기 조합을 사용한 4k 임의 I/O 결과를 보여줍니다.

![Windows 2019 standard _D32ds_v4 4K 임의 I/O 테스트를 보여주는 차트.](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

다음 차트에서는 순차 I/O의 결과를 보여줍니다.

![Windows 2019 standard _D32ds_v4 64K 순차 처리량을 보여주는 차트.](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="performance-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>1TB 데이터 세트가 포함된 VM 5대를 사용하여 스케일링하는 경우의 성능

5개의 VM을 사용한 이러한 테스트에서는 단일 VM과 동일한 테스트 환경을 사용하며 각 프로세스는 자체 파일에 씁니다.

다음 차트는 임의 I/O에 대한 결과를 보여줍니다.

![Windows 2019 standard _D32ds_v4 4K 5 인스턴스 임의 I/O 테스트를 보여주는 차트.](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

다음 차트에서는 순차 I/O의 결과를 보여줍니다.

![Windows 2019 standard _D32ds_v4 64K 5 인스턴스 순차 처리량을 보여주는 차트.](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-to-monitor-hyper-v-ethernet-adapters"></a>Hyper-V 이더넷 어댑터를 모니터링하는 방법  

FIO로 테스트 하는 데 사용되는 한 가지 전략은 `numjobs=16`을 설정하는 것입니다. 이렇게 하면 각 작업이 16개의 특정 인스턴스로 포크되어 Microsoft Hyper-V 네트워크 어댑터를 최대화할 수 있습니다.

Windows 성능 모니터의 각 어댑터에서 **성능 모니터 > 카운터 추가 > 네트워크 인터페이스 > Microsoft Hyper-V 네트워크 어댑터** 를 선택하여 활동을 확인할 수 있습니다.

![성능 모니터 카운터 추가 인터페이스를 보여주는 스크린샷.](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

볼륨에서 데이터 트래픽을 실행한 후 Windows 성능 모니터에서 어댑터를 모니터링할 수 있습니다. 이러한 16개의 가상 어댑터를 모두 사용하지 않으면 네트워크 대역폭 용량을 최대화하지 못할 수 있습니다.

![성능 모니터 출력을 보여주는 스크린샷.](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="smb-encryption"></a>SMB 암호화

이 섹션은 SMB 암호화(SMB 3.0 및 SMB 3.1.1)를 이해하는 데 도움이 됩니다. 

[SMB 암호화](/windows-server/storage/file-server/smb-security)는 SMB 데이터에 대한 엔드투엔드 암호화 기능을 제공하며, 신뢰할 수 없는 네트워크에서 발생하는 도청으로부터 데이터를 보호합니다. SMB 암호화는 SMB 3.0 이상에서 지원됩니다. 

스토리지에 요청을 보낼 때 클라이언트는 요청을 암호화하고, 스토리지에서 암호를 해독합니다. 마찬가지로, 응답은 서버에서 암호화되고 클라이언트에서 암호를 해독합니다.

Windows 10, Windows 2012 이상 버전에서 SMB 암호화를 지원합니다.

### <a name="smb-encryption-and-azure-netapp-files"></a>SMB 암호화 및 Azure NetApp Files

SMB 암호화는 Azure NetApp Files의 공유 수준에서 사용할 수 있습니다. SMB 3.0은 AES-CCM 알고리즘을 사용하는 반면 SMB 3.1.1은 AES-GCM 알고리즘을 사용합니다.

SMB 암호화는 필요하지 않습니다. 따라서 사용자가 Azure NetApp Files에서 SMB 암호화를 사용하도록 요청하는 경우 지정된 공유에서만 사용할 수 있습니다. Azure NetApp Files 공유는 인터넷에 노출되지 않습니다. 지정된 VNet 내에서 VPN 또는 ExpressRoute를 통해서만 액세스할 수 있으므로 Azure NetApp Files 공유는 기본적으로 안전합니다. SMB 암호화를 사용할지 여부는 전적으로 사용자의 선택입니다. 이 기능을 사용하도록 설정하기 전에 예상되는 성능 저하를 확인합니다.

### <a name="impact-of-smb-encryption-on-client-workloads"></a><a name="smb_encryption_impact"></a>SMB 암호화가 클라이언트 워크로드에 미치는 영향

SMB 암호화는 클라이언트(메시지 암호화 및 암호 해독을 위한 CPU 오버헤드)와 스토리지(처리량 감소)에 모두 영향을 주지만 다음 표에는 스토리지 영향만 나와 있습니다. 프로덕션에 워크로드를 배포하기 전에 암호화 성능이 애플리케이션에 미치는 영향을 테스트해야 합니다.

|     I/O 프로필       |     영향        |
|-  |-  |
|     읽기 및 쓰기 워크로드      |     10%~15%        |
|     메타데이터 집약적        |     5%    |

## <a name="accelerated-networking"></a>가속 네트워킹 

최대 성능을 얻으려면 가능한 한, 가상 머신에서 [가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-powershell.md)을 구성하는 것이 좋습니다. 다음 고려 사항에 유의하시기 바랍니다.  

* Azure Portal에서는 기본적으로 이 기능을 지원하는 가상 머신에 대해 가속 네트워킹을 사용하도록 설정합니다.  하지만 Anable 및 유사한 구성 도구와 같은 다른 배포 방법은 그렇지 않을 수 있습니다.  가속화된 네트워킹을 활성화하지 못하면 컴퓨터 성능이 저하될 수 있습니다.  
* 인스턴스 종류 또는 크기를 지원하지 않기 때문에 가상 머신의 네트워크 인터페이스에서 가속화된 네트워킹을 사용하도록 설정하지 않은 경우, 더 큰 인스턴스 종류에서는 가상 머신을 사용하지 않도록 설정된 상태로 유지됩니다. 이러한 경우 수동 작업을 수행해야 합니다.

## <a name="rss"></a>RSS 

Azure NetApp Files는 RSS(수신 쪽 스케일링)를 지원합니다.

SMB 다중 채널을 사용하면 SMB3 클라이언트가 단일 RSS가 가능한 NIC(네트워크 인터페이스 카드)를 통해 Azure NetApp Files SMB 서버에 여러 TCP 연결을 설정할 수 있습니다. 

Azure 가상 머신 NIC에서 RSS를 지원하는지 확인하려면 다음과 같이 `Get-SmbClientNetworkInterface` 명령을 실행하고 `RSS Capable` 필드를 확인합니다. 

![Azure 가상 머신에 대한 RSS 출력을 보여주는 스크린샷.](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)


## <a name="multiple-nics-on-smb-clients"></a>SMB 클라이언트의 여러 NIC

SMB용 클라이언트에서 여러 NIC를 구성할 수는 없습니다. SMB 클라이언트는 SMB 서버에서 반환되는 NIC 수와 일치합니다.  각 스토리지 볼륨은 하나의 스토리지 엔드포인트에서만 액세스할 수 있습니다.  즉, 지정된 SMB 관계에 하나의 NIC만 사용됩니다.  

아래 `Get-SmbClientNetworkInterace` 출력에서 볼 수 있듯 가상 머신에 두 개의 네트워크 인터페이스(15와 12)가 있습니다.  다음 `Get-SmbMultichannelConnection` 명령에서와 같이 두 개의 RSS 지원 NICS가 있지만 SMB 공유와 관련하여 인터페이스 12만 사용되며 인터페이스 15는 사용되지 않습니다.

![RSS 지원 NIC의 출력을 표시하는 스크린샷.](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="next-steps"></a>다음 단계  

- [SMB FAQ](faq-smb.md)
- Azure NetApp Files와 함께 SMB 파일 공유를 사용하는 방법에 대해서는 [Azure NetApp Files: SMB 워크로드를 위한 관리되는 Enterprise 파일 공유](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1)를 참조하세요.
