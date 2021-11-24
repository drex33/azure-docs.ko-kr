---
title: Azure Portal에서 작업 영역 배포-Azure 의료 Api
description: 이 문서에서는 사용자가 Azure Portal 작업 영역을 배포 하는 방법을 설명 합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 07/12/2021
ms.author: ginle
ms.custom: mode-portal
ms.openlocfilehash: 89be6d8bab9ef544ffa62a85f0b87afb15fab013
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061568"
---
# <a name="deploy-healthcare-apis-preview-workspace-using-azure-portal"></a>Azure Portal를 사용 하 여 의료 Api (미리 보기) 작업 영역 배포

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 Azure Portal를 통해 Azure 의료 Api를 배포 하 여 작업 영역을 만드는 방법에 대해 알아봅니다. 작업 영역은 FHIR 서비스, DICOM® 서비스 및 IoT 커넥터와 같은 모든 의료 Api 서비스에 대 한 중앙 집중식 논리적 컨테이너입니다. 해당 하는 경우 모든 기본 데이터 집합 및 서비스 간에 공유 되는 특정 구성 설정을 구성 하 고 관리할 수 있습니다.


## <a name="prerequisite"></a>필수 조건

Azure Portal에서 작업 영역을 만들려면 계정 구독이 있어야 합니다. Azure 구독이 없는 경우 [지금 무료 azure 계정 만들기](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)를 참조 하세요.

## <a name="create-new-azure-service"></a>새 Azure 서비스 만들기

Azure Portal에서 **리소스 생성** 를 선택합니다.

[![리소스 ](media/create-resource.png) 만들기 ](media/create-resource.png#lightbox)

## <a name="search-for-azure-healthcare-apis"></a>Azure 의료 Api 검색

Searchbox에서 **Azure 의료 api** 를 입력 합니다.

[![의료 api ](media/search-for-healthcare-apis.png) 검색 ](media/search-for-healthcare-apis.png#lightbox)

## <a name="create-azure-healthcare-api-account"></a>Azure 의료 API 계정 만들기

**만들기** 를 선택 하 여 새 Azure 의료 api 계정을 만듭니다.

   [![작업 영역 미리 보기 ](media/create-workspace-preview.png) 만들기 ](media/create-workspace-preview.png#lightbox)

## <a name="enter-subscription-and-instance-details"></a>구독 및 인스턴스 세부 정보 입력

1. 드롭다운 목록에서 **구독** 및 **리소스 그룹** 을 선택 하거나 **새로 만들기** 를 선택 합니다.

   [![작업 영역 새로 ](media/create-healthcare-api-workspace-new.png) 만들기 ](media/create-healthcare-api-workspace-new.png#lightbox)

2. 작업 영역의 **이름을** 입력 한 다음 **지역을** 선택 합니다. 이름은 3 자에서 24 자 사이 여야 하며 모두 소문자입니다. 이름에 사용할 수 없는 문자 이므로 하이픈 "-"을 사용 하지 마십시오. 지역 및 가용성 영역에 대 한 자세한 내용은 [Azure에서 지역 및 가용성 영역](../availability-zones/az-overview.md)을 참조 하세요.

3. (**선택 사항**) **다음: 태그 >** 를 선택 합니다. **이름** 및 **값** 을 입력 하 고 **다음: 검토 + 만들기** 를 선택 합니다. 

   [![태그 ](media/tags-new.png) ](media/tags-new.png#lightbox)

   태그는 리소스를 분류 하는 데 사용 되는 이름/값 쌍입니다. 태그에 대 한 자세한 내용은 [태그를 사용 하 여 Azure 리소스 및 관리 계층 구조 구성](.././azure-resource-manager/management/tag-resources.md)을 참조 하세요.

4. **만들기** 를 선택합니다.

[![작업 영역 용어 ](media/workspace-terms.png) ](media/workspace-terms.png)


   **선택 사항**: 새로 생성 된 작업 영역을 **자동화 하기 위해 템플릿 다운로드** 를 선택할 수 있습니다.


## <a name="next-steps"></a>다음 단계

작업 영역을 만들었으므로 이제 다음을 수행할 수 있습니다.

* FHIR 서비스 배포
* DICOM 서비스 배포
* IoT Connector를 배포 하 고 데이터를 fhir 서비스에 수집 합니다.
* 변환과 식별 되지 않는 Api를 통해 데이터를 다양 한 형식 및 보조 사용으로 변환 합니다.


[![다른 서비스 ](media/healthcare-apis-deploy-services.png) 배포 ](media/healthcare-apis-deploy-services.png)

>[!div class="nextstepaction"]
>[작업 영역 개요](workspace-overview.md)
