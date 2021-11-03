---
title: Azure에 연결된 Azure Video Analyzer for Media(이전의 Video Indexer) 계정 만들기
description: Azure에 연결된 Azure Video Analyzer for Media(이전의 Video Indexer) 계정을 만드는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 10/19/2021
ms.author: itnorman
ms.custom: ignite-fall-2021
ms.openlocfilehash: 55a0a203bb359ca0d5cad44b5e773cee0f65edef
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016971"
---
# <a name="create-a-video-analyzer-for-media-account"></a>Video Analyzer for Media 계정 만들기

Azure Video Analyzer for Media(이전의 Video Indexer) 계정을 만들 때 평가판 계정(특정의 체험 인덱싱 시간(분)을 가져오는 경우) 또는 유료 옵션(할당량으로 제한되지 않은 경우)을 선택할 수 있습니다. 평가판을 통해 Video Analyzer for Media는 최대 600분의 체험 인덱싱을 제공하고, [개발자 포털](https://aka.ms/avam-dev-portal)에서 Video Analyzer API를 구독하는 사용자에게 최대 2400분의 체험 인덱싱을 제공합니다. 유료 옵션을 통해 Azure Video Analyzer for Media는 클래식 계정(일반 공급) 및 ARM 기반 계정(공개 미리 보기)이라는 두 가지 유형의 계정을 제공합니다. 두 계정 간의 주요 차이점은 계정 관리 플랫폼입니다. 클래식 계정은 API Management 기반으로 빌드되는 반면 ARM 기반 계정 관리는 Azure 기반으로 빌드되며, 기본적으로 역할 기반 액세스 제어(Azure RBAC)를 통해 모든 서비스에 액세스 제어를 사용할 수 있습니다.

* [API](https://aka.ms/avam-dev-portal)를 통해 Video Analyzer for Media **클래식** 계정을 만들 수 있습니다.

* 다음 중 하나를 통해 Video Analyzer for Media **ARM 기반** 계정을 만들 수 있습니다.

  1.  [Video Analyzer for Media 포털](https://aka.ms/vi-portal-link)

  2.  [Azure Portal](https://ms.portal.azure.com/#home)

  3.  [ARM template 빠른 시작](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/ARM-Samples/Create-Account)

### <a name="to-read-more-on-how-to-create-a-new-arm-based-video-analyzer-for-media-account-read-this-article"></a>**새 ARM 기반** Video Analyzer for Media 계정을 만드는 방법에 대한 자세한 내용은 이 [문서](create-video-analyzer-for-media-account.md)를 참조하세요.

## <a name="how-to-create-classic-accounts"></a>클래식 계정을 만드는 방법
이 문서에서는 Video Analyzer for Media 클래식 계정을 만드는 방법을 보여줍니다. 이 토픽에서는 자동(기본값) 흐름을 사용하여 Azure에 연결하는 단계를 설명합니다. Azure에 수동으로 연결하는 방법도 보여줍니다(고급).

*평가판* 에서 *유료 ARM-Based* Video Analyzer for Media 계정으로 이동하는 경우, [평가판 계정](#import-your-content-from-the-trial-account) 섹션에서 설명한 대로 모든 비디오 및 모델 사용자 지정을 새 계정에 복사하도록 선택할 수 있습니다.

이 문서에서는 [Azure Government에 Video Analyzer for Media 계정 연결](#video-analyzer-for-media-in-azure-government)에 대해서도 설명합니다.

## <a name="prerequisites-for-connecting-to-azure"></a>Azure 연결을 위한 사전 요구 사항

* Azure 구독

    아직 Azure 구독이 없는 경우 [Azure 평가판](https://azure.microsoft.com/free/)에 가입합니다.
* Azure Active Directory(Azure AD) 도메인.

    Azure AD 도메인이 없는 경우 Azure 구독을 사용하여 이 도메인을 만듭니다. 자세한 내용은 [Azure Active Directory에서 사용자 지정 도메인 이름 관리](../../active-directory/enterprise-users/domains-manage.md)를 참조하세요
* **애플리케이션 관리자** 역할을 하는 Azure AD 도메인의 사용자. Video Analyzer for Media 계정을 Azure에 연결할 때 이 멤버를 사용합니다.

    이 사용자는 직장 또는 학교 계정이 있는 Azure AD 사용자여야 합니다. outlook.com, live.com 또는 hotmail.com와 같은 개인 계정을 사용하지 마세요.

    ![모든 Azure AD 사용자](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>자동 흐름의 추가 필수 구성 요소

* Azure AD 도메인의 사용자 및 멤버.

    Video Analyzer for Media 계정을 Azure에 연결할 때 이 멤버를 사용합니다.

    이 사용자는 **소유자** 역할이 있거나 **기여자** 와 **사용자 액세스 관리자** 역할이 모두 있는 Azure 구독의 멤버여야 합니다. 두 역할을 가진 사용자를 두 번 추가할 수 있습니다. 기여자 역할과 사용자 액세스 관리자 역할이 각각 있는 멤버로 두 번 추가합니다. [자세한 내용은 Azure 리소스에 대한 사용자의 액세스 권한 보기](../../role-based-access-control/check-access.md)에서 알아보세요.

    ![액세스 제어](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>수동 흐름의 추가 필수 구성 요소

* Azure Portal을 사용하여 EventGrid 리소스 공급자를 등록합니다.

    [Azure Portal](https://portal.azure.com/)에서 **구독**->[구독]->**ResourceProviders** 로 이동합니다.

    **Microsoft.Media** 및 **Microsoft.EventGrid** 를 검색합니다. "등록됨" 상태가 아닌 경우 **등록** 을 클릭합니다. 등록하는 데 몇 분 정도 걸립니다.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure-manually-advanced-option"></a>Azure에 수동으로 연결(고급 옵션)

Azure 연결이 실패한 경우 수동으로 연결하여 문제를 해결할 수 있습니다.

> [!NOTE]
> Media Services 계정과 이를 연결하는 Video Analyzer for Media 계정, 그리고 같은 Media Services 계정에 연결된 Azure Storage 계정 총 3개 계정은 같은 영역에 있어야 합니다.

### <a name="create-and-configure-a-media-services-account"></a>Media Services 계정을 만들고 구성

1. [Azure](https://portal.azure.com/) Portal을 사용하여 [계정 만들기](../../media-services/previous/media-services-portal-create-account.md)에 설명된 대로 Azure Media Services 계정을 만듭니다.

     Media Services 계정이 클래식 API로 생성되었는지 확인합니다. 
 
    ![Media Services 클래식 API](./media/create-account/enable-classic-api.png)


    Media Services 계정에 대한 스토리지 계정을 생성할 때 계정 종류로 **StorageV2** 를 선택하고 복제 필드로 **지역 중복 (GRS)** 을 선택합니다.

    ![새 AMS 계정](./media/create-account/create-new-ams-account.png)

    > [!NOTE]
    > Media Services 리소스 및 계정 이름을 기록해 둡니다. 다음 섹션의 단계에서 그것들이 필요합니다.

1. Video Analyzer for Media 웹 애플리케이션에서 비디오를 재생하기 전에 새 Media Services 계정의 기본 **스트리밍 엔드포인트** 를 시작해야 합니다.

    새 Media Services 계정에서 **스트리밍 엔드포인트** 를 클릭합니다. 스트리밍 엔드포인트를 선택하고 [시작]을 누릅니다.

    ![스트리밍 엔드포인트](./media/create-account/create-ams-account-se.png)
4. Video Analyzer for Media가 Media Services API를 인증하려면 AD 앱을 만들어야 합니다. 다음 단계는 [Azure Portal을 사용하여 Azure AD 인증 시작](../../media-services/previous/media-services-portal-get-started-with-aad.md)에 설명된 Azure AD 인증 프로세스를 안내합니다.

    1. 새 Media Services 계정에서 **API 액세스** 를 선택합니다.
    2. [서비스 주체 인증 방법](../../media-services/previous/media-services-portal-get-started-with-aad.md)을 선택합니다.
    3. 클라이언트 ID 및 클라이언트 암호 가져오기

        **설정**->**키** 를 선택한 후, **설명** 을 추가한 뒤에 **저장** 을 누르면 키 값이 채워집니다.

        키가 만료되면 계정 소유자는 Video Analyzer for Media 지원에 문의하여 키를 갱신해야 합니다.

        > [!NOTE]
        > 키 값 및 애플리케이션 ID를 적어 둡니다. 다음 섹션의 단계에서 필요합니다.

### <a name="connect-manually"></a>수동으로 연결

[Video Analyzer for Media](https://www.videoindexer.ai/) 페이지의 **Azure 구독에 새 계정 생성** 대화 상자에서 **수동 구성으로 전환** 링크를 선택합니다.

대화 상자에서 다음 정보를 입력합니다.

|설정|설명|
|---|---|
|Video Analyzer for Media 계정 지역|Video Analyzer for Media 계정 지역의 이름입니다. 성능을 향상시키고 비용을 절감하려면 Azure Media Services 리소스 및 Azure Storage 계정이 있는 영역의 이름을 지정하는 것이 좋습니다. |
|Azure AD 테넌트|Azure AD 테넌트의 이름입니다(예: "contoso.onmicrosoft.com"). Azure Portal에서 테넌트 정보를 검색할 수 있습니다. 오른쪽 위 모서리에서 로그인한 사용자의 이름 위로 커서를 이동합니다. **도메인** 오른쪽에서 이름을 찾습니다.|
|구독 ID|이 연결을 만들어야 하는 Azure 구독입니다. 구독 ID는 Azure Portal에서 검색할 수 있습니다. 왼쪽 패널에서 **모든 서비스** 를 선택하고, "가입"을 검색합니다. **구독** 을 선택하고 구독 목록에서 원하는 ID를 선택합니다.|
|Azure Media Services 리소스 그룹 이름|Media Services 계정을 만든 리소스 그룹의 이름입니다.|
|Media Services 리소스 이름|이전 섹션에서 만든 Azure Media Services 계정의 이름입니다.|
|애플리케이션 ID|이전 섹션에서 만든 Azure AD 애플리케이션 ID(지정된 Media Services 계정에 대한 권한이 있는)입니다.|
|애플리케이션 키|이전 섹션에서 만든 Azure AD 애플리케이션 키입니다. |

### <a name="import-your-content-from-the-trial-account"></a>*평가판* 계정에서 콘텐츠 가져오기

새 **ARM 기반** 계정을 만들 때 콘텐츠를 *평가판* 계정에서 새 **ARM 기반** 계정으로 무료로 가져올 수 있는 옵션이 있습니다.
> [!NOTE]
> * 평가판에서 가져오기는 평가판 계정당 한 번만 수행할 수 있습니다.
> * 가져오기를 할당하기 전에 대상 ARM-Based 계정을 만들고 사용할 수 있어야 합니다.  
> * 대상 ARM-Based 계정은 빈 계정이어야 합니다(미디어 파일을 인덱싱하지 않음).

데이터를 가져오려면 다음 단계를 수행합니다.
 1. [Azure Video Analyzer for Media 포털](https://aka.ms/vi-portal-link)로 이동합니다.
 2. 평가판 계정을 선택하고 *계정 설정* 페이지로 이동합니다.
 3. *ARM 기반 계정으로 콘텐츠 가져오기* 를 클릭합니다.
 4. 드롭다운 메뉴에서 데이터를 가져올 ARM 기반 계정을 선택합니다.
   * 계정 ID가 표시되지 않으면 Azure Portal 또는 계정 목록에서 계정 ID를 복사하여 Azure Video Analyzer for Media Portal의 측면 블레이드에 붙여넣을 수 있습니다.
 5. **콘텐츠 가져오기** 를 클릭합니다.  

![import](./media/create-account/import-steps.png)


모든 미디어 및 콘텐츠 모델 사용자 지정은 *평가판* 계정에서 새 ARM 기반 계정으로 복사됩니다.


> [!NOTE]
>
> Azure Government 클라우드에서는 *평가판* 계정이 가능하지 않습니다.

## <a name="azure-media-services-considerations"></a>Azure Media Services 고려 사항

다음과 같은 Azure Media Services 관련 고려 사항이 적용됩니다.

* 기존 Media Services 계정에 연결하려는 경우 클래식 API를 사용하여 Media Services 계정을 만들었는지 확인합니다. 
 
    ![Media Services 클래식 API](./media/create-account/enable-classic-api.png)
* 기존 Media Services 계정에 연결하게 되면 Video Analyzer for Media는 기존 미디어 **예약 단위** 구성을 변경하지 않습니다.

   계획된 로드에 따라 미디어 예약 단위의 유형과 수를 조정해야 할 수 있습니다. 로드가 높고 단위 또는 속도가 충분하지 않으면 비디오 처리로 인해 시간 제한 오류가 발생할 수 있습니다.
* 새 Media Services 계정에 연결하면 Video Analyzer for Media가 자동으로 기본 **스트리밍 엔드포인트** 를 시작합니다.

    ![Media Services 스트리밍 엔드포인트](./media/create-account/ams-streaming-endpoint.png)

    스트리밍 엔드포인트의 시작 시간이 상당히 깁니다. 따라서 계정을 Azure에 연결하는 시점부터 Video Analyzer for Media 웹 앱에서 비디오를 스트리밍하고 시청할 수 있게 되는 시점까지 몇 분 정도 걸릴 수 있습니다.
* 기존 Media Services 계정에 연결하게 되면 Video Analyzer for Media가 기본 스트리밍 엔드포인트 구성을 변경하지 않습니다. 실행 중인 **스트리밍 엔드포인트** 가 없는 경우 이 Media Services 계정 또는 Video Analyzer for Media에서 비디오를 볼 수 없습니다.
* 자동으로 연결하면 Video Analyzer for Media가 미디어 **예약 단위** 를 S3 단위 10개로 설정합니다.

    ![Media Services 예약 단위](./media/create-account/ams-reserved-units.png)
    
## <a name="automate-creation-of-the-video-analyzer-for-media-account"></a>Video Analyzer for Media 계정 만들기 자동화

계정 생성을 자동화하는 과정은 다음 두 단계로 진행됩니다:
 
1. Azure Resource Manager를 사용하여 Azure Media Services 계정 + Azure AD 어플리케이션을 만듭니다.

    [Media Services 계정 생성 템플릿](https://github.com/Azure-Samples/media-services-v3-arm-templates)의 예를 살펴보세요.
1. [Media Services 및 Azure AD 어플리케이션으로 Create-Account](https://videoindexer.ai.azure.us/account/login?source=apim)를 호출 합니다.

## <a name="video-analyzer-for-media-in-azure-government"></a>Azure Government의 Video Analyzer for Media

### <a name="prerequisites-for-connecting-to-azure-government"></a>Azure Government 연결을 위한 사전 요구 사항

-   [Azure Government에서 Azure 구독](../../azure-government/index.yml).
- Azure Government에서 Azure AD 계정.
- 위 [Azure에 연결하기 위한 필수 구성 요소](#prerequisites-for-connecting-to-azure)에 설명된 대로 사용 권한 및 리소스의 모든 사전 요구 사항입니다. [자동 흐름에 대한 추가 사전 요구 사항](#additional-prerequisites-for-automatic-flow) 및 [수동 흐름의 추가 사전 요구 사항](#additional-prerequisites-for-manual-flow)을 확인해야 합니다.

### <a name="create-new-account-via-the-azure-government-portal"></a>Azure Government 포털을 통해 새 계정 생성하기

> [!NOTE]
> Azure Government 클라우드에는 Video Analyzer for Media의 *평가판* 환경이 포함되어 있지 않습니다.

Video Analyzer for Media 포털을 통해 유료 계정을 만들려면 다음을 수행합니다.

1. https://videoindexer.ai.azure.us 로 이동 
1. Azure Government Azure AD 계정으로 로그인합니다.
1.  소유자이거나 기여자인 Azure Government의 Video Analyzer for Media 계정이 없으면 계정 생성을 시작할 수 있는 빈 환경을 사용하게 됩니다. 

    흐름의 나머지 부분은 위에 설명된 것처럼, 선택할 지역은 Video Analyzer for Media를 사용할 수 있는 Government 영역입니다. 

    이미 Azure Government에서 하나 이상의 Video Analyzer for Media 계정에 대한 기여자 또는 관리자인 경우, 해당 계정으로 이동하여 위에서 설명한 대로 필요한 경우 다음 단계를 시작하여 추가 계정을 생성할 수 있습니다.
    
### <a name="create-new-account-via-the-api-on-azure-government"></a>Azure Government의 API를 통해 새 계정 생성하기

Azure Government에서 유료 계정을 만들려면 [유료-계정-생성]()에 설명된 지침을 따르세요. 이 API 엔드포인트에는 Government 클라우드 지역만 포함됩니다.

### <a name="limitations-of-video-analyzer-for-media-on-azure-government"></a>Azure Government에 대한 Video Analyzer for Media의 제한 사항

*   Government 클라우드에서는 수동 콘텐츠 조정을 사용할 수 없습니다. 

    공용 클라우드에서 콘텐츠가 콘텐츠 조정을 기반으로 악의적인 것으로 간주되는 경우 고객은 해당 콘텐츠를 확인하고 잠재적으로 해당 결정을 되돌릴 수 있습니다.  
*   평가판 계정 없음. 
* Bing 설명 - Gov 클라우드에서는 확인된 유명 인사 및 명명된 엔티티에 대한 설명을 제시하지 않습니다. 이는 오직 UI 기능만입니다. 

## <a name="clean-up-resources"></a>리소스 정리

이 튜토리얼을 완료한 후 사용하지 않을 리소스를 삭제합니다.

### <a name="delete-a-video-analyzer-for-media-account"></a>Video Analyzer for Media 계정 삭제

Video Analyzer for Media 계정을 삭제하려면 Video Analyzer for Media 웹 사이트에서 계정을 삭제할 수 있습니다. 계정을 삭제하려면 소유자여야 합니다.

계정->**설정** -> **이 계정 삭제** 를 선택합니다. 

계정은 90일 내에 영구적으로 삭제됩니다.

## <a name="firewall"></a>방화벽

[방화벽 뒤에 있는 스토리지 계정](faq.yml#can-a-storage-account-connected-to-the-media-services-account-be-behind-a-firewall)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[API 사용](video-indexer-use-apis.md)의 설명에 따라 평가판 계정 및/또는 Azure에 연결된 Video Analyzer for Media 계정과 프로그래밍 방식으로 상호 작용할 수 있습니다.

Azure에 연결할 때 사용한 것과 동일한 Azure AD 사용자를 사용해야 합니다.
