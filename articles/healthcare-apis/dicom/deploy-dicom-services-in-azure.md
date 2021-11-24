---
title: Azure Portal를 사용 하 여 DICOM 서비스 배포-Azure 의료 Api
description: 이 문서에서는 Azure Portal에 DICOM 서비스를 배포 하는 방법을 설명 합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 08/04/2021
ms.author: aersoy
ms.custom: mode-portal
ms.openlocfilehash: fefaf7fc2daced95ffde90834ad38131ecfc6b94
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133041683"
---
# <a name="deploy-dicom-service-using-the-azure-portal"></a>Azure Portal를 사용 하 여 DICOM 서비스 배포

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 빠른 시작에서는 Azure Portal를 사용 하 여 DICOM 서비스를 배포 하는 방법을 알아봅니다.

배포가 완료 되 면 Azure Portal를 사용 하 여 새로 만든 DICOM 서비스로 이동 하 여 서비스 URL을 비롯 한 세부 정보를 볼 수 있습니다. DICOM 서비스에 액세스 하기 위한 서비스 URL은입니다. ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` 요청을 만들 때 버전을 url의 일부로 지정 해야 합니다. 자세한 내용은 [DICOM 서비스에 대 한 API 버전 관리 설명서](api-versioning-dicom-service.md)에서 찾을 수 있습니다.

## <a name="prerequisite"></a>필수 조건

DICOM 서비스를 배포 하려면 Azure Portal에서 만든 작업 영역이 있어야 합니다. 작업 영역을 만드는 방법에 대 한 자세한 내용은 **Azure Portal에서 작업 영역 배포** 를 참조 하세요.

## <a name="deploying-dicom-service"></a>DICOM 서비스 배포

1. Azure Portal의 **리소스 그룹** 페이지에서 **의료 api 작업 영역의** 이름을 선택 합니다.

   [![작업 영역 리소스 그룹을 선택 합니다. ](media/select-workspace-resource-group.png) ](media/select-workspace-resource-group.png#lightbox)

2. **DICOM Service 배포** 를 선택 합니다.

   [![dicom 서비스를 배포 합니다. ](media/workspace-deploy-dicom-services.png) ](media/workspace-deploy-dicom-services.png#lightbox)


3. **DICOM 서비스 추가** 를 선택 합니다.

   [![dicom 서비스를 추가 합니다. ](media/add-dicom-service.png) ](media/add-dicom-service.png#lightbox)


4. DICOM 서비스의 이름을 입력 한 다음 **검토 + 만들기** 를 선택 합니다. 

    [![dicom 서비스 이름입니다. ](media/enter-dicom-service-name.png) ](media/enter-dicom-service-name.png#lightbox)


   (**선택 사항**) **다음: 태그 >** 를 선택 합니다.

    태그는 리소스를 분류 하는 데 사용 되는 이름/값 쌍입니다. 태그에 대 한 자세한 내용은 [태그를 사용 하 여 Azure 리소스 및 관리 계층 구조 구성](../../azure-resource-manager/management/tag-resources.md)을 참조 하세요.

5. 녹색 유효성 검사 확인 표시가 표시 되 면 **만들기** 를 선택 하 여 DICOM 서비스를 배포 합니다.

6. 배포 프로세스가 완료 되 면 **리소스로 이동** 을 선택 합니다.  

   [![dicom 리소스로 이동 합니다. ](media/go-to-resource.png) ](media/go-to-resource.png#lightbox)



   새로 배포 된 DICOM 서비스의 결과는 다음과 같습니다.

   [![dicom 배포를 완료 했습니다. ](media/results-deployed-dicom-service.png) ](media/results-deployed-dicom-service.png#lightbox)



## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)
