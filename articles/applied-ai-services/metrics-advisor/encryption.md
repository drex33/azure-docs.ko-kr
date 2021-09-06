---
title: Metrics Advisor 서비스 암호화
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 Metrics Advisor 서비스 암호화.
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: mbullwin
ms.openlocfilehash: 03f6a30bbe857b59c3fa8a6fa668bdac7cb681b1
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342307"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>미사용 데이터의 Metrics Advisor 서비스 암호화

Metrics Advisor 서비스는 데이터가 클라우드에 유지될 때 자동으로 데이터를 암호화합니다. Metrics Advisor 서비스 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 약정을 충족하는 데 도움이 됩니다.

[!INCLUDE [cognitive-services-about-encryption](../../cognitive-services/includes/cognitive-services-about-encryption.md)]

Metrics Advisor는 BYOS(Bring Your Own Storage)를 사용하여 CMK 및 이중 암호화를 지원합니다. 

## <a name="steps-to-create-a-metrics-advisor-with-byos"></a>BYOS를 사용하여 Metrics Advisor를 만드는 단계

### <a name="step1-create-an-azure-database-for-postgresql-and-set-admin"></a>1단계. Azure Database for PostgreSQL 만들기 및 관리자 설정

- PostgreSQL용 Azure Database 만들기

    Azure Portal에 로그인하고 Azure Database for PostgreSQL의 리소스를 만듭니다. 주의해야 할 몇 가지 사항:

    1. **‘단일 서버’** 배포 옵션을 선택합니다. 
    2. ‘데이터 원본’을 선택할 때 **‘None’** 으로 지정합니다.
    3. ‘위치’의 경우 Metrics Advisor 리소스와 **동일한 위치** 내에서 만듭니다.
    4. ‘버전’은 **11** 로 설정해야 합니다. 
    5. ‘컴퓨팅 + 스토리지’는 최소 **32개 vCore** 가 있는 ‘메모리 최적화’ SKU를 선택해야 합니다.
    
    ![PostgreSQL용 Azure Database 만들기](media/cmk-create.png)

- 새로 만든 PG에 대한 Active Directory 관리자 설정

    Azure Database for PostgreSQL을 성공적으로 만든 후 새로 만든 Azure PG 리소스의 리소스 페이지로 이동합니다. ‘Active Directory 관리자’ 탭을 선택하고 자신을 관리자로 설정합니다.


### <a name="step2-create-a-metrics-advisor-resource-and-enable-managed-identity"></a>2단계. Metrics Advisor 리소스 만들기 및 관리 ID 사용

- Azure Portal에서 Metrics Advisor 리소스 만들기

    Azure Portal로 다시 이동하여 ‘Metrics Advisor’를 검색합니다. Metrics Advisor를 만들 때 다음을 기억하세요.

    1. 생성한 Azure Database for PostgreSQL과 **동일한 ‘지역’** 을 선택합니다. 
    2. ‘사용자 스토리지 제공’을 **‘예’** 로 표시하고 드롭다운 목록에서 방금 만든 Azure Database for PostgreSQL을 선택합니다.

- Metrics Advisor의 관리 ID 사용

    Metrics Advisor 리소스를 만든 후 ‘ID’를 선택하고 ‘상태’를 **‘켜기’** 로 설정하여 관리 ID를 사용하도록 설정합니다.

- 관리 ID의 애플리케이션 ID 얻기

    Azure Active Directory로 이동한 후, ‘엔터프라이즈 애플리케이션’을 선택합니다. ‘애플리케이션 형식’을 **‘관리 ID’** 로 변경하고 Metrics Advisor의 리소스 이름을 복사하여 검색합니다. 그런 다음, 쿼리 결과에서 ‘애플리케이션 ID’를 보고 복사할 수 있습니다.

### <a name="step3-grant-metrics-advisor-access-permission-to-your-azure-database-for-postgresql"></a>3단계. Azure Database for PostgreSQL에 Metrics Advisor 액세스 권한 부여

- 관리 ID에 대한 **‘소유자’** 역할을 사용자의 Azure Database for PostgreSQL에 부여

- 방화벽 규칙 설정

    1. ‘Azure 서비스에 대한 액세스 허용’을 ‘예’로 설정합니다. 
    2. clientIP 주소를 추가하여 Azure Database for PostgreSQL에 로그인합니다.

- 리소스 유형 ‘ https://ossrdbms-aad.database.windows.net ’을 사용하여 계정에 대한 액세스 토큰을 얻습니다. 액세스 토큰은 사용자 계정에서 Azure Database for PostgreSQL에 로그인하는 데 필요한 암호입니다. `az` 클라이언트를 사용하는 예제:

   ```
   az login
   az account get-access-token --resource https://ossrdbms-aad.database.windows.net
   ```

- 토큰을 얻은 후 사용자의 Azure Database for PostgreSQL에 로그인하는 데 사용합니다. ‘servername’을 Azure Database for PostgreSQL의 ‘개요’에서 찾을 수 있는 것으로 바꿉니다.

   ```
   export PGPASSWORD=<access-token>
   psql -h <servername> -U <adminaccount@servername> -d postgres
   ```

- 로그인한 후 다음 명령을 실행하여 Metrics Advisor 액세스 권한을 Azure Database for PostgreSQL에 부여합니다. ‘appid’를 2단계에서 얻을 수 있는 것으로 바꿉니다.

   ```
   SET aad_validate_oids_in_tenant = off;
   CREATE ROLE metricsadvisor WITH LOGIN PASSWORD '<appid>' IN ROLE azure_ad_user;
   ALTER ROLE metricsadvisor CREATEDB;
   GRANT azure_pg_admin TO metricsadvisor;
   ```

위의 모든 단계를 완료하여 CMK가 지원되는 Metrics Advisor 리소스를 성공적으로 만들었습니다. Metrics Advisor에 액세스할 수 있게 될 때까지 몇 분 정도 기다립니다.

## <a name="next-steps"></a>다음 단계

* [Metrics Advisor 서비스 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault에 대해 자세히 알아보기](../../key-vault/general/overview.md)