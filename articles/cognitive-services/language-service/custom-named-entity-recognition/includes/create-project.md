---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: aeb4c7ef44791b174940790fa3e03f0f40ed1ba4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103193"
---
리소스 및 스토리지 컨테이너가 구성되면 새 사용자 지정 NER 프로젝트를 만듭니다. 프로젝트는 데이터를 기반으로 하는 사용자 지정 AI 모델을 빌드하기 위한 작업 영역입니다. 사용자 및 사용 중인 Azure 리소스에 대한 기여자 액세스 권한이 있는 다른 사용자만 프로젝트에 액세스할 수 있습니다.

1. [언어 스튜디오 포털](https://aka.ms/languageStudio)에 로그인합니다. 구독 및 언어 리소스를 선택할 수 있는 창이 표시됩니다. 위의 단계에서 만든 리소스를 선택합니다. 

2. **엔터티 추출** 섹션을 찾고, 사용 가능한 서비스에서 **사용자 지정 명명된 엔터티 인식** 을 선택합니다.

3. 프로젝트 페이지의 상단 메뉴에서 **새 프로젝트 만들기** 를 선택합니다. 프로젝트를 만들면 태그를 데이터에 지정하고, 모델을 학습시키고, 평가하고, 향상시키고, 배포할 수 있습니다. 

    
    :::image type="content" source="../media/create-project.png" alt-text="프로젝트 만들기 페이지의 스크린샷" lightbox="../media/create-project.png":::


4.  **새 프로젝트 만들기** 를 클릭하면 스토리지 계정을 연결할 수 있는 화면이 표시됩니다. 스토리지 계정을 찾을 수 없는 경우 위의 단계를 사용하여 리소스를 만들었는지 확인합니다. 

    >[!NOTE]
    > * 사용하는 각 새 리소스에 대해 이 단계를 한 번만 수행하면 됩니다. 
    > * 이 프로세스는 되돌릴 수 없으며, 스토리지 계정을 리소스에 연결하는 경우 나중에 해당 연결을 끊을 수 없습니다.
    > * 리소스는 하나의 스토리지 계정에만 연결할 수 있습니다.
    > * 스토리지 계정을 이미 연결한 경우 **프로젝트 형식 선택** 화면이 대신 표시됩니다. 다음 단계를 참조하세요.
    
    :::image type="content" source="../media/connect-storage.png" alt-text="스토리지 연결 화면을 보여 주는 스크린샷" lightbox="../media/connect-storage.png":::

<!--If you're using a preexisting resource, see [creating Azure resources](../concepts/use-azure-resources.md). When you are done, select **Next**.--> 

5. 프로젝트에 있는 파일의 이름, 설명 및 언어를 포함한 프로젝트 정보를 입력합니다. 프로젝트 이름은 나중에 변경할 수 없습니다. 

6. 입력한 데이터를 검토하고, **프로젝트 만들기** 를 선택합니다.
