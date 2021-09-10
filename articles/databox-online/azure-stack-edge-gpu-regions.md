---
title: GPU가 있는 Microsoft Azure Stack Edge Pro의 지역 선택 | Microsoft Docs
description: Azure Stack Edge 서비스, 데이터 스토리지, GPU가 있는 Azure Stack Edge Pro용 디바이스, Azure Stack Edge Pro R용 디바이스, Azure Stack Edge Mini R용 디바이스의 지역을 선택하는 방법을 설명합니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/18/2021
ms.author: alkohli
ms.openlocfilehash: 7b585c61dd46339be71e66be66b402a0e107c194
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122568146"
---
# <a name="choosing-a-region-for-azure-stack-edge"></a>Azure Stack Edge의 지역 선택

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

이 문서에서는 Azure Stack Edge 서비스, 데이터 스토리지 및 Azure Stack Edge 디바이스의 디바이스 배송에 대한 지역 가용성을 설명하고 지역 선택이 서비스, 성능 및 청구에 미치는 영향을 설명합니다.


## <a name="overview"></a>개요

성능, 요구 사항 및 데이터 위치 기본 설정에 대한 고객의 요구 사항을 충족하기 위해 전 세계 여러 지역에서 Azure 데이터 센터가 운영됩니다. Azure 지리적 위치는 하나 이상의 Azure 지역을 포함하는 전 세계의 정의된 영역입니다. Azure 지역은 하나 이상의 데이터 센터를 포함하는 지리적 위치 내의 영역입니다.

Azure 지역을 선택하는 것은 매우 중요하며 지역 선택은 데이터 상주 및 주권, 서비스 가용성, 성능, 비용 및 중복성 등의 요인에 의해 영향을 받습니다. 지역 선택 방법에 대한 자세한 내용은 [적합한 Azure 지역 선택](https://azure.microsoft.com/overview/datacenters/how-to-choose/)을 참조하세요.

Azure Stack Edge Pro GPU 디바이스의 경우 지역 선택은 다음 요소에 따라 결정됩니다.

- Azure Stack Edge 서비스를 사용할 수 있는 지역입니다.
- 최고의 성능을 위해 Azure Stack Edge 데이터를 저장하는 스토리지 계정이 있어야 하는 지역입니다.
- Azure Stack Edge 디바이스를 배송할 수 있는 국가/지역입니다.


## <a name="region-availability-for-the-service"></a>서비스의 지역 가용성

Azure Stack Edge 서비스는 현재 미국 동부, 서유럽 및 SE 아시아 퍼블릭 지역에서 지원됩니다. **이 세 지역은 전 세계의 지리적 위치를 지원합니다.**

서비스 지역은 Azure Stack Edge 관리 리소스에 할당된 국가 또는 지역입니다. 관리 리소스에서는 Azure Stack Edge 서비스를 사용하여 Azure Stack Edge 디바이스를 활성화하고 배포하며 반환합니다.

Azure Stack Edge 서비스에서는 디바이스 상태, 문제, 오류, 경고 및 디바이스의 “활성” 여부도 모니터링합니다. 이 서비스는 청구를 위한 사용량 및 소비량 미터(디바이스의 컨트롤 플레인 정보)를 모니터링합니다. 관리 리소스에 할당된 지역은 청구가 발생하는 지역입니다.

디바이스를 활성화하려면 Azure Stack Edge 서비스에 연결해야 합니다. 서비스와 더 이상 상호 작용하지 않으려면 디바이스를 연결 해제 모드로 전환할 수 있습니다. 

데이터는 Azure Stack Edge 서비스를 통해 전달되지 않습니다. 데이터 원본의 고객 지역에 배포된 스토리지 계정과 디바이스 간에 데이터가 흐릅니다. 

일반적으로 디바이스를 배포하려는 지리적 지역에 가장 가까운 위치를 선택합니다. 하지만 디바이스와 서비스를 서로 다른 위치에 배포할 수도 있습니다.

## <a name="region-availability-for-data-storage"></a>데이터 스토리지의 지역 가용성

Azure Stack Edge 데이터는 Azure Storage 계정에 저장되며 이러한 계정은 모든 Azure 지역에서 사용할 수 있습니다. Azure 스토리지 계정을 만들 때 스토리지 계정의 기본 위치를 선택합니다. 이 선택에 따라 데이터가 있는 지역이 결정됩니다.

[디바이스에서 공유를 생성](azure-stack-edge-gpu-deploy-add-shares.md#add-a-share)할 때 스토리지 계정을 선택합니다. Azure Stack Edge 서비스와 Azure Storage는 두 개의 별도 위치에 있을 수 있습니다.

일반적으로 스토리지 계정에 대한 서비스에 가장 가까운 지역을 선택합니다. 그러나 가장 가까운 Microsoft Azure 지역이 실제로는 대기 시간이 가장 낮은 지역이 아닐 수 있습니다. 대기 시간은 네트워크 서비스 성능을 나타내며 디바이스의 성능도 나타냅니다. 따라서 다른 지역의 스토리지 계정을 선택하는 경우, 사용자의 서비스와 스토리지 계정에 연결된 지역 간의 대기 시간에 알아야 합니다.

## <a name="region-of-device"></a>디바이스 지역

Azure Stack Edge 모델을 사용할 수 있는 국가와 지역을 알아보려면 제품 개요를 참조하세요.

- [GPU가 포함된 Azure Stack Edge Pro의 지역 가용성](azure-stack-edge-gpu-overview.md#region-availability)
- [Azure Stack Edge Pro R의 지역 가용성](azure-stack-edge-pro-r-overview.md#region-availability)
- [Azure Stack Edge Pro Mini R의 지역 가용성](azure-stack-edge-mini-r-overview.md#region-availability)

Microsoft는 물리적 하드웨어를 배송하고 Azure Stack Edge의 하드웨어 예비 부품 교체를 해당 지역에 제공할 수 있습니다.

> [!IMPORTANT]
> Azure Stack Edge가 지원되지 않는 지역에 Azure Stack Edge 물리적 디바이스를 배치하지 마세요. Microsoft는 Azure Stack Edge가 지원되지 않는 국가/지역에 교체용 부품을 배송할 수 없습니다.


## <a name="next-steps"></a>다음 단계

* [다양한 Azure Stack Edge 모델의 가격 책정](https://azure.microsoft.com/pricing/details/azure-stack/edge/)에 대해 자세히 알아봅니다.
* [ your Azure Stack Edge Pro 디바이스 배포 준비](azure-stack-edge-gpu-deploy-prep.md).
