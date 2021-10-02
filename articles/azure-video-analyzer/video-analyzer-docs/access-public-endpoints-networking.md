---
title: 퍼블릭 엔드포인트 및 네트워킹
description: Azure Video Analyzer는 관리, 분석 및 재생을 비롯한 다양한 제품 시나리오를 가능하게 하는 공용 네트워크 엔드포인트 집합을 노출합니다. 이 문서에서는 퍼블릭 엔드포인트 및 네트워킹에 액세스하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 0debf9b00bc8c3d78810fb377aa6e065589e6f96
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389649"
---
# <a name="public-endpoints-and-networking"></a>퍼블릭 엔드포인트 및 네트워킹

Azure Video Analyzer는 관리, 분석 및 재생을 비롯한 다양한 제품 시나리오를 가능하게 하는 공용 네트워크 엔드포인트 집합을 노출합니다. 이 문서에서는 이러한 엔드포인트에 대해 설명하고 엔드포인트 사용 방법에 대한 몇 가지 세부 정보를 제공합니다. 아래 다이어그램은 연결된 Azure 서비스에서 노출하는 일부 주요 엔드포인트 외에도 이러한 엔드포인트를 설명합니다.

:::image type="content" source="./media/access-public-endpoints-networking/endpoints-and-networking.svg" alt-text="이미지는 Azure Video Analyzer 공용 엔드포인트를 나타냅니다.":::

## <a name="video-analyzer-endpoints"></a>Video Analyzer 엔드포인트 

이 섹션에서는 Video Analyzer 엔드포인트 목록을 제공합니다.

### <a name="streaming"></a>스트리밍

* **목적:** [Video Analyzer 플레이어 위젯](player-widget.md) 또는 호환되는 DASH/HLS 플레이어에서 사용할 수 있는 오디오, 비디오 및 유추 데이터를 노출합니다.
* **인증 & 권한 부여:** 엔드포인트 권한 부여는 Video Analyzer 서비스에서 발급한 토큰을 통해 적용됩니다. 토큰은 단일 비디오로 제한되며 비디오별로 클라이언트 및 관리 API에 적용된 권한 부여 규칙에 따라 암시적으로 발급됩니다. 권한 부여 흐름은 Video Analyzer 플레이어 위젯에 의해 자동으로 처리됩니다.
* **요구 사항:** 클라우드를 통한 콘텐츠 재생을 위해 이 엔드포인트 집합에 액세스해야 합니다.

### <a name="client-apis"></a>클라이언트 API

* **목적:** [Video Analyzer 비디오 리소스](terminology.md#video)에 대한 메타데이터(제목, 설명 등)를 노출합니다. 이를 통해 고객이 개발한 클라이언트 애플리케이션에서 풍부한 비디오 개체를 표시할 수 있습니다. 이 메타데이터는 Video Analyzer 플레이어 위젯에서 활용되며 고객 애플리케이션에서 직접 활용할 수도 있습니다.
* **인증 및 권한 부여:** 엔드포인트 권한 부여는 고객 정의 [액세스 정책과](access-policies.md) 고객이 발급한 JWT 토큰의 조합을 통해 적용됩니다. Video Analyzer 관리 API를 통해 하나 이상의 액세스 정책을 정의할 수 있습니다. 이러한 정책은 액세스 범위 및 토큰에서 유효성을 검사하는 데 필요한 클레임을 설명합니다. Video Analyzer 계정에 만든 액세스 정책이 없으면 액세스가 거부됩니다.
* **요구 사항:** 비디오 분석기 플레이어 위젯 및 유사한 고객 개발 클라이언트 애플리케이션이 비디오 메타데이터를 검색하고 재생 권한 부여를 위해 이 엔드포인트에 액세스해야 합니다.

### <a name="edge-service-integration"></a>Edge 서비스 통합

* **목적**: 

    * Video Analyzer 에지 모듈에서 정기적으로 다운로드하는 정책을 노출합니다. 이러한 정책은 청구 및 연결 요구 사항과 같은 에지 모듈의 기본 동작을 제어합니다.
    * Video Analyzer 에지 모듈이 비디오 데이터를 고객의 스토리지 계정에 기록할 수 있도록 하는 Azure Storage SAS URL 검색을 포함하여 클라우드에 비디오 게시를 오케스트레이션합니다.
* **인증 및 권한 부여:** 초기 인증은 Video Analyzer 관리 API에서 발급한 수명이 짧은 프로비전 토큰을 통해 수행됩니다. 초기 핸드셰이크가 완료되면 모듈과 서비스는 이 시점부터 사용되는 자동 회전 권한 부여 키 집합을 교환합니다.
* **요구 사항:** Video Analyzer Edge 모듈을 올바르게 작동하려면 이 엔드포인트에 액세스해야 합니다. 36시간 내에 이 엔드포인트에 연결할 수 없는 경우 에지 모듈의 작동이 중지됩니다.

## <a name="telemetry"></a>원격 분석

* **목적:** Microsoft에서 Video Analyzer 에지 모듈을 사용하는 방법을 더 잘 이해하고 호환성, 성능 및 기타 제품 영역에서 수행할 수 있는 향후 개선 작업을 사전에 식별할 수 있도록 원격 분석 데이터의 선택적 주기적 제출입니다.
* **인증 및 권한 부여:** 권한 부여는 미리 설정된 키를 기반으로 합니다.
* **요구 사항:** 이 엔드포인트에 대한 액세스는 선택 사항이며 제품 기능을 방해하지 않습니다. 데이터 수집 및 제출은 모듈 쌍 속성을 통해 비활성화할 수 있습니다.

## <a name="associated-azure-endpoints"></a>연결된 Azure 엔드포인트 

> [!NOTE]
> 이 문서에서 설명하는 엔드포인트 목록은 연결된 서비스 엔드포인트의 포괄적인 목록이 아닙니다. Video Analyzer의 정상적인 작동에 필요한 엔드포인트의 정보 목록입니다. 각 서비스에서 노출하는 엔드포인트의 전체 목록은 각 개별 Azure 서비스 설명서를 참조하세요.

## <a name="azure-storage"></a>Azure Storage

* **목적:** 파이프라인(TODO: 링크)이 비디오 싱크(TODO: pipeline.md 섹션에 대한 링크) 노드를 통해 클라우드에 비디오를 저장하도록 구성된 경우 오디오, 비디오 및 유추 데이터를 기록합니다.
* **인증 및 권한 부여:** 권한 부여는 표준 Azure Storage 서비스 인증 및 권한 부여 적용에 의해 수행됩니다. 이 경우 스토리지는 컨테이너별 SAS URL을 통해 액세스됩니다.
* **요구 사항:** 비디오 분석기 에지 파이프라인이 비디오를 클라우드에 보관하도록 구성된 경우에만 이 엔드포인트에 액세스해야 합니다.

## <a name="iot-hub"></a>IoT Hub

* **목적:** Azure IoT Hub 및 Edge 디바이스에 대한 제어 및 데이터 평면입니다.
* **인증 및 권한 부여:** Azure IoT Hub 설명서를 참조하세요.
* **요구 사항:** Azure Video Analyzer 에지 모듈이 올바르게 작동하도록 하려면 Azure IoT Edge 런타임을 사용하여 에지 디바이스를 올바르게 구성하고 작동해야 합니다.

## <a name="114----tls-encryption"></a>1.1.4 TLS 암호화 

* **암호화 및 서버 인증:** 모든 Video Analyzer 엔드포인트는 TLS 1.2 규격 엔드포인트를 통해 노출됩니다.

## <a name="115----references"></a>1.1.5 참조 

공용:

* [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)
* [Azure IoT Hub 엔드포인트 이해](../../iot-hub/iot-hub-devguide-endpoints.md)
* [Azure Private Link란?](../../private-link/private-link-overview.md)
* [Azure 서비스 태그 개요](../../virtual-network/service-tags-overview.md)

## <a name="next-steps"></a>다음 단계

[액세스 정책](access-policies.md) 
