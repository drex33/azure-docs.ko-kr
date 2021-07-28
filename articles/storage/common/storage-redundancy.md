---
title: 데이터 중복
titleSuffix: Azure Storage
description: Azure Storage에서 데이터 중복성을 이해합니다. Microsoft Azure Storage 계정의 데이터는 내구성 및 고가용성을 위해 복제됩니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3f185f24c824008a6488ab2e9401dd05439daafb
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111984971"
---
# <a name="azure-storage-redundancy"></a>Azure Storage 중복성

Azure Storage는 항상 계획된 이벤트 그리고 일시적인 하드웨어 오류, 네트워크 또는 정전, 대규모 자연 재해 등의 계획되지 않은 이벤트로부터 데이터를 보호하기 위해 항상 여러 복사본을 저장합니다. 중복성은 스토리지 계정이 오류 발생 시에도 가용성 및 내구성 목표를 충족하는지 확인합니다.

시나리오에 가장 적합한 중복성 옵션을 결정할 때에는 저렴한 비용과 높은 가용성 간의 균형을 고려하세요. 선택해야 하는 중복성 옵션을 결정하는 데 도움이 되는 요소는 다음과 같습니다.  

- 기본 지역에서 데이터를 복제하는 방법
- 지역 재해로부터 보호하기 위해 데이터가 주 지역에서 지리적으로 멀리 떨어진 보조 지역으로 복제되는지 여부
- 어떤 이유로든 기본 지역을 사용할 수 없는 경우 보조 지역의 복제된 데이터에 대한 읽기 액세스 권한이 응용 프로그램에 필요한지 여부

## <a name="redundancy-in-the-primary-region"></a>기본 지역의 중복성

Azure Storage 계정의 데이터는 항상 기본 지역에서 세 번 복제됩니다. Azure Storage는 기본 지역에서 데이터를 복제하는 방법에 두 가지 옵션을 제공합니다.

- **LRS(로컬 중복 저장소)** 는 기본 지역의 단일 물리적 위치 내에서 데이터를 동기적으로 세 번 복사합니다. LRS는 가장 저렴한 복제 옵션이지만 고가용성이나 높은 내구성이 필요한 애플리케이션에는 권장되지 않습니다.
- **ZRS(영역 중복 저장소)** 는 기본 지역에 있는 3개의 Azure 가용성 영역에서 데이터를 동기적으로 복사합니다. 고가용성이 필요한 응용 프로그램의 경우 Microsoft는 기본 지역에서 ZRS를 사용하고 보조 지역에도 복제하는 것을 권장합니다.

> [!NOTE]
> Azure Data Lake Storage Gen2 워크로드의 주 지역에서 ZRS를 사용하는 것이 좋습니다.

### <a name="locally-redundant-storage"></a>LRS(로컬 중복 스토리지)

LRS(로컬 중복 스토리지)는 기본 지역의 단일 데이터 센터 내에서 데이터를 세 번 복제합니다. LRS는 지정된 1년 동안 개체에 99.999999999%(11개의 9) 이상의 내구성을 제공합니다.

LRS는 가장 저렴한 중복성 옵션이며 다른 옵션에 비해 내구성이 가장 낮습니다. LRS는 서버 랙 및 드라이브 오류로부터 데이터를 보호합니다. 그러나 데이터 센터 내에서 화재나 홍수와 같은 재해가 발생하는 경우 LRS를 사용하는 저장소 계정의 모든 복제본이 손실되거나 복구할 수 없게 됩니다. 이 위험을 완화하기 위해 Microsoft는 [ZRS(영역 중복 저장소)](#zone-redundant-storage), [GRS(지역 중복 저장소)](#geo-redundant-storage) 또는 [GZRS(지역 영역 중복 저장소)](#geo-zone-redundant-storage)를 사용하는 것을 권장합니다.

LRS를 사용하는 저장소 계정에 대한 쓰기 요청은 동기적으로 발생합니다. 쓰기 작업은 3개의 모든 복제본에 데이터가 작성된 후에만 성공적으로 반환됩니다.

다음 다이어그램에서는 LRS를 사용하여 단일 데이터 센터 내에서 데이터를 복제하는 방법을 보여 줍니다.

:::image type="content" source="media/storage-redundancy/locally-redundant-storage.png" alt-text="LRS를 사용하여 단일 데이터 센터 내에서 데이터가 복제되는 방식을 보여 주는 다이어그램":::

LRS는 다음과 같은 시나리오에 적합합니다.

- 데이터 손실이 발생하더라도 쉽게 재구성할 수 있는 데이터를 애플리케이션에 저장하는 경우 LRS를 선택할 수 있습니다.
- 데이터 거버넌스 요구 사항으로 인해 응용 프로그램이 국가 또는 지역 내에서만 데이터 복제로 제한되는 경우 LRS를 선택할 수 있습니다. 경우에 따라 데이터가 지리적으로 복제되는 쌍을 이루는 지역이 다른 국가나 지역에 있을 수 있습니다. 쌍을 이루는 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.

### <a name="zone-redundant-storage"></a>영역 중복 스토리지

ZRS(영역 중복 저장소)는 기본 지역에 있는 3개의 Azure 가용성 영역에서 Azure Storage 데이터를 동기적으로 복사합니다. 각 가용성 영역은 독립적인 전원, 냉각 및 네트워킹을 갖춘 별도의 물리적 위치입니다. ZRS는 지정된 1년 동안 99.9999999999%(12개의 9) 이상의 내구성을 Azure Storage 데이터 개체에 제공합니다.

ZRS를 사용하면 영역을 사용할 수 없게 되는 경우에도 읽기 및 쓰기 작업에 모두 계속해서 액세스할 수 있습니다. 영역을 사용할 수 없는 경우 Azure에서 DNS 재지정과 같은 네트워킹 업데이트를 수행합니다. 업데이트가 완료되기 전에 데이터에 액세스하는 경우 이러한 업데이트는 응용 프로그램에 영향을 줄 수 있습니다. ZRS용 응용 프로그램을 디자인하는 경우에는 지수 백오프를 사용하는 다시 시도 정책을 구현하는 것과 같은 일시적인 오류 처리에 대한 사례를 따릅니다.

ZRS를 사용하는 저장소 계정에 대한 쓰기 요청은 동기적으로 발생합니다. 쓰기 작업은 3개의 가용성 영역에서 데이터가 모든 복제본에 작성된 후에만 성공적으로 반환됩니다.

Microsoft는 고가용성이 필요한 시나리오인 경우 주 지역에서 ZRS를 사용하는 것을 권장합니다. ZRS도 데이터 관리 요구 사항을 충족하기 위해 국가나 지역 내에서 데이터 복제를 제한하는 데 사용하는 것이 좋습니다.

다음 다이어그램에서는 ZRS를 사용하여 주 지역의 가용성 영역에서 데이터를 복제하는 방법을 보여 줍니다.

:::image type="content" source="media/storage-redundancy/zone-redundant-storage.png" alt-text="ZRS를 사용하여 기본 지역에서 데이터가 복제되는 방식을 보여 주는 다이어그램":::

ZRS는 데이터를 일시적으로 사용할 수 없는 경우 탁월한 성능, 낮은 대기 시간 및 복원력을 제공합니다. 그러나 ZRS 자체만으로는 여러 영역에 영구적인 영향을 주는 지역 재해로부터 데이터를 보호하지 못할 수 있습니다. 지역 재해를 방지하기 위해 Microsoft는 기본 지역에서 ZRS를 사용하고 보조 지역으로 데이터를 지역 복제하는 [GZRS(지역 영역 중복 저장소)](#geo-zone-redundant-storage)를 사용하는 것을 권장합니다.

다음 표에서는 지역에서 ZRS를 지원하는 저장소 계정 유형을 보여줍니다.

| Storage 계정 유형 | 지원되는 지역 | 지원되는 서비스 |
|--|--|--|
| 범용 v2<sup>1</sup> | (아프리카) 남아프리카 북부<br /> (아시아 태평양) 동남아시아<br /> (아시아 태평양) 오스트레일리아 동부<br /> (아시아 태평양) 일본 동부<br /> (캐나다) 캐나다 중부<br /> (유럽) 북유럽<br /> (유럽) 서유럽<br /> (유럽) 프랑스 중부<br /> (유럽) 독일 중서부<br /> (유럽) 영국 남부<br /> (남미) 브라질 남부<br /> (미국) 미국 중부<br /> (미국) 미국 동부<br /> (미국) 미국 동부 2<br /> (미국) 미국 중남부<br /> (미국) 미국 서부 2 | 블록 Blob<br /> 페이지 Blob<sup>2</sup><br /> 파일 공유(표준)<br /> 테이블<br /> 큐<br /> |
| Premium 블록 Blob<sup>1</sup> | 동남아시아<br /> 오스트레일리아 동부<br /> 북유럽<br /> 서유럽<br /> 프랑스 중부 <br /> 일본 동부<br /> 영국 남부 <br /> 미국 동부 <br /> 미국 동부 2 <br /> 미국 서부 2| 프리미엄 블록 Blob 전용 |
| 프리미엄 파일 공유 | 동남아시아<br /> 오스트레일리아 동부<br /> 북유럽<br /> 서유럽<br /> 프랑스 중부 <br /> 일본 동부<br /> 영국 남부 <br /> 미국 동부 <br /> 미국 동부 2 <br /> 미국 서부 2 | 프리미엄 파일 공유 전용 |

<sup>1</sup> 보관 계층은 현재 ZRS 계정에 지원되지 않습니다.<br />
<sup>2</sup> 가상 컴퓨터에 대한 Azure 관리 디스크를 포함하는 저장소 계정은 항상 LRS를 사용합니다. Azure 비관리 디스크는 LRS도 사용해야 합니다. GRS를 사용하는 Azure 비관리 디스크에 대한 저장소 계정을 만들 수 있지만 비동기 지역에서 복제에 대한 일관성 문제로 인해 권장되지는 않습니다. 관리 디스크 및 비관리 디스크는 모두 ZRS 또는 GZRS를 지원하지 않습니다. 관리 디스크에 대한 자세한 내용은 [Azure 관리 디스크의 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요.

ZRS를 지원하는 지역에 대한 자세한 내용은 [Azure 가용성 영역이란 무엇인가요?](../../availability-zones/az-overview.md)의 **지역별 서비스 지원** 을 참조하세요.

## <a name="redundancy-in-a-secondary-region"></a>보조 지역의 중복성

높은 내구성이 필요한 애플리케이션의 경우 스토리지 계정의 데이터를 주 지역에서 수백 킬로미터 떨어진 보조 지역에 추가로 복사하도록 선택할 수 있습니다. 저장소 계정이 보조 지역으로 복사되면 전체 지역 중단 또는 기본 지역을 복구할 수 없는 재해가 발생하더라도 데이터는 지속됩니다.

스토리지 계정을 만들 때 계정에 대한 기본 지역을 선택합니다. 쌍을 이루는 보조 지역은 주 지역에 따라 결정되며 변경할 수 없습니다. Azure에서 지원되는 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)을 참조하세요.

Azure Storage는 보조 지역으로 데이터를 복사하기 위한 다음과 같은 두 가지 옵션을 제공 합니다.

- **GRS(지역 중복 저장소)** 는 LRS를 사용하여 기본 지역의 단일 물리적 위치 내에서 데이터를 동기적으로 세 번 복사합니다. 그런 다음 보조 지역의 단일 물리적 위치에 데이터를 비동기적으로 복사합니다. 보조 지역 내에서 데이터는 항상 LRS를 사용하여 동기적으로 세 번 복제됩니다.
- **GZRS(지역 영역 중복 저장소)** 는 ZRS를 사용하여 기본 지역에 있는 3개의 Azure 가용성 영역에서 데이터를 동기적으로 복사합니다. 그런 다음 보조 지역의 단일 물리적 위치에 데이터를 비동기적으로 복사합니다. 보조 지역 내에서 데이터는 항상 LRS를 사용하여 동기적으로 세 번 복제됩니다.

> [!NOTE]
> GRS와 GZRS의 주요 차이점은 기본 지역에서 데이터가 복제되는 방식입니다. 보조 지역 내에서 데이터는 항상 LRS를 사용하여 동기적으로 세 번 복제됩니다. 보조 지역의 LRS는 하드웨어 오류로부터 데이터를 보호합니다.

GRS 또는 GZRS를 사용하면 보조 지역에 대한 장애 조치(failover)가 없는 한 읽기 또는 쓰기 액세스를 위해 보조 지역의 데이터를 사용할 수 없습니다. 보조 지역에 대한 읽기 액세스의 경우 RA-GRS(읽기 액세스 지역 영역 중복 스토리지) 또는 RA-GZRS(읽기 액세스 지역 영역 중복 스토리지)를 사용하도록 스토리지 계정을 구성합니다. 자세한 내용은 [보조 지역의 데이터에 대한 읽기 권한](#read-access-to-data-in-the-secondary-region)을 참조하세요.

기본 지역을 사용할 수 없는 경우에는 보조 지역에 대해 장애 조치(failover)하도록 선택할 수 있습니다. 장애 조치(failover)가 완료되면 보조 지역은 기본 지역이 되고 데이터를 다시 읽고 쓸 수 있습니다. 재해 복구에 대한 자세한 내용 및 보조 지역에 대해 장애 조치(failover)하는 방법은 [재해 복구 및 저장소 계정 장애 조치(failover)](storage-disaster-recovery-guidance.md)를 참조하세요.

> [!IMPORTANT]
> 데이터가 보조 지역에 비동기식으로 복제되기 때문에 기본 지역을 복구할 수 없는 경우 기본 지역에 영향을 미치는 오류로 인해 데이터가 손실될 수 있습니다. 기본 지역에 대한 최신 쓰기와 보조 지역에 대한 마지막 쓰기 사이의 간격을 RPO(복구 지점 목표)라고 합니다. RPO는 데이터를 복구할 수 있는 시점을 나타냅니다. Azure Storage의 RPO는 일반적으로 15분 미만이지만 현재 보조 지역에 데이터를 복제하는 데 걸리는 시간에 대한 SLA는 없습니다.

### <a name="geo-redundant-storage"></a>지역 중복 스토리지

GRS(지역 중복 저장소)는 LRS를 사용하여 기본 지역의 단일 물리적 위치 내에서 데이터를 동기적으로 세 번 복사합니다. 그런 다음, 기본 지역에서 수백 마일 떨어진 보조 지역의 단일 물리적 위치에 데이터를 비동기식으로 복사합니다. GRS는 지정된 1년 동안 99.99999999999999%(16개의 9) 이상의 내구성을 Azure Storage 데이터 개체에 제공합니다.

쓰기 작업은 먼저 기본 위치에 커밋되고 LRS를 사용하여 복제됩니다. 그런 다음, 업데이트는 보조 지역에 비동기적으로 복제됩니다. 데이터가 보조 위치에 기록되는 경우 LRS를 사용하여 해당 위치 내에도 복제됩니다.

다음 다이어그램은 데이터가 GRS 또는 RA-GRS로 복제되는 방법을 보여 줍니다.

:::image type="content" source="media/storage-redundancy/geo-redundant-storage.png" alt-text="GRS 또는 RA-GRS를 사용하여 데이터가 복제되는 방법을 보여 주는 다이어그램":::

### <a name="geo-zone-redundant-storage"></a>지역 영역 중복 스토리지

GZRS(지역 영역 중복 저장소)는 가용성 영역 전체의 중복성으로 제공되는 고가용성과 지역에서 복제를 통해 제공되는 지역 중단 방지를 결합합니다. GZRS 저장소 계정의 데이터는 기본 지역의 [Azure 가용성 영역](../../availability-zones/az-overview.md)에 복사되며 지역 재해로부터 보호하기 위해 보조 지리적 지역에도 복제됩니다. Microsoft는 재해 복구를 위해 최대 일관성, 내구성과 가용성, 뛰어난 성능 및 복원력이 필요한 응용 프로그램에 GZRS를 사용하는 것을 권장합니다.

GZRS 저장소 계정을 사용하면 가용성 영역을 사용할 수 없거나 복구할 수 없는 경우 데이터를 계속해서 읽고 쓸 수 있습니다. 또한 전체 지역 중단 또는 기본 지역을 복구할 수 없는 재해가 발생하는 경우에도 데이터가 지속됩니다. GZRS는 지정된 1년 동안 개체에 99.99999999999999%(16개의 9) 이상의 내구성을 제공하도록 디자인되었습니다.

다음 다이어그램은 GZRS 또는 RA-GZRS를 사용하여 데이터가 복제되는 방법을 보여 줍니다.

:::image type="content" source="media/storage-redundancy/geo-zone-redundant-storage.png" alt-text="GZRS 또는 RA-GZRS를 사용하여 데이터가 복제되는 방법을 보여 주는 다이어그램":::

범용 v2 저장소 계정만 GZRS 및 RA-GZRS를 지원합니다. 애플리케이션 계정 유형에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요. GZRS 및 RA-GZRS는 블록 Blob, 페이지 Blob(VHD 디스크 제외), 파일, 테이블 및 큐를 지원합니다.

GZRS 및 RA-GZRS는 다음 지역에서 지원됩니다.

- (아프리카) 남아프리카 북부
- (아시아 태평양) 동남아시아
- (아시아 태평양) 오스트레일리아 동부
- (아시아 태평양) 인도 중부
- (아시아 태평양) 일본 동부
- (아시아 태평양) 한국 중부
- (캐나다) 캐나다 중부
- (유럽) 북유럽
- (유럽) 서유럽
- (유럽) 프랑스 중부
- (유럽) 독일 중서부
- (유럽) 영국 남부
- (남미) 브라질 남부
- (미국) 미국 중부
- (미국) 미국 동부
- (미국) 미국 동부 2
- (미국) 미국 중남부
- (미국) 미국 서부 2

가격 책정에 대한 자세한 내용은 [Blob](https://azure.microsoft.com/pricing/details/storage/blobs), [파일](https://azure.microsoft.com/pricing/details/storage/files/), [큐](https://azure.microsoft.com/pricing/details/storage/queues/) 및 [테이블](https://azure.microsoft.com/pricing/details/storage/tables/)에 대한 가격 책정을 참조하세요.

## <a name="read-access-to-data-in-the-secondary-region"></a>보조 지역의 데이터에 대한 읽기 액세스

지역 중복 저장소(GRS 또는 GZRS 사용)는 지역 중단을 방지하기 위해 보조 지역의 다른 물리적 위치로 데이터를 복제합니다. 단, 고객 또는 Microsoft가 기본 지역에서 보조 지역으로 장애 조치(failover)를 시작하는 경우에는 해당 데이터를 읽을 수 있습니다. 보조 지역에 대한 읽기 액세스를 사용하도록 설정하면 항상 데이터를 읽을 수 있습니다. 주 지역을 사용할 수 없게 되는 상황도 포함됩니다. 보조 지역에 대한 읽기 액세스의 경우 RA-GRS(읽기 액세스 지역 중복 스토리지) 또는 RA-GZRS(읽기 액세스 지역 영역 중복 스토리지)를 사용하도록 설정합니다.

> [!NOTE]
> Azure Files는 RA-GRS(읽기 액세스 지역 중복 스토리지) 및 RA-GZRS(읽기 액세스 지역 중복 스토리지)를 지원하지 않습니다.

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>보조 지역에 대한 읽기 액세스를 위한 응용 프로그램 디자인

저장소 계정이 보조 지역에 대한 읽기 액세스로 구성된 경우 어떤 이유로든 기본 지역을 사용할 수 없으면 보조 지역에서 데이터 읽기로 원활하게 전환되도록 응용 프로그램을 디자인할 수 있습니다. 

보조 지역은 RA-GRS 또는 RA-GZRS를 사용하도록 설정한 후 읽기 액세스에 사용할 수 있으므로, 중단 시 보조 지역에서 올바르게 읽을 수 있도록 애플리케이션을 미리 테스트할 수 있습니다. 지역 중복을 활용하도록 애플리케이션을 디자인하는 방법에 관한 자세한 내용은 [지역 중복을 사용하여 고가용성 애플리케이션 디자인](geo-redundant-design.md)을 참조하세요.

보조 지역에 대한 읽기 액세스를 사용하도록 설정하면 기본 엔드포인트뿐만 아니라 보조 엔드포인트에서도 애플리케이션을 읽을 수 있습니다. 보조 끝점은 계정 이름에 *-보조* 접미사가 추가됩니다. 예를 들어 Blob Storage에 대한 기본 끝점이 `myaccount.blob.core.windows.net`인 경우 보조 끝점은 `myaccount-secondary.blob.core.windows.net`입니다. 저장소 계정에 대한 액세스 키는 기본 및 보조 끝점에 대해 동일합니다.

### <a name="check-the-last-sync-time-property"></a>마지막 동기화 시간 속성 확인

데이터는 보조 지역에 비동기적으로 복제되기 때문에 보조 지역은 기본 지역 뒤에 있는 경우가 많습니다. 기본 지역에서 오류가 발생하는 경우 기본 지역에 대한 모든 쓰기가 보조 지역에 아직 복제되지 않은 것일 수 있습니다.

보조 지역에 복제된 쓰기 작업을 확인하기 위해 응용 프로그램은 저장소 계정에 대한 **마지막 동기화 시간** 속성을 확인할 수 있습니다. 마지막 동기화 시간 이전에 기본 지역에 기록 작성된 모든 쓰기 작업이 보조 지역에 성공적으로 복제되었으므로 보조 지역에서 읽을 수 있습니다. 마지막 동기화 시간 이후에 기본 지역에 작성된 쓰기 작업은 보조 지역에 복제될 수도 있고 그렇지 않을 수도 있습니다. 즉, 읽기 작업에 사용하지 못할 수 있습니다.

Azure PowerShell, Azure CLI 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 **마지막 동기화 시간** 속성의 값을 쿼리할 수 있습니다. **마지막 동기화 시간** 속성은 GMT 날짜/시간 값입니다. 자세한 내용은 [저장소 계정에 대한 마지막 동기화 시간 속성 확인](last-sync-time-get.md)을 참조하세요.

## <a name="summary-of-redundancy-options"></a>중복성 옵션 요약

다음 섹션의 표에는 Azure Storage에 사용 가능한 중복성 옵션이 요약되어 있습니다.

### <a name="durability-and-availability-parameters"></a>내구성 및 가용성 매개 변수

다음 표에는 각 중복성 옵션에 대한 주요 매개 변수가 설명되어 있습니다.

| 매개 변수 | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| 지정된 1년 동안 개체의 내구성 비율 | 최소 99.999999999% | 최소 99.9999999999% | 최소 99.99999999999999% | 최소 99.99999999999999% |
| 읽기 요청에 대한 가용성 | 최소 99.9%(쿨 액세스 계층에 대해 99%) | 최소 99.9%(쿨 액세스 계층에 대해 99%) | GRS의 경우 최소 99.9%(쿨 액세스 계층에 대해 99%)<br /><br />RA-GRS의 경우 최소 99.99%(쿨 액세스 계층에 대해 99.9%) | GZRS의 경우 최소 99.9%(쿨 액세스 계층에 대해 99%)<br /><br />RA-GZRS의 경우 최소 99.99%(쿨 액세스 계층에 대해 99.9%) |
| 쓰기 요청에 대한 가용성 | 최소 99.9%(쿨 액세스 계층에 대해 99%) | 최소 99.9%(쿨 액세스 계층에 대해 99%) | 최소 99.9%(쿨 액세스 계층에 대해 99%) | 최소 99.9%(쿨 액세스 계층에 대해 99%) |
| 별도 노드에 여러 개의 데이터 사본이 유지됩니다. | 단일 지역 내 3개의 복사본 | 단일 지역 내에서 별도의 가용성 영역에 있는 세 개의 복사본 | 주 지역에 3개, 보조 지역에 3개를 포함하여 총 6개 복사본 | 주 지역의 개별 가용성 영역에서 3개, 보조 지역에 있는 3개의 로컬 중복 복사본을 포함하여 총 6개 복사본 |

### <a name="durability-and-availability-by-outage-scenario"></a>중단 시나리오별 내구성 및 가용성

다음 표는 저장소 계정에 유효한 중복성 유형에 따라 제공된 시나리오에서 데이터를 지속적으로 사용할 수 있는지 여부를 나타냅니다.

| 중단 시나리오 | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| 데이터 센터 내의 노드를 사용할 수 없게 됨 | 예 | 예 | 예 | 예 |
| 전체 데이터 센터(영역 또는 비영역)를 사용할 수 없게 됨 | 예 | 예 | 예<sup>1</sup> | 예 |
| 지역 전체 중단이 기본 지역에서 발생함 | 예 | 예 | 예<sup>1</sup> | 예<sup>1</sup> |
| 기본 지역을 사용할 수 없는 경우에는 보조 지역에 대한 읽기 액세스가 가능함 | 예 | 예 | 예(RA-GRS 사용) | 예(RA-GZRS 사용) |

<sup>1</sup> 기본 지역을 사용할 수 없는 경우 쓰기 가용성을 복원하려면 계정 장애 조치(failover)가 필요합니다. 자세한 내용은 [재해 복구 및 저장소 계정 장애 조치(failover)](storage-disaster-recovery-guidance.md)를 참조하세요.

### <a name="supported-azure-storage-services"></a>지원되는 Azure Storage 서비스

다음 표에서는 각 Azure Storage 서비스에서 지원되는 중복성 옵션을 보여 줍니다.

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Blob Storage<br />Queue Storage<br />Table Storage<br />Azure 파일<br />Azure Managed Disks | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure 파일 | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure 파일<br /> | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure 파일<br /> |

### <a name="supported-storage-account-types"></a>지원되는 스토리지 계정 형식

다음 표에서는 각 유형의 저장소 계정에서 지원 되는 중복성 옵션을 보여 줍니다. 저장소 계정 유형에 대한 자세한 내용은 [저장소 계정 개요](storage-account-overview.md)를 참조하세요.

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| 범용 v2<br /> 범용 v1<br /> Premium 블록 Blob<br /> 레거시 Blob<br /> 프리미엄 파일 공유 | 범용 v2<br /> Premium 블록 Blob<br /> 프리미엄 파일 공유 | 범용 v2<br /> 범용 v1<br /> 레거시 Blob | 범용 v2 |

모든 저장소 계정의 모든 데이터는 저장소 계정에 대한 중복성 옵션에 따라 복사됩니다. 블록 Blob, 추가 Blob, 페이지 Blob, 큐, 테이블 및 파일을 포함하는 개체가 복사됩니다. 보관 계층을 포함하여 모든 계층의 데이터가 복사됩니다. Blob 계층에 대한 자세한 내용은 [Azure Blob Storage: 핫, 쿨 및 보관 액세스 계층](../blobs/storage-blob-storage-tiers.md)을 참조하세요.

각 중복성 옵션에 대한 가격 책정 정보는 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

> [!NOTE]
> Azure Premium 디스크 저장소는 현재 LRS(로컬 중복 저장소)만 지원합니다. 블록 Blob Storage 계정은 특정 지역의 LRS(로컬 중복 저장소) 및 ZRS(영역 중복 저장소)를 지원합니다.

## <a name="data-integrity"></a>데이터 무결성

Azure Storage는 CRC(순환 중복 검사)를 사용하여 저장된 데이터의 무결성을 정기적으로 확인합니다. 데이터 손상이 감지되면 중복 데이터를 사용하여 복구됩니다. 또한 Azure Storage는 데이터를 저장하거나 검색할 때 데이터 패킷의 손상을 감지하기 위해 모든 네트워크 트래픽에 대한 체크섬을 계산합니다.

## <a name="see-also"></a>참고 항목

- [저장소 계정에 대한 마지막 동기화 시간 속성 확인](last-sync-time-get.md)
- [저장소 계정에 대한 중복성 옵션 변경](redundancy-migration.md)
- [지리적 중복성을 사용하여 고가용성 응용 프로그램 디자인](geo-redundant-design.md)
- [재해 복구 및 저장소 계정 장애 조치(failover)](storage-disaster-recovery-guidance.md)
