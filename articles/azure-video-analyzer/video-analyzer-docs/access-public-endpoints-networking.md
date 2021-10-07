---
title: 공용 끝점 및 네트워킹
description: Azure Video Analyzer는 관리, 수집 및 재생을 비롯 한 다양 한 제품 시나리오를 가능 하 게 하는 공용 네트워크 끝점 집합을 노출 합니다. 이 문서에서는 공용 끝점 및 네트워킹에 액세스 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: ad02a9ddd0ff2893e14d48f6698c83108f1acd63
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659605"
---
# <a name="public-endpoints-and-networking"></a>공용 끝점 및 네트워킹

Azure Video Analyzer는 관리, 수집 및 재생을 비롯 한 다양 한 제품 시나리오를 가능 하 게 하는 공용 네트워크 끝점 집합을 노출 합니다. 이 문서에서는 이러한 끝점을 설명 하 고 이러한 끝점을 사용 하는 방법에 대 한 세부 정보를 제공 합니다. 아래 다이어그램은 연결 된 Azure 서비스에서 노출 하는 일부 키 끝점 외에도 이러한 끝점을 보여 줍니다.

:::image type="content" source="./media/access-public-endpoints-networking/endpoints-and-networking.svg" alt-text="이미지는 Azure Video Analyzer 공용 끝점을 나타냅니다.":::

## <a name="video-analyzer-endpoints"></a>비디오 분석기 끝점 

이 섹션에서는 비디오 분석기 끝점의 목록을 제공 합니다.

### <a name="streaming"></a>스트리밍

* **목적**: [비디오 분석기 플레이어 위젯](player-widget.md) 또는 호환 되는 대시/HLS 플레이어에서 사용할 수 있는 오디오, 비디오 및 유추 데이터를 노출 합니다.
* **인증 & 권한 부여**: 끝점 권한 부여는 Video Analyzer 서비스에서 발급 된 토큰을 통해 적용 됩니다. 토큰은 단일 비디오로 제한 되며, 비디오 별로 클라이언트 및 관리 Api에 적용 된 권한 부여 규칙에 따라 암시적으로 발급 됩니다. 권한 부여 흐름은 Video Analyzer 플레이어 위젯에 의해 자동으로 처리 됩니다.
* **요구 사항**:이 끝점 집합에 대 한 액세스는 클라우드를 통해 콘텐츠를 재생 하는 데 필요 합니다.

### <a name="client-apis"></a>클라이언트 API

* **목적**: [video Analyzer 비디오 리소스](terminology.md#video)에 대 한 메타 데이터 (제목, 설명 등)를 노출 합니다. 이렇게 하면 고객이 개발한 클라이언트 응용 프로그램에서 리치 비디오 개체를 표시할 수 있습니다. 이 메타 데이터는 비디오 분석기 플레이어 위젯에 의해 활용 되며 고객 응용 프로그램에서 직접 활용할 수도 있습니다.
* **인증 및 권한 부여**: 끝점 권한 부여는 고객이 정의한 [액세스 정책과](access-policies.md) 고객이 발급 한 JWT 토큰의 조합을 통해 적용 됩니다. 하나 이상의 액세스 정책은 비디오 분석기 관리 Api를 통해 정의할 수 있습니다. 이러한 정책은 액세스 범위와 토큰에서 유효성을 검사 하는 데 필요한 클레임을 설명 합니다. 비디오 분석기 계정에서 액세스 정책이 생성 되지 않은 경우 액세스가 거부 됩니다.
* **요구 사항**:이 끝점에 대 한 액세스는 비디오 분석기 플레이어 위젯 및 비슷한 고객이 개발한 클라이언트 응용 프로그램이 비디오 메타 데이터 및 재생 권한 부여를 검색 하는 데 필요 합니다.

### <a name="edge-service-integration"></a>Edge 서비스 통합

* **목적**: 

    * 는 Video Analyzer edge 모듈에 의해 주기적으로 다운로드 되는 정책을 노출 합니다. 이러한 정책은 청구 및 연결 요구 사항과 같은에 지 모듈의 기본 동작을 제어 합니다.
    * 비디오 분석기 edge 모듈이 고객의 저장소 계정에 비디오 데이터를 기록 하는 데 사용할 수 있는 Azure storage SAS Url의 검색을 포함 하 여 클라우드로의 비디오 게시 오케스트레이션
* **인증 및 권한 부여**: 초기 인증은 Video Analyzer 관리 api에서 발급 한 수명이 짧은 프로 비전 토큰을 통해 수행 됩니다. 초기 핸드셰이크가 완료 되 면 모듈 및 서비스는이 시점에서 사용 되는 자동 회전 권한 부여 키 집합을 교환 합니다.
* **요구 사항**: Video Analyzer Edge 모듈의 올바른 기능을 사용 하려면이 끝점에 대 한 액세스가 필요 합니다. 36 시간 내에이 끝점에 연결할 수 없는 경우에 지 모듈의 작동이 중지 됩니다.

## <a name="telemetry"></a>원격 분석

* **목적**: Microsoft에서 Video Analyzer edge 모듈 사용 방법을 더 잘 이해 하 고 호환성, 성능 및 기타 제품 영역에서 수행할 수 있는 향후 개선 사항을 사전에 파악 하는 데 사용할 수 있는 원격 분석 데이터의 주기적인 전송 (선택 사항)입니다.
* **인증 및 권한 부여**: 권한 부여는 미리 설정 된 키를 기반으로 합니다.
* **요구 사항**:이 끝점에 대 한 액세스는 선택 사항이 며 제품 기능을 방해 하지 않습니다. 모듈 쌍 속성을 통해 데이터 수집 및 제출을 사용 하지 않도록 설정할 수 있습니다.

## <a name="associated-azure-endpoints"></a>연결 된 Azure 끝점 

> [!NOTE]
> 이 문서에서 설명 하는 끝점 목록은 연결 된 서비스 끝점의 포괄적인 목록이 아닙니다. 비디오 분석기의 정상적인 작동에 필요한 끝점의 정보를 제공 하는 목록입니다. 각 서비스에서 노출 하는 끝점의 전체 목록은 각 개별 Azure 서비스 설명서를 참조 하세요.

## <a name="azure-storage"></a>Azure Storage

* **목적**: [파이프라인이](pipeline.md) [비디오 싱크](pipeline.md#video-sink) 노드를 통해 클라우드에 비디오를 저장 하도록 구성 된 경우 오디오, 비디오 및 유추 데이터를 기록 합니다.
* **인증 및 권한 부여**: 권한 부여는 표준 Azure Storage 서비스 인증 및 권한 부여 적용을 통해 수행 됩니다. 이 경우 컨테이너 관련 SAS Url을 통해 저장소에 액세스 합니다.
* **요구 사항**:이 끝점에 대 한 액세스는 비디오 분석기 edge 파이프라인이 비디오를 클라우드에 보관 하도록 구성 된 경우에만 필요 합니다.

## <a name="iot-hub"></a>IoT Hub

* **목적**: Azure IoT 허브 및에 지 장치에 대 한 제어 및 데이터 평면입니다.
* **인증 및 권한 부여**: Azure IoT 허브 설명서를 참조 하세요.
* **요구 사항**: Azure Video Analyzer edge 모듈이 제대로 작동 하도록 하려면 Azure IoT edge 런타임으로 올바르게 구성 되 고 작동 하는 edge 장치가 필요 합니다.

##  <a name="tls-encryption"></a>TLS 암호화 

* **암호화 및 서버 인증**: 모든 비디오 분석기 끝점은 TLS 1.2 규격 끝점을 통해 노출 됩니다.

##  <a name="references"></a>참조 

공용:

* [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)
* [Azure IoT 허브 끝점 이해](../../iot-hub/iot-hub-devguide-endpoints.md)
* [Azure Private Link란?](../../private-link/private-link-overview.md)
* [Azure 서비스 태그 개요](../../virtual-network/service-tags-overview.md)

## <a name="next-steps"></a>다음 단계

[액세스 정책](access-policies.md) 
