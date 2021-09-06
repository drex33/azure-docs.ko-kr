---
title: Azure Stack Edge Pro GPU/Pro R/Mini R에 대한 데이터 보존 및 복원력
description: Azure Stack Edge에 대한 데이터 보존 상태를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: alkohli
ms.openlocfilehash: 6edb7aafdee046f2dd198d54e566c69b791cc1eb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535885"
---
# <a name="data-residency-and-resiliency-for-azure-stack-edge-preview"></a>Azure Stack Edge에 대한 데이터 보존 및 복원력(미리 보기)

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge에 대해 데이터 보존 및 복원력 동작을 이해하기 위해 필요한 정보 및 서비스에서 데이터 보존을 사용하는 방법을 설명합니다.  

## <a name="about-data-residency-for-azure-stack-edge"></a>Azure Stack Edge에 대한 데이터 보존 정보 

Azure Stack Edge 서비스는 서비스가 제공되는 모든 지리적 위치에서 고객 데이터를 저장하고 처리할 때 [Azure 지역 쌍](../best-practices-availability-paired-regions.md#azure-regional-pairs)을 사용합니다. 동남 아시아(싱가포르) 지역의 경우 서비스가 현재 홍콩과 쌍을 이룹니다. Azure 지역 쌍은 싱가포르에 저장된 데이터가 홍콩에 복제됨을 의미합니다. 싱가포르에는 고객 데이터가 국가 경계를 벗어나지 않도록 요구하는 법률이 적용됩니다. 

고객 데이터가 단일 지역에만 상주되도록 하기 위해 Azure Stack Edge 서비스에서 새로운 옵션이 사용됩니다. 이 옵션을 선택하면 서비스가 싱가포르 지역 내에서만 고객 데이터를 저장하고 처리할 수 있습니다. 고객 데이터는 홍콩으로 복제되지 않습니다. 민감한 데이터가 아닌 서비스 특정 메타데이터는 쌍으로 지정된 지역으로 계속 복제됩니다.  

이 옵션을 사용하면 영역 전체 중단 시 서비스 복원력이 유지되지만 지역 전체 중단 시에는 복원력이 유지되지 않습니다. 지역 전체 중단이 중요한 경우 지역별 쌍 기반 복제를 선택해야 합니다.

단일 지역 데이터 복원력 옵션은 동남아시아(싱가포르)에서만 사용 가능합니다. 다른 모든 지역의 경우 Azure Stack Edge는 고객이 지정한 지리적 위치에서 고객 데이터를 저장하고 처리합니다.

Azure Stack Edge 서비스의 데이터 보존 상태는 다음 서비스 특성에 따라 요약할 수 있습니다.

- 기존 Azure Stack Edge 주문 및 관리 서비스
- 새 주문 전달에 사용되는 새로운 Azure Edge Hardware Center(미리 보기)
<!--- Telemetry for the device and the service.
- Proactive Support log collection where any logs that the service generates are stored in a single region and are not replicated to the paired region.-->

Azure Stack Edge 서비스는 다음 종속 서비스와 통합되고 해당 동작도 요약되어 있습니다. 

- Azure Arc를 지원하는 Kubernetes
- Azure IoT Hub 및 Azure IoT Edge
<!--- Azure Key Vault -->


## <a name="azure-stack-edge-classic-ordering-and-management-resource"></a>Azure Stack Edge 클래식 주문 및 관리 리소스 

Azure Stack Edge 주문을 위해 기존 경험을 사용 중이면 서비스가 현재 Azure 지역 쌍을 사용하여 리전 전체 중단에 대한 데이터 복원력을 구현합니다. 싱가포르의 기존 Azure Stack Edge 리소스의 경우 이 데이터가 홍콩에 복제됩니다.

새 Azure Stack Edge 리소스를 만드는 경우 싱가포르에서만 데이터 보존을 사용하도록 선택할 수 있습니다. 이 옵션을 선택하면 데이터가 홍콩에 복제되지 않습니다. 지역 전체 서비스 중단이 발생할 때는 두 가지 옵션이 있습니다.

- 싱가포르 지역이 복원될 때까지 기다립니다.

- 다른 지역에 리소스를 만들고, 디바이스를 초기화하고, 새 리소스를 통해 디바이스를 관리합니다. 자세한 내용은 [Azure Stack Edge 디바이스 초기화 및 다시 활성화](azure-stack-edge-reset-reactivate-device.md)를 참조하세요.

## <a name="azure-edge-hardware-center-ordering-and-management-resource"></a>Azure Edge Hardware Center 주문 및 관리 리소스 

이제 새로운 Azure Edge Hardware Center 서비스(미리 보기)가 제공되며 이를 통해 Azure Stack Edge 리소스를 만들고 관리할 수 있습니다. 동남아시아 지역에서 주문할 때는 싱가포르 내에서만 데이터를 보존하고 복제하지 않도록 옵션을 선택할 수 있습니다. 

지역 전체 중단의 경우 주문 리소스에 액세스할 수 없습니다. 리소스 반환, 취소, 삭제는 지원되지 않습니다. 주문 상태에 대해 업데이트를 요청하거나 서비스 중단 중에 급하게 디바이스 반환을 시작해야 하는 경우 Microsoft 지원이 해당 요청을 처리합니다.

자세한 내용은 [Azure Edge Hardware Center를 통해 주문 만들기](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)를 참조하세요.


<!--## Azure Stack Edge telemetry

As Azure Stack Edge is a first-party Microsoft device, the telemetry from the device is automatically collected (without the user consent) and sent to Microsoft. This telemetry is stored in a common central location. This gathered telemetry provides valuable insights into enterprise deployments of Azure Stack Edge. This telemetry is also used for security, health, quality, and performance analysis.

- Microsoft collects telemetry for the infrastructure VMs (for example, Kubernetes master VM and Kubernetes worker VM) deployed on your Azure Stack Edge device and hosts. Telemetry is also gathered for other services that run on Azure Stack Edge device (for example, local Azure Resource Manager, Kubernetes dashboard). 
- The telemetry data is encrypted-in-transit as well at rest.
- Raw telemetry data sent to Microsoft is retained for 90 days. Aggregated data is retained for longer.
- For all the containerized workloads (deployed via IoT Edge and Kubernetes) and VM workloads, the application data is considered as the customer data. This data can only be accessed by the customer unless it pertains to the underlying infrastructure. 

For more information, see [Use the Kubernetes dashboard to monitor the Kubernetes cluster health on your Azure Stack Edge Pro device](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).-->

## <a name="azure-stack-edge-dependent-services"></a>Azure Stack Edge 종속 서비스

Azure Arc를 지원하는 Kubernetes, Azure IoT Hub 및 Azure IoT Edge 및 Azure Key Vault는 Azure Stack Edge와 통합되는 서비스입니다.

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc를 지원하는 Kubernetes 

Azure Arc를 지원하는 Kubernetes는 Azure Stack Edge의 추가 기능으로 제공됩니다. 싱가포르(동남아시아)의 경우 Azure Arc 데이터는 싱가포르 내에만 보존되고 홍콩으로 복제되지 않습니다. <!--If there is a region-wide outage, the service is not resilient.-->

<!--For all other regions, Azure Arc supports Azure Regional Pair and is resilient to any region-wide outages.--> 
<!--For more information, see [Data residency and resiliency for Azure Arc enabled Kubernetes clusters]().-->


### <a name="azure-iot"></a>Azure IoT

Azure IoT는 Azure Stack Edge에 대한 추가 기능으로 제공됩니다. 싱가포르(동남아시아)의 경우 Azure IoT는 쌍으로 지정된 지역을 사용하고 데이터를 홍콩에 복제합니다. 즉, Azure IoT는 지역 전체 중단에 대해 복원력이 있습니다. 

<!--For more information, see [Data residency and resiliency for Azure IoT]().-->


<!--### Azure Key Vault

Azure Key Vault currently uses Azure Regional Pair for region outage resiliency. For new Azure Key Vault resources, an option is now available that can be enabled at the subscription level. When enabled, if your service is deployed in Singapore (Southeast Asia), you can control the data replication to Hong Kong. 

If you choose to store and process the data only in Singapore region, then the service will not be resilient to region-wide outages. -->
<!--For more information, see [Data residency and resiliency for Azure Key Vault]().-->

## <a name="next-steps"></a>다음 단계

- [Azure 데이터 보존 요구 사항](https://azure.microsoft.com/global-infrastructure/data-residency/)에 대해 자세히 알아봅니다.