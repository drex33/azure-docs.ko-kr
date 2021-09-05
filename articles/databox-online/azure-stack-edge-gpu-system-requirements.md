---
title: Microsoft Azure Stack Edge 시스템 요구 사항 | Microsoft Docs
description: Microsoft Azure Stack Edge 솔루션과 Azure Stack Edge에 연결된 클라이언트에 관한 시스템 요구 사항에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 04/26/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: d30ce268c7e117e5673af51ee32bea3b8923576b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536358"
---
# <a name="system-requirements-for-azure-stack-edge-pro-with-gpu"></a>GPU를 사용하는 Azure Stack Edge Pro의 시스템 요구 사항 

이 문서에서는 Microsoft Azure Stack Edge Pro GPU 솔루션 및 Azure Stack Edge Pro에 연결하는 클라이언트에 대한 중요한 시스템 요구 사항을 설명합니다. Azure Stack Edge Pro를 배포하기 전에 정보를 신중하게 검토하는 것이 좋습니다. 배포 및 후속 작업 중에 필요하면 이 정보를 다시 참조할 수 있습니다.

Azure Stack Edge Pro의 시스템 요구 사항은 다음과 같습니다.

- **호스트의 소프트웨어 요구 사항**: 지원되는 플랫폼, 로컬 구성 UI용 브라우저, SMB 클라이언트, 디바이스에 액세스하는 클라이언트에 대한 추가 요구 사항을 설명합니다.
- **디바이스의 네트워킹 요구 사항**: 물리적 디바이스가 작동하기 위한 네트워킹 요구 사항에 관한 정보를 제공합니다.

## <a name="supported-os-for-clients-connected-to-device"></a>디바이스에 연결된 클라이언트에 지원되는 OS

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>디바이스에 액세스하는 클라이언트에 지원되는 프로토콜

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-azure-storage-accounts"></a>지원되는 Azure Storage 계정

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-edge-storage-accounts"></a>지원되는 Edge 스토리지 계정

다음 Edge 스토리지 계정은 디바이스의 REST 인터페이스에서 지원됩니다. Edge 스토리지 계정은 디바이스에서 만들어집니다. 자세한 내용은 [Edge 스토리지 계정](azure-stack-edge-gpu-manage-storage-accounts.md#about-edge-storage-accounts)을 참조하세요.

|형식  |스토리지 계정  |의견  |
|---------|---------|---------|
|Standard     |GPv1: 블록 Blob         |         |

*페이지 Blob 및 Azure Files는 현재 지원되지 않습니다.

## <a name="supported-local-azure-resource-manager-storage-accounts"></a>지원되는 로컬 Azure Resource Manager 스토리지 계정

이러한 스토리지 계정은 로컬 Azure Resource Manager에 연결할 때 디바이스 로컬 API를 통해 생성됩니다. 지원되는 스토리지 계정은 다음과 같습니다.

|형식  |스토리지 계정  |의견  |
|---------|---------|---------|
|Standard     |GPv1: 블록 Blob, Page Blob        | SKU 유형은 Standard_LRS입니다       |
|Premium     |GPv1: 블록 Blob, Page Blob        | SKU 유형은 Premium_LRS입니다        |


## <a name="supported-storage-types"></a>지원되는 스토리지 형식

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>로컬 웹 UI에 지원되는 브라우저

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>네트워킹 포트 요구 사항

### <a name="port-requirements-for-azure-stack-edge-pro"></a>Azure Stack Edge Pro의 포트 요구 사항

다음 테이블에는 SMB, 클라우드 또는 관리 트래픽을 고려하여 방화벽에서 열려야 하는 포트가 나열되어 있습니다. 이 테이블에서 *인* 또는 *인바운드* 는 디바이스에 대한 들어오는 클라이언트 요청 액세스에서 방향을 참조합니다. 아웃 또는 아웃바운드는 배포 후 데이터를 외부로 보내는 Azure Stack Edge Pro 디바이스에서 방향을 참조합니다.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>IoT Edge에 대한 포트 요구 사항

Azure IoT Edge는 지원되는 IoT Hub 프로토콜을 사용하여 온-프레미스 Edge 디바이스에서 Azure 클라우드로의 아웃바운드 통신을 허용합니다. 인바운드 통신은 Azure IoT Hub에서 메시지를 Azure IoT Edge 디바이스에 푸시해야 하는 특정 시나리오에만 필요합니다(예: 클라우드에서 디바이스로 메시지 전송).

Azure IoT Edge 런타임을 호스트하는 서버의 포트 구성에 대한 다음 표를 사용하세요.

| 포트 번호 | 인 또는 아웃 | 포트 범위 | 필수 | 지침 |
|----------|-----------|------------|----------|----------|
| TCP 443(HTTPS)| 아웃       | WAN        | Yes      | IoT Edge 프로비전을 위해 아웃바운드로 엽니다. 수동 스크립트 또는 Azure IoT DPS(디바이스 프로비저닝 서비스)를 사용하는 경우 이 구성이 필요합니다.|

전체 정보를 보려면 [IoT Edge 배포에 대한 방화벽 및 포트 구성 규칙](../iot-edge/troubleshoot.md)으로 이동하세요.

## <a name="url-patterns-for-firewall-rules"></a>방화벽 규칙에 대한 URL 패턴

네트워크 관리자는 URL 패턴을 기준으로 하는 고급 방화벽 규칙이 인바운드 및 아웃바운드 트래픽을 필터링하도록 구성할 수 있습니다. Azure Stack Edge Pro 디바이스 및 Data Box Edge 서비스는 Azure Service Bus, Azure Active Directory Access Control, 스토리지 계정 및 Microsoft 업데이트 서버 등의 다른 Microsoft 애플리케이션에 의존합니다. 이러한 애플리케이션과 연결된 URL 패턴을 사용하여 방화벽 규칙을 구성할 수 있습니다. 이러한 애플리케이션과 연결된 URL 패턴은 달라질 수 있습니다. 이렇게 변경하려면 네트워크 관리자가 Azure Stack Edge Pro에 대한 방화벽 규칙을 모니터링하고 필요에 따라 업데이트해야 합니다.

Azure Stack Edge Pro 고정 IP 주소에 따라 대부분의 경우 자유롭게 아웃바운드 트래픽에 대한 방화벽 규칙을 설정하는 것이 좋습니다. 그러나 보안 환경을 만드는 데 필요한 고급 방화벽 규칙을 설정하려면 아래 정보를 사용할 수 있습니다.

> [!NOTE]
> - 디바이스(원본) IP는 항상 클라우드를 사용하도록 설정된 네트워크 인터페이스로 설정해야 합니다.
> - 대상 IP는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)로 설정해야 합니다.

### <a name="url-patterns-for-gateway-feature"></a>게이트웨이 기능용 URL 패턴

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>컴퓨팅 기능용 URL 패턴

| URL 패턴                      | 구성 요소 또는 기능                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft 컨테이너 레지스트리(필수)               |
| https://\*.azurecr.io                     | 개인 및 타사 컨테이너 레지스트리(선택 사항) | 
| https://\*.azure-devices.net              | IoT Hub 액세스(필수)                             | 
| https://\*.docker.com              | StorageClass(필수)                             | 

### <a name="url-patterns-for-monitoring"></a>모니터링할 URL 패턴

Linux용 Log Analytics 에이전트의 컨테이너화된 버전을 사용하는 경우 Azure Monitor에 다음 URL 패턴을 추가합니다.

| URL 패턴 | 포트 | 구성 요소 또는 기능 |
|-------------|-------------|----------------------------|
| https://\*ods.opinsights.azure.com | 443 | 데이터 수집 |
| https://\*.oms.opinsights.azure.com | 443 | OMS(Operations Management Suite) 온보딩 |
| https://\*.dc.services.visualstudio.com | 443 | Azure 퍼블릭 클라우드 Application Insights를 사용하는 에이전트 원격 분석 |

자세한 내용은 [컨테이너 정보를 모니터링하기 위한 네트워크 방화벽 요구 사항](../azure-monitor/containers/container-insights-onboard.md#network-firewall-requirements)을 참조하세요.

### <a name="url-patterns-for-gateway-for-azure-government"></a>Azure Government용 게이트웨이의 URL 패턴

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Azure Government 컴퓨팅 URL 패턴

| URL 패턴                      | 구성 요소 또는 기능                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Microsoft 컨테이너 레지스트리(필수)               |
| https://\*.azure-devices.us              | IoT Hub 액세스(필수)           |
| https://\*.azurecr.us                    | 개인 및 타사 컨테이너 레지스트리(선택 사항) | 

### <a name="url-patterns-for-monitoring-for-azure-government"></a>Azure Government 모니터링을 위한 URL 패턴

Linux용 Log Analytics 에이전트의 컨테이너화된 버전을 사용하는 경우 Azure Monitor에 다음 URL 패턴을 추가합니다.

| URL 패턴 | 포트 | 구성 요소 또는 기능 |
|-------------|-------------|----------------------------|
| https://\*ods.opinsights.azure.us | 443 | 데이터 수집 |
| https://\*.oms.opinsights.azure.us | 443 | OMS(Operations Management Suite) 온보딩 |
| https://\*.dc.services.visualstudio.com | 443 | Azure 퍼블릭 클라우드 Application Insights를 사용하는 에이전트 원격 분석 |


## <a name="internet-bandwidth"></a>인터넷 대역폭

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>컴퓨팅 크기 조정 고려 사항

솔루션을 개발하고 테스트하는 동안 사용자 환경을 사용하여 Azure Stack Edge Pro 디바이스에 충분한 용량이 있는지 확인하고 디바이스에서 최적의 성능을 얻을 수 있습니다.

고려해야 할 요소는 다음과 같습니다.

- **컨테이너 관련 사항** - 다음 사항을 고려하십시오.

    - 컨테이너 공간이 얼마인가요? 컨테이너가 소비하는 메모리, 스토리지 및 CPU의 양이 얼마인가요?
    - 워크로드에 있는 컨테이너가 몇 개인가요? 여러 개의 경량 컨테이너를 사용하거나 리소스를 많이 사용하는 컨테이너를 사용할 수 있습니다.
    - 컨테이너에 할당되는 리소스와 컨테이너가 소비하는 리소스(메모리 공간)는 무엇인가요?
    - 컨테이너는 몇 개의 계층을 공유하나요? 컨테이너 이미지는 계층의 스택으로 구성된 파일 번들입니다. 컨테이너 이미지의 경우 리소스 사용량 계산을 위한 계층 수와 개별 크기를 결정합니다.
    - 사용하지 않는 컨테이너가 있나요? 중지된 컨테이너도 디스크 공간을 차지합니다.
    - 컨테이너가 어떤 언어로 작성되어 있나요?
- **처리된 데이터의 크기** - 컨테이너가 처리하는 데이터의 양은 얼마인가요? 데이터가 디스크 공간을 사용하나요 아니면 메모리에서 처리되나요?
- **예상 성능** - 솔루션의 원하는 성능 특징은 무엇인가요? 

솔루션의 성능을 이해하고 개선하기 위해 다음을 사용할 수 있습니다:

- Azure Portal에서 사용할 수 있는 컴퓨팅 메트릭 Azure Stack Edge 리소스로 이동한 다음 **모니터링 > 메트릭** 으로 이동하세요. **Edge 컴퓨팅 - 메모리 사용량** 및 **에지 컴퓨팅 - CPU 백분율** 을 확인하여 사용 가능한 리소스와 소모되는 리소스 양을 파악합니다.
- 컴퓨팅 모듈을 모니터링하고 문제를 해결하려면 [Kubernetes 문제 디버깅](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)으로 이동하세요.

마지막으로, 프로덕션 환경에 배포하기 전에 데이터 집합에 대한 솔루션의 유효성을 검사하고 Azure Stack Edge Pro에서 성능을 정량화해야 합니다.

## <a name="next-step"></a>다음 단계

- [Azure Stack Edge Pro 배포](azure-stack-edge-gpu-deploy-prep.md)
