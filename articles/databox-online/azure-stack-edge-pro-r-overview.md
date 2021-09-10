---
title: Microsoft Azure Stack Edge Pro R 개요 | Microsoft Docs
description: Azure에 대한 네트워크 기반 전송에 물리적 디바이스를 사용하며 가혹한 환경에 배포될 수 있는 스토리지 솔루션인 Azure Stack Edge Pro R 디바이스를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 08/11/2021
ms.author: alkohli
ms.openlocfilehash: ab133e05cd08be42b212e72f801ee59ffa104d4a
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429644"
---
# <a name="what-is-the-azure-stack-edge-pro-r"></a>Azure Stack Edge Pro R이란?

Azure Stack Edge Pro R은 가혹한 환경에서 사용하도록 설계된 러기드, 에지 컴퓨팅 디바이스입니다. Azure Stack Edge Pro R은 Hardware-as-a-Service 솔루션으로 제공됩니다. Microsoft는 네트워크 스토리지 게이트웨이의 역할을 하고 가속화된 AI 추론을 사용하는 GPU(그래픽 처리 디바이스)가 기본 제공되는 클라우드 관리 디바이스를 제공합니다.

이 문서에서는 Azure Stack Edge Pro R 솔루션의 개요, 주요 기능 및 이 디바이스를 배포할 수 있는 시나리오를 소개합니다.


## <a name="key-capabilities"></a>주요 기능

Azure Stack Edge Pro R의 기능은 다음과 같습니다.

|기능 |Description  |
|---------|---------|
|러기드 하드웨어| 가혹한 환경을 견디도록 설계된 러기드 서버급 하드웨어입니다. 휴대용 전송 사례에 포함된 디바이스입니다. |
|클라우드 관리     |디바이스 및 서비스는 Azure Portal를 통해 관리됩니다.|
|Edge 컴퓨팅 워크로드   |데이터를 분석, 처리, 필터링할 수 있습니다. VM 및 컨테이너화된 워크로드를 지원합니다.|
|가속 AI 유추| Nvidia T4 GPU에서 사용됩니다.|
|데이터 액세스     | 클라우드 API를 사용하여 Azure Storage Blob 및 Azure Files에서 데이터에 직접 액세스해 클라우드에서 데이터를 추가로 처리할 수 있습니다. 디바이스의 로컬 캐시는 가장 최근에 사용한 파일에 빠르게 액세스하는 데 사용됩니다.|
|연결 해제 모드| 필요한 경우 Azure Stack Hub를 통해 디바이스와 서비스를 관리할 수 있습니다. 오프라인 모드에서 애플리케이션을 배포, 실행, 관리합니다. <br> 연결 끊김 모드에서도 오프라인 업로드 시나리오가 지원됩니다.|
|지원되는 파일 전송 프로토콜     |데이터 수집을 위한 표준 SMB, NFS 및 REST 프로토콜이 지원됩니다. <br> 지원되는 버전에 대한 자세한 내용은 [Azure Stack Edge Pro R 시스템 요구 사항](azure-stack-edge-gpu-system-requirements.md)을 참조하세요.|
|데이터 새로 고침     | 클라우드의 최신 데이터로 로컬 파일을 새로 고칠 수 있습니다.|
|이중 암호화    | 자동 암호화 드라이브를 사용하면 첫 번째 암호화 계층이 제공됩니다. VPN은 두 번째 암호화 계층을 제공합니다. *https* 를 통해 클라우드로 안전하게 데이터를 전송하고 로컬에서 데이터를 암호화할 수 있도록 BitLocker가 지원됩니다.|
|대역폭 제한| 사용량이 많은 시간 동안 대역폭 사용을 제한하는 데 사용됩니다.|
|간편한 주문| Azure Edge Hardware Center(미리 보기)를 통해 디바이스의 대량 주문 및 추적이 가능합니다.|

<!--|Scale out file server| Available as 1-node and 4-node cluster configurations|-->

## <a name="use-cases"></a>사용 사례

다음은 데이터를 Azure로 보내기 전에 Edge에서 유추하고 전처리하는 빠른 ML(Machine Learning)에 Azure Stack Edge Pro R을 사용할 수 있는 다양한 시나리오입니다.

- **Azure Machine Learning에서 유추** - Azure Stack Edge Pro R을 사용하면 ML 모델을 실행하여 결과를 빠르게 생성한 다음 클라우드로 데이터를 전송하기 전에 해당 결과와 관련한 조치를 취할 수 있습니다. ML 모델을 계속 다시 학습시키고 개선할 수 있도록 필요에 따라 전체 데이터 집합이 전송될 수 있습니다. Azure Stack Edge Pro R 디바이스에서 Azure ML 하드웨어 가속 모델을 사용하는 방법에 대한 자세한 내용은 [Azure Stack Edge Pro R에서 Azure ML 하드웨어 가속 모델 배포](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)를 참조하세요.

- **데이터 전처리** - 데이터를 Azure에 전송하기 전에 변환하여 실행 가능한 데이터 세트를 만듭니다. 전처리를 사용하여 다음 작업을 수행할 수 있습니다.

    - 데이터 집계
    - 데이터를 수정합니다. 예를 들어 개인 데이터를 제거합니다.
    - 스토리지 및 대역폭을 최적화하거나 추가 분석을 위해 하위 세트 데이터를 만듭니다.
    - IoT 이벤트 분석 및 대응

- **네트워크를 통해 Azure로 데이터 전송** - Azure Stack Edge Pro R을 사용하면 Azure로 데이터를 쉽고 빠르게 전송하여 추가 컴퓨팅 및 분석을 수행하거나 데이터를 보관할 수 있습니다.

## <a name="components"></a>구성 요소

Azure Stack Edge Pro R 솔루션은 Azure Stack Edge 리소스, Azure Stack Edge Pro R 러기드 디바이스, 물리적 디바이스 및 로컬 웹 UI로 구성됩니다.

- **Azure Stack Edge Pro R 물리적 디바이스** - 러기드 전송 사례에 포함된 1-노드 컴퓨팅 및 스토리지 디바이스입니다. 선택적 UPS(무정전 전원 공급 장치)도 사용할 수 있습니다.

    ![Azure Stack Edge Pro R 1노드 디바이스](media/azure-stack-edge-pro-r-overview/device-image-1.png)

    [!INCLUDE [azure-stack-edge-gateway-edge-hardware-center-overview](../../includes/azure-stack-edge-gateway-edge-hardware-center-overview.md)]    

    자세한 내용은 [Azure Stack Edge Pro R 디바이스에 대한 주문 만들기](azure-stack-edge-pro-r-deploy-prep.md#create-a-new-resource)를 참조하세요.

- **Azure Stack Edge 리소스** – 다양한 지리적 위치에서 액세스할 수 있는 웹 인터페이스에서 러기드 Azure Stack Edge Pro R 디바이스를 관리할 수 있는 Azure Portal의 리소스입니다. Azure Stack Edge 리소스를 사용하여 리소스를 생성/관리하고, 디바이스와 경고를 확인/관리하고, 공유를 관리합니다.  

- **Azure Stack Edge Pro R 로컬 웹 UI** - 주로 디바이스의 초기 구성에 사용되는 Azure Stack Edge Pro R 디바이스의 브라우저 기반 로컬 사용자 인터페이스입니다. 로컬 웹 UI를 사용하여 진단을 실행하고, Azure Stack Edge Pro 디바이스를 종료 및 다시 시작하고, 복사 로그를 보고, Microsoft 지원에 연락하여 서비스를 요청하세요.

    [!INCLUDE [azure-stack-edge-gateway-local-web-ui-languages](../../includes/azure-stack-edge-gateway-local-web-ui-languages.md)]


## <a name="region-availability"></a>지역 가용성

데이터를 전송하는 Azure Stack Edge Pro R 물리적 디바이스, Azure 리소스 및 대상 스토리지 계정이 모두 같은 지역에 있지 않아도 됩니다.

- **리소스 가용성** - Azure Stack Edge 리소스를 사용할 수 있는 모든 지역 목록을 보려면 [지역별로 사용 가능한 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)으로 이동합니다. 

- **디바이스 가용성** - Azure Stack Edge Pro R 디바이스를 사용할 수 있는 모든 국가 목록을 보려면 [Azure Stack Edge Pro R 가격 책정](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeProR)의 **Azure Stack Edge Pro R** 탭에서 **가용성** 섹션으로 이동합니다.

- **대상 스토리지 계정** - 데이터를 저장하는 스토리지 계정은 모든 Azure 지역에서 사용할 수 있습니다. 성능을 최적화하려면 스토리지 계정이 Azure Stack Edge Pro R 데이터를 저장하는 지역이 디바이스를 사용하는 지역과 가까이 있어야 합니다. 스토리지 계정의 지역과 디바이스의 지역 간 거리가 멀면 대기 시간이 길어지고 성능이 저하됩니다.

Azure Stack Edge 서비스는 비 지역 서비스입니다. 자세한 내용은 [Azure의 지역 및 가용성 영역](../availability-zones/az-overview.md)을 참조하세요. Azure Stack Edge 서비스는 특정 Azure 지역에 대한 종속성이 없으므로 영역 전체 중단 및 지역 전체 중단에 탄력적으로 대처할 수 있습니다.

Azure Stack Edge 서비스, 디바이스, 데이터 스토리지 지역을 선택할 때의 고려 사항에 대한 자세한 내용은 [Azure Stack Edge 지역 선택](azure-stack-edge-gpu-regions.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro R 시스템 요구 사항](azure-stack-edge-gpu-system-requirements.md)을 검토합니다.
<!--- Understand the [Azure Stack Edge Pro R limits](azure-stack-edge-limits.md).-->
