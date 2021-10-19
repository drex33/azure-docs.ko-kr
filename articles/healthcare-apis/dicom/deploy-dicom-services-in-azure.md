---
title: Azure Portal 사용하여 DICOM 서비스 배포 - Azure Healthcare API
description: 이 문서에서는 Azure Portal DICOM 서비스를 배포하는 방법을 설명합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: b07d9bba578aeac026864e40a23236816b510f1e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787242"
---
# <a name="deploy-dicom-service-using-the-azure-portal"></a>Azure Portal 사용하여 DICOM 서비스 배포

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 빠른 시작에서는 Azure Portal 사용하여 DICOM 서비스를 배포하는 방법을 알아봅니다.

배포가 완료되면 Azure Portal 사용하여 새로 만든 DICOM 서비스로 이동하여 서비스 URL을 포함한 세부 정보를 볼 수 있습니다. DICOM 서비스에 액세스하는 서비스 URL은 ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` 입니다. 요청할 때 버전을 URL의 일부로 지정해야 합니다. 자세한 내용은 [DICOM 서비스용 API 버전 버전 설명서](api-versioning-dicom-service.md)에서 확인할 수 있습니다.

## <a name="prerequisite"></a>필수 조건

DICOM 서비스를 배포하려면 Azure Portal 만든 작업 영역이 있어야 합니다. 작업 영역을 만드는 자세한 내용은 **Azure Portal 작업 영역 배포를** 참조하세요.

## <a name="deploying-dicom-service"></a>DICOM 서비스 배포

1. Azure Portal **리소스 그룹** 페이지에서 **의료 API 작업 영역의** 이름을 선택합니다.

   [![작업 영역 리소스 그룹을 선택합니다. ](media/select-workspace-resource-group.png) ](media/select-workspace-resource-group.png#lightbox)

2. **DICOM 서비스 배포를** 선택합니다.

   [![dicom 서비스를 배포합니다. ](media/workspace-deploy-dicom-services.png) ](media/workspace-deploy-dicom-services.png#lightbox)


3. **DICOM 서비스 추가를** 선택합니다.

   [![dicom 서비스를 추가합니다. ](media/add-dicom-service.png) ](media/add-dicom-service.png#lightbox)


4. DICOM 서비스의 이름을 입력한 **다음, 검토 + 만들기를** 선택합니다. 

    [![dicom 서비스 이름입니다. ](media/enter-dicom-service-name.png) ](media/enter-dicom-service-name.png#lightbox)


   (**선택 사항)** **다음: 태그 >** 선택합니다.

    태그는 리소스를 분류하는 데 사용되는 이름/값 쌍입니다. 태그에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 및 관리 계층 구조 구성을 참조하세요.](../../azure-resource-manager/management/tag-resources.md)

5. 녹색 유효성 검사 확인 표시가 표시되면 **만들기를** 선택하여 DICOM 서비스를 배포합니다.

6. 배포 프로세스가 완료되면 **리소스로 이동을** 선택합니다.  

   [![dicom 리소스로 이동합니다. ](media/go-to-resource.png) ](media/go-to-resource.png#lightbox)



   새로 배포된 DICOM 서비스의 결과는 아래와 같습니다.

   [![dicom에서 배포를 완료했습니다. ](media/results-deployed-dicom-service.png) ](media/results-deployed-dicom-service.png#lightbox)



## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)






