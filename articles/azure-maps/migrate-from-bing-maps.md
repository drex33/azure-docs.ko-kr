---
title: '자습서: Bing Maps에서 Azure Maps로 마이그레이션 | Microsoft Azure Maps'
description: Bing Maps에서 Microsoft Azure Maps로 마이그레이션하는 방법에 대한 자습서입니다. Azure Maps API 및 SDK로 전환하는 방법을 안내하는 지침입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/17/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 074a21174798b53ee10c27e620d6227d1aacec98
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429697"
---
# <a name="tutorial-migrate-from-bing-maps-to-azure-maps"></a>자습서: Bing Maps에서 Azure Maps로 마이그레이션

이 가이드에서는 Bing Maps의 웹, 모바일 및 서버 기반 애플리케이션을 Azure Maps 플랫폼으로 마이그레이션하는 방법에 대한 인사이트를 제공합니다. 이 가이드에는 Azure Maps로 마이그레이션하기 위한 비교 코드 샘플, 마이그레이션 제안 사항 및 모범 사례가 포함되어 있습니다. 

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Maps에서 사용할 수 있는 해당 Bing Maps 기능에 대한 대략적인 비교.
> * 고려해야 할 라이선스 차이점.
> * 마이그레이션을 계획하는 방법.
> * 기술 리소스 및 지원을 찾을 수 있는 위치

## <a name="prerequisites"></a>필수 조건

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
2. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
3. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](how-to-manage-authentication.md)를 참조하세요.

## <a name="azure-maps-platform-overview"></a>Azure Maps 플랫폼 개요

Azure Maps는 모든 산업의 개발자에게 지리적 컨텍스트를 웹 및 모바일 애플리케이션에 제공하는 데 사용할 수 있는 최신 지도 데이터가 포함된 강력한 지리 공간적 기능을 제공합니다. Azure Maps는 웹 및 Android SDK와 함께 맵, 검색, 라우팅, 트래픽, 표준 시간대, 지오펜싱, 맵 데이터, 날씨 데이터 및 여러 서비스를 위한 Azure One API 호환 REST API 세트로, 개발을 쉽고 유연하게 해주며, 여러 플랫폼 간에 이식이 가능하도록 해줍니다. [Azure Maps는 Power BI에서도 사용할 수 있습니다](power-bi-visual-getting-started.md).

## <a name="high-level-platform-comparison"></a>상위 수준 플랫폼 비교

다음 표에서는 Bing Maps 기능의 상위 수준 목록과 Azure Maps 기능에 대한 관련 지원을 제공합니다. 이 목록에는 내게 필요한 옵션, 지오펜싱 API, 트래픽 서비스, 공간 작업, 직접 맵 타일 액세스 및 일괄 처리 서비스와 같은 추가 Azure Maps 기능은 포함되지 않습니다.

| Bing Maps 기능                     | Azure Maps 지원 |
|---------------------------------------|:------------------:|
| 웹 SDK                               | ✓                  |
| Android SDK                           | ✓                  |
| iOS SDK                               | 계획            |
| UWP SDK                               | 해당 없음                 |
| WPF SDK                               | 해당 없음                 |
| REST 서비스 API                     | ✓                  |
| 자동 제안                           | ✓                  |
| 방향(트럭 포함)          | ✓                  |
| 거리 행렬                       | ✓                  |
| 권한 상승                            | ✓      |
| 이미지 - 정적 맵                  | ✓                  |
| 이미지 메타데이터                      | ✓                  |
| 등시성                            | ✓                  |
| 로컬 인사이트                        | ✓                  |
| 로컬 검색                          | ✓                  |
| 위치 인식                  | ✓                  |
| 위치(전방/역방향 지오코딩) | ✓                  |
| 최적화된 일정표 경로            | 계획            |
| 도로에 맞춤                         | ✓                  |
| SDS(Spatial Data Services)           | Partial            |
| 표준 시간대                             | ✓                  |
| 교통 사고                     | ✓                  |
| 구성 기반 맵             | 해당 없음                |

Bing Maps는 기본 키 기반 인증을 제공합니다. Azure Maps는 기본 키 기반 인증과 매우 안전한 Azure Active Directory 인증을 모두 제공합니다.

## <a name="licensing-considerations"></a>라이선스 고려 사항

Bing Maps에서 Azure Maps로 마이그레이션할 때 라이선스와 관련하여 다음 정보를 고려해야 합니다.

* Azure Maps는 로드된 맵 타일의 수에 따라 대화형 맵의 사용량에 대한 요금이 청구되는 반면, Bing Maps는 맵 컨트롤(세션) 로드에 대한 요금이 부과됩니다. 개발자의 비용을 줄이기 위해 Azure Maps는 지도 타일을 자동으로 캐시합니다. 로드되는 모든 15개의 맵 타일에 대해 하나의 Azure Maps 트랜잭션이 생성됩니다. 대화형 Azure Maps SDK는 512픽셀 타일을 사용하고 평균적으로는 페이지 보기당 하나 이상의 트랜잭션을 생성합니다.

* Azure Maps를 사용하면 해당 플랫폼의 데이터를 Azure에 저장할 수 있습니다. [사용 약관](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)에 따라 최대 6개월 동안 다른 곳에서 캐시될 수도 있습니다.

다음은 Azure Maps에 대한 몇 가지 라이선스 관련 리소스입니다.

-   [Azure Maps 가격 책정](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Azure Maps 사용 약관](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)(Microsoft Online Services 사용 약관에 포함됨)
-   [Azure Maps에서 올바른 가격 책정 계층 선택](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>권장 마이그레이션 계획

다음은 상위 수준 마이그레이션 계획의 예입니다.

1.  애플리케이션에서 사용 중인 Bing Maps SDK 및 서비스의 인벤토리를 작성하고, Azure Maps에서 마이그레이션할 다른 SDK 및 서비스를 제공하는지 확인합니다.
2.  <https://azure.com>에서 Azure 구독을 만듭니다(아직 Azure 구독이 없는 경우).
3.  Azure Maps 계정([설명서](./how-to-manage-account-keys.md))과 인증 키 또는 Azure Active Directory([설명서](./how-to-manage-authentication.md))를 만듭니다.
4.  애플리케이션 코드를 마이그레이션합니다.
5.  마이그레이션된 애플리케이션을 테스트합니다.
6.  마이그레이션된 애플리케이션을 프로덕션 환경에 배포합니다.

## <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

Azure Maps 계정을 만들고 Azure Maps 플랫폼에 액세스하려면 다음 단계를 수행합니다.

1. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
2. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
3. [Azure Maps 계정](./how-to-manage-account-keys.md)을 만듭니다.
4. 보안 강화를 위해 [Azure Maps 구독 키를 가져오거나](./how-to-manage-authentication.md#view-authentication-details) Azure Active Directory 인증을 설정합니다.

## <a name="azure-maps-technical-resources"></a>Azure Maps 기술 리소스

다음은 Azure Maps에 대한 유용한 기술 리소스 목록입니다.

* 개요: <https://azure.com/maps>
* 설명서: <https://aka.ms/AzureMapsDocs>
* 웹 SDK 코드 샘플: <https://aka.ms/AzureMapsSamples>
* 개발자 포럼: <https://aka.ms/AzureMapsForums>
* 비디오: <https://aka.ms/AzureMapsVideos>
* 블로그: <https://aka.ms/AzureMapsBlog>
* Azure Maps 피드백(UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>마이그레이션 지원

개발자는 [포럼](/answers/topics/azure-maps.html) 또는 여러 Azure 지원 옵션(<https://azure.microsoft.com/support/options/>) 중 하나를 통해 마이그레이션 지원을 검색할 수 있습니다.

## <a name="new-terminology"></a>새 용어

다음 목록에는 일반적인 Bing Maps 용어와 해당 Azure Maps 용어가 포함되어 있습니다.

| Bing Maps 용어                    | Azure Maps 용어                                                |
|-----------------------------------|----------------------------------------------------------------|
| 항공                            | 위성 또는 항공                                            |
| 방향                        | 라우팅이라고도 합니다.                             |
| 엔터티                          | 기하 도형 또는 기능                                         |
| `EntityCollection`                | 데이터 원본 또는 레이어                                           |
| `Geopoint`                        | 위치                                                       |
| `GeoXML`                          | 공간 IO 모듈의 XML 파일                             |
| 그라운드 오버레이                    | 이미지 레이어                                                    |
| 하이브리드(맵 형식 참조) | 위성(도로 포함)                                           |
| Infobox                           | Popup                                                          |
| Location                          | 위치                                                       |
| `LocationRect`                    | 경계 상자                                                   |
| 맵 유형                          | 맵 스타일                                                      |
| 탐색 모음                    | 맵 스타일 선택, 확대/축소 컨트롤, 피치 컨트롤, 나침반 컨트롤 |
| Pushpin                           | 버블 레이어, 기호 레이어 또는 HTML 표식                      |

## <a name="clean-up-resources"></a>리소스 정리

정리가 필요한 리소스가 없습니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 Bing Maps 애플리케이션을 마이그레이션하는 방법에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [웹앱 마이그레이션](migrate-from-bing-maps-web-app.md)
