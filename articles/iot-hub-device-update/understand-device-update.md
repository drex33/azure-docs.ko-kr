---
title: Azure IoT Hub에 대한 디바이스 업데이트 소개 | Microsoft Docs
description: IoT Hub에 대한 디바이스 업데이트는 IoT 디바이스에 대한 무선 업데이트(OTA)를 배포할 수 있도록 하는 서비스입니다.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 0b763f9f6c83f6255a460fc4441f8cfdb74193f0
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399374"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>IoT Hub에 대한 디바이스 업데이트(미리 보기) 개요

IoT Hub에 대한 디바이스 업데이트는 IoT 디바이스에 대한 무선 업데이트(OTA)를 배포할 수 있도록 하는 서비스입니다.

조직의 생산성 및 운영 효율성을 더욱 향상시킬 수 있는 IoT(사물 인터넷) 솔루션을 채택하는 속도는 계속해서 빨라질 것입니다. 이를 통해 이러한 솔루션을 구성하는 디바이스는 안정성과 보안을 기반으로 구축되며 대규모로 쉽게 연결하고 관리할 수 있습니다. IoT Hub에 대한 디바이스 업데이트는 고객이 작은 센서에서 게이트웨이 수준 디바이스에 이르기까지 모든 항목에 대해 무선 업데이트를 게시, 배포 및 관리하는 데 사용할 수 있는 엔드투엔드 플랫폼입니다. 

IoT 지원 디지털 변환의 모든 이점을 실현하려면 고객은 대규모 디바이스를 운영, 유지 관리 및 업데이트하는 이 기능이 필요합니다. IoT Hub에 대한 디바이스 업데이트를 구현할 때의 이점에 대해 알아봅니다. 보안 위협에 신속하게 대응하고 새로운 기능을 배포하여 자체 업데이트 플랫폼을 빌드하는 추가 개발 및 유지 관리 비용을 들이지 않고도 비즈니스 목표를 얻을 수 있습니다.

## <a name="support-for-a-wide-range-of-iot-devices"></a>광범위한 IoT 디바이스에 대한 지원


IoT Hub에 대한 디바이스 업데이트는 [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)와의 통합을 통해 최적화된 업데이트 배포 및 간소화된 작업을 제공하도록 설계되었습니다. 이러한 통합을 통해 기존 솔루션에서 디바이스 업데이트를 쉽게 채택할 수 있습니다. 거의 모든 디바이스를 연결하는 클라우드 호스팅 솔루션을 제공합니다. 디바이스 업데이트는 Linux 및 [Azure RTOS](https://azure.microsoft.com/services/rtos/)(실시간 운영 체제)를 비롯한 광범위한 IoT 운영 체제를 지원하며 오픈 소스를 통해 확장할 수 있습니다. STMicroelectronics, NXP, Renesas 및 Microchip을 포함한 반도체 파트너와 함께 IoT Hub 제품을 위한 디바이스 업데이트를 공동으로 개발하고 있습니다. 시작 가이드가 포함된 주요 반도체 평가 보드의 [샘플](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU)을 참조하여 MCU 클래스 디바이스에 무선(OTA) 업데이트를 구성, 빌드 및 배포하는 방법을 알아봅니다. 

디바이스 업데이트 에이전트 시뮬레이터 이진 및 Raspberry Pi 참조 Yocto 이미지를 모두 제공합니다.
IoT Hub에 대한 디바이스 업데이트는 Azure IoT Edge 디바이스 업데이트도 지원합니다. Ubuntu Server 18.04 amd64 플랫폼용 디바이스 업데이트 에이전트가 제공됩니다. IoT Hub에 대한 디바이스 업데이트는 위의 플랫폼 중 하나를 실행하지 않는 경우에도 오픈 소스 코드를 제공합니다. 실행 중인 배포에 에이전트를 이식할 수 있습니다.

디바이스 업데이트는 IoT 플러그 앤 플레이에서 작동하며 필요한 IoT 플러그 앤 플레이 인터페이스를 지원하는 모든 디바이스를 관리할 수 있습니다. 자세한 내용은 [IoT Hub 및 IoT 플러그 앤 플레이에 대한 디바이스 업데이트](device-update-plug-and-play.md)를 참조하세요.

## <a name="support-for-a-wide-range-of-update-artifacts"></a>광범위한 업데이트 아티팩트 지원

IoT Hub에 대한 디바이스 업데이트는 이미지 기반 및 패키지 기반의 두 가지 업데이트 형식을 지원합니다.

패키지 기반 업데이트는 디바이스의 특정 구성 요소 또는 애플리케이션만 변경 대상으로 하는 업데이트입니다. 그러면 대역폭 사용량이 줄어들며 업데이트를 다운로드하고 설치하는 시간을 줄일 수 있습니다. 패키지 업데이트는 일반적으로 업데이트를 적용할 때 디바이스의 가동 중지 시간을 최소화하고 이미지를 만드는 오버헤드를 방지할 수 있습니다.

이미지 업데이트는 디바이스의 최종 상태에서 더 높은 수준의 신뢰도를 제공합니다. 일반적으로 사전 프로덕션 환경과 프로덕션 환경 간에 이미지 업데이트 결과를 복제하는 것이 더 쉽습니다. 패키지 및 해당 종속성과 동일한 문제를 일으키지 않기 때문입니다.
원자성 특성 때문에 A/B 장애 조치(failover) 모델을 쉽게 채택할 수 있습니다.

정답은 없으며 특정 사용 사례에 따라 다르게 선택할 수 있습니다. IoT Hub에 대한 디바이스 업데이트는 이미지 및 패키지 형식의 업데이트를 모두 지원하므로 디바이스 환경에 적합한 업데이트 모델을 선택할 수 있습니다.

## <a name="flexible-features-for-updating-devices"></a>디바이스 업데이트를 위한 유연한 기능

IoT Hub에 대한 디바이스 업데이트 기능은 다음을 비롯하여 강력하고 유연한 환경을 제공합니다.

* Azure IoT Hub와 통합된 관리 UX 업데이트
* 디바이스 그룹화 및 업데이트 일정 제어를 통한 점진적 업데이트 출시
* 자동화 및 사용자 지정 포털 환경을 사용하도록 설정하는 프로그래밍 API
* 많은 이기종 디바이스에 대한 업데이트 준수 및 상태 요약 보기
* 원활한 롤백을 제공하기 위한 복원력 디바이스 업데이트(A/B) 지원
* Azure.com 포털을 통해 사용할 수 있는 구독 및 역할 기반 액세스 제어
* 클라우드에 연결되지 않은 디바이스를 업데이트할 수 있도록 온-프레미스 콘텐츠 캐시 및 중첩된 에지 지원
* 자세한 업데이트 관리 및 보고 도구 

사용자는 IoT Hub에 대한 디바이스 업데이트 관리 및 배포 제어를 사용하여 생산성을 극대화하고 귀중한 시간을 절약할 수 있습니다. IoT Hub에 대한 디바이스 업데이트에는 디바이스를 그룹화하고 업데이트를 배포해야 하는 디바이스를 지정하는 기능이 포함되어 있습니다. 또한 사용자는 업데이트 배포 상태를 확인하고 각 디바이스가 업데이트를 성공적으로 적용하는지 확인할 수 있습니다.

업데이트 오류가 발생하면 IoT Hub에 대한 디바이스 업데이트를 사용하여 업데이트를 적용하지 못한 디바이스를 식별하고 관련 오류 세부 정보를 확인할 수도 있습니다. 업데이트에 실패한 디바이스를 식별할 수 있으면 원본을 정확하게 찾아 내는 데 소요하는 많은 시간을 절약할 수 있습니다.

### <a name="best-in-class-security-at-global-scale"></a>글로벌 규모의 최고 수준의 보안

Microsoft Azure는 전 세계에 10억 개 이상의 IoT 디바이스를 지원합니다. 이 숫자는 매일 빠르게 증가하고 있습니다. IoT Hub에 대한 디바이스 업데이트는 이 환경과 Windows 업데이트 플랫폼에서 보여준 검증된 안정성을 기반으로 하며, 이를 통해 글로벌 규모에서 디바이스를 원활하게 업데이트할 수 있습니다.

IoT Hub에 대한 디바이스 업데이트는 Microsoft Azure용으로 개발된 포괄적인 클라우드-에지 보안을 사용하므로 고객이 처음부터 구축하는 방법을 파악하는 데 시간을 투자할 필요가 없습니다.


## <a name="device-update-workflows"></a>디바이스 업데이트 워크플로

디바이스 업데이트 기능은 에이전트 통합, 가져오기 및 관리의 세 가지 영역으로 나눌 수 있습니다.

### <a name="device-update-agent"></a>디바이스 업데이트 에이전트

업데이트 명령이 디바이스에서 수신되면 업데이트의 요청된 단계(다운로드, 설치 및 적용 중 하나)가 실행됩니다. 각 단계에서 배포의 현재 상태를 볼 수 있도록 IoT Hub를 통해 디바이스 업데이트에 상태가 반환됩니다. 진행 중인 업데이트가 없으면 상태가 "유휴" 상태로 반환됩니다. 배포는 언제든 취소될 수 있습니다.

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="디바이스 업데이트 에이전트 워크플로의 다이어그램" lightbox="media/understand-device-update/client-agent-workflow.png":::

디바이스 업데이트 에이전트에 대해 [자세히 알아보기](device-update-agent-overview.md) 

### <a name="importing"></a>가져오기

가져오기는 업데이트를 디바이스에 배포할 수 있도록 디바이스 업데이트에 수집되는 방법입니다. 디바이스 업데이트는 디바이스당 단일 업데이트의 출시를 지원합니다. 따라서 전체 OS 파티션을 한 번에 업데이트하는 전체 이미지 업데이트 또는 지정된 리포지토리에서 디바이스에 업데이트하려는 모든 패키지를 설명하는 [APT 매니페스트](device-update-apt-manifest.md)]에 이상적입니다. 업데이트를 디바이스 업데이트로 가져오려면 먼저 업데이트를 설명하는 가져오기 매니페스트를 만든 다음 업데이트 파일 및 가져오기 매니페스트를 인터넷 액세스가 가능한 위치에 업로드합니다. 그런 다음, Azure Portal 또는 [디바이스 업데이트 REST API](/rest/api/deviceupdate/)를 사용하여 비동기 업데이트 가져오기 프로세스를 시작할 수 있습니다. 디바이스 업데이트는 파일을 업로드하고 처리하며 IoT 디바이스에 배포하는 데 사용할 수 있도록 합니다.

중요한 콘텐츠의 경우 Azure Blob Storage에 대한 임시 SAS와 같은 SAS(공유 액세스 서명)를 사용하여 다운로드를 보호합니다. [SAS에 대해 자세히 알아보세요.](../storage/common/storage-sas-overview.md)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="IoT Hub에 대한 디바이스 업데이트 가져오기 워크플로의 다이어그램" lightbox="media/understand-device-update/import-update.png":::

가져오기 업데이트에 대해 [자세히 알아보기](import-concepts.md) 

### <a name="grouping-and-deployment"></a>그룹화 및 배포

업데이트를 가져온 후에는 디바이스 및 디바이스 클래스에 대해 호환되는 업데이트를 볼 수 있습니다.

디바이스 업데이트는 IoT Hub의 태그를 통해 **그룹** 의 개념을 지원합니다. 먼저 테스트 그룹에 업데이트를 배포하는 것은 프로덕션 출시 중에 문제가 발생하는 위험을 줄이기 위한 좋은 방법입니다.

디바이스 업데이트에서 배포는 적절한 콘텐츠를 특정한 호환 디바이스 집합에 연결하는 방법입니다. 디바이스 업데이트는 각 디바이스에 명령을 전송하고, 업데이트를 다운로드 및 설치하도록 지시하고, 상태를 되돌리는 프로세스를 오케스트레이션합니다.

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="IoT Hub에 대한 디바이스 업데이트 그룹화 및 배포 워크플로의 다이어그램" lightbox="media/understand-device-update/manage-deploy-updates.png":::

배포 개념에 대해 [자세히 알아보기](device-update-compliance.md)

디바이스 업데이트 그룹에 대해 [자세히 알아보기](device-update-groups.md)


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [장치 업데이트 계정 및 인스턴스 만들기](create-device-update-account.md)
