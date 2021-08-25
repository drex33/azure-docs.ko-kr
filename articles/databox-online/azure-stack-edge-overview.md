---
title: Microsoft Azure Stack Edge Pro FPGA 개요 | Microsoft Docs
description: 물리적 디바이스를 사용하여 네트워크를 통해 Azure로 데이터를 전송하는 스토리지 솔루션인 Azure Stack Edge Pro FPGA에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/01/2021
ms.author: alkohli
ms.openlocfilehash: 47fab96220a3a2bc63864c86bdff210673c01aa0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738105"
---
# <a name="what-is-azure-stack-edge-pro-fpga"></a>Azure Stack Edge Pro FPGA란?

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

Azure Stack Edge Pro with FPGA는 네트워크 데이터 전송 기능이 포함된 AI 지원 에지 컴퓨팅 디바이스입니다. 이 문서에서는 FPGA 솔루션, 주요 기능 및 배포 시나리오가 포함된 Azure Stack Edge Pro의 개요를 제공합니다.

Azure Stack Edge Pro with FPGA는 Hardware-as-a-Service 솔루션입니다. Microsoft는 가속화된 AI 유추를 지원하고 네트워크 스토리지 게이트웨이의 모든 기능을 포함하는 기본 제공 FPGA(Field Programmable Gate Array)가 있는 클라우드 관리 디바이스를 제공합니다.

Azure Data Box Edge는 Azure Stack Edge로 재브랜드되었습니다.

## <a name="use-cases"></a>사용 사례

다음은 데이터를 Azure로 보내기 전에 Edge에서 유추하고 전처리하는 빠른 ML(Machine Learning)에 Azure Stack Edge Pro FPGA를 사용할 수 있는 다양한 시나리오입니다.

- **Azure Machine Learning에서 유추** - Azure Stack Edge Pro FPGA를 사용하면 ML 모델을 실행하여 결과를 빠르게 생성한 다음, 클라우드로 데이터를 전송하기 전에 해당 결과와 관련한 조치를 취할 수 있습니다. ML 모델을 계속 다시 학습시키고 개선할 수 있도록 필요에 따라 전체 데이터 집합이 전송될 수 있습니다. Azure Stack Edge Pro FPGA 디바이스에서 Azure ML 하드웨어 가속 모델을 사용하는 방법에 대한 자세한 내용은 [Azure Stack Edge Pro FPGA에서 Azure ML 하드웨어 가속 모델 배포](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)를 참조하세요.

- **데이터 전처리** - 데이터를 Azure에 전송하기 전에 변환하여 실행 가능한 데이터 세트를 만듭니다. 전처리를 사용하여 다음 작업을 수행할 수 있습니다. 

    - 데이터 집계
    - 데이터를 수정합니다. 예를 들어 개인 데이터를 제거합니다.
    - 스토리지 및 대역폭을 최적화하거나 추가 분석을 위해 하위 세트 데이터를 만듭니다.
    - IoT 이벤트 분석 및 대응 

- **네트워크를 통해 Azure로 데이터 전송** - Azure Stack Edge Pro FPGA를 사용하면 Azure로 데이터를 쉽고 빠르게 전송하여 추가 컴퓨팅 및 분석을 수행하거나 데이터를 보관할 수 있습니다. 

## <a name="key-capabilities"></a>주요 기능

Azure Stack Edge Pro FPGA의 기능은 다음과 같습니다.

|기능 |Description  |
|---------|---------|
|가속 AI 유추| 기본 제공 FPGA를 통해 지원됩니다.|
|컴퓨팅       |데이터를 분석, 처리, 필터링할 수 있습니다.|
|고성능 | 고성능 컴퓨팅 및 데이터 전송.|
|데이터 액세스     | 클라우드 API를 사용하여 Azure Storage Blob 및 Azure Files에서 데이터에 직접 액세스해 클라우드에서 데이터를 추가로 처리할 수 있습니다. 디바이스의 로컬 캐시는 가장 최근에 사용한 파일에 빠르게 액세스하는 데 사용됩니다.|
|클라우드 관리     |디바이스 및 서비스는 Azure Portal를 통해 관리됩니다.  |
|오프라인 업로드     | 연결 끊김 모드에서도 오프라인 업로드 시나리오가 지원됩니다.|
|지원되는 프로토콜     | 데이터 수집을 위한 표준 SMB 및 NFS 프로토콜이 지원됩니다. <br> 지원되는 버전에 대한 자세한 내용은 [Azure Stack Edge Pro FPGA 시스템 요구 사항](azure-stack-edge-system-requirements.md)을 참조하세요.|
|데이터 새로 고침     | 클라우드의 최신 데이터로 로컬 파일을 새로 고칠 수 있습니다.|
|암호화    | *https* 를 통해 클라우드로 안전하게 데이터를 전송하고 로컬에서 데이터를 암호화할 수 있도록 BitLocker가 지원됩니다.|
|대역폭 제한| 사용량이 많은 시간 동안 대역폭 사용을 제한하는 데 사용됩니다.|
|ExpressRoute | ExpressRoute를 통해 보안이 강화되었습니다. 로컬 디바이스에서 클라우드 스토리지 엔드포인트로의 트래픽이 ExpressRoute를 통해 이동하는 피어링 구성을 사용합니다. 자세한 내용은 [ExpressRoute 개요](../expressroute/expressroute-introduction.md)를 참조하세요.

## <a name="components"></a>구성 요소

Azure Stack Edge Pro FPGA 솔루션은 Azure Stack Edge 리소스, Azure Stack Edge Pro FPGA 물리적 디바이스 및 로컬 웹 UI로 구성됩니다.

* **Azure Stack Edge Pro FPGA 물리적 디바이스**: Azure로 데이터를 전송하도록 구성할 수 있는 Microsoft 제공 1U 랙 탑재 서버입니다.
    
* **Azure Stack Edge 리소스**: 다양한 지리적 위치에서 액세스할 수 있는 웹 인터페이스에서 Azure Stack Edge Pro FPGA 디바이스를 관리할 수 있는 Azure Portal의 리소스입니다. Azure Stack Edge 리소스를 사용하여 리소스를 생성 및 관리하고, 공유를 관리하고, 디바이스와 경고를 보고 관리합니다.
  
   <!--[The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

   Azure Stack Edge Pro FPGA의 수명이 다함에 따라 새 Azure Stack Edge Pro FPGA 디바이스에 대한 주문이 채워지지 않습니다. 신규 고객인 경우 워크로드에 대한 Azure Stack Edge Pro - GPU 디바이스를 사용하여 탐색하는 것이 좋습니다. 자세한 내용은 [GPU를 사용하는 Edge Pro Azure Stack Pro란?](azure-stack-edge-gpu-overview.md)을 참조하세요. GPU 디바이스가 있는 Azure Stack Edge Pro를 주문하는 방법에 대한 자세한 내용은 [Azure Stack Edge Pro - GPU에 대한 새 리소스 만들기](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)를 참조하세요.

   기존 고객인 경우 기존 Azure Stack Edge Pro FPGA 디바이스를 교체하거나 다시 설정해야 하는 경우에도 새 Azure Stack Edge 리소스를 만들 수 있습니다. 지침은 [Azure Stack Edge Pro FPGA 디바이스에 대한 주문 만들기](azure-stack-edge-deploy-prep.md#create-new-resource-for-existing-device)를 참조하세요.

* **Azure Stack Edge Pro FPGA 로컬 웹 UI** - 로컬 웹 UI를 사용하여 진단을 실행하고, Azure Stack Edge Pro FPGA 디바이스를 종료 및 다시 시작하고, 복사 로그를 보고, Microsoft 지원에 연락하여 서비스를 요청하세요.

    <!--![The Azure Stack Edge Pro FPGA local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    웹 기반 UI 사용에 대한 자세한 내용은 [웹 기반 UI를 사용하여 Azure Stack Edge Pro FPGA 관리](azure-stack-edge-manage-access-power-connectivity-mode.md)를 참조하세요.

## <a name="region-availability"></a>지역 가용성

데이터를 전송하는 Azure Stack Edge Pro FPGA 물리적 디바이스, Azure 리소스 및 대상 스토리지 계정이 모두 같은 지역에 있지 않아도 됩니다.

- **리소스 가용성** - Azure Stack Edge 리소스를 사용할 수 있는 모든 지역 목록을 보려면 [지역별로 사용 가능한 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)을 참조하세요. Azure Stack Edge Pro FPGA는 Azure Government 클라우드에도 배포할 수 있습니다. 자세한 내용은 [Azure Government란?](../azure-government/documentation-government-welcome.md)을 참조하세요.
    
- **대상 스토리지 계정** - 데이터를 저장하는 스토리지 계정은 모든 Azure 지역에서 사용할 수 있습니다. 성능을 최적화하려면 스토리지 계정이 Azure Stack Edge Pro FPGA 데이터를 저장하는 지역이 디바이스를 사용하는 지역과 가까이 있어야 합니다. 스토리지 계정의 지역과 디바이스의 지역 간 거리가 멀면 대기 시간이 길어지고 성능이 저하됩니다.

Azure Stack Edge 서비스는 비 지역 서비스입니다. 자세한 내용은 [Azure의 지역 및 가용성 영역](../availability-zones/az-overview.md)을 참조하세요. Azure Stack Edge 서비스는 특정 Azure 지역에 대한 종속성이 없으므로 영역 전체 중단 및 지역 전체 중단에 탄력적으로 대처할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro FPGA 시스템 요구 사항](azure-stack-edge-system-requirements.md)을 검토합니다.
- [Azure Stack Edge Pro FPGA 제한](azure-stack-edge-limits.md)을 이해합니다.
- Azure Portal에서 [Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-prep.md)를 배포합니다.