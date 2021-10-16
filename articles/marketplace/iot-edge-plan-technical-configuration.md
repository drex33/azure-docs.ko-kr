---
title: Azure Marketplace에서 IoT Edge 모듈 제품의 플랜 기술 구성 설정
description: Azure Marketplace에서 IoT Edge 모듈 제품의 플랜 기술 구성을 설정합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: 28e9bbcb4086cc9a9bd5e18ef27eb1264f086e29
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070302"
---
# <a name="set-plan-technical-configuration-for-an-iot-edge-module-offer"></a>IoT Edge 모듈 제품의 플랜 기술 구성 설정

IoT Edge 모듈 제품 유형은 IoT Edge 디바이스에서 실행되는 특정 유형의 컨테이너입니다. 플랜 **기술 구성** 탭에서는 고객이 모듈을 사용하는 데 도움이 되는 구성 설정과 함께 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 내 컨테이너 이미지 리포지토리의 참조 정보를 제공할 수 있습니다.

제품을 제출한 후에는 IoT Edge 컨테이너 이미지가 특정 퍼블릭 컨테이너 레지스트리의 Azure Marketplace로 복사됩니다. Azure 사용자의 모듈 사용에 대한 모든 요청은 프라이빗 컨테이너 레지스트리가 아닌 Azure Marketplace 공용 컨테이너 레지스트리에서 제공됩니다.

여러 플랫폼을 대상으로 태그를 사용하여 여러 버전의 모듈 컨테이너 이미지를 제공할 수 있습니다. 태그 및 버전 관리에 대한 자세한 내용은 [IoT Edge 모듈 기술 자산 준비](iot-edge-technical-asset.md)를 참조하세요.

## <a name="image-repository-details"></a>이미지 리포지토리 세부 정보

이미지 원본으로 **Azure Container Registry** 를 선택합니다.

**Azure 구독 ID** 를 제공하여 리소스 사용량이 보고되고 컨테이너 이미지를 포함하는 Azure Container Registry에 대한 서비스 요금이 청구될 수 있도록 합니다. 이 ID는 Azure Portal의 [구독 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 찾을 수 있습니다.

컨테이너 이미지로 Azure Container Registry를 포함하는 [**Azure 리소스 그룹 이름**](../azure-resource-manager/management/manage-resource-groups-portal.md)을 제공합니다. 리소스 그룹은 위의 구독 ID에서 액세스할 수 있어야 합니다. Azure Portal의 [리소스 그룹](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) 페이지에서 이름을 찾을 수 있습니다.

컨테이너 이미지를 포함하는 [**Azure Container Registry 이름**](../container-registry/container-registry-intro.md)을 제공합니다. 컨테이너 레지스트리는 앞에서 제공한 Azure 리소스 그룹에 있어야 합니다. 전체 로그인 서버 이름이 아닌 레지스트리 이름만 제공합니다. 이름에서 **azurecr.io** 를 생략합니다. Azure Portal의 [컨테이너 레지스트리 페이지](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)에서 레지스트리 이름을 찾을 수 있습니다.

컨테이너 이미지가 있는 Azure Container Registry와 관련된 [**Azure Container Registry에 대한 관리 사용자 이름**](../container-registry/container-registry-authentication.md#admin-account)을 제공합니다. 회사에서 레지스트리에 액세스할 수 있도록 하려면 사용자 이름 및 암호(다음 단계)가 필요합니다. 관리 사용자 이름 및 암호를 가져오려면 Azure CLI(명령줄 인터페이스)를 사용하여 **admin enabled** 속성을 **True** 로 설정합니다. 필요에 따라 Azure Portal에서 **관리 사용자** 를 **사용** 으로 설정할 수 있습니다.

:::image type="content" source="media/iot-edge/example-iot-update-container-registry.png" alt-text="컨테이너 레지스트리 업데이트 대화 상자를 보여 줍니다.":::

#### <a name="call-out-description"></a>설명

1. 관리 사용자

<br>Azure Container Registry와 연결되고 컨테이너 이미지가 있는 관리 사용자 이름에 대한 **Azure Container Registry의 관리자 암호** 를 제공합니다. 회사에서 레지스트리에 액세스할 수 있도록 하려면 사용자 이름 및 암호가 필요합니다. **Container Registry** > **액세스 키** 로 이동하거나 Azure CLI에서 [show 명령](/cli/azure/acr/credential#az-acr-credential-show)을 사용하여 Azure Portal의 암호를 가져올 수 있습니다.

:::image type="content" source="media/iot-edge/example-iot-access-keys.png" alt-text="Azure Portal의 액세스 키 화면을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>설명

1. 액세스 키
2. 사용자 이름
3. 암호

이미지를 포함하는 **Azure Container Registry 내의 리포지토리의 이름** 을 제공합니다. 레지스트리에 이미지를 푸시할 때 리포지토리의 이름을 지정합니다. [Container Registry](https://azure.microsoft.com/services/container-registry/) > **리포지토리 페이지** 로 이동하여 리포지토리의 이름을 찾을 수 있습니다. 자세한 내용은 [Azure Portal에서 컨테이너 레지스트리 리포지토리 보기](../container-registry/container-registry-repositories.md)를 참조하세요. 이름이 설정되면 변경할 수 없습니다. 계정의 각 제품마다 고유한 이름을 사용합니다.

> [!NOTE]
> Edge 모듈 인증에 대해 암호화된 Azure Container Registry는 지원되지 않습니다. 암호화를 사용하지 않고 Azure Container Registry를 만들어야 합니다.

## <a name="image-versions"></a>이미지 버전

고객은 업데이트를 게시할 때 Azure Marketplace에서 자동으로 업데이트를 다운로드할 수 있어야 합니다. 업데이트하지 않으려면 특정 버전의 이미지를 유지할 수 있어야 합니다. 이 작업을 수행하려면 이미지를 업데이트할 때마다 새 이미지 태그를 추가해야 합니다.

지원되는 모든 플랫폼에서 이미지의 최신 버전을 가리키는 **이미지 태그** 를 포함하려면 **이미지 버전 추가** 를 선택합니다. 또한 버전 태그를 포함해야 합니다(예: xx.xx.xx, xx는 숫자). 고객은 여러 플랫폼을 대상으로 하는 [매니페스트 태그](https://github.com/estesp/manifest-tool)를 사용해야 합니다. 매니페스트 태그에서 참조하는 모든 태그는 업로드할 수 있도록 추가되어야 합니다. 모든 매니페스트 태그(최신 태그 제외)는 X.Y 또는 X.Y.Z로 시작해야 합니다(X, Y, Z는 정수입니다). 예를 들어 최신 태그가 `1.0.1-linux-x64`, `1.0.1-linux-arm32` 및 `1.0.1-windows-arm32`를 가리키는 경우 이러한 6개의 태그는 이 필드에 추가되어야 합니다. 태그 및 버전 관리에 관한 자세한 내용은 [IoT Edge 모듈 기술 자산 준비](iot-edge-technical-asset.md)를 참조하세요.

> [!TIP]
> 테스트 중에 이미지를 식별할 수 있도록 이미지에 테스트 태그를 추가합니다.

## <a name="default-deployment-settings"></a>기본 배포 설정

IoT Edge 모듈을 배포하는 가장 일반적인 설정을 정의합니다(선택 사항). 이러한 기본 설정으로 IoT Edge 모듈 기본 제공을 시작하도록 하여 고객 배포를 최적화합니다.

**기본 경로**. IoT Edge 허브는 모듈, IoT Hub 및 디바이스 간의 통신을 관리합니다. 모듈과 IoT Hub 간에 데이터 입력 및 출력에 대한 기본 경로를 설정할 수 있습니다. 이를 통해 메시지를 처리하거나 추가 코드를 작성하기 위한 추가 서비스 없이 필요한 곳으로 메시지를 보낼 수 있습니다. 경로는 이름/값 쌍을 사용하여 생성됩니다. 최대 5개의 기본 경로 이름을 각각 최대 512자까지 정의할 수 있습니다.

경로 값에 올바른 [경로 구문](../iot-edge/module-composition.md#declare-routes)을 사용합니다(일반적으로 FROM/message/* INTO $upstream으로 정의됨). 즉, 모듈에서 전송하는 모든 메시지가 IoT Hub으로 이동합니다. 모듈을 참조하려면 공백 및 특수 문자 없이 **제품 이름** 이 될 해당 기본 모듈 이름을 사용합니다. 아직 알려지지 않은 다른 모듈을 참조하려면 <FROM_MODULE_NAME> 규칙을 사용하여 고객이 이 정보를 업데이트해야 한다는 것을 알도록 합니다. IoT Edge 경로에 대한 자세한 내용은 [경로 선언](../iot-edge/module-composition.md#declare-routes)을 참조하세요.

예를 들어 모듈 ContosoModule에서 ContosoInput에 대한 입력을 수신하고 ContosoOutput에서 데이터를 출력하는 경우 다음과 같은 두 가지 기본 경로를 정의하는 것이 좋습니다.

- 이름 #1: ToContosoModule
- 값 #1: FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- 이름 #2: FromContosoModuleToCloud
- 값 #2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**기본 모듈 쌍 desired 속성** 입니다. 모듈 쌍은 desired 속성을 비롯하여 모듈 인스턴스에 대한 상태 정보를 저장하는 IoT Hub의 JSON 문서입니다. 원하는 속성은 reported 속성과 함께 모듈 구성 또는 조건을 동기화하는 데 사용됩니다. 솔루션 백 엔드에서 desired 속성을 설정하고, 모듈에서 이 속성을 읽을 수 있습니다. 모듈은 desired 속성에서 변경 알림을 받을 수 있습니다. desired 속성은 최대 5개의 이름/값 쌍을 사용하여 생성되며 각 기본값은 512자 미만이어야 합니다. 최대 5개의 이름/값 쌍 desired 속성을 정의할 수 있습니다. 쌍 desired 속성의 값은 최대 4개 수준의 중첩된 계층 구조의 배열이 없는 이스케이프되지 않은 유효한 JSON이어야 합니다. 기본값에 필요한 매개 변수가 적합하지 않은 시나리오에서(예: 고객 서버의 IP 주소) 매개 변수를 기본값으로 추가할 수 있습니다. 쌍 desired 속성에 대한 자세한 내용은 [desired 속성 정의 또는 업데이트](../iot-edge/module-composition.md#define-or-update-desired-properties)를 참조하세요.

예를 들어 모듈이 쌍 desired 속성을 사용하여 동적으로 구성할 수 있는 새로 고침 빈도를 지원하는 경우 다음 기본 쌍 desired 속성을 정의하는 것이 좋습니다.

- 이름 #1: RefreshRate
- 값 #1: 60

**기본 환경 변수**. 환경 변수는 구성 프로세스를 돕는 모듈에 보충 정보를 제공합니다. 환경 변수는 이름/값 쌍을 사용하여 만들어집니다. 각 기본 환경 변수 이름 및 값은 512자 미만이어야 하며 최대 5개까지 정의할 수 있습니다. 기본값에 필요한 매개 변수가 적합하지 않은 경우(예: 고객 서버의 IP 주소) 매개 변수를 기본값으로 추가합니다.

예를 들어 모듈을 시작하기 전에 사용 약관에 동의해야 하는 경우 다음 환경 변수를 정의할 수 있습니다.

- 이름 #1: ACCEPT_EULA
- 값 #1: Y

**기본 컨테이너 만들기 옵션**. 컨테이너 만들기 옵션을 사용하여 IoT Edge 모듈 Docker 컨테이너를 만듭니다. IoT Edge는 Docker 엔진 API 만들기 컨테이너 옵션을 지원합니다. [컨테이너 나열](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList)에 있는 모든 옵션을 확인하세요. 만들기 옵션 필드는 유효한 JSON이고 이스케이프되지 않아야 하며 512자 미만이어야 합니다.

예를 들어 모듈에 포트 바인딩이 필요한 경우 다음 만들기 옵션을 정의합니다.

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="samples"></a>샘플

다음은 Azure Marketplace 계획 세부 정보의 예입니다(모든 나열된 가격은 예시용이며 실제 비용을 반영하지 않음).

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-plan.png" alt-text="Azure Marketplace 계획 세부 정보를 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>설명

1. Offer name
2. 플랜 이름
3. 플랜 설명

<br>다음은 Azure Portal 계획 정보의 예입니다(모든 나열된 가격은 예시용이며 실제 비용을 반영하지 않음).

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-plan-details.png" alt-text="Azure Portal 계획 세부 정보를 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>설명

1. Offer name
2. 플랜 이름
3. 플랜 설명

**초안 저장** 을 선택한 다음, 왼쪽 탐색 메뉴에서 **← 플랜 개요** 를 선택하여 플랜 개요 페이지로 돌아갑니다.

## <a name="next-steps"></a>다음 단계

- **Microsoft와 공동 판매** 하려면(선택 사항) 왼쪽 탐색 메뉴에서 해당 옵션을 선택합니다. 자세한 내용은 [공동 판매 파트너 참여](./co-sell-overview.md)를 참조하세요.
- 공동 판매를 설정하지 않았거나 완료한 경우 [제품을 검토하고 게시](review-publish-offer.md)해야 합니다.