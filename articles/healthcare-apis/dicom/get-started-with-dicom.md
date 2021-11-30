---
title: DICOM 서비스 시작-Azure 의료 Api
description: 이 문서에서는 Azure 의료 Api에서 DICOM 서비스를 시작 하는 방법을 설명 합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: bc3c691d7a990e47b6afe624854d9085169fe0b2
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273208"
---
# <a name="get-started-with-the-dicom-service"></a>DICOM 서비스 시작

이 문서에서는 [Azure 의료 api](../healthcare-apis-overview.md)에서 DICOM 서비스를 시작 하는 기본 단계에 대해 간략하게 설명 합니다. 

필수 구성 요소로 서 azure 구독이 필요 하며 azure 리소스 그룹을 만들고 Azure 리소스를 배포할 수 있는 적절 한 권한이 부여 되었습니다. 모든 단계를 수행 하거나 기존 환경이 있는 경우 일부를 건너뛸 수 있습니다. 또한 모든 단계를 결합 하 고 PowerShell, Azure CLI 및 REST API 스크립트에서 완료할 수 있습니다.

[![DICOM를 사용 하 여 시작](media/get-started-with-dicom.png)](media/get-started-with-dicom.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Azure 구독에서 작업 영역 만들기

[Azure Portal](../healthcare-apis-quickstart.md) 에서 또는 PowerShell, Azure CLI 및 REST API를 사용 하 여 작업 영역을 만들 수 있습니다. [의료 api 샘플](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)에서 스크립트를 찾을 수 있습니다.

> [!NOTE]
> 각 Azure 구독에서 만들 수 있는 작업 영역 수와 DICOM 서비스 인스턴스 수에는 제한이 있습니다.

## <a name="create-a-dicom-service-in-the-workspace"></a>작업 영역에서 DICOM 서비스 만들기

[Azure Portal](deploy-dicom-services-in-azure.md) 에서 또는 PowerShell, Azure CLI 및 REST API를 사용 하 여 DICOM 서비스 인스턴스를 만들 수 있습니다. [의료 api 샘플](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)에서 스크립트를 찾을 수 있습니다.

필요에 따라 작업 영역에서 [Fhir 서비스](../fhir/fhir-portal-quickstart.md) 및 [IoT 커넥터](../iot/deploy-iot-connector-in-azure.md) 를 만들 수 있습니다.

## <a name="access-the-dicom-service"></a>DICOM 서비스 액세스

DICOM 서비스는 사용 하지 않도록 설정할 수 없는 Azure Active Directory (Azure AD)에 의해 보호 됩니다. 서비스 API에 액세스 하려면 Azure AD에서 서비스 주체 라고도 하는 클라이언트 응용 프로그램을 만들고 적절 한 권한을 부여 해야 합니다.

### <a name="register-a-client-application"></a>클라이언트 응용 프로그램 등록

[Azure Portal](../register-application.md)에서 또는 PowerShell 및 Azure CLI 스크립트를 사용 하 여 클라이언트 응용 프로그램을 만들거나 등록할 수 있습니다. 이 클라이언트 응용 프로그램은 하나 이상의 DICOM 서비스 인스턴스에 사용할 수 있습니다. Azure 의료 Api의 다른 서비스에도 사용할 수 있습니다.

인증서 또는 클라이언트 암호를 사용 하 여 클라이언트 응용 프로그램을 만든 경우 만료 되기 전에 인증서 또는 클라이언트 암호를 갱신 하 고 응용 프로그램에서 클라이언트 자격 증명을 바꾸어야 합니다.

클라이언트 응용 프로그램을 삭제할 수 있습니다. 이를 수행 하기 전에 프로덕션, 개발, 테스트 또는 QA (품질 보증) 환경에서 사용 되지 않는지 확인 합니다.

### <a name="grant-access-permissions"></a>액세스 권한 부여

액세스 권한을 부여 하거나 [Azure Portal](../configure-azure-rbac.md)또는 PowerShell 및 Azure CLI 스크립트를 사용 하 여 역할을 할당할 수 있습니다.

### <a name="perform-create-read-update-and-delete-crud-transactions"></a>CRUD (만들기, 읽기, 업데이트 및 삭제) 트랜잭션 수행

응용 프로그램의 DICOM 서비스에 대해 만들기, 읽기 (검색), 업데이트 및 삭제 (CRUD) 트랜잭션을 수행 하거나 Postman, REST 클라이언트, 말아 및 Python과 같은 도구를 사용 하 여 수행할 수 있습니다. DICOM 서비스는 기본적으로 보호 되므로 액세스 토큰을 받아서 트랜잭션 요청에 포함 해야 합니다.

#### <a name="get-an-access-token"></a>액세스 토큰 가져오기

PowerShell, Azure CLI, REST CLI 또는 .NET SDK를 사용 하 여 Azure AD 액세스 토큰을 가져올 수 있습니다.  자세한 내용은 [액세스 토큰 가져오기](../get-access-token.md)를 참조 하세요.

#### <a name="access-using-existing-tools"></a>기존 도구를 사용 하 여 액세스

- [Postman](../use-postman.md)
- [REST 클라이언트](../using-rest-client.md)
- [.NET C #](dicomweb-standard-apis-c-sharp.md)
- [cURL](dicomweb-standard-apis-curl.md)
- [Python](dicomweb-standard-apis-python.md)

### <a name="dicomweb-standard-apis-and-change-feed"></a>DICOMweb 표준 Api 및 변경 피드

DICOMweb 표준 Api에 대 한 자세한 내용과 [DICOM 서비스](dicom-services-overview.md) 설명서의 변경 피드를 확인할 수 있습니다.

#### <a name="dicomcast"></a>DICOMCast

Open Source [DICOMCast](https://github.com/microsoft/dicom-server/tree/main/converter/dicom-cast) 프로젝트를 사용 하 여 FHIR 데이터 작업을 수행할 수 있습니다. 향후이 기능은 관리 되는 서비스에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 DICOM 서비스 사용을 시작 하는 기본 단계에 대해 설명 했습니다. 작업 영역에서 DICOM 서비스를 배포 하는 방법에 대 한 자세한 내용은을 참조 하세요.

>[!div class="nextstepaction"]
>[Azure Portal를 사용 하 여 DICOM 서비스 배포](deploy-dicom-services-in-azure.md)