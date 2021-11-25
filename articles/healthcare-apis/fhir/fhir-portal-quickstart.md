---
title: Azure Healthcare API 내에서 FHIR 서비스 배포
description: 이 문서에서는 Azure Portal FHIR 서비스를 배포하는 방법을 사용자에게 제공합니다.
author: stevewohl
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 09/10/2021
ms.author: ginle
ms.custom: mode-other
ms.openlocfilehash: 659fb82e35cd25622158d05a88d85a16e736c770
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133069711"
---
# <a name="deploy-a-fhir-service-within-azure-healthcare-apis---using-portal"></a>Azure Healthcare API 내에서 FHIR 서비스 배포 - 포털 사용

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 Azure Portal 사용하여 Azure Healthcare API(여기서는 FHIR 서비스라고 함) 내에서 FHIR 서비스를 배포하는 방법을 알아봅니다.

## <a name="prerequisite"></a>필수 조건

시작하기 전에 Azure Healthcare API를 이미 배포했어야 합니다. Azure Healthcare API 배포에 대한 자세한 내용은 [Azure Portal 작업 영역 배포를](../healthcare-apis-quickstart.md)참조하세요.

## <a name="create-a-new-fhir-service"></a>새 FHIR 서비스 만들기

작업 영역에서 **FHIR 서비스 배포를 선택합니다.**

[![FHIR 서비스 ](media/fhir-service/deploy-fhir-services.png) 배포 ](media/fhir-service/deploy-fhir-services.png#lightbox)

**+ FHIR 서비스 추가를** 선택합니다.

[![FHIR 서비스 ](media/fhir-service/add-fhir-service.png) 추가 ](media/fhir-service/add-fhir-service.png#lightbox)

FHIR 서비스의 **계정 이름을** 입력합니다. **FHIR** 버전(STU3 또는 **R4)을** 선택한 **다음, 검토 + 만들기를** 선택합니다.

[![FHIR 서비스 ](media/fhir-service/create-fhir-service.png) 만들기 ](media/fhir-service/create-fhir-service.png#lightbox)

**만들기를** 선택하기 전에 FHIR 서비스의 **기본 사항** 및 **추가 설정의** 속성을 검토합니다. 돌아가서 변경해야 하는 경우 **이전** 을 선택합니다. **유효성 검사 성공** 메시지가 표시되는지 확인합니다. 

[![FHIR 서비스 ](media/fhir-service/validation-fhir-service.png) 유효성 검사 ](media/fhir-service/validation-fhir-service.png#lightbox)

## <a name="additional-settings-optional"></a>추가 설정(선택 사항)

**추가 설정** 탭을 선택하여 인증 설정을 볼 수도 있습니다. Azure API for FHIR의 기본 구성은 **데이터 평면 역할 할당에 Azure RBAC를 사용** 하는 것입니다. 이 모드로 구성되면 FHIR 서비스에 대한 "기관"이 구독의 Azure Active Directory 테넌트로 설정됩니다.

[![추가 설정 FHIR 서비스 ](media/fhir-service/additional-settings-tab.png) ](media/fhir-service/additional-settings-tab.png#lightbox)

**허용되는 개체 ID를** 입력하는 상자가 회색으로 표시됩니다. 이 경우 역할 할당을 구성하는 데 Azure RBAC를 사용하기 때문입니다.

외부 또는 보조 Azure Active Directory 테넌트를 사용하도록 FHIR 서비스를 구성하려는 경우 권한을 변경하고 서버에 대한 액세스를 허용해야 하는 사용자 및 그룹의 개체 ID를 입력할 수 있습니다.

## <a name="fetch-fhir-api-capability-statement"></a>Fetch FHIR API 기능 문

새 FHIR API 계정이 프로비전되어 있는지 확인하려면 로 검색하여 기능 문을 `https://<WORKSPACE NAME>-<ACCOUNT-NAME>.fhir.azurehealthcareapis.com/metadata` 가져옵니다.

## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[Postman을 사용하여 FHIR 서비스에 액세스](../use-postman.md)
