---
title: SMB 다중 채널 성능 - Azure Files
description: SMB 다중 채널 성능에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e19011751115e305be40b33bec72e35e47a80293
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967267"
---
# <a name="smb-multichannel-performance"></a>SMB 다중 채널 성능
SMB 다중 채널을 사용하면 SMB 3.x 클라이언트가 SMB 파일 공유에 대한 다중 네트워크 연결을 설정할 수 있습니다. Azure Files는 프리미엄 파일 공유(FileStorage 스토리지 계정 종류의 파일 공유)에서 SMB 다중 채널을 지원합니다. Azure Files에서 SMB 다중 채널을 사용하도록 설정하기 위한 추가 비용은 없습니다. SMB 다중 채널은 기본적으로 사용하지 않도록 설정되어 있습니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="benefits"></a>이점
SMB 다중 채널을 사용하면 클라이언트는 소유 비용을 줄이는 동시에 성능을 향상시키는 여러 네트워크 연결을 사용할 수 있습니다. 여러 NIC의 대역폭 집계를 통해 성능을 향상시키고 NIC에 대한 RSS(수신측 배율) 지원을 활용하여 IO 부하를 여러 CPU에 분산시킵니다.

- **향상된 처리량**: 다중 연결을 사용하면 여러 경로를 통해 데이터를 병렬로 전송할 수 있으므로, 더 큰 IO와 파일 크기를 사용하며 더 많은 처리량을 요구하는 워크로드를 단일 VM 또는 더 작은 VM 집합에서도 훌륭히 활용할 수 있습니다. 해당 워크로드에는 콘텐츠 생성 또는 코드 변환, 유전체학, 금융 서비스 위험 분석을 위한 미디어와 엔터테인먼트가 포함됩니다.
- **높은 IOPS**: NIC RSS 기능을 사용하면 다중 연결된 여러 CPU에서 효과적으로 부하를 분산할 수 있습니다. 이렇게 하면 VM CPU의 IOPS 규모를 늘리고 더 효율적으로 활용할 수 있습니다. 데이터베이스 애플리케이션과 같이 IO 크기가 작은 워크로드에 유용합니다.
- **네트워크 내결함성**: 다중 연결을 사용하면 클라이언트가 개별 연결에만 의존하지 않으므로 중단 위험이 줄어듭니다.
- **자동 구성**: 클라이언트 및 스토리지 계정에서 SMB 다중 채널을 사용하면 기존 연결의 동적 검색이 가능해지고 필요하면 추가 연결 경로를 만들 수 있습니다.
- **비용 최적화**: 프리미엄 공유에 연결했을 때 단일 VM 또는 소규모 VM 세트에서 더 큰 규모의 워크로드를 수행할 수 있습니다. 이렇게 하면 워크로드를 실행하고 관리하는 데 필요한 VM 수를 줄여 총 소유 비용을 줄일 수 있습니다.

SMB 다중 채널에 대한 자세한 내용은 [Windows 설명서](/azure-stack/hci/manage/manage-smb-multichannel)를 참조하세요.

이 기능은 다중 스레드 애플리케이션의 성능을 크게 향상시키지만 일반적으로 단일 스레드 애플리케이션에는 도움이 되지 않습니다. 자세한 내용은 [성능 비교](#performance-comparison) 섹션을 참조하세요.

## <a name="limitations"></a>제한 사항
Azure 파일 공유에 대한 SMB 다중 채널에는 현재 다음과 같은 제한 사항이 있습니다.
- SMB 3.1.1을 사용하는 [Windows](storage-how-to-use-files-windows.md) 및 [Linux](storage-how-to-use-files-linux.md) 클라이언트에서만 지원됩니다. SMB 클라이언트 운영 체제가 권장 수준으로 패치되었는지 확인합니다.
- 최대 채널 수는 4개입니다. 자세한 내용은 [여기](storage-troubleshooting-files-performance.md#cause-4-number-of-smb-channels-exceeds-four)를 참조하세요.

## <a name="configuration"></a>구성
SMB 다중 채널은 기능이 클라이언트 측(사용자의 클라이언트)과 서비스 측(사용자의 Azure 스토리지 계정) 양쪽에서 사용하도록 설정된 경우에만 작동합니다.

Windows 클라이언트에서 SMB 다중 채널은 기본적으로 사용하도록 설정되어 있습니다. 다음 PowerShell 명령을 실행하여 설정을 확인할 수 있습니다. 

```PowerShell
Get-SmbClientConfiguration | Select-Object -Property EnableMultichannel
```
 
Azure 스토리지 계정에서 SMB 다중 채널을 사용하도록 설정해야 합니다. [SMB 다중 채널 사용](files-smb-protocol.md#smb-multichannel)을 참조하세요.

### <a name="disable-smb-multichannel"></a>SMB 다중 채널 사용 안 함
대부분의 시나리오, 특히 다중 스레드 워크로드의 경우 클라이언트는 SMB 다중 채널로 인한 성능 향상을 확인하게 될 것입니다. 하지만 단일 스레드 워크로드와 같은 일부 특정 시나리오, 혹은 테스트 목적으로 SMB 다중 채널을 사용하지 않도록 설정할 수도 있습니다. 자세한 내용은 [성능 비교](#performance-comparison)를 참조하세요.

## <a name="verify-smb-multichannel-is-configured-correctly"></a>SMB 다중 채널이 올바르게 구성되었는지 확인

1. 프리미엄 파일 공유를 만들거나 기존 파일 공유를 사용합니다.
1. 클라이언트에서 SMB 다중 채널을 지원하는지 확인합니다(하나 이상의 네트워크 어댑터에서 수신측 배율을 사용하도록 설정된 경우). 자세한 내용은 [Windows 설명서](/azure-stack/hci/manage/manage-smb-multichannel)를 참조하세요.
1. 파일 공유를 클라이언트에 탑재합니다.
1. 애플리케이션을 사용하여 부하를 생성합니다.
    robocopy/MT와 같은 복사 도구나 파일 읽기/쓰기를 할 수 있는 Diskspd와 같은 퍼포먼스 툴로 부하를 생성할 수 있습니다.
1. 관리자 권한으로 PowerShell을 열고 `Get-SmbMultichannelConnection |fl` 명령을 실행합니다.
1. **MaxChannels** 와 **CurrentChannels** 속성을 찾습니다.

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Get-SMBMultichannelConnection 결과의 스크린샷" lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>성능 비교

읽기/쓰기 워크로드 패턴에는 단일 스레드 및 다중 스레드의 두 가지 범주가 있습니다. 대부분의 워크로드는 여러 파일을 사용하지만 단일 파일을 공유는 특정한 사용 사례도 있을 수 있습니다. 이 섹션에서는 다양한 사용 사례와 각 사례가 성능에 끼치는 영향을 설명합니다. 일반적으로 대부분의 워크로드는 다중 스레드이며 워크로드를 여러 파일에 분산하여 SMB 다중 채널의 상당한 성능 향상을 확인할 수 있습니다.

- **다중 스레드/다중 파일**: 워크로드 패턴에 따라 다중 채널에 IO 읽기 및 쓰기 성능의 비약적인 향상을 확인할 수 있습니다. 성능 향상은 IOPS, 처리량, 대기 시간 면에서 2X부터 4X까지 차이가 있습니다. 이 카테고리에서는 최고의 성능을 위해 SMB 다중 채널을 사용하도록 설정해야 합니다.
- **다중 스레드/단일 파일**: 이 카테고리의 대부분의 사용 사례에서는 SMB 다중 채널을 사용한 워크로드, 특히 워크로드의 평균 IO 크기가 >~16k인 경우에 성능이 향상됩니다. SMB 다중 채널의 혜택을 받은 몇 가지 예제 시나리오는 큰 크기의 단일 파일 백업 또는 복구입니다. SMB 다중 채널을 사용하지 않도록 설정할 예외 사례는 워크로드 부하가 작은 IO인 경우 정도입니다. 이 경우에는 ~10% 정도 성능이 약간 저하될 수 있습니다. 사용 사례에 따라 부하를 여러 파일에 분산하거나 기능을 사용하지 않도록 설정하는 것이 좋습니다. 자세한 내용은 [구성](#configuration) 섹션을 참조하세요.
- **단일 스레드/다중 파일 또는 단일 파일**: 대부분의 단일 스레드 워크로드의 경우 병렬 처리 부족으로 인해 성능상의 이점이 미미합니다. 일반적으로 SMB 다중 채널을 사용하는 경우에는 ~10% 정도 성능이 약간 저하됩니다. 이 경우 한 가지 예외 사례를 제외하고 SMB 다중 채널을 사용하지 않도록 설정하는 게 이상적입니다. 단일 스레드 워크로드에서 여러 파일에 부하를 분산하고 더 큰 IO 크기(>~16k)를 평균적으로 사용하는 경우, SMB 다중 채널을 사용하면 성능이 약간 향상됩니다.

### <a name="performance-test-configuration"></a>성능 테스트 구성

이 문서의 차트에서 사용된 구성은 4개의 채널이 포함된 단일 RSS를 사용하도록 설정된 NIC를 사용하는 단일 표준 D32s v3 VM입니다. 부하는 IO 깊이 10 및 다양한 IO 크기의 무작위 IO로 다중 스레드 처리된 diskspd.exe를 이용해 생성하였습니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수|예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512(800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="성능 테스트 구성 스크린샷" lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>SMB 다중 채널을 사용하는 다중 스레드/복수 파일

다양한 IO 크기의 10개 파일로 부하가 생성되었습니다. 스케일 업 테스트 결과, SMB 다중 채널을 사용하도록 설정된 IOPS 및 처리량 테스트 결과 양쪽 모두에서 상당한 성능 향상을 보였습니다. 다음은 결과를 나타낸 다이어그램입니다.

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="성능 다이어그램" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="처리량 성능 다이어그램" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- 단일 NIC에서 읽기의 경우 2X에서 3X의 성능 향상이 관찰되었고, 쓰기의 경우 IOPS와 처리량 양쪽 모두 3X-4X의 향상을 기록했습니다.
- SMB 다중 채널은 IOPS와 처리량이 단일 NIC와 4개 채널로 제한이 걸린 상태에서도 VM의 한계치까지 도달하게 했습니다.
- 송신 또는 스토리지 읽기는 측정되지 않아서 읽기 처리량은 VM 게시 제한인 16000Mbps(2GiB/s)를 초과할 수 있었습니다. 테스트에서는 >2.7GiB/s를 기록했습니다. 수신 또는 저장소 쓰기에서는 여전히 VM 한계치에 종속되었습니다.
- 여러 파일에 부하를 분산시킴으로써 상당히 개선되었습니다.

이 테스트에 사용된 예제 명령은 다음과 같습니다. 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>SMB 다중 채널을 사용하는 다중 스레드/단일 파일 워크로드

부하는 단일 128GiB 파일로 생성되었습니다. SMB 다중 채널을 사용하도록 설정했을 때 다중 스레드/단일 파일로 수행한 스케일 업 테스트는 대부분의 사례에서 향상된 결과를 보여 주었습니다. 다음은 결과를 나타낸 다이어그램입니다.

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="IOPS 성능 다이어그램." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="단일 파일 처리량 성능 다이어그램." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- 평균 IO 크기가 더 큰 단일 NIC(>~16k)에서는 읽기 및 쓰기 성능이 모두 크게 향상되었습니다.
- 더 작은 IO 크기의 경우 SMB 다중 채널을 사용하는 경우 성능에 ~10% 정도 약간의 영향을 받았습니다. 부하를 여러 파일에 분산하거나 기능을 사용하지 않도록 설정하여 이 현상을 완화할 수 있었습니다.
- 성능은 여전히 [단일 파일 제한](storage-files-scale-targets.md#file-scale-targets)에 의해 바인딩됩니다.

## <a name="optimizing-performance"></a>성능 최적화

성능을 최적화하는 데 도움이 되는 팁은 다음과 같습니다.

- 네트워크 대기 시간을 줄이기 위해 스토리지 계정 및 클라이언트가 동일한 Azure 지역에 함께 배치되었는지 확인합니다.
- 다중 스레드 애플리케이션을 사용하여 부하를 여러 파일에 분산합니다.
- SMB 다중 채널의 성능 이점은 부하를 분산하는 파일 수가 늘어날수록 더 커집니다.
- 프리미엄 공유 성능은 프로비저닝된 공유 크기(IOPS/송신/수신) 및 단일 파일 제한에 의해 바인딩됩니다. 자세한 내용은 [프리미엄 파일 공유를 위한 프로비저닝 이해](understanding-billing.md#provisioned-model)를 참조하세요.
- 단일 VM 클라이언트의 최대 성능은 VM 제한에 계속 바인딩되어 있습니다. 예를 들어 [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md)는 최대 대역폭 16000MBps(또는 2GBps)를 지원할 수 있습니다. VM에서의 송신(스토리지 쓰기)은 측정되었고 수신(스토리지 읽기)은 측정되지 않았습니다. 파일 공유 성능은 머신 네트워크 제한, CPU, 내부 스토리지 사용이 가능한 네트워크 대역폭, IO 크기, 병렬 처리 및 기타 요인에 따라 달라집니다.
- 최초 테스트는 일반적으로 웜업이므로 그 결과는 폐기하고 테스트를 반복합니다.
- 단일 클라이언트에 의해 성능이 제한되고 워크로드가 여전히 프로비저닝된 공유 제한을 밑도는 경우에는, 여러 클라이언트에 부하를 분산하여 성능을 더 높일 수 있습니다.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>IOPS, 처리량과 IO 크기 간의 관계

**처리량 = IO 크기 * IOPS**

IO 크기가 클수록 처리량이 늘어나고 지연 시간이 길어져 네트워크 IOPS 수가 줄어듭니다. IO 크기가 작을수록 IOPS가 높아지지만 네트워크 처리량이 적어지고 지연 시간이 줄어듭니다.

## <a name="next-steps"></a>다음 단계
- [SMB 다중 채널 사용](files-smb-protocol.md#smb-multichannel)
- SMB 다중 채널에 대한 자세한 정보를 알아보려면 [Windows 설명서](/azure-stack/hci/manage/manage-smb-multichannel)를 참조하세요.
