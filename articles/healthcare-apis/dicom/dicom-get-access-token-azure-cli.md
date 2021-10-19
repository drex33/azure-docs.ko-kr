---
title: Azure CLI 사용하여 액세스 토큰 얻기 - DICOM 서비스용 Azure Healthcare API
description: 이 문서에서는 Azure CLI 사용하여 DICOM 서비스에 대한 액세스 토큰을 가져오는 방법을 설명합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: b48a36a5f9549fef2d484dd79b938c1a8e833e8f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435391"
---
# <a name="get-access-token-for-the-dicom-service-using-azure-cli"></a>Azure CLI 사용하여 DICOM 서비스에 대한 액세스 토큰을 얻습니다.

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 Azure CLI 사용하여 DICOM 서비스에 대한 액세스 토큰을 가져오는 방법을 알아봅니다. [DICOM 서비스를 배포할](deploy-dicom-services-in-azure.md)때 서비스에 액세스할 수 있는 사용자 또는 서비스 주체 집합을 구성합니다. 사용자 개체 ID가 허용되는 개체 ID 목록에 있는 경우 Azure CLI를 사용하여 가져온 토큰을 통해 서비스에 액세스할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Azure Cloud Shell에서 Bash 환경을 사용합니다.


[![Azure Cloud Shell 시작합니다. ](media/launch-cloud-shell.png) ](media/launch-cloud-shell.png#lightbox)

원하는 경우 Azure CLI를 [설치](/cli/azure/install-azure-cli)하여 CLI 참조 명령을 실행합니다.

* 로컬 설치를 사용하는 경우 [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 Azure CLI에 로그인합니다. 인증 프로세스를 완료하려면 터미널에 표시되는 단계를 수행합니다. 추가 로그인 옵션은 [Azure CLI를 사용하여 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.
* 메시지가 표시되면 처음 사용할 때 Azure CLI 확장을 설치합니다. 확장에 대한 자세한 내용은 [Azure CLI에서 확장 사용](/cli/azure/azure-cli-extensions-overview)을 참조하세요.
* [az version](/cli/azure/reference-index#az_version)을 실행하여 설치된 버전과 종속 라이브러리를 찾습니다. 최신 버전으로 업그레이드하려면 [az upgrade](/cli/azure/reference-index#az_upgrade)를 실행합니다.

## <a name="obtain-a-token"></a>토큰 얻기

DICOM 서비스는 `resource` `Audience` DICOM 서버 의 URI와 같은 URI가 있는 또는 를  `https://dicom.healthcareapis.azure.com` 사용합니다. 토큰을 가져오고 다음 명령을 사용하여 변수()에 저장할 수 있습니다. `$token`


```Azure CLICopy
Try It
$token=$(az account get-access-token --resource=https://dicom.healthcareapis.azure.com --query accessToken --output tsv)
```

## <a name="use-with-the-dicom-service"></a>DICOM 서비스와 함께 사용

```Azure CLICopy
Try It
curl -X GET --header "Authorization: Bearer $token"  https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com/v<version of REST API>/changefeed
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure CLI 사용하여 DICOM 서비스에 대한 액세스 토큰을 가져오는 방법을 배웠습니다. 

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)