---
title: Azure Portal를 사용하여 Cloud Services(확장 지원)를 위한 ARM 템플릿 생성
description: Azure Portal를 사용하여 Cloud Services(확장 지원)를 위한 ARM 템플릿 및 매개 변수 파일을 생성하고 다운로드합니다.
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: 6654d1e167eefea1544262dab279b15b87b6b2ba
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460298"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Azure Portal를 사용하여 Cloud Services(확장 지원)를 위한 ARM 템플릿 생성

이 문서에서는 클라우드 서비스를 위한 [Azure Portal](https://portal.azure.com)에서 ARM 템플릿 및 매개 변수 파일을 다운로드하는 방법을 설명합니다. ARM 템플릿과 매개 변수 파일은 PowerShell을 통한 배포에서 클라우드 서비스를 만들거나 업데이트하는 데 사용할 수 있습니다.

## <a name="get-arm-template-via-portal"></a>포털을 통해 ARM 템플릿 가져오기

  1. Azure Portal로 이동하여 [새 클라우드 서비스를 만듭니다](deploy-portal.md). 클라우드 서비스 구성, 패키지 및 정의 파일을 추가합니다. 
    :::image type="content" source="media/deploy-portal-4.png" alt-text="만드는 동안 기본 탭의 업로드 섹션을 보여 주는 이미지":::
  
  2. 모든 필드가 완료되면 검토 및 만들기 탭으로 이동하여 배포 구성의 유효성을 검사하고 클라우드 서비스(추가 지원) **자동화 템플릿 다운로드** 를 클릭합니다.
    :::image type="content" source="media/download-template-portal-1.png" alt-text="이미지는 Azure Portal의 클라우드 서비스(확장 지원)에서 템플릿을 다운로드하는 모습을 보여 줍니다.":::
  
  3. 템플릿 파일 및 매개 변수 파일을 다운로드합니다. 
    :::image type="content" source="media/generate-template-portal-3.png" alt-text="이미지는 Azure Portal에서 템플릿 파일을 다운로드하는 모습을 보여 줍니다.":::
  
  4. 검토 및 만들기 탭에서 패키지 SAS URI 및 구성 SAS URI를 복사하고 parameter.json 파일에 추가합니다. 이제 이러한 파일을 사용하여 PowerShell을 통해 새 클라우드 서비스를 만들 수 있습니다.
    :::image type="content" source="media/download-template-portal-2.png" alt-text="이미지는 Azure Portal의 패키지 SAS URI 및 구성 SAS URI 매개 변수를 표시합니다.":::
  
## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.yml)을 검토합니다.
- [Azure Portal](deploy-portal.md)을 사용한 클라우드 서비스(추가 지원) 배포
  
