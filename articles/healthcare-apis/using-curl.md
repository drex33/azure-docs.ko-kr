---
title: cURL을 통해 Azure Healthcare API에 액세스
description: 이 문서에서는 cURL을 사용하여 의료 API에 액세스하는 방법을 설명합니다.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: ddc7b291ba6ecd86e4915523345c40054794a993
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787499"
---
# <a name="access-the-healthcare-apis-preview-with-curl"></a>cURL을 통해 의료 API(미리 보기) 액세스 

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 cURL을 사용하여 Azure Healthcare API에 액세스하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

### <a name="powershell"></a>PowerShell

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/)
* 코드를 로컬로 실행하려면 [PowerShell](/powershell/module/powershellget/) 및 [Azure Az PowerShell](/powershell/azure/install-az-ps)을 설치합니다.
* 필요에 따라 Rest 클라이언트 확장을 사용하여 Visual Studio Code 스크립트를 실행할 수 있습니다. 자세한 내용은 [Rest 클라이언트 문서에 대한 링크 만들기를 참조하세요.](using-rest-client.md)
* [cURL](https://curl.se/download.html)을 다운로드하여 설치합니다.

### <a name="cli"></a>CLI

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/)
* 코드를 로컬로 실행하려면 [Azure CLI](/cli/azure/install-azure-cli)설치합니다. 
* 필요에 따라 Windows [위해 Git에](https://gitforwindows.org/)포함된 Git Bash와 같은 Bash 셸을 설치합니다.
* 필요에 따라 rest 클라이언트 확장을 사용하여 Visual Studio Code 스크립트를 실행합니다. 자세한 내용은 [Rest 클라이언트 문서에 대한 링크 만들기를 참조하세요.](using-rest-client.md)
* [cURL](https://curl.se/download.html)을 다운로드하여 설치합니다.

## <a name="obtain-azure-access-token"></a>Azure 액세스 토큰 얻기

의료 API에 액세스하기 전에 사용자 또는 클라이언트 앱에 적절한 권한을 부여해야 합니다. 사용 권한을 부여하는 방법에 대한 자세한 내용은 [의료 API 권한 부여를 참조하세요.](authentication-authorization.md)

의료 API에 대한 Azure 액세스 토큰을 가져오는 방법에는 여러 가지가 있습니다. 

> [!NOTE]
> Azure에 로그인했는지, 의료 API 인스턴스를 배포한 Azure 구독 및 테넌트인지 확인합니다.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
### check Azure environment and PowerShell versions
Get-AzContext 
Set-AzContext -Subscription <subscriptionid>
$PSVersionTable.PSVersion
Get-InstalledModule -Name Az -AllVersions
curl --version

### get access token for the FHIR service
$fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
$token=(Get-AzAccessToken -ResourceUrl $fhirservice).Token

### Get access token for the DICOM service
$dicomtokenurl= "https://dicom.healthcareapis.azure.com/"
$token=$( Get-AzAccessToken -ResourceUrl $dicomtokenurl).Token
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
### check Azure environment and CLI versions
az account show --output table
az account set --subscription <subscriptionid>
cli –version
curl --version

### get access token for the FHIR service
$fhirservice=https://<fhirservice>.fhir.azurehealthcareapis.com
token=$(az account get-access-token --resource=$fhirservice --query accessToken --output tsv)

### get access token for the DICOM service
dicomtokenurl= https://dicom.healthcareapis.azure.com/
token=$(az account get-access-token --resource=$dicomtokenurl --query accessToken --output tsv)
```

---

## <a name="access-data-in-the-fhir-service"></a>FHIR 서비스의 데이터에 액세스

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
$fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
```

---

`curl -X GET --header "Authorization: Bearer $token" $fhirservice/Patient`

[![curl 스크립트를 사용하여 FHIR 서비스의 데이터에 액세스합니다. ](media/curl-fhir.png) ](media/curl-fhir.png#lightbox)

## <a name="access-data-in-the-dicom-service"></a>DICOM 서비스의 데이터에 액세스

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
$dicomservice="https://<dicomservice>.dicom.azurehealthcareapis.com"
```
# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
dicomservice="https://<dicomservice>.dicom.azurehealthcareapis.com"
```
---

`curl -X GET --header "Authorization: Bearer $token" $dicomservice/changefeed?includemetadata=false`

[![curl 스크립트를 사용하여 DICOM 서비스의 데이터에 액세스합니다. ](media/curl-dicom.png) ](media/curl-dicom.png#lightbox)

## <a name="next-steps"></a>다음 단계

이 문서에서는 cURL을 사용하여 의료 API 데이터에 액세스하는 방법을 배웠습니다.

Visual Studio Code REST 클라이언트 확장을 사용하여 의료 API 데이터에 액세스하는 방법에 대한 자세한 내용은 다음을 참조하세요. 

>[!div class="nextstepaction"]
>[REST 클라이언트를 사용하여 의료 API에 액세스](using-rest-client.md)