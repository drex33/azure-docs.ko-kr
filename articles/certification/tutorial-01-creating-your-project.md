---
title: Azure Certified Device 프로그램 - 자습서 - 프로젝트 만들기
description: Azure Certified Device 포털에서 프로젝트를 만들기 위한 가이드
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 06/22/2021
ms.custom: template-tutorial
ms.openlocfilehash: f634cb35590a428613858a88a71db1d0792ceb06
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112967505"
---
# <a name="tutorial-create-your-project"></a>자습서: 프로젝트 만들기

Azure Certified Device 프로그램을 통해 디바이스를 인증하기로 선택한 것을 축하드립니다! 이제 디바이스를 위한 적절한 인증 프로그램을 선택하고 포털에서 시작할 준비가 되었습니다.

이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> * [Azure Certified Device 포털](https://certify.azure.com/)에 로그인
> * 디바이스에 대한 새 인증 프로젝트 만들기
> * 프로젝트의 기본 디바이스 세부 정보 지정

## <a name="prerequisites"></a>필수 구성 요소

- 유효한 회사/학교 [Azure Active Directory 계정](../active-directory/fundamentals/active-directory-whatis.md).
- 확인된 MPN(Microsoft 파트너 네트워크) 계정. MPN 계정이 없는 경우 시작하기 전에 [파트너 네트워크에 가입](https://partner.microsoft.com/)합니다. 

> [!NOTE] 
> MPN 계정을 설정하거나 유효성을 검사하는 데 문제가 있는 경우 [파트너 센터 지원](/partner-center) 설명서를 참조하세요.


## <a name="signing-into-the-azure-certified-device-portal"></a>Azure Certified Device 포털에 로그인

시작하려면 포털에 로그인해야 합니다. 여기에서 디바이스 정보를 제공하고, 인증 테스트를 완료하고, Azure Certified Device 카탈로그에 대한 디바이스 게시를 관리할 수 있습니다.

1. [Azure Certified Device 포털](https://certify.azure.com)로 이동합니다.
1. 왼쪽에서 `Company profile`을 선택하고 제조업체 정보를 업데이트합니다.
   ![회사 프로필 섹션](./media/images/company-profile.png)
1. 프로그램 계약에 동의하여 프로젝트를 시작합니다.

## <a name="creating-your-project-on-the-portal"></a>포털에서 프로젝트 만들기

이제 포털에서 모든 설정을 완료했으므로 인증 프로세스를 시작할 수 있습니다. 먼저 디바이스에 대한 프로젝트를 만들어야 합니다.

1. 홈 화면에서 `Create new project`를 선택합니다. 그러면 다음 섹션에서 기본 디바이스 정보를 추가하는 창이 열립니다.

 ![새 프로젝트 만들기 단추 이미지](./media/images/create-new-project.png)

## <a name="identifying-basic-device-information"></a>기본 디바이스 정보 식별

그런 다음, 기본 디바이스 정보를 제공해야 합니다. 이 정보는 나중에 편집할 수 있습니다.

1. `Basics` 섹션 아래에서 요청된 필드를 완료합니다. **필수** 필드에 대한 설명은 아래 표를 참조하세요.

    | 필드                  | Description                                                                                                                         |
    |------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
    | 프로젝트 이름           | Azure Certified Device 카탈로그에 표시되지 않는 내부 이름                                                        |
    | 디바이스 이름            | 디바이스에 대한 공식 이름                                                                                                |
    | 디바이스 유형            | 완성된 제품 또는 솔루션 지원 개발자 키트의 사양입니다.     용어에 대한 자세한 내용은 [인증 용어집](./resources-glossary.md)을 참조하세요.                                                                     |
    | 디바이스 클래스           | 게이트웨이, 센서 또는 기타.  용어에 대한 자세한 내용은 [인증 용어집](./resources-glossary.md)을 참조하세요.                                                                    |
    | 디바이스 소스 코드 URL | 솔루션 지원 개발 키트를 인증하는 경우 필요합니다. 그렇지 않은 경우 선택 사항입니다. URL은 디바이스 코드에 대한 GitHub 위치여야 합니다. |

    > [!Note]
    > Microsoft 서비스(예: Azure Sphere)를 마케팅하는 경우 디바이스 이름이 Microsoft [브랜딩 지침](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks)을 준수하는지 확인하세요.

1. `Next` 단추를 선택하여 `Certifications` 탭을 계속 진행합니다.

    ![새 프로젝트 만들기 양식, 인증 탭의 이미지](./media/images/create-new-project-certificationswindow.png)

1. 디바이스에 대해 달성하려는 인증을 지정합니다.
1. `Create`를 선택하면 새 프로젝트가 저장되고 포털의 홈 페이지에 표시됩니다.

    ![프로젝트 테이블의 이미지](./media/images/project-table.png)

1. 테이블의 프로젝트 이름을 선택합니다. 그러면 디바이스에 대한 기타 세부 정보를 추가하고 볼 수 있는 프로젝트 요약 페이지가 시작됩니다.

    ![프로젝트 세부 정보 페이지의 이미지](./media/images/device-details-section.png)

## <a name="next-steps"></a>다음 단계

이제 인증 서비스를 사용하여 디바이스 세부 정보를 추가하고 디바이스를 테스트할 준비가 되었습니다. 다음 문서로 넘어가서 디바이스 세부 정보를 편집하는 방법을 알아보세요.
> [!div class="nextstepaction"]
> [자습서: 디바이스 세부 정보 추가](tutorial-02-adding-device-details.md)