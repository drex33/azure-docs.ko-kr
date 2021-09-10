---
title: Azure Security Center에 GCP 계정 연결
description: Azure Security Center에서 GCP 리소스 모니터링
author: memildin
ms.author: memildin
ms.date: 02/08/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 087a9d39cf3e5d361a8a2ea33f330aef727ea7b9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741985"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>Azure Security Center에 GCP 계정 연결

클라우드 워크로드가 일반적으로 여러 클라우드 플랫폼을 사용하는 경우 클라우드 보안 서비스도 여러 클라우드 플랫폼을 사용해야 합니다.

Azure Security Center는 Azure, AWS(Amazon Web Services) 및 GCP(Google Cloud Platform)의 워크로드를 보호합니다.

GCP 계정을 Security Center에 온보딩하면 GCP Security Command Center와 Azure Security Center가 통합됩니다. 따라서 Security Center는 이러한 클라우드 환경 모두에서 가시성과 보호를 지원하여 다음을 제공합니다.

- 잘못된 보안 구성 검색
- Security Center 권장 사항 및 GCP Security Command Center의 검사 결과를 보여 주는 단일 보기
- Security Center의 보안 점수 계산에 GCP 리소스 통합
- CIS 표준을 기반으로 하는 GCP Security Command Center 권장 사항을 Security Center의 규정 준수 대시보드에 통합

아래 스크린샷에서는 Security Center의 개요 대시보드에 표시되는 GCP 프로젝트를 볼 수 있습니다.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="Security Center의 개요 대시보드에 나열된 3개 GCP 프로젝트" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|[서버용 Azure Defender](defender-for-servers-introduction.md) 필요|
|필요한 역할 및 권한:|관련 Azure 구독에 대한 **소유자** 또는 **기여자**|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)|
|||

## <a name="connect-your-gcp-account"></a>GCP 계정 연결

Security Center에서 모니터링하려는 모든 조직에 대한 커넥터를 만듭니다.

GCP 계정을 특정 Azure 구독에 연결하는 경우 [Google Cloud 리소스 계층 구조](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#resource-hierarchy-detail) 및 다음 지침을 고려하세요.

- *조직* 수준에서 GCP 계정을 ASC에 연결할 수 있습니다.
- 여러 조직을 하나의 Azure 구독에 연결할 수 있습니다.
- 여러 조직을 여러 Azure 구독에 연결할 수 있습니다.
- 조직을 연결하면 해당 조직 내의 모든 *프로젝트* 가 모두 Security Center에 추가됩니다.

아래 단계에 따라 GCP 클라우드 커넥터를 만듭니다. 

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>1단계: 보안 상태 분석을 사용하여 GCP Security Command Center 설정

조직의 모든 GCP 프로젝트에 대해 다음과 같은 작업도 수행해야 합니다.

1. [GCP 설명서의 이러한 지침](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup)을 참고하여 **GCP Security Command Center** 를 설정합니다.
1. [GCP 설명서의 이러한 지침](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics)을 참고하여 **보안 상태 분석** 을 사용하도록 설정합니다.
1. Security Command Center로 흐르는 데이터가 있는지 확인합니다.

보안 구성에 GCP 환경을 연결하는 방법에 대한 지침은 Google의 보안 구성 권장 사항 사용에 대한 권장 사항을 따릅니다. 통합은 Google Security Command Center를 활용하고 청구에 영향을 줄 수 있는 추가 리소스를 사용합니다.

처음으로 보안 상태 분석을 사용하도록 설정하는 경우 데이터를 사용할 수 있기까지 몇 시간이 걸릴 수 있습니다.


### <a name="step-2-enable-gcp-security-command-center-api"></a>2단계. GCP Security Command Center API 사용

1. Google의 **Cloud Console API 라이브러리** 에서 Azure Security Center에 연결하려는 조직의 각 프로젝트를 선택합니다.
1. API 라이브러리에서 **Security Command Center API** 를 찾아 선택합니다.
1. API 페이지에서 **사용** 을 선택합니다.

[Security Command Center API](https://cloud.google.com/security-command-center/docs/reference/rest/)에 대해 자세히 알아보세요.


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>3단계: 보안 구성 통합을 위한 전용 서비스 계정 만들기

1. **GCP 콘솔** 에서 필요한 서비스 계정을 만드는 조직의 프로젝트를 선택합니다. 

    > [!NOTE]
    > 이 서비스 계정이 조직 수준에서 추가되면 Security Command Center를 통해 조직에서 사용하도록 설정된 다른 모든 프로젝트에서 수집한 데이터에 액세스하는 데 사용됩니다. 

1. **탐색 메뉴** 의 **IAM 및 관리** 옵션 아래에서 **서비스 계정** 을 선택합니다.
1. **서비스 계정 만들기** 를 선택합니다.
1. 계정 이름을 입력하고 **만들기** 를 선택합니다.
1. **역할** 을 **Security Center 관리 뷰어** 로 지정하고 **계속** 을 선택합니다.
1. **사용자에게 이 서비스 계정에 대한 액세스 권한 부여** 섹션은 선택 사항입니다. **완료** 를 선택합니다.
1. 생성된 서비스 계정의 **이메일 값** 을 복사하고 나중에 사용할 수 있도록 저장합니다.
1. **탐색 메뉴** 의 **IAM 및 관리** 옵션 아래에서 **IAM** 을 선택합니다.
    1. 조직 수준으로 전환합니다.
    1. **ADD** 를 선택합니다.
    1. **새 구성원** 필드에 이전에 복사한 **이메일** 값을 붙여넣습니다.
    1. 역할을 **Security Center 관리 뷰어** 로 지정한 다음, **저장** 을 선택합니다.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="관련 GCP 권한 설정.":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>4단계. 전용 서비스 계정에 대한 프라이빗 키 만들기
1. 프로젝트 수준으로 전환합니다.
1. **탐색 메뉴** 의 **IAM 및 관리** 옵션 아래에서 **서비스 계정** 을 선택합니다.
1. 전용 서비스 계정을 열고 편집을 선택합니다.
1. **키** 섹션에서 **키 추가** 를 선택한 다음, **새 키 만들기** 를 선택합니다.
1. 프라이빗 키 만들기 화면에서 **JSON** 을 선택한 다음, **만들기** 를 선택합니다.
1. 나중에 사용할 수 있도록 이 JSON 파일을 저장합니다.


### <a name="step-5-connect-gcp-to-security-center"></a>5단계. Security Center에 GCP 연결
1. Security Center 메뉴에서 **클라우드 커넥터** 를 선택합니다.
1. GCP 계정 추가를 선택합니다.
1. 온보딩 페이지에서 다음을 수행한 후 **다음** 을 선택합니다.
    1. 선택한 구독의 유효성을 검사합니다.
    1. **표시 이름** 필드에 커넥터의 표시 이름을 입력합니다.
    1. **조직 ID** 필드에 조직의 ID를 입력합니다. 잘 모르는 경우에는 [조직 생성 및 관리](https://cloud.google.com/resource-manager/docs/creating-managing-organization)를 참조하세요.
    1. **프라이빗 키** 파일 상자에서 [4단계. 전용 서비스 계정에 대한 프라이빗 키 만들기](#step-4-create-a-private-key-for-the-dedicated-service-account)에서 다운로드한 JSON 파일을 찾습니다.


### <a name="step-6-confirmation"></a>6단계. 확인

커넥터가 성공적으로 생성되고 GCP Security Command Center가 올바르게 구성된 경우:

- GCP CIS 표준이 Security Center의 규정 준수 대시보드에 표시됩니다.
- 온보딩이 완료되고 5~10분 후에 Security Center 포털과 규정 준수 대시보드에 GCP 리소스에 대한 보안 권장 사항이 표시됩니다.   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="Security Center의 권장 사항 페이지에 표시되는 GCP 리소스 및 권장 사항":::


## <a name="monitoring-your-gcp-resources"></a>GCP 리소스 모니터링

위와 같이 Azure Security Center의 보안 권장 사항 페이지에는 Azure 및 AWS 리소스와 GCP 리소스가 함께 표시되어 진정한 다중 클라우드 보기를 구현합니다.

리소스 종류별로 리소스에 대한 모든 활성 권장 사항을 보려면 Security Center의 자산 인벤토리 페이지를 사용하여 관심 있는 GCP 리소스 종류로 필터링합니다.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="GCP 옵션을 보여 주는 자산 인벤토리 페이지의 리소스 종류 필터"::: 


## <a name="faq---connecting-gcp-accounts-to-azure-security-center"></a>FAQ - Azure Security Center에 GCP 계정 연결

### <a name="can-i-connect-multiple-gcp-organizations-to-security-center"></a>여러 GCP 조직을 Security Center에 연결할 수 있나요?
예. Security Center의 GCP 커넥터는 *조직* 수준에서 Google Cloud 리소스를 연결합니다. 

Security Center에서 모니터링하려는 모든 GCP 조직에 대한 커넥터를 만듭니다. 조직을 연결하면 해당 조직 내의 모든 프로젝트가 Security Center에 추가됩니다.

[Google의 온라인 문서](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy)에서 Google Cloud 리소스 계층 구조에 대해 알아보세요.


### <a name="is-there-an-api-for-connecting-my-gcp-resources-to-security-center"></a>내 GCP 리소스를 Security Center에 연결할 수 있는 API가 있나요?
예. REST API를 사용하여 Security Center 클라우드 커넥터를 만들거나 편집하거나 삭제하려면 [커넥터 API](/rest/api/securitycenter/connectors) 세부 정보를 참조하세요.

## <a name="next-steps"></a>다음 단계

GCP 계정 연결은 Azure Security Center에서 사용할 수 있는 다중 클라우드 환경의 일부입니다. 관련 정보는 다음 페이지를 참조하세요.

- [Azure Security Center에 AWS 계정 연결](quickstart-onboard-aws.md)
- [Google Cloud 리소스 계층 구조](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy) - Google 온라인 문서에서 Google Cloud 리소스 계층 구조에 대해 알아보기