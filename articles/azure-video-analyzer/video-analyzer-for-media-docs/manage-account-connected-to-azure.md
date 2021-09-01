---
title: Azure Video Analyzer for Media(이전의 Video Indexer) 계정 관리
titleSuffix: Azure Video Analyzer for Media
description: Azure에 연결된 Azure Video Analyzer for Media(이전의 Video Indexer) 계정을 관리하는 방법을 알아봅니다.
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 836cff6a8b55378089a6215b9d0c25e3fcac76c4
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305178"
---
# <a name="manage-a-video-analyzer-for-media-account-connected-to-azure"></a>Azure에 연결된 Video Analyzer for Media 계정 관리.

이 문서에서는 Azure 구독 및 Azure Media Services 계정에 연결된 Azure Video Analyzer for Media(이전의 Video Indexer) 계정을 관리하는 방법을 보여 줍니다.

> [!NOTE]
> 이 항목에서 설명하는 계정 구성 조정을 수행하려면 Video Analyzer for Media 계정 소유자여야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

[Azure에 연결](connect-to-azure.md)에 설명된 대로 Video Analyzer for Media 계정을 Azure에 연결합니다.

[필수 조건](connect-to-azure.md#prerequisites-for-connecting-to-azure)을 따르고, 이 문서의 [고려 사항](connect-to-azure.md#azure-media-services-considerations)을 검토하세요.

## <a name="examine-account-settings"></a>계정 설정 검사

이 섹션에서는 Video Analyzer for Media 계정의 설정을 살펴봅니다.

설정을 보려면 다음을 수행합니다.

1. 오른쪽 위 모서리에서 사용자 아이콘을 클릭하고 **설정** 을 선택합니다.

    ![Video Analyzer for Media의 설정](./media/manage-account-connected-to-azure/select-settings.png)

2. **설정** 페이지에서 **계정** 탭을 선택합니다.

Videos Indexer 계정이 Azure에 연결된 경우 다음과 같은 항목이 표시됩니다.

* 기본 Azure Media Services 계정의 이름
* 실행 및 큐 대기 중인 인덱싱 작업의 수
* 할당된 예약 단위의 유형 및 수

계정을 조정해야 하는 경우 **설정** 페이지에 계정 구성에 대한 관련 오류 및 경고가 표시됩니다. 메시지에는 변경해야 하는 Azure Portal의 정확한 위치에 대한 링크가 포함되어 있습니다. 자세한 내용은 뒤에 나오는 [오류 및 경고](#errors-and-warnings) 섹션을 참조하세요.

## <a name="repair-the-connection-to-azure"></a>Azure에 대한 연결 복구

[Video Analyzer for Media](https://www.videoindexer.ai/) 페이지의 **Azure Media Services에 대한 연결 업데이트** 대화 상자에서 다음 설정에 대한 값을 제공하라는 메시지가 표시됩니다.

|설정|설명|
|---|---|
|Azure 구독 ID|구독 ID는 Azure Portal에서 검색할 수 있습니다. 왼쪽 패널에서 **모든 서비스** 를 클릭하고 "구독"을 검색합니다. **구독** 을 선택하고 구독 목록에서 원하는 ID를 선택합니다.|
|Azure Media Services 리소스 그룹 이름|Media Services 계정을 만든 리소스 그룹의 이름입니다.|
|애플리케이션 ID|이 Video Analyzer for Media 계정에 대해 만든 Azure AD 애플리케이션 ID(지정된 Media Services 계정에 대한 권한 포함)입니다. <br/><br/>앱 ID를 얻으려면 Azure Portal로 이동합니다. Media Services 계정에서 계정을 선택하고 **API 액세스** 로 이동합니다. **서비스 주체를 사용하여 Media Services API에 연결** -> **Azure AD 앱** 을 선택합니다. 관련 매개 변수를 복사합니다.|
|애플리케이션 키|위에서 지정한 Media Services 계정과 연결된 Azure AD 애플리케이션 키입니다. <br/><br/>앱 키를 얻으려면 Azure Portal로 이동합니다. Media Services 계정에서 계정을 선택하고 **API 액세스** 로 이동합니다. **서비스 주체를 사용하여 Media Services API에 연결** -> **애플리케이션 관리** -> **인증서 및 비밀** 을 선택합니다. 관련 매개 변수를 복사합니다.|

## <a name="errors-and-warnings"></a>오류 및 경고

계정에서 일부를 조정할 필요가 있으면 **설정** 페이지에서 계정 구성과 관련된 오류 및 경고를 볼 수 있습니다. 메시지에는 변경해야 하는 Azure Portal의 정확한 위치에 대한 링크가 포함되어 있습니다. 이 섹션에서는 오류 및 경고 메시지에 대한 자세한 정보를 제공합니다.

* EventGrid

    Azure Portal을 사용하여 EventGrid 리소스 공급자를 등록해야 합니다. [Azure Portal](https://portal.azure.com/)에서 **구독** > [구독] > **ResourceProviders** > **Microsoft.EventGrid** 로 차례로 이동합니다. **등록됨** 상태가 아니면 **등록** 을 선택합니다. 등록하는 데 몇 분 정도 걸립니다.

* 스트리밍 엔드포인트

    기본 Media Services 계정에 시작됨 상태의 기본 **스트리밍 엔드포인트** 가 있는지 확인합니다. 그렇지 않으면 이 Media Services 계정이나 Video Analyzer for Media에서 비디오를 볼 수 없습니다.

* 미디어 예약 단위

    비디오를 인덱싱하려면 미디어 예약 단위를 Media Services 리소스에 할당해야 합니다. 최적의 인덱싱 성능을 위해 S3 예약 단위를 10개 이상 할당하는 것이 좋습니다. 가격 책정 정보는 [Media Services 가격](https://azure.microsoft.com/pricing/details/media-services/) 페이지의 FAQ 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

[API 사용](video-indexer-use-apis.md)의 설명에 따라 평가판 계정 또는 Azure에 연결된 Video Analyzer for Media 계정과 프로그래밍 방식으로 상호 작용할 수 있습니다.

Azure에 연결할 때 사용한 것과 동일한 Azure AD 사용자를 사용합니다.
