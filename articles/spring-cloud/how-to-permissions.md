---
title: Azure Spring Cloud에서 권한 사용
description: 이 문서에서는 Azure Spring Cloud 리소스에 대한 권한을 위임하는 사용자 지정 역할을 만드는 방법을 보여 줍니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 61c4eb0322de60bb90d49bd71b111fe8b2db07ea
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567529"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Azure Spring Cloud에서 권한을 사용하는 방법
이 문서에서는 Azure Spring Cloud 리소스에 대한 권한을 위임하는 사용자 지정 역할을 만드는 방법을 보여 줍니다. 사용자 지정 역할은 다양한 스톡 권한으로 [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 확장합니다.

다음 사용자 지정 역할을 구현합니다.

* **개발자 역할**:

    * 배포
    * 테스트
    * 앱 다시 시작
    * Git 리포지토리에서 앱 구성 적용 및 변경
    * 로그 스트림 가져오기

* **DevOps 엔지니어 역할**

    * Azure Spring Cloud에서 모든 항목을 만들기, 읽기, 업데이트, 삭제
* **운영 - 사이트 안정성 엔지니어링 역할**:

    * 앱 다시 시작
    * 로그 스트림 가져오기
    * 앱 또는 구성 변경할 수 없음

* **Azure Pipelines/Jenkins/GitHub Actions 역할**:

    * 만들기, 읽기, 업데이트 및 삭제 작업 수행
    * Terraform 또는 ARM 템플릿을 사용하여 Azure Spring Cloud와 Azure Pipelines, Jenkins 또는 GitHub Actions를 비롯한 서비스 인스턴스 내의 앱에서 모든 항목 만들기 및 구성

## <a name="define-the-developer-role"></a>개발자 역할 정의

개발자 역할에는 앱을 다시 시작하고 로그 스트림을 볼 권한이 포함됩니다. 이 역할은 앱 또는 구성을 변경할 수 없습니다.

#### <a name="portal"></a>[포털](#tab/Azure-portal)
1. Azure Portal에서 사용자 지정 역할을 할당할 수 있는 구독을 엽니다.
2. **액세스 제어(IAM)** 를 엽니다.
3. **추가** 를 선택합니다.
4. **사용자 지정 역할 추가** 를 선택합니다.
5. **다음** 을 선택합니다.

   ![사용자 지정 역할 만들기 창의 기본 사항 탭을 보여 주는 스크린샷](media/spring-cloud-permissions/create-custom-role.png)

6. **권한 추가** 를 선택합니다.

   ![권한 추가 단추를 보여 주는 스크린샷](media/spring-cloud-permissions/add-permissions.png)


7. 검색 상자에서 **Microsoft.app** 을 검색합니다. **Microsoft Azure Spring Cloud** 를 선택합니다.

   ![Microsoft.app 검색 결과를 보여 주는 스크린샷](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. 개발자 역할에 대한 권한을 선택합니다.

   **Microsoft.AppPlatform/Spring** 에서 다음을 선택합니다.
   * **쓰기: Azure Spring Cloud 서비스 인스턴스 만들거나 업데이트**
   * **읽기: Azure Spring Cloud 서비스 인스턴스 가져오기**
   * **기타: Azure Spring Cloud 서비스 인스턴스 테스트 키 나열**

   **Microsoft.AppPlatform/Spring/apps** 에서 다음을 선택합니다.
   * **읽기: Microsoft Azure Spring Cloud 애플리케이션 읽기**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 리소스 업로드 URL 가져오기**

   **Microsoft.AppPlatform/Spring/apps/bindings** 에서 다음을 선택합니다.
   * **읽기: Microsoft Azure Spring Cloud 애플리케이션 바인딩 읽기**

   **Microsoft.AppPlatform/Spring/apps/deployments** 에서 다음을 선택합니다.
   * **쓰기: Microsoft Azure Spring Cloud 애플리케이션 배포 작성**
   * **읽기: Microsoft Azure Spring Cloud 애플리케이션 배포 읽기**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 시작**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 중지**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 다시 시작**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 로그 파일 URL 가져오기**

   **Microsoft.AppPlatform/Spring/apps/domains** 에서 다음을 선택합니다.
   * **읽기: Microsoft Azure Spring Cloud 애플리케이션 사용자 지정 도메인 읽기**

   **Microsoft.AppPlatform/Spring/certificates** 에서 다음을 선택합니다.
   * **읽기: Microsoft Azure Spring Cloud 인증서 읽기**

   **Microsoft.AppPlatform/locations/operationResults/Spring** 에서 다음을 선택합니다.
   * **읽기: 작업 결과 읽기**

   **Microsoft.AppPlatform/locations/operationStatus/operationId** 에서 다음을 선택합니다.
   * **읽기: 작업 상태 읽기**

    [ ![개발자 권한에 대한 선택 옵션을 보여 주는 스크린샷](media/spring-cloud-permissions/developer-permissions-box.png) ](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. **추가** 를 선택합니다.

10. 권한을 검토합니다.

11. **검토 및 만들기** 를 선택합니다.

#### <a name="json"></a>[JSON](#tab/JSON)
1. Azure Portal에서 사용자 지정 역할을 할당할 수 있는 구독을 엽니다.
2. **액세스 제어(IAM)** 를 엽니다.
3. **추가** 를 선택합니다.
4. **사용자 지정 역할 추가** 를 선택합니다.
5. **다음** 을 선택합니다.

6. **JSON** 탭을 선택합니다.

7. **편집** 을 선택하고 기본 텍스트를 삭제합니다.

   ![기본 JSON 텍스트를 보여 주는 스크린샷](media/spring-cloud-permissions/create-custom-role-edit-json.png)

8. 다음 JSON을 붙여넣어 개발자 역할을 정의합니다.

   ```json
   {
     "properties": {
       "roleName": "Developer",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/domains/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/Spring/certificates/read",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

   ![개발자 역할에 대한 JSON을 보여 주는 스크린샷](media/spring-cloud-permissions/create-custom-role-json.png)

9. **저장** 을 선택합니다.

10. 권한을 검토합니다.

11. **검토 및 만들기** 를 선택합니다.

---

## <a name="define-the-devops-engineer-role"></a>DevOps 엔지니어 역할 정의

이 절차에서는 Azure Spring Cloud 앱을 배포, 테스트 및 다시 시작할 권한이 있는 역할을 정의합니다.

#### <a name="portal"></a>[포털](#tab/Azure-portal)
1. 개발자 역할을 추가하는 절차의 1~4단계를 반복합니다.

2. DevOps 엔지니어 역할에 대한 권한을 선택합니다.

   **Microsoft.AppPlatform/Spring** 에서 다음을 선택합니다.
   * **쓰기: Azure Spring Cloud 서비스 인스턴스 만들거나 업데이트**
   * **삭제: Azure Spring Cloud 서비스 인스턴스 삭제**
   * **읽기: Azure Spring Cloud 서비스 인스턴스 가져오기**
   * **기타: Azure Spring Cloud 서비스 인스턴스 테스트 엔드포인트 사용**
   * **기타: Azure Spring Cloud 서비스 인스턴스 테스트 엔드포인트 사용 안 함**
   * **기타: Azure Spring Cloud 서비스 인스턴스 테스트 키 나열**
   * **기타: Azure Spring Cloud 서비스 인스턴스 테스트 키 다시 생성**

   **Microsoft.AppPlatform/Spring/apps** 에서 다음을 선택합니다.
   * **쓰기: Microsoft Azure Spring Cloud 애플리케이션 작성**
   * **삭제: Microsoft Azure Spring Cloud 애플리케이션 삭제**
   * **읽기: Microsoft Azure Spring Cloud 애플리케이션 읽기**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 리소스 업로드 URL 가져오기**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 사용자 지정 도메인 유효성 검사**

   **Microsoft.AppPlatform/Spring/apps/bindings** 에서 다음을 선택합니다.
   * **쓰기: Microsoft Azure Spring Cloud 애플리케이션 바인딩 작성**
   * **삭제: Microsoft Azure Spring Cloud 애플리케이션 바인딩 삭제**
   * **읽기: Microsoft Azure Spring Cloud 애플리케이션 바인딩 읽기**

   **Microsoft.AppPlatform/Spring/apps/deployments** 에서 다음을 선택합니다.
   * **쓰기: Microsoft Azure Spring Cloud 애플리케이션 배포 작성**
   * **삭제: Azure Spring Cloud 애플리케이션 배포 삭제**
   * **읽기: Microsoft Azure Spring Cloud 애플리케이션 배포 읽기**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 시작**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 중지**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 다시 시작**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 로그 파일 URL 가져오기**

   **Microsoft.AppPlatform/Spring/apps/deployments/skus** 에서 다음을 선택합니다.
   * **읽기: 애플리케이션 배포를 사용할 수 있는 SKU 나열**

   **Microsoft.AppPlatform/locations** 에서 다음을 선택합니다.
   * **기타: 이름 가용성을 확인합니다.**

   **Microsoft.AppPlatform/locations/operationResults/Spring** 에서 다음을 선택합니다.
   * **읽기: 작업 결과 읽기**

   **Microsoft.AppPlatform/locations/operationStatus/operationId** 에서 다음을 선택합니다.
   * **읽기: 작업 상태 읽기**

   **Microsoft.AppPlatform/skus** 에서 다음을 선택합니다.
   * **읽기: 사용 가능한 SKU 나열**

   [ ![DevOps 권한에 대한 선택 옵션을 보여 주는 스크린샷](media/spring-cloud-permissions/dev-ops-permissions.png) ](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. **추가** 를 선택합니다.

4. 권한을 검토합니다.

5. **검토 및 만들기** 를 선택합니다.

#### <a name="json"></a>[JSON](#tab/JSON)

1. 개발자 역할을 추가하는 절차의 1~4단계를 반복합니다.
2. **다음** 을 선택합니다.

3. **JSON** 탭을 선택합니다.

4. **편집** 을 선택하고 기본 텍스트를 삭제합니다.

   ![기본 JSON 텍스트를 보여 주는 스크린샷](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. 다음 JSON에 붙여넣어 DevOps 엔지니어 역할을 정의합니다.

   ```json
   {
     "properties": {
       "roleName": "DevOps engineer",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/delete",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/enableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/disableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/regenerateTestKey/action",
             "Microsoft.AppPlatform/Spring/apps/write",
             "Microsoft.AppPlatform/Spring/apps/delete",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/validateDomain/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/write",
             "Microsoft.AppPlatform/Spring/apps/bindings/delete",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/delete",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/skus/read",
             "Microsoft.AppPlatform/locations/checkNameAvailability/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read",
             "Microsoft.AppPlatform/skus/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. 권한을 검토합니다.

7. **검토 및 만들기** 를 선택합니다.

---

## <a name="define-the-ops---site-reliability-engineering-role"></a>운영 - 사이트 안정성 엔지니어링 역할 정의

이 절차에서는 Azure Spring Cloud 앱을 배포, 테스트 및 다시 시작할 권한이 있는 역할을 정의합니다.

#### <a name="portal"></a>[포털](#tab/Azure-portal)
1. 개발자 역할을 추가하는 절차의 1~4단계를 반복합니다.
2. 운영 - 사이트 안정성 엔지니어링 역할에 대한 권한을 선택합니다.

   **Microsoft.AppPlatform/Spring** 에서 다음을 선택합니다.
   * **읽기: Azure Spring Cloud 서비스 인스턴스 가져오기**
   * **기타: Azure Spring Cloud 서비스 인스턴스 테스트 키 나열**

   **Microsoft.AppPlatform/Spring/apps** 에서 다음을 선택합니다.
   * **읽기: Microsoft Azure Spring Cloud 애플리케이션 읽기**

   **Microsoft.AppPlatform/apps/deployments** 에서 다음을 선택합니다.
   * **읽기: Microsoft Azure Spring Cloud 애플리케이션 배포 읽기**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 시작**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 중지**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 다시 시작**

   **Microsoft.AppPlatform/locations/operationResults/Spring** 에서 다음을 선택합니다.
   * **읽기: 작업 결과 읽기**

   **Microsoft.AppPlatform/locations/operationStatus/operationId** 에서 다음을 선택합니다.
   * **읽기: 작업 상태 읽기**

   [ ![운영 - 사이트 안정성 엔지니어링 권한에 대한 선택 항목을 보여 주는 스크린샷](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. **추가** 를 선택합니다.

4. 권한을 검토합니다.

5. **검토 및 만들기** 를 선택합니다.

#### <a name="json"></a>[JSON](#tab/JSON)
1. 개발자 역할을 추가하는 절차의 1~4단계를 반복합니다.
2. **다음** 을 선택합니다.

3. **JSON** 탭을 선택합니다.

4. **편집** 을 선택하고 기본 텍스트를 삭제합니다.

   ![기본 JSON 텍스트를 보여 주는 스크린샷](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. 다음 JSON에 붙여넣어 운영 - 사이트 안정성 엔지니어링 역할을 정의합니다.

   ```json
   {
     "properties": {
       "roleName": "Ops - Site Reliability Engineering",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. 권한을 검토합니다.

7. **검토 및 만들기** 를 선택합니다.

---

## <a name="define-the-azure-pipelines--jenkins--github-actions-role"></a>Azure Pipelines/Jenkins/GitHub Actions 역할 정의

이 역할은 Azure Spring Cloud 및 서비스 인스턴스를 사용하는 앱에서 모든 항목을 만들고 구성할 수 있습니다. 이 역할은 코드를 릴리스 또는 배포하는 데 사용할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/Azure-portal)

1. 개발자 역할을 추가하는 절차의 1~4단계를 반복합니다.
2. **권한** 옵션을 엽니다.

3. Azure Pipelines/Jenkins/GitHub Actions 역할에 대한 사용 권한을 선택합니다.

   **Microsoft.AppPlatform/Spring** 에서 다음을 선택합니다.
   * **쓰기: Azure Spring Cloud 서비스 인스턴스 만들거나 업데이트**
   * **삭제: Azure Spring Cloud 서비스 인스턴스 삭제**
   * **읽기: Azure Spring Cloud 서비스 인스턴스 가져오기**
   * **기타: Azure Spring Cloud 서비스 인스턴스 테스트 엔드포인트 사용**
   * **기타: Azure Spring Cloud 서비스 인스턴스 테스트 엔드포인트 사용 안 함**
   * **기타: Azure Spring Cloud 서비스 인스턴스 테스트 키 나열**
   * **기타: Azure Spring Cloud 서비스 인스턴스 테스트 키 다시 생성**

   **Microsoft.AppPlatform/Spring/apps** 에서 다음을 선택합니다.
   * **쓰기: Microsoft Azure Spring Cloud 애플리케이션 작성**
   * **삭제: Microsoft Azure Spring Cloud 애플리케이션 삭제**
   * **읽기: Microsoft Azure Spring Cloud 애플리케이션 읽기**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 리소스 업로드 URL 가져오기**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 사용자 지정 도메인 유효성 검사**

   **Microsoft.AppPlatform/Spring/apps/bindings** 에서 다음을 선택합니다.
   * **쓰기: Microsoft Azure Spring Cloud 애플리케이션 바인딩 작성**
   * **삭제: Microsoft Azure Spring Cloud 애플리케이션 바인딩 삭제**
   * **읽기: Microsoft Azure Spring Cloud 애플리케이션 바인딩 읽기**

   **Microsoft.AppPlatform/Spring/apps/deployments** 에서 다음을 선택합니다.
   * **쓰기: Microsoft Azure Spring Cloud 애플리케이션 배포 작성**
   * **삭제: Azure Spring Cloud 애플리케이션 배포 삭제**
   * **읽기: Microsoft Azure Spring Cloud 애플리케이션 배포 읽기**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 시작**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 중지**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 다시 시작**
   * **기타: Microsoft Azure Spring Cloud 애플리케이션 배포 로그 파일 URL 가져오기**

   **Microsoft.AppPlatform/Spring/apps/deployments/skus** 에서 다음을 선택합니다.
   * **읽기: 애플리케이션 배포를 사용할 수 있는 SKU 나열**

   **Microsoft.AppPlatform/locations** 에서 다음을 선택합니다.
   * **기타: 이름 가용성을 확인합니다.**

   **Microsoft.AppPlatform/locations/operationResults/Spring** 에서 다음을 선택합니다.
   * **읽기: 작업 결과 읽기**

   **Microsoft.AppPlatform/locations/operationStatus/operationId** 에서 다음을 선택합니다.
   * **읽기: 작업 상태 읽기**

   **Microsoft.AppPlatform/skus** 에서 다음을 선택합니다.
   * **읽기: 사용 가능한 SKU 나열**

   [ ![Azure Pipelines/Jenkins/GitHub Actions 권한에 대한 선택 항목을 보여 주는 스크린샷](media/spring-cloud-permissions/pipelines-permissions-box.png) ](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)

4. **추가** 를 선택합니다.

5. 권한을 검토합니다.

6. **검토 및 만들기** 를 선택합니다.

#### <a name="json"></a>[JSON](#tab/JSON)

1. 개발자 역할을 추가하는 절차의 1~4단계를 반복합니다.
2. **다음** 을 선택합니다.

3. **JSON** 탭을 선택합니다.

4. **편집** 을 선택하고 기본 텍스트를 삭제합니다.

   ![기본 JSON 텍스트를 보여 주는 스크린샷](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. 다음 JSON에 붙여넣어 Azure Pipelines/Jenkins/GitHub Actions 역할을 정의합니다.

   ```json
   {
     "properties": {
       "roleName": "Azure Pipelines/Provisioning",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/delete",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/enableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/disableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/regenerateTestKey/action",
             "Microsoft.AppPlatform/Spring/apps/write",
             "Microsoft.AppPlatform/Spring/apps/delete",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/validateDomain/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/write",
             "Microsoft.AppPlatform/Spring/apps/bindings/delete",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/delete",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/skus/read",
             "Microsoft.AppPlatform/locations/checkNameAvailability/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. **추가** 를 선택합니다.

7. 권한을 검토합니다.

---

## <a name="see-also"></a>참고 항목

* [Azure Portal을 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트](../role-based-access-control/custom-roles-portal.md)

사용자 지정 권한을 정의하는 세 가지 방법에 대한 자세한 내용은 다음을 참조하세요.
* [역할 복제](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [처음부터 시작](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [JSON에서 시작](../role-based-access-control/custom-roles-portal.md#start-from-json)
