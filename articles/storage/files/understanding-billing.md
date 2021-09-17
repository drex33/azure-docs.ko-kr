---
title: Azure Files 청구 이해 | Microsoft Docs
description: Azure 파일 공유에 대해 프로비저닝된 종량제 청구 모델을 해석하는 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/17/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 07d89aab2f957b6161f81525cfd9ae703be65d48
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123476271"
---
# <a name="understand-azure-files-billing"></a>Azure Files 청구 이해
Azure Files는 프로비저닝과 종량제라는 두 가지 고유한 청구 모델을 제공합니다. 프로비저닝 모델은 **FileStorage** 스토리지 계정 종류에 배포된 파일 공유인 프리미엄 파일 공유에만 사용할 수 있습니다. 종량제 모델은 **범용 버전 2(GPv2)** 스토리지 계정 종류에 배포된 파일 공유인 표준 파일 공유에만 사용할 수 있습니다. 이 문서에서는 월간 Azure Files 청구를 이해하는 데 도움이 되도록 두 모델의 작동 방식을 설명합니다.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/m5_-GsKv4-o" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        이 비디오는 Azure 파일 공유를 최적화하여 비용을 최소화하는 방법 및 Azure Files를 온-프레미스 및 클라우드의 다른 파일 스토리지 제품과 비교하는 방법을 비롯하여 Azure Files 청구 모델의 기본 사항에 대해 논의하는 인터뷰입니다.
   :::column-end:::
:::row-end:::

Azure Files 가격 책정 정보는 [Azure Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/)를 참조하세요.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |

## <a name="storage-units"></a>스토리지 단위    
Azure Files은 2가지 측정 단위를 사용하여 스토리지 용량(KiB, MiB, GiB 및 TiB)을 나타냅니다. 운영 체제는 동일한 측정 단위 또는 계산 시스템을 사용하거나 사용하지 않을 수 있습니다.

### <a name="windows"></a>Windows
Windows 운영 체제와 Azure Files는 base-2 계산 시스템을 사용하여 스토리지 용량을 측정하지만 단위에 레이블을 지정할 때 차이가 있습니다. Azure Files는 스토리지 용량을 base-2 측정 단위로 레이블링하고 Windows는 스토리지 용량을 base-10 측정 단위로 레이블링합니다. 스토리지 용량을 보고할 때 Windows는 스토리지 용량을 base-2에서 base-10으로 변환하지 않습니다.

| 머리글자어 | 정의                         | 단위     | Windows는 다음과 같이 표시합니다. |
|---------|------------------------------------|----------|---------------------|
| KiB     | 1,024바이트                        | 키비바이트 | KB(킬로바이트)       |
| MiB     | 1,024KiB(1,048,576바이트)        | 메비바이트 | MB(메가바이트)       |
| GiB     | 1024MiB(1,073,741,824바이트)     | 기비바이트 | GB(기가바이트)       |
| TiB     | 1024GiB(1,099,511,627,776바이트) | 테비바이트 | TB(테라바이트)       |

### <a name="macos"></a>macOS
어떤 계산 시스템이 사용되는지 확인하려면 Apple 웹 사이트의 [iOS 및 macOS가 스토리지 용량을 보고하는 방법](https://support.apple.com/HT201402)을 참조하세요.

### <a name="linux"></a>Linux
각 운영 체제 또는 개별 소프트웨어에서 다른 계산 시스템을 사용할 수 있습니다. 스토리지 용량을 보고하는 방법을 확인하려면 해당 설명서를 참조하세요.

## <a name="reserve-capacity"></a>예약 용량
Azure Files는 스토리지 용량 예약을 지원하여 사용자는 스토리지 사용률에 미리 커밋하여 스토리지 할인을 이용할 수 있습니다. 프로덕션 워크로드 또는 일관된 공간을 가진 개발/테스트 워크로드에 대한 예약 인스턴스 구매를 고려해야 합니다. 예약된 용량을 구매하는 경우 예약에서 다음 차원을 지정해야 합니다.

- **용량 크기**: 용량 예약은 10TiB 또는 100TiB 중 하나일 수 있으며, 더 높은 용량 예약을 구매에는 더 큰 할인이 제공됩니다. 워크로드 요구 사항에 맞게 다양한 용량 크기의 예약을 포함하여 여러 예약을 구매할 수 있습니다. 예를 들어 프로덕션 배포에 120TiB의 파일 공유가 있는 경우 총 용량 요구 사항을 충족하기 위해 100TiB 예약과 10TiB 예약 2개를 구매할 수 있습니다.
- **기간**: 예약은 1년 또는 3년 동안 구매할 수 있으며, 더 긴 예약 기간을 구매하면 더 큰 할인을 받을 수 있습니다. 
- **계층**: 용량 예약에 대한 Azure Files 계층입니다. Azure Files에 대한 예약은 현재 프리미엄, 핫 및 쿨 계층에서 사용할 수 있습니다.
- **위치**: 용량 예약을 위한 Azure 지역입니다. Azure 지역의 하위 집합에서 용량 예약을 사용할 수 있습니다.
- **중복성**: 용량 예약에 대한 스토리지 중복성입니다. 예약은 LRS, ZRS, GRS 및 GZRS를 포함하여 Azure Files가 지원하는 모든 중복에 대해 지원됩니다.

용량 예약을 구매하면 기존 스토리지 사용률에 의해 자동으로 소비됩니다. 예약한 것보다 더 많은 스토리지를 사용하는 경우 용량 예약에 포함되지 않는 잔액에 대해서는 정가를 지불하게 됩니다. 예약에는 트랜잭션, 대역폭 및 데이터 전송 요금이 포함되지 않습니다.

스토리지 예약을 구매하는 방법에 대한 자세한 내용은 [예약된 용량으로 Azure Files에 대한 비용 최적화](files-reserve-capacity.md)를 참조하세요.

## <a name="provisioned-model"></a>프로비저닝된 모델
Azure Files는 프리미엄 파일 공유에 프로비저닝된 모델을 사용합니다. 프로비저닝 비즈니스 모델에서 사용하는 내용에 따라 비용이 청구되는 것이 아니라 Azure Files 서비스에 스토리지 요구 사항을 사전에 지정합니다. 이는 특정 양의 스토리지로 Azure 파일 공유를 프로비저닝할 때, 공간을 사용할 때 온-프레미스의 물리적 미디어의 비용 결제를 시작하지 않는 것처럼 사용 여부에 관계없이 해당 스토리지에 대한 비용을 결제한다는 점에서 온-프레미스 하드웨어 구매와 유사합니다. 온-프레미스에서 물리적 미디어를 구매하는 것과 달리 프로비저닝된 파일 공유는 스토리지 및 IO 성능 특성에 따라 동적으로 확장 또는 축소할 수 있습니다.

프로비저닝된 파일 공유 크기는 언제든지 늘릴 수 있지만 마지막으로 늘린 후 24시간이 지나야 줄일 수 있습니다. 할당량을 늘리지 않고 24시간을 기다린 후 다시 늘릴 때까지 공유 할당량을 원하는 만큼 줄일 수 있습니다. IOPS/처리량 크기 조정은 프로비저닝된 크기 변경 후 몇 분 이내에 적용됩니다.

프로비저닝된 공유의 크기를 사용된 GiB 이하로 줄일 수 있습니다. 이렇게 하면 데이터가 손실되지 않지만 여전히 사용된 크기에 대해 요금이 청구되고 사용된 크기가 아닌 프로비저닝된 공유의 성능(기준 IOPS, 처리량 및 버스트 IOPS)을 받습니다.

### <a name="provisioning-method"></a>프로비저닝 방법
프리미엄 파일 공유를 프로비저닝할 때 워크로드에 필요한 GiB 수를 지정합니다. 프로비저닝하는 각 GiB는 고정 비율로 추가 IOPS 및 처리량을 제공합니다. 보장되는 기준 IOPS 외에도 각 프리미엄 파일 공유는 최상의 수준으로 버스팅을 지원합니다. IOPS 및 처리량에 대한 수식은 다음과 같습니다.

| 항목 | 값 |
|-|-|
| 파일 공유의 최소 크기 | 100GiB |
| 프로비저닝 단위 | 1GiB |
| 기준 IOPS 수식 | `MIN(400 + 1 * ProvisionedGiB, 100000)` |
| 버스트 한도 | `MIN(MAX(4000, 3 * BaselineIOPS), 100000)` |
| 버스트 크레딧 | `BurstLimit * 3600` |
| 수신 속도 | `40 MiB/sec + 0.04 * ProvisionedGiB` |
| 송신 속도 | `60 MiB/sec + 0.06 * ProvisionedGiB` |

다음 표는 프로비저닝된 공유 크기에 대한 이러한 공식의 몇 가지 예를 보여줍니다.

| 용량(GiB) | 기준 IOPS | 버스트 IOPS | 버스트 크레딧 | 수신(MiB/초) | 송신(MiB/초) |
|-|-|-|-|-|-|
| 100 | 500 | 최대 4,000 | 14,400,000 | 44 | 66 |
| 500 | 900 | 최대 4,000 | 14,400,000 | 60 | 90 |
| 1,024 | 1,424 | 최대 4,000 | 14,400,000 | 81 | 122 |
| 5,120 | 5,520 | 최대 15,360 | 55,296,000 | 245 | 368 |
| 10,240 | 10,640 | 최대 30,720 | 110,592,000 | 450 | 675 |
| 33,792 | 34,192 | 최대 100,000 | 360,000,000 | 1,392 | 2,088 |
| 51,200 | 51,600 | 최대 100,000 | 360,000,000 | 2,088 | 3,132 |
| 102,400 | 100,000 | 최대 100,000 | 360,000,000 | 4,136 | 6,204 |

효과적인 파일 공유 성능은 다른 여러 요인 중에서도 머신 네트워크 제한, 사용 가능한 네트워크 대역폭, IO 크기, 병렬 처리의 영향을 받습니다. 예를 들어, 8KiB 읽기/쓰기 IO 크기의 내부 테스트에서 SMB 다중 채널이 활성화되지 않은 단일 Windows 가상 머신, *표준 F16s_v2* , SMB를 통한 프리미엄 파일 공유에 연결된 경우 20K 읽기 IOPS 및 15K 쓰기 IOPS를 달성할 수 있습니다. 512MiB 읽기/쓰기 IO 크기의 경우, 동일한 VM이 1.1GiB/s 송신 및 370MiB/s 수신 처리량을 달성할 수 있습니다. 프리미엄 공유에서 SMB 다중 채널을 사용하는 경우 동일한 클라이언트에서 최대 \~3배의 성능을 달성할 수 있습니다. 최대 성능 확장을 실현하려면 [SMB 다중 채널 사용하고](files-smb-protocol.md#smb-multichannel) 부하를 여러 VM에 분산합니다. 몇 가지 일반적인 성능 문제와 해결 방법에 관해서는 [SMB 다중 채널 성능](storage-files-smb-multichannel-performance.md) 및 [문제 해결 가이드](storage-troubleshooting-files-performance.md)를 참조하세요.

### <a name="bursting"></a>버스팅
워크로드에 최대 수요를 충족하기 위해 추가 성능이 필요한 경우 공유는 버스트 크레딧을 사용하여 공유 기준 IOPS 제한을 초과하여 수요를 충족하는 데 필요한 공유 성능을 제공할 수 있습니다. 프리미엄 파일 공유는 IOPS를 최대 4,000 또는 최대 3배 중 더 높은 값으로 버스팅할 수 있습니다. 버스팅은 자동화되며 크레딧 시스템을 기반으로 작동합니다. 버스팅은 가장 효율적으로 작동하며 버스트 한도는 보장되지 않습니다.

크레딧은 파일 공유에 대한 트래픽이 기준 IOPS 이하가 될 때마다 버스트 버킷에 누적됩니다. 예를 들어, 100GiB 공유에는 500 기준 IOPS가 있습니다. 공유의 실제 트래픽이 1초 간격으로 100 IOPS인 경우 사용되지 않은 400 IOPS는 버스트 버킷에 크레딧으로 적립됩니다. 마찬가지로 유휴 1TiB 공유는 1,424 IOPS의 버스트 크레딧을 적립합니다. 이러한 크레딧은 나중에 작업이 기준 IOPS를 초과할 때 사용됩니다.

공유가 기준 IOPS를 초과하고 버스트 버킷에 크레딧이 있을 때마다 최대 허용 최대 버스트 속도로 버스트됩니다. 크레딧이 남아 있는 한 공유는 계속 버스팅할 수 있지만, 이는 누적된 버스트 크레딧 수에 따라 달라집니다. 기준 IOPS를 초과하는 각 IO는 1 크레딧을 사용하고 모든 크레딧을 사용하면 공유는 기준 IOPS로 돌아갑니다.

공유 크레딧에는 세 가지 상태가 있습니다.

- 파일 공유가 기준 IOPS보다 적게 사용하는 경우 적립됩니다.
- 파일 공유가 기준 IOPS보다 많이 사용하고 버스트 모드인 경우 감소합니다.
- 파일 공유가 정확히 기준 IOPS를 사용하는 경우(크레딧이 적립되거나 사용되지 않는 경우) 유지됩니다.

새 파일 공유는 버스트 버킷의 전체 크레딧 수로 시작합니다. 공유 IOPS가 서버의 제한으로 인해 기준 IOPS 이하로 떨어지면 버스트 크레딧이 적립되지 않습니다.

## <a name="pay-as-you-go-model"></a>종량제 모델
Azure Files는 표준 파일 공유에 종량제 비즈니스 모델을 사용합니다. 종량제 비즈니스 모델에서 결제 금액은 프로비저닝된 양이 아닌 실제 사용량에 따라 결정됩니다. 사용량이 많은 경우 디스크에 저장된 데이터 양에 대한 비용을 결제한 다음, 해당 데이터의 사용량에 따라 추가 트랜잭션 세트를 결제합니다. 종량제 모델은 향후 성장 또는 성능 요구 사항을 고려하여 과도하게 프로비저닝하거나 데이터 공간 내의 워크로드가 시간에 따라 달라지는 경우 프로비저닝을 해제할 필요가 없기 때문에 비용 효율적일 수 있습니다. 반면, 종량제 모델은 최종 사용자의 사용에 따라 달라지기 때문에 예산 책정 프로세스의 일부로 계획하기가 어려울 수 있습니다.

### <a name="differences-in-standard-tiers"></a>표준 계층의 차이점
표준 파일 공유를 만들 때 트랜잭션 최적화 계층, 핫 계층 및 쿨 계층 중에서 선택할 수 있습니다. 이 세 계층은 정확히 동일한 표준 스토리지 하드웨어에 저장됩니다. 이러한 세 계층의 주요 차이점은 더 높은 쿨 계층에서 저장 데이터 스토리지 가격이 더 낮고, 트랜잭션 가격이 더 높다는 것입니다. 이는 다음을 의미합니다.

- 트랜잭션 최적화는 이름에서 암시하듯이 높은 트랜잭션 워크로드에 대한 가격을 최적화합니다. 트랜잭션 최적화의 경우 저장 데이터 스토리지 가격이 가장 높지만 트랜잭션 가격은 가장 낮습니다.
- 핫은 많은 수의 트랜잭션이 포함되지 않은 활성 워크로드에 적합하며, 저장 데이터 스토리지 가격이 약간 낮지만 트랜잭션 가격은 트랜잭션 최적화에 비해 약간 더 높습니다. 이를 트랜잭션 최적화 계층과 쿨 계층의 중간으로 생각하세요.
- 쿨은 작업이 많지 않은 워크로드에 대한 가격을 최적화하여 가장 낮은 저장 데이터 스토리지 가격을 제공하지만, 트랜잭션 가격이 가장 높습니다.

트랜잭션 최적화 계층에 자주 액세스하지 않는 워크로드를 배치하는 경우, 한 달에 몇 회 정도 수행하는 공유 트랜잭션에 대해서는 결제 금액이 거의 없지만 데이터 스토리지에는 많은 금액을 결제하게 됩니다. 이 동일한 공유를 쿨 계층으로 이동하는 경우, 이 워크로드에 대해 트랜잭션을 매우 드물게 수행하기 때문에 트랜잭션 비용에 대해 거의 결제하지 않지만 쿨 계층의 데이터 스토리지 가격은 훨씬 저렴합니다. 사용 사례에 적합한 계층을 선택하면 비용을 크게 줄일 수 있습니다.

마찬가지로 쿨 계층에 매우 자주 액세스하는 워크로드를 배치하는 경우, 트랜잭션 비용을 훨씬 더 많이 결제하지만 데이터 스토리지 비용을 줄일 수 있습니다. 이로 인해 트랜잭션 가격 상승으로 인한 비용 증가가 데이터 스토리지 가격 하락으로 인한 비용 절감보다 더 큰 상황이 될 수 있으며, 트랜잭션 최적화보다 더 큰 비용을 쿨에 결제하게 됩니다. 일부 사용량 수준은 핫 계층이 가장 비용 효율적인 계층이 될 수 있지만, 쿨 계층은 트랜잭션 최적화 계층보다 비용이 더 많이 듭니다.

워크로드 및 작업 수준에 따라 표준 파일 공유에 가장 비용 효율적인 계층이 결정됩니다. 실제로 가장 비용 효율적인 계층을 선택하는 가장 좋은 방법은 공유의 실제 리소스 사용량(저장된 데이터, 쓰기 트랜잭션 등)을 확인하는 것입니다.

### <a name="what-are-transactions"></a>트랜잭션이란?
트랜잭션은 파일 공유의 콘텐츠를 업로드, 다운로드 또는 조작하기 위해 Azure Files에 대한 작업 또는 요청입니다. 파일 공유에 대해 취한 모든 조치는 하나 이상의 트랜잭션으로 변환되고, 종량제 청구 모델을 사용하는 표준 공유에서 트랜잭션 비용으로 변환됩니다.

기본 트랜잭션 범주에는 쓰기, 목록, 읽기, 기타, 삭제 등 5가지가 있습니다. REST API 또는 SMB를 통해 수행되는 모든 작업은 다음 4가지 범주 중 하나로 버킷됩니다.

| 작업 유형 | 쓰기 트랜잭션 | 나열 트랜잭션 | 읽기 트랜잭션 | 기타 트랜잭션 | 삭제 트랜잭션 |
|-|-|-|-|-|-|
| 관리 작업 | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li><li>`SetShareACL`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| 데이터 작업 | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> NFS 4.1은 프로비저닝된 청구 모델을 사용하는 프리미엄 파일 공유에만 사용할 수 있으며, 트랜잭션은 프리미엄 파일 공유에 대한 청구에 영향을 주지 않습니다.

## <a name="file-storage-comparison-checklist"></a>파일 스토리지 비교 검사 목록
Azure Files 비용을 다른 파일 스토리지 옵션과 비교하여 정확하게 평가하려면 다음 질문을 고려하십시오.

- **스토리지, IOPS 및 대역폭에 대한 요금을 어떻게 지불하나요?**  
    Azure Files에서 사용하는 청구 모델은 [프리미엄](#provisioned-model) 또는 [표준](#pay-as-you-go-model) 파일 공유를 배포하는지에 따라 달라집니다. 대부분의 클라우드 솔루션에는 프로비전된 스토리지(가격 확정, 단순성) 또는 종량제 스토리지(실제 사용하는 양만큼만 지불)의 원칙과 일치하는 모델이 있습니다. 프로비전된 모델의 경우 최소 프로비전된 공유 크기, 프로비저닝 단위 및 프로비저닝을 늘리고 줄이는 기능이 특히 중요합니다. 

- **스토리지 복원력과 중복성을 어떻게 실현하나요?**  
    Azure Files에서 스토리지 복원력 및 중복성은 제품 제공 시 내장되어 있습니다. 모든 계층 및 중복성 수준은 데이터의 고가용성을 보장하며 세 개 이상의 데이터 복사본에 대한 액세스를 제공합니다. 다른 파일 스토리지 옵션을 고려할 때 스토리지 복원력 및 중복성이 기본 제공되는지 아니면 직접 어셈블해야 하는지를 고려합니다. 

- **무엇을 관리해야 하나요?**  
    Azure Files에서 기본 관리 단위는 스토리지 계정입니다. 다른 솔루션에는 운영 체제 업데이트 또는 가상 리소스 관리(VM, 디스크, 네트워크 IP 주소 등)와 같은 추가 관리가 필요할 수 있습니다.

- **백업 비용은 어떻게 되나요?**  
    Azure Files에서는 Azure Backup 통합을 쉽게 사용할 수 있으며 백업 스토리지는 비용 공유의 일부로 청구됩니다(백업은 차등 스냅샷으로 저장됨). 다른 솔루션에는 백업 소프트웨어 라이선스 및 추가 백업 스토리지 비용이 필요할 수 있습니다.

## <a name="see-also"></a>참고 항목
- [Azure Files 가격 책정](https://azure.microsoft.com/pricing/details/storage/files/)
- [Azure Files 배포 계획](storage-files-planning.md) 및 [Azure 파일 동기화 배포 계획](../file-sync/file-sync-planning.md)
- [파일 공유 만들기](storage-how-to-create-file-share.md) 및 [Azure 파일 동기화 배포](../file-sync/file-sync-deployment-guide.md)