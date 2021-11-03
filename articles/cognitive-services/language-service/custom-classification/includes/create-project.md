---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: b71810497c555a33fcf8a7359c32c8397cb6cec8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053131"
---
리소스 및 스토리지 컨테이너가 구성되면 새 텍스트 분류 프로젝트를 만듭니다. 프로젝트는 데이터를 기반으로 하는 사용자 지정 AI 모델을 빌드하기 위한 작업 영역입니다. 사용자 및 사용 중인 Azure 리소스에 대한 기여자 액세스 권한이 있는 다른 사용자만 프로젝트에 액세스할 수 있습니다.

1. [Language Studio](https://aka.ms/languageStudio)에 로그인합니다. 구독 및 언어 리소스를 선택할 수 있는 창이 표시됩니다. 위의 단계에서 만든 리소스를 선택합니다.

2. Language Studio의 **텍스트 분류** 섹션 아래의 사용 가능한 서비스에서 **사용자 지정 텍스트 분류** 를 선택하여 리소스를 선택합니다.

3. 프로젝트 페이지의 상단 메뉴에서 **새 프로젝트 만들기** 를 선택합니다. 프로젝트를 만들면 태그를 데이터에 지정하고, 모델을 학습시키고, 평가하고, 향상시키고, 배포할 수 있습니다. 

    :::image type="content" source="../media/create-project.png" alt-text="프로젝트 만들기 페이지의 스크린샷" lightbox="../media/create-project.png":::

4. 위의 단계를 사용하여 리소스를 만든 경우 프로젝트에 대한 정보(예: 이름)를 추가하고 스토리지 컨테이너를 선택해야 합니다.

    1. 프로젝트 형식을 선택합니다. 이 빠른 시작에서는 다중 레이블 분류 프로젝트를 만듭니다. 그런 후 **Next** 를 클릭합니다.

    2. 프로젝트에 있는 파일의 이름, 설명 및 언어를 포함한 프로젝트 정보를 입력합니다. 프로젝트 이름은 나중에 변경할 수 없습니다.

        >[!TIP]
        > 데이터 세트는 완전히 동일한 언어로 되어 있지 않아도 됩니다. 지원되는 언어가 각각 다른 여러 파일을 사용할 수 있습니다. 데이터 세트에 다른 언어의 파일이 포함되어 있거나 런타임 중에 다른 언어를 사용하는 경우 프로젝트에 대한 기본 정보를 입력할 때 **다국어 데이터 세트 사용** 을 선택합니다.

    3. 데이터를 업로드한 컨테이너를 선택합니다. 이 빠른 시작에서는 컨테이너에서 사용할 수 있는 기존 태그 파일을 사용합니다. 그런 후 **Next** 를 클릭합니다.
 
    4. 입력한 데이터를 검토하고, **프로젝트 만들기** 를 선택합니다.
