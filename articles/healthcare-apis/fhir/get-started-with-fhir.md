---
title: FHIR 서비스 시작 하기-Azure 의료 Api
description: 이 문서에서는 Azure 의료 Api에서 FHIR 서비스를 시작 하는 방법을 설명 합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: f679f82531c84b5c05de4bddc2551c9c51c6d09b
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273227"
---
# <a name="get-started-with-the-fhir-service"></a>FHIR 서비스 시작

이 문서에서는 [Azure 의료 api](../healthcare-apis-overview.md)에서 FHIR 서비스를 시작 하는 기본 단계에 대해 간략하게 설명 합니다.

필수 구성 요소로 서 azure 구독이 필요 하며 azure 리소스 그룹을 만들고 Azure 리소스를 배포할 수 있는 적절 한 권한이 부여 되었습니다. 모든 단계를 수행 하거나 기존 환경이 있는 경우 일부를 건너뛸 수 있습니다. 또한 모든 단계를 결합 하 고 PowerShell, Azure CLI 및 REST API 스크립트에서 완료할 수 있습니다.

[![FHIR 서비스 흐름 다이어그램을 시작 합니다.](media/get-started-with-fhir.png)](media/get-started-with-fhir.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Azure 구독에 작업 영역 만들기

[Azure Portal](../healthcare-apis-quickstart.md)에서 또는 PowerShell, Azure CLI 및 REST API를 사용 하 여 작업 영역을 만들 수 있습니다. [의료 api 샘플](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)에서 스크립트를 찾을 수 있습니다.

> [!NOTE]
> 각 Azure 구독에서 만들 수 있는 작업 영역 수 및 FHIR 서비스 인스턴스 수에는 제한이 있습니다.

## <a name="create-a-fhir-service-in-the-workspace"></a>작업 영역에서 FHIR 서비스 만들기

[Azure Portal](../fhir/fhir-portal-quickstart.md)에서 또는 PowerShell, Azure CLI 및 REST API를 사용 하 여 FHIR 서비스 인스턴스를 만들 수 있습니다. [의료 api 샘플](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)에서 스크립트를 찾을 수 있습니다.

필요에 따라 작업 영역에서 [DICOM 서비스](../dicom/deploy-dicom-services-in-azure.md) 와 [IoT 커넥터](../iot/deploy-iot-connector-in-azure.md) 를 만들 수 있습니다.

## <a name="access-the-fhir-service"></a>FHIR 서비스 액세스

fhir 서비스는 사용 하지 않도록 설정할 수 없는 Azure Active Directory (Azure AD)에 의해 보호 됩니다. 서비스 API에 액세스 하려면 Azure AD에서 서비스 주체 라고도 하는 클라이언트 응용 프로그램을 만들고 적절 한 권한을 부여 해야 합니다.

### <a name="register-a-client-application"></a>클라이언트 응용 프로그램 등록

[Azure Portal](../register-application.md)에서 또는 PowerShell 및 Azure CLI 스크립트를 사용 하 여 클라이언트 응용 프로그램을 만들거나 등록할 수 있습니다. 이 클라이언트 응용 프로그램은 하나 이상의 FHIR 서비스 인스턴스에 사용할 수 있습니다. Azure 의료 Api의 다른 서비스에도 사용할 수 있습니다.

인증서 또는 클라이언트 암호를 사용 하 여 클라이언트 응용 프로그램을 만든 경우 만료 되기 전에 인증서 또는 클라이언트 암호를 갱신 하 고 응용 프로그램에서 클라이언트 자격 증명을 바꾸어야 합니다.

클라이언트 응용 프로그램을 삭제할 수 있습니다. 클라이언트 응용 프로그램을 삭제 하기 전에 프로덕션, 개발, 테스트 또는 QA (품질 보증) 환경에서 사용 되지 않는지 확인 합니다.

### <a name="grant-access-permissions"></a>액세스 권한 부여

액세스 권한을 부여 하거나 [Azure Portal](../configure-azure-rbac.md)또는 PowerShell 및 Azure CLI 스크립트를 사용 하 여 역할을 할당할 수 있습니다.

### <a name="perform-create-read-update-and-delete-crud-transactions"></a>CRUD (만들기, 읽기, 업데이트 및 삭제) 트랜잭션 수행

응용 프로그램의 FHIR 서비스에 대 한 만들기, 읽기 (검색), 업데이트 및 삭제 (CRUD) 트랜잭션을 수행 하거나 Postman, REST 클라이언트, 말아와 같은 도구를 사용 하 여 수행할 수 있습니다. FHIR 서비스는 기본적으로 보호 되므로 액세스 토큰을 받아서 트랜잭션 요청에 포함 해야 합니다.

#### <a name="get-an-access-token"></a>액세스 토큰 가져오기

PowerShell, Azure CLI, REST CCI 또는 .NET SDK를 사용 하 여 Azure AD 액세스 토큰을 가져올 수 있습니다.  자세한 내용은 [액세스 토큰 가져오기](../get-access-token.md)를 참조 하세요.

#### <a name="access-using-existing-tools"></a>기존 도구를 사용 하 여 액세스

- [Postman](../use-postman.md)
- [Rest 클라이언트](../using-rest-client.md)
- [cURL](../using-curl.md)

#### <a name="load-data"></a>데이터 로드

FHIR 서비스에 대 한 POST 또는 PUT 메서드를 사용 하 여 데이터를 직접 로드할 수 있습니다. 데이터를 대량 로드 하기 위해 아래 나열 된 오픈 소스 도구 중 하나를 사용할 수 있습니다.
 
- [Fhir 로더](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/FHIRDL) 이는 .NET 콘솔 앱 이며 Azure storage에 저장 된 데이터를 FHIR 서비스에 로드 합니다. 단일 스레드 앱 이지만 로컬 또는 Docker 컨테이너에서 여러 복사본을 실행할 수 있습니다. 
- [대량 로더](https://github.com/microsoft/fhir-loader) 이 도구는 Azure 함수 앱 (마이크로 서비스) 이며 병렬 스레드에서 실행 됩니다.
- [대량 가져오기](https://github.com/microsoft/fhir-server/blob/main/docs/BulkImport.md) 이 도구는 오픈 소스 FHIR 서버 에서만 작동 합니다. Azure 의료 Api에는 나중에 사용할 수 있습니다.

### <a name="cms-search-profile-validation-and-reindex"></a>CMS, 검색, 프로필 유효성 검사 및 재 인덱싱

상호 운용성 및 환자 액세스, 검색, 프로필 유효성 검사 및 [Fhir 서비스](overview.md) 설명서에서의 재 인덱싱에 대 한 자세한 내용을 확인할 수 있습니다.

### <a name="export-data"></a>데이터 내보내기

필요에 따라 데이터를 [Azure Storage](../data-transformation/export-data.md) 으로 내보내고 ($export) 분석 또는 기계 학습 프로젝트에서 사용할 수 있습니다. 데이터를 "있는 그대로" 또는 [비 id](../data-transformation/de-identified-export.md) 형식으로 내보낼 수 있습니다 `ndjson` . 

오픈 소스 프로젝트를 사용 하 여 [Synapse](../data-transformation/move-to-synapse.md) 으로 데이터를 내보낼 수도 있습니다. 향후이 기능은 관리 되는 서비스에 통합 됩니다.

### <a name="converting-data"></a>데이터 변환

필요에 따라 [HL7 v2](../data-transformation/convert-data.md) 및 기타 형식 데이터를 FHIR로 변환할 수 있습니다.

### <a name="using-fhir-data-in-power-bi-dashboard"></a>Power BI 대시보드에서 fhir 데이터 사용

필요에 따라 fhir 데이터를 사용 하 여 Power BI 대시보드 보고서를 만들 수 있습니다.

- [FHIR 용 파워 쿼리 커넥터](https://docs.microsoft.com/power-query/connectors/fhir/fhir)
- [IoT 커넥터 및 Microsoft Power BI](../iot/iot-connector-power-bi.md)

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 서비스 사용을 시작 하는 기본 단계에 대해 설명 했습니다. 작업 영역에서 FHIR 서비스를 배포 하는 방법에 대 한 자세한 내용은을 참조 하세요.

>[!div class="nextstepaction"]
>[Azure 의료 Api 내에서 FHIR 서비스 배포](fhir-portal-quickstart.md)
