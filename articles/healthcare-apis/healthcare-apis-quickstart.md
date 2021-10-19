---
title: Azure Portal 작업 영역 배포 - Azure Healthcare API
description: 이 문서에서는 사용자에게 Azure Portal 작업 영역을 배포하는 방법을 설명합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 07/12/2021
ms.author: ginle
ms.openlocfilehash: 1b77b05f5780f41a9b4c573d117b18f2493b635a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786115"
---
# <a name="deploy-healthcare-apis-preview-workspace-using-azure-portal"></a>Azure Portal 사용하여 의료 API(미리 보기) 작업 영역 배포

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 Azure Portal 통해 Azure Healthcare API를 배포하여 작업 영역을 만드는 방법을 알아봅니다. 작업 영역은 FHIR 서비스, DICOM® 서비스 및 IoT 커넥터와 같은 모든 의료 API 서비스에 대한 중앙 집중식 논리 컨테이너입니다. 해당하는 경우 모든 기본 데이터 세트 및 서비스 간에 공유되는 특정 구성 설정을 구성하고 관리할 수 있습니다.


## <a name="prerequisite"></a>필수 조건

Azure Portal 작업 영역을 만들려면 먼저 계정 구독이 있어야 합니다. Azure 구독이 없는 경우 지금 [무료 Azure 계정 만들기를 참조하세요.](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)

## <a name="create-new-azure-service"></a>새 Azure 서비스 만들기

Azure Portal에서 **리소스 생성** 를 선택합니다.

[![리소스 ](media/create-resource.png) 만들기 ](media/create-resource.png#lightbox)

## <a name="search-for-azure-healthcare-apis"></a>Azure Healthcare API 검색

검색 상자에 Azure **Healthcare API를 입력합니다.**

[![의료 API ](media/search-for-healthcare-apis.png) 검색 ](media/search-for-healthcare-apis.png#lightbox)

## <a name="create-azure-healthcare-api-account"></a>Azure Healthcare API 계정 만들기

**만들기를** 선택하여 새 Azure Healthcare API 계정을 만듭니다.

   [![작업 영역 미리 보기 ](media/create-workspace-preview.png) 만들기 ](media/create-workspace-preview.png#lightbox)

## <a name="enter-subscription-and-instance-details"></a>구독 및 인스턴스 세부 정보 입력

1. 드롭다운 목록에서 **구독** 및 **리소스 그룹을** 선택하거나 **새 만들기를** 선택합니다.

   [![작업 영역 새로 ](media/create-healthcare-api-workspace-new.png) 만들기 ](media/create-healthcare-api-workspace-new.png#lightbox)

2. 작업 영역의 **이름을** 입력한 다음 지역 **을** 선택합니다. 이름은 모두 소문자로 된 3~24자의 영숫자여야 합니다. 하이픈 "-"은 이름에 잘못된 문자이기 때문에 사용하지 마십시오. 지역 및 가용성 영역에 대한 자세한 내용은 [Azure의 지역 및 가용성 영역 참조하세요.](../availability-zones/az-overview.md)

3. (**선택 사항)** **다음: 태그 >** 선택합니다. **이름** 및 **값** 을 입력한 **다음, 다음: 검토 + 만들기를** 선택합니다. 

   [![태그 ](media/tags-new.png) ](media/tags-new.png#lightbox)

   태그는 리소스를 분류하는 데 사용되는 이름/값 쌍입니다. 태그에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 및 관리 계층 구조 구성을 참조하세요.](.././azure-resource-manager/management/tag-resources.md)

4. **만들기** 를 선택합니다.

[![작업 영역 용어 ](media/workspace-terms.png) ](media/workspace-terms.png)


   **선택 사항:** 새로 만든 작업 영역의 **자동화를 위해 템플릿 다운로드를** 선택할 수 있습니다.


## <a name="next-steps"></a>다음 단계

이제 작업 영역이 만들어졌으므로 다음을 수행할 수 있습니다.

* FHIR 서비스 배포
* DICOM 서비스 배포
* IoT Connector 배포하고 FHIR 서비스에 데이터를 수집합니다.
* 변환 및 식별 해제 API를 통해 데이터를 다양한 형식 및 보조 사용으로 변환합니다.


[![다른 서비스 ](media/healthcare-apis-deploy-services.png) 배포 ](media/healthcare-apis-deploy-services.png)

>[!div class="nextstepaction"]
>[작업 영역 개요](workspace-overview.md)

