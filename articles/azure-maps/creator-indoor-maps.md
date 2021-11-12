---
title: Azure Maps Creator에서 실내 맵 작업
description: 이 문서에서는 Azure Maps Creator 서비스에 적용되는 개념을 소개합니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/11/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 0e4f25e140c9b7d2693c94ead25b375e2298adbc
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400018"
---
# <a name="creator-for-indoor-maps"></a>실내 맵용 Creator

이 문서에서는 Azure Maps Creator에 적용되는 개념과 도구를 소개합니다. Azure Maps Creator API 및 SDK를 사용하기 전에 이 문서를 참조하는 것이 좋습니다.

Creator를 사용하여 실내 맵 데이터를 기준으로 하는 맵 기능을 갖춘 애플리케이션을 개발할 수 있습니다. 이 문서에서는 맵 데이터를 업로드, 변환, 생성 및 사용하는 프로세스에 대해 설명합니다.  일반적으로 워크플로는 고유한 전문 지식 및 책임 영역에 속하는 두 명의 서로 다른 개인이 완료합니다.

- 맵 작성자: 맵 데이터 큐레이팅 및 준비를 담당합니다.
- Creator 맵 데이터 사용자: 애플리케이션에서 고객 맵 데이터를 활용합니다.

다음 다이어그램에서는 전체 워크플로를 보여 줍니다.

![Creator 맵 데이터 워크플로](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator"></a>Azure Maps Creator 만들기

Creator 서비스를 사용하려면 Gen 2 가격 책정 계층이 있는 Azure 지도 계정에서 Azure 지도 Creator를 만들어야 합니다. Azure Maps에서 Azure Maps Creator를 만드는 방법에 대한 자세한 내용은 [Azure Maps Creator 관리](how-to-manage-creator.md)를 참조하세요.

> [!TIP]
> 가격 책정 정보는 Azure [지도 가격 책정의](https://aka.ms/CreatorPricing) *작성자* 섹션을 참조하세요.

## <a name="creator-authentication"></a>Creator 인증

Creator는 IAM(Azure Maps Access Control) 설정을 상속합니다. 데이터 액세스에 대한 모든 API 호출은 인증 및 권한 부여 규칙과 함께 전송되어야 합니다.

Creator 사용량 현황 데이터는 Azure Maps 사용량 차트 및 활동 로그에 통합됩니다. 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

>[!Important]
>다음을 사용하는 것이 좋습니다.
>
> - Creator 서비스를 사용하여 Azure Maps 계정으로 빌드된 모든 솔루션에서 Azure Active Directory(Azure AD). Azure AD에 대한 자세한 내용은 [Azure AD 인증](azure-maps-authentication.md#azure-ad-authentication)을 참조하세요.
>
>- 역할 기반 액세스 제어 설정. 이러한 설정을 사용하여 맵 제작자는 Azure Maps Data Contributor 역할을 할 수 있고, Creator 맵 데이터 사용자는 Azure Maps Data Reader 역할을 할 수 있습니다. 자세한 내용은 [역할 기반 액세스 제어를 통해 권한 부여](azure-maps-authentication.md#authorization-with-role-based-access-control)를 참조하세요.

## <a name="creator-data-item-types"></a>Creator 데이터 항목 형식

Creator 서비스는 다음 섹션에서 정의하고 설명하는 다양한 데이터 형식을 만들고 저장하고 사용합니다. Creator 데이터 항목은 다음 형식일 수 있습니다.

- 변환된 데이터
- 데이터 세트
- 타일 세트
- 기능 상태 세트

## <a name="upload-a-drawing-package"></a>그리기 패키지 업로드

Creator는 업로드된 그리기 패키지를 변환하여 실내 맵 데이터를 수집합니다. 그리기 패키지는 건조되거나 리모델링된 시설을 나타냅니다. 그리기 패키지 요구 사항에 대한 자세한 내용은 [그리기 패키지 요구 사항](drawing-requirements.md)을 참조하세요.

[Azure Maps 데이터 업로드 API](/rest/api/maps/data-v2/update-preview)를 사용하여 그리기 패키지를 업로드합니다. 그리기 패키징이 업로드된 후 데이터 업로드 API는 사용자 데이터 식별자(`udid`)를 반환합니다. 그런 다음, `udid`를 사용하여 업로드된 패키지를 실내 맵 데이터로 변환할 수 있습니다.

## <a name="convert-a-drawing-package"></a>그리기 패키지 변환

[Azure Maps Conversion 서비스](/rest/api/maps/v2/conversion)는 업로드된 그리기 패키지를 실내 맵 데이터로 변환합니다. 또한 Conversion 서비스는 패키지의 유효성을 검사합니다. 유효성 검사 문제는 다음 두 가지 유형으로 분류됩니다.

- 오류: 오류가 검색되면 변환 프로세스가 실패합니다. 오류가 발생하면 Conversion 서비스에서 [Azure Maps 그리기 오류 시각화 도우미](drawing-error-visualizer.md) 독립 실행형 웹 애플리케이션에 대한 링크를 제공합니다. 그리기 오류 시각화 도우미를 사용하여 변환 프로세스 중에 발생한 [그리기 패키지 경고 및 오류](drawing-conversion-error-codes.md)를 검사할 수 있습니다. 오류를 수정한 후에는 패키지를 업로드하고 변환할 수 있습니다.
- 경고: 경고가 검색되면 변환이 성공합니다. 그러나 모든 경고를 검토하고 해결하는 것이 좋습니다. 경고는 변환의 일부가 무시되었거나 자동으로 수정되었음을 나타냅니다. 경고가 해결되지 않으면 후속 프로세스에서 오류가 발생할 수 있습니다.
자세한 내용은 [그리기 패키지 경고 및 오류](drawing-conversion-error-codes.md)를 참조하세요.

## <a name="create-indoor-map-data"></a>실내 맵 데이터 만들기

Azure Maps Creator는 맵 만들기를 지원하는 다음 서비스를 제공합니다.

- [데이터 세트 서비스](/rest/api/maps/v2/dataset).
- [타일 세트 서비스](/rest/api/maps/v2/tileset).
타일 세트 서비스를 사용하여 데이터 세트의 벡터 기반 표현을 만듭니다. 애플리케이션에서 타일 세트를 사용하여 데이터 세트의 시각적 타일 기반 보기를 표시할 수 있습니다.
- [기능 상태 서비스](/rest/api/maps/v2/feature-state). 기능 상태 서비스를 사용하여 동적 맵 스타일 지정을 지원할 수 있습니다. 애플리케이션은 동적 맵 스타일 지정을 사용하여 실시간 이벤트를 IoT 시스템에서 제공하는 공간에 반영할 수 있습니다.

### <a name="datasets"></a>데이터 세트

데이터 세트는 실내 맵 기능의 컬렉션입니다. 실내 맵 기능은 변환된 그리기 패키지에 정의된 시설을 나타냅니다. [데이터 세트 서비스](/rest/api/maps/v2/dataset)를 사용하여 데이터 세트가 만들어지면 원하는 수의 [타일 세트](#tilesets) 또는 [기능 상태 세트](#feature-statesets)를 만들 수 있습니다.

언제든지 개발자는 [데이터 세트 서비스](/rest/api/maps/v2/dataset)를 사용하여 언제든지 시설을 기존 데이터 세트에 추가하거나 제거할 수 있습니다. API를 사용하여 기존 데이터 세트를 업데이트하는 방법에 대한 자세한 내용은 [데이터 세트 서비스](/rest/api/maps/v2/dataset)의 추가 옵션을 참조하세요. 데이터 세트를 업데이트하는 방법에 대한 예제는 [데이터 유지 관리](#data-maintenance)를 참조하세요.

### <a name="tilesets"></a>타일 세트

타일 세트는 균일한 그리드 타일 세트를 나타내는 벡터 데이터의 컬렉션입니다. 개발자는 [타일 세트 서비스](/rest/api/maps/v2/tileset)를 사용하여 데이터 세트에서 타일 세트를 만들 수 있습니다.

여러 콘텐츠 단계를 반영하기 위해 동일한 데이터 세트에서 여러 타일 세트를 만들 수 있습니다. 예를 들어 가구와 장비가 있는 타일 세트와 가구 및 장비가 없는 타일 세트를 각각 만들 수 있습니다. 최신 데이터 업데이트가 있는 타일 세트와 최신 데이터 업데이트가 없는 또 다른 타일 세트를 각각 생성하도록 선택할 수 있습니다.

타일 세트는 벡터 데이터 외에도 맵 렌더링 최적화를 위한 메타데이터를 제공합니다. 예를 들어 타일 세트 메타데이터에는 타일 세트의 최소 및 최대 확대/축소 수준이 포함됩니다. 또한 메타데이터는 타일 세트의 지리적 범위를 정의하는 경계 상자를 제공합니다. 애플리케이션은 경계 상자를 사용하여 정확한 중심점을 프로그래밍 방식으로 설정할 수 있습니다. 타일 세트 메타데이터에 대한 자세한 내용은 [타일 세트 나열 API](/rest/api/maps/v2/tileset/list)를 참조하세요.

타일 세트를 만든 후에는 [Render V2 서비스](#render-v2-get-map-tile-api)를 통해 검색할 수 있습니다.

타일 세트가 오래되고 더 이상 유용하지 않은 경우 타일 세트를 삭제할 수 있습니다. 타일 세트를 삭제하는 방법에 대한 자세한 내용은 [데이터 유지 관리](#data-maintenance)를 참조하세요.

>[!NOTE]
>타일 세트는 만들어진 데이터 세트와 독립적입니다. 데이터 세트에서 타일 세트를 만든 다음, 해당 데이터 세트를 업데이트하더라도 타일 세트가 업데이트되지 않습니다. 
>
>데이터 세트의 변경 내용을 반영하려면 새 타일 세트를 만들어야 합니다. 마찬가지로 타일 세트를 삭제하더라도 데이터 세트에는 영향을 주지 않습니다.

### <a name="feature-statesets"></a>기능 상태 세트

기능 상태 세트는 방 또는 장비와 같은 데이터 세트 기능에 할당된 동적 속성의 컬렉션(상태)입니다. 상태의 예는 온도 또는 점유율일 수 있습니다. 각 상태는 속성 이름, 값 및 마지막 업데이트의 타임스탬프를 포함한 키/값 쌍입니다.

[기능 상태 서비스](/rest/api/maps/v2/feature-state/create-stateset)를 사용하여 데이터 세트에 대한 기능 상태 세트를 만들고 관리할 수 있습니다. 상태 세트는 하나 이상의 *상태* 로 정의됩니다. 방과 같은 각 기능에는 하나의 *상태* 가 연결되어 있을 수 있습니다.

상태 세트의 각 *상태* 값은 IoT 디바이스 또는 다른 애플리케이션에서 업데이트하거나 검색할 수 있습니다.  예를 들어 [기능 상태 업데이트 API](/rest/api/maps/v2/feature-state/update-states)를 사용하면 공간 점유율을 측정하는 디바이스에서 방의 상태 변화를 체계적으로 게시할 수 있습니다.

애플리케이션은 기능 상태 세트를 사용하여 현재 상태 및 각각의 맵 스타일에 따라 시설의 기능을 동적으로 렌더링할 수 있습니다. 기능 상태 세트를 사용하여 렌더링 맵의 기능 스타일을 지정하는 방법에 대한 자세한 내용은 [실내 맵 모듈](#indoor-maps-module)을 참조하세요.

>[!NOTE]
>타일 세트와 마찬가지로 데이터 세트를 변경하더라도 기존 기능 상태 세트에는 영향을 주지 않으며, 기능 상태 세트를 삭제하더라도 연결된 데이터 세트에는 영향을 주지 않습니다.

## <a name="using-indoor-maps"></a>실내 맵 사용

### <a name="render-v2-get-map-tile-api"></a>Render V2–맵 타일 가져오기 API

Azure Maps [Render V2 - 맵 타일 가져오기 API](/rest/api/maps/render-v2/get-map-tile)가 Creator 타일 세트를 지원하도록 확장되었습니다.

애플리케이션은 Render V2-맵 타일 가져오기 API를 사용하여 타일 세트를 요청할 수 있습니다. 그런 다음, 타일 세트를 맵 컨트롤 또는 SDK에 통합할 수 있습니다. Render V2 서비스를 사용하는 맵 컨트롤의 예제는 [실내 맵 모듈](#indoor-maps-module)을 참조하세요.

### <a name="web-feature-service-api"></a>웹 기능 서비스 API

[WFS(Web Feature Service) API](/rest/api/maps/v2/wfs)를 사용하여 데이터 세트를 쿼리할 수 있습니다. WFS는 [Open Geospatial Consortium API 기능](http://docs.opengeospatial.org/DRAFTS/17-069r1.html)을 따릅니다. WFS API를 사용하여 데이터 세트 자체 내의 기능을 쿼리할 수 있습니다. 예를 들어 WFS를 사용하여 특정 시설 및 층 수준의 모든 중간 규모 회의실을 찾을 수 있습니다.

### <a name="alias-api"></a>별칭 API

변환, 데이터 세트, 타일 세트 및 기능 상태와 같은 Creator 서비스는 API에서 만든 각 리소스에 대한 식별자를 반환합니다. [별칭 API](/rest/api/maps/v2/alias)를 사용하면 리소스 식별자를 참조하는 별칭을 할당할 수 있습니다.

### <a name="indoor-maps-module"></a>실내 맵 모듈

[Azure Maps 웹 SDK](./index.yml)에는 실내 맵 모듈이 포함되어 있습니다. 이 모듈은 확장된 기능을 Azure Maps *맵 컨트롤* 라이브러리에 제공합니다. 실내 맵 모듈은 Creator에서 만든 실내 맵을 렌더링합니다. *층 선택기* 와 같은 위젯을 통합하여 사용자가 여러 층을 시각화할 수 있습니다.

실내 맵 모듈을 사용하여 실내 맵 데이터를 다른 [Azure Maps 서비스](./index.yml)와 통합하는 웹 애플리케이션을 만들 수 있습니다. 가장 일반적인 애플리케이션 설치에는 도로, 이미지, 날씨, 교통 체계와 같은 정보를 다른 맵에서 실내 맵에 추가하는 것이 포함될 수 있습니다.

실내 맵 모듈은 동적 맵 스타일 지정도 지원합니다. 애플리케이션에서 기능 상태 세트 동적 스타일 지정을 구현하는 방법에 대한 단계별 연습은 [실내 맵 모듈 사용](how-to-use-indoor-module.md)을 참조하세요.

### <a name="azure-maps-integration"></a>Azure Maps 통합

실내 맵용 솔루션 개발을 시작하면서 기존 Azure Maps 기능을 통합하는 방법을 찾을 수 있습니다. 예를 들어 Creator 실내 맵에서 [Azure Maps 지오펜스 API](/rest/api/maps/spatial/postgeofence)를 사용하여 자산 추적 또는 안전 시나리오를 구현할 수 있습니다. 예를 들어, 지오펜스 API를 사용하여 작업자가 특정 실내 영역에 출입하는지 여부를 결정할 수 있습니다. IoT 원격 분석에 Azure Maps를 연결하는 방법에 대한 자세한 내용은 [이 IoT 공간 분석 자습서](tutorial-iot-hub-maps.md)를 참조하세요.

### <a name="data-maintenance"></a>데이터 유지 관리

 Azure Maps Creator 나열, 업데이트 및 삭제 API를 사용하여 데이터 세트, 타일 세트 및 기능 상태 세트를 나열, 업데이트 및 삭제할 수 있습니다.

>[!NOTE]
>항목 목록을 검토하여 항목을 삭제할지 결정할 때 해당 삭제로 인해 모든 종속 API 또는 애플리케이션에 미치는 영향을 고려합니다. 예를 들어, [Render V2-맵 타일 가져오기 API](/rest/api/maps/render-v2/get-map-tile)를 통해 애플리케이션에서 사용하는 타일 세트를 삭제하면 애플리케이션이 해당 타일 세트를 렌더링하지 못합니다.

### <a name="example-updating-a-dataset"></a>예제: 데이터 세트 업데이트

다음 예제에서는 데이터 세트를 업데이트하고, 새 타일 세트를 만들고, 이전 타일 세트를 삭제하는 방법을 보여 줍니다.

1. [그리기 패키지 업로드](#upload-a-drawing-package) 및 [그리기 패키지 변환](#convert-a-drawing-package) 섹션의 단계에 따라 새 그리기 패키지를 업로드하고 변환합니다.
2. [데이터 세트 만들기 API](/rest/api/maps/v2/dataset/create)를 사용하여 변환된 데이터를 기존 데이터 세트에 추가합니다.
3. [타일 세트 만들기 API](/rest/api/maps/v2/tileset/create)를 사용하여 업데이트된 데이터 세트에서 새 타일 세트를 생성합니다.
4. 다음 단계를 위해 새 **tilesetId** 를 저장합니다.
5. 업데이트된 캠퍼스 데이터 세트의 시각화를 사용하도록 설정하려면 애플리케이션의 타일 세트 식별자를 업데이트합니다. 이전 타일 세트가 더 이상 사용되지 않는 경우 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Creator 실내 맵 만들기](tutorial-creator-indoor-maps.md)
