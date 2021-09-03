---
title: Azure Stack Edge Pro GPU, Pro R, Mini R 인증서 개요
description: Azure Stack Edge Pro GPU 디바이스에서 사용할 수 있는 모든 인증서의 개요를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: 558b31262a74a351ef17e42eb79772645f9a4641
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290377"
---
# <a name="what-are-certificates-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU의 인증서는 무엇인가요?

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 디바이스에 설치할 수 있는 인증서 유형에 대해 설명합니다. 이 문서에는 각 인증서 유형에 대한 세부 정보도 포함되어 있습니다.  

## <a name="about-certificates"></a>인증서 정보

인증서는 신뢰할 수 있는 타사(예: **인증 기관**)에서 **서명**(확인)한 엔터티(예: 도메인 이름)와 **공개 키** 간의 링크를 제공합니다.  인증서는 신뢰할 수 있는 공개 암호화 키를 배포하여 편리한 방법을 제공합니다. 따라서 인증서는 통신을 신뢰할 수 있고 암호화된 정보를 올바른 서버에 보내는지 확인합니다. 

## <a name="deploying-certificates-on-device"></a>디바이스에 인증서 배포

Azure Stack Edge 디바이스에서 자체 서명된 인증서를 사용하거나 자체 인증서를 가져올 수 있습니다.

- **디바이스 생성 인증서**: 디바이스가 처음 구성될 때 자체 서명된 인증서가 자동으로 생성됩니다. 필요한 경우 로컬 웹 UI를 통해 이러한 인증서를 다시 생성할 수 있습니다. 인증서가 재생성되면 디바이스에 액세스하는 데 사용되는 클라이언트에서 인증서를 다운로드하여 가져옵니다.

- **사용자 고유의 인증서 가져오기**: 원하는 경우 사용자 고유의 인증서를 가져올 수 있습니다. 사용자 고유의 인증서를 가져오려는 경우 따라야 하는 지침이 있습니다.

- 이 문서에서 Azure Stack Edge 디바이스에서 사용할 수 있는 인증서 유형을 이해하는 것으로 시작합니다.
- 다음으로 [각 인증서 유형에 대한 인증서 요구 사항](azure-stack-edge-gpu-certificate-requirements.md)을 검토합니다.  
- 그런 다음, [Azure PowerShell 통해 인증서를 만들](azure-stack-edge-gpu-create-certificates-powershell.md)거나 [준비 검사기 도구를 통해 인증서를 만들](azure-stack-edge-gpu-create-certificates-tool.md) 수 있습니다.
- 마지막으로 [인증서를 적절한 형식으로 변환](azure-stack-edge-gpu-prepare-certificates-device-upload.md)하여 디바이스에 업로드할 수 있도록 합니다.
- 디바이스에 [인증서를 업로드](azure-stack-edge-gpu-manage-certificates.md#upload-certificates-on-your-device)합니다.
- 디바이스에 액세스하는 [클라이언트의 인증서를 가져옵니다](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

## <a name="types-of-certificates"></a>인증서의 종류

디바이스에 대해 가져올 수 있는 다양한 유형의 인증서는 다음과 같습니다. 
- 서명 인증서
    - 루트 CA
    - 중급

- 노드 인증서

- 엔드포인트 인증서   
   
    - Azure Resource Manager 인증서
    - Blob 스토리지 인증서

- 로컬 UI 인증서
- IoT 디바이스 인증서
    
- Kubernetes 인증서
    
- Wi-Fi 인증서
- VPN 인증서  

- 암호화 인증서
    - 지원 세션 인증서

이러한 각 인증서는 다음 섹션에서 자세히 설명합니다.

## <a name="signing-chain-certificates"></a>서명 체인 인증서

이러한 인증서는 인증서에 서명하는 기관 또는 서명 인증 기관에 대한 인증서입니다. 

### <a name="types"></a>형식

이러한 인증서는 루트 인증서 또는 중간 인증서일 수 있습니다. 루트 인증서는 항상 자체 서명됩니다. 중간 인증서는 자체 서명되지 않으며 서명 기관에서 서명합니다.

#### <a name="caveats"></a>제한 사항

- 루트 인증서는 서명 체인 인증서여야 합니다.
- 루트 인증서는 다음 형식으로 디바이스에 업로드할 수 있습니다. 
    - **DER** – `.cer` 파일 확장명으로 사용할 수 있습니다.
    - **Base-64로 인코딩** – `.cer` 파일 확장명으로 사용할 수 있습니다.
    - **P7b** – 루트 및 중간 인증서를 포함하는 서명 체인 인증서에만 사용됩니다.
- 서명 체인 인증서는 항상 다른 인증서를 업로드하기 전에 업로드됩니다.


## <a name="node-certificates"></a>노드 인증서

<!--Your  device could be a 1-node device or a 4-node device.--> 디바이스의 모든 노드에서 서로 지속적으로 통신하므로 신뢰 관계가 있어야 합니다. 노드 인증서는 이러한 신뢰를 설정하는 방법을 제공합니다. 또한 https를 통해 원격 PowerShell 세션을 사용하여 디바이스 노드에 연결하는 경우에도 노드 인증서가 작동됩니다.

#### <a name="caveats"></a>제한 사항

- 노드 인증서는 내보낼 수 있는 프라이빗 키와 함께 `.pfx`형식으로 제공해야 합니다. 
- 하나의 와일드카드 노드 인증서 또는 4개의 개별 노드 인증서를 만들고 업로드할 수 있습니다. 
- DNS 도메인은 변경되지만 디바이스 이름이 변경되지 않는 경우 노드 인증서를 변경해야 합니다. 사용자 고유의 노드 인증서를 가져오는 경우 디바이스 일련 번호는 변경할 수 없고 도메인 이름만 변경할 수 있습니다.
- 노드 인증서를 만드는 경우 다음 표를 지침으로 사용합니다.
   
    |형식 |SN(주체 이름)  |SAN(주체 대체 이름)  |주체 이름 예 |
    |---------|---------|---------|---------|
    |노드|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>엔드포인트 인증서

디바이스에서 공개하는 모든 엔드포인트의 경우 신뢰할 수 있는 통신을 위해 인증서가 필요합니다. 엔드포인트 인증서에는 REST API를 통해 Azure Resource Manager 및 Blob 스토리지에 액세스할 때 필요한 인증서가 포함됩니다. 

사용자 고유의 서명된 인증서를 가져오는 경우 인증서의 해당 서명 체인도 필요합니다. 서명 체인, Azure Resource Manager 및 디바이스의 Blob 인증서의 경우 디바이스를 인증하고 통신하려면 클라이언트 컴퓨터에도 해당 인증서가 필요합니다.

#### <a name="caveats"></a>제한 사항

- 엔드포인트 인증서는 프라이빗 키가 포함된 `.pfx` 형식이어야 합니다. 서명 체인은 DER 형식(`.cer` 파일 확장명)이어야 합니다. 
- 사용자 고유의 엔드포인트 인증서를 가져오는 경우 개별 인증서 또는 다중 도메인 인증서가 될 수 있습니다. 
- 서명 체인을 가져오는 경우 엔드포인트 인증서를 업로드하기 전에 서명 체인 인증서를 업로드해야 합니다.
- 디바이스 이름 또는 DNS 도메인 이름이 변경되면 이러한 인증서를 변경해야 합니다.
- 와일드카드 엔드포인트 인증서를 사용할 수 있습니다.
- 엔드포인트 인증서의 속성은 일반적인 SSL 인증서의 속성과 비슷합니다. 
- 엔드포인트 인증서를 만드는 경우 다음 표를 사용합니다.

    |형식 |SN(주체 이름)  |SAN(주체 대체 이름)  |주체 이름 예 |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob 스토리지|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |두 엔드포인트 모두에 대한 다중 SAN 단일 인증서|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>로컬 UI 인증서

브라우저를 통해 디바이스의 로컬 웹 UI에 액세스할 수 있습니다. 이 통신의 보안을 유지하기 위해 사용자 고유의 인증서를 업로드할 수 있습니다. 

#### <a name="caveats"></a>제한 사항

- 로컬 UI 인증서도 내보낼 수 있는 프라이빗 키와 함께 `.pfx` 형식으로 업로드됩니다.
- 로컬 UI 인증서가 업로드되면 브라우저를 다시 시작하고 캐시를 지워야 합니다. 브라우저 특정의 지침을 참조하세요.

    |형식 |SN(주체 이름)  |SAN(주체 대체 이름)  |주체 이름 예 |
    |---------|---------|---------|---------|
    |로컬 UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge 디바이스 인증서

디바이스는 연결된 IoT Edge 디바이스에서 사용하도록 설정된 컴퓨팅이 있는 IoT 디바이스이기도 합니다. 이 IoT Edge 디바이스와 이 디바이스에 연결할 수 있는 다운스트림 디바이스 간의 보안 통신을 위해 IoT Edge 인증서도 업로드할 수 있습니다. 

디바이스와 함께 컴퓨팅 시나리오만 사용하려는 경우 해당 디바이스에는 사용할 수 있는 자체 서명된 인증서가 있습니다. 그러나 디바이스가 다운스트림 디바이스에 연결되어 있는 경우 사용자 고유의 인증서를 가져와야 합니다.

이 신뢰 관계를 사용하도록 설정하려면 다음 세 가지 IoT Edge 인증서를 설치해야 합니다.

- **루트 인증 기관 또는 소유자 인증 기관**
- **디바이스 인증 기관** 
- **디바이스 키 인증서**


#### <a name="caveats"></a>제한 사항

- IoT Edge 인증서는 `.pem` 형식으로 업로드됩니다. 

IoT Edge 인증서에 대한 자세한 내용은 [Azure IoT Edge 인증서 세부 정보](../iot-edge/iot-edge-certs.md#iot-edge-certificates) 및 [IoT Edge 프로덕션 인증서 만들기](../iot-edge/how-to-manage-device-certificates.md?preserve-view=true&view=iotedge-2020-11#create-production-certificates)를 참조하세요.

## <a name="kubernetes-certificates"></a>Kubernetes 인증서

디바이스에 Edge 컨테이너 레지스트리가 있는 경우 디바이스의 레지스트리에 액세스하는 클라이언트와의 보안 통신을 위해 Edge Container Registry 인증서가 필요합니다.

#### <a name="caveats"></a>제한 사항

- Edge Container Registry 인증서는 프라이빗 키를 사용하여 *.pfx* 형식으로 업로드해야 합니다.

## <a name="vpn-certificates"></a>VPN 인증서

VPN(지점 및 사이트 간)이 디바이스에 구성된 경우 자체 VPN 인증서를 가져와 통신을 신뢰하는지 확인할 수 있습니다. 루트 인증서는 Azure VPN Gateway에 설치되고 클라이언트 인증서는 지점 및 사이트 간 VNet에 연결하는 각 클라이언트 컴퓨터에 설치됩니다.

#### <a name="caveats"></a>제한 사항

- VPN 인증서는 프라이빗 키가 있는 *.pfx* 형식으로 업로드해야 합니다.
- VPN 인증서는 디바이스 이름, 디바이스 일련 번호 또는 디바이스 구성에 종속되지 않습니다. 외부 FQDN만 필요합니다.
- 클라이언트 OID가 설정되어 있는지 확인합니다.

자세한 내용은 [PowerShell을 사용하여 지점 및 사이트 간 인증서 생성 및 내보내기](../vpn-gateway/vpn-gateway-certificates-point-to-site.md#generate-and-export-certificates-for-point-to-site-using-powershell)를 참조하세요.

## <a name="wi-fi-certificates"></a>Wi-Fi 인증서

디바이스가 WPA2-Enterprise 무선 네트워크에서 작동하도록 구성된 경우 무선 네트워크를 통해 발생하는 모든 통신에 대해 Wi-Fi 인증서도 필요합니다. 

#### <a name="caveats"></a>제한 사항

- Wi-Fi 인증서는 프라이빗 키가 있는 *.pfx* 형식으로 업로드해야 합니다.
- 클라이언트 OID가 설정되어 있는지 확인합니다.

## <a name="support-session-certificates"></a>지원 세션 인증서

디바이스에 문제가 발생하는 경우 이러한 문제를 해결하기 위해 디바이스에서 원격 PowerShell 지원 세션이 열릴 수 있습니다. 이 지원 세션에서 암호화된 보안 통신을 사용하도록 설정하려면 인증서를 업로드할 수 있습니다.

#### <a name="caveats"></a>제한 사항

- 암호 해독 도구를 사용하여 프라이빗 키가 있는 해당 `.pfx` 인증서가 클라이언트 컴퓨터에 설치되어 있는지 확인합니다.
- 인증서에 대한 **키 사용** 필드가 **인증서 서명** 이 아닌지 확인합니다. 이를 확인하려면 마우스 오른쪽 단추로 인증서를 클릭하고, **열기** 를 선택한 다음, **세부 정보** 탭에서 **키 사용** 을 찾습니다. 

- 지원 세션 인증서는 `.cer` 확장명의 DER 형식으로 제공해야 합니다.


## <a name="next-steps"></a>다음 단계

[인증서 요구 사항 검토](azure-stack-edge-gpu-certificate-requirements.md)