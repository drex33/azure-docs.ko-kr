---
title: Azure Marketplace에서 호스트형 시험 사용의 자세한 구성
description: 상업용 마켓플레이스에서 호스트형 시험 사용의 구성 단계 설명
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 12/03/2021
ms.openlocfilehash: 92a806a5a89ccd9cd6c67a084b468c0c0519b50b
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133542062"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>호스트형 시험 사용의 세부 구성

이 문서에서는 dataverse에서 dynamics 365 앱에 대해 호스트 된 테스트 드라이브를 구성 하는 방법 및 Power Apps 또는 dynamics 365 작업 앱을 구성 하는 방법을 설명 합니다.

> [!TIP]
> 상용 marketplace에서 고객의 시험에서 볼 수 있는 내용을 보려면 [Azure Marketplace 무엇 인가요?](/marketplace/azure-marketplace-overview#take-action-on-a-listing) 및 [Microsoft AppSource](/marketplace/appsource-overview)정의를 참조 하세요.

## <a name="configure-for-dynamics-365-apps-on-dataverse-and-power-apps"></a>Dataverse에서 Dynamics 365 앱에 대 한 구성 및 Power Apps

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)에 로그인합니다.
2. 위의 링크에 액세스할 수 없는 경우 애플리케이션을 게시하려면 [여기](https://appsource.microsoft.com/partners/list-an-app)에서 요청을 제출해야 합니다. 요청을 검토한 후 게시 프로세스를 시작할 수 있는 권한이 부여됩니다.
3. **dataverse에서 기존 dynamics 365 앱** 을 찾고, 데이터를 제공 하거나 **Power Apps 하 여 새 dynamics 365 앱** 을 만들 Power Apps.
4. **제품 설치** 페이지에서 **테스트 드라이브 사용** 확인란을 선택 하 고 **테스트 드라이브의 종류** (아래 글머리 기호 참조)를 선택한 다음 **초안 저장** 을 선택 합니다.

    [![' 테스트 드라이브 사용 ' 확인란을 선택 하는 방법을 보여 줍니다.](./media/test-drive/enable-test-drive-check-box-workspaces.png)](./media/test-drive/enable-test-drive-check-box-workspaces.png#lightbox)

    - **테스트 드라이브의 유형** – **Dataverse에서 Dynamics 365 앱을 선택 하 고 Power Apps** 합니다. 이 옵션은 시험 사용 사용자 프로비저닝 및 프로비저닝 해제를 수행하는 서비스를 Microsoft에서 호스트하고 유지 관리함을 나타냅니다.

5. [이러한 지침](./test-drive-azure-subscription-setup.md)에 따라 테넌트의 시험 사용 사용자를 프로비저닝/프로비저닝 해제할 수 있는 권한을 Microsoft AppSource에 부여합니다. 이 단계에서는 아래에 나와 있는 **Azure AD 앱 ID** 및 **Azure AD 앱 키** 값을 생성합니다.
6. **Test drive**  >  **기술 구성** 페이지에서 이러한 필드를 완료 합니다.

    [![테스트 드라이브 기술 구성 페이지를 보여 줍니다.](media/test-drive/technical-config-details-workspaces.png)](media/test-drive/technical-config-details-workspaces.png#lightbox)

    - **최대 동시 시험 사용 수** – 동시에 활성 시험 사용을 실행할 수 있는 동시 사용자 수입니다. 각 사용자는 시험 사용이 활성화된 상태에서 Dynamics 라이선스를 사용하므로 시험 사용 사용자가 사용할 수 있는 Dynamics 라이선스가 충분한지 확인합니다. 권장 수는 3 ~ 5입니다.
    - **시험 사용 기간** - 사용자의 시험 사용이 활성 상태로 유지되는 시간입니다. 시간이 만료되면 사용자는 테넌트에서 프로비저닝 해제됩니다. 앱의 복잡성에 따라 2 ~ 24시간을 권장합니다. 시간이 만료된 후 시험 사용에 다시 액세스하려는 사용자는 언제든지 시험 사용을 또다시 요청할 수 있습니다.
    - **인스턴스 URL** – 시험 사용을 시작할 때 시험 사용 사용자에게 전송되는 URL입니다. 일반적으로는 앱과 샘플 데이터가 설치된 Dynamics 365 인스턴스의 URL입니다. 값 예: `https://testdrive.crm.dynamics.com`.
    - **인스턴스 웹 API URL** – Dynamics 365 인스턴스의 웹 API URL입니다. Microsoft Dynamics 365 인스턴스에 로그인하고, **설정** > **사용자 지정** > **개발자 리소스** > **인스턴스 웹 API** 로 차례로 이동하여 이 값을 검색하고 주소(URL)를 복사합니다. 예를 들어 값:

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="인스턴스 웹 API의 예":::

    - **역할 이름** – 시험 사용을 위해 만든 사용자 지정 Dynamics 365 보안 역할의 이름입니다. 또는 기존 역할을 사용할 수 있습니다. Customer Engagement 인스턴스에 로그인하려면 새 역할에 최소 필수 권한을 추가해야 합니다. [Microsoft Dynamics 365에 로그인하는 데 필요한 최소 권한](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365)을 참조하세요. 시험 사용 중에 사용자에게 이 역할이 할당됩니다. 값 예: `testdriverole`.
    
        > [!IMPORTANT]
        > 보안 그룹 검사가 추가되지 않았는지 확인합니다. 이를 통해 사용자를 Customer Engagement 인스턴스와 동기화할 수 있습니다.

    - **Azure Active Directory 테넌트 ID** - Dynamics 365 인스턴스용 Azure 테넌트의 ID입니다. 이 값을 검색하려면 Azure Portal에 로그인하고 **Azure Active Directory** > **속성** 으로 이동하고 디렉터리 ID를 복사합니다. 값 예: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory 테넌트 이름** - Dynamics 365 인스턴스용 Azure 테넌트의 이름입니다. `<tenantname>.onmicrosoft.com` 형식을 사용합니다. 값 예: `testdrive.onmicrosoft.com`.
    - **Azure Active Directory 애플리케이션 ID** – 5단계에서 만든 Azure AD(Active Directory) 앱의 ID입니다. 값 예: `53852862-a2ae-4e43-9461-faa49650a096`.
    - **Azure Active Directory 애플리케이션 클라이언트 암호** – 5단계에서 만든 Azure AD 앱에 대한 비밀입니다. 값 예: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`.

7. **Drive marketplace 목록 테스트** 페이지의 필드를 완료 합니다.

    [![Marketplace 목록 세부 정보 페이지를 보여 줍니다. ](./media/test-drive/marketplace-listing-details-workspaces.png) ](./media/test-drive/marketplace-listing-details-workspaces.png#lightbox)

    - **설명** – 시험 사용의 개요입니다. 이 텍스트는 시험 사용을 프로비저닝하는 동안 사용자에게 표시됩니다. 서식 있는 콘텐츠를 제공 하려는 경우이 필드는 HTML을 지원 합니다 (필수).
    - **사용자 수동** – 테스트 드라이브 사용자가 앱을 사용 하는 방법을 이해 하는 데 도움이 되는 PDF 사용자 설명서입니다 (필수).
    - **시험 사용 데모 비디오**: 앱을 소개하는 비디오입니다(선택 사항).

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)에 로그인합니다.
2. 위의 링크에 액세스할 수 없는 경우 애플리케이션을 게시하려면 [여기](https://appsource.microsoft.com/partners/list-an-app)에서 요청을 제출해야 합니다. 요청을 검토한 후 게시 프로세스를 시작할 수 있는 권한이 부여됩니다.
3. **dataverse에서 기존 dynamics 365 앱** 을 찾고, 데이터를 제공 하거나 **Power Apps 하 여 새 dynamics 365 앱** 을 만들 Power Apps.
4. **시험 사용 지원** 확인란을 선택하고 **시험 사용 유형**(아래 글머리 기호 참조)을 선택한 다음 **저장** 을 선택합니다.

    [![‘시험 사용 지원’ 확인란 선택](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **테스트 드라이브의 유형** – **Dataverse에서 Dynamics 365 앱을 선택 하 고 Power Apps** 합니다. 이 옵션은 시험 사용 사용자 프로비저닝 및 프로비저닝 해제를 수행하는 서비스를 Microsoft에서 호스트하고 유지 관리함을 나타냅니다.

5. [이러한 지침](./test-drive-azure-subscription-setup.md)에 따라 테넌트의 시험 사용 사용자를 프로비저닝/프로비저닝 해제할 수 있는 권한을 Microsoft AppSource에 부여합니다. 이 단계에서는 아래에 나와 있는 **Azure AD 앱 ID** 및 **Azure AD 앱 키** 값을 생성합니다.
6. **시험 사용 기술 구성** 페이지에서 다음 필드를 완료합니다.

    [![시험 사용 기술 구성 페이지](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **최대 동시 시험 사용 수** – 동시에 활성 시험 사용을 실행할 수 있는 동시 사용자 수입니다. 각 사용자는 시험 사용이 활성화된 상태에서 Dynamics 라이선스를 사용하므로 시험 사용 사용자가 사용할 수 있는 Dynamics 라이선스가 충분한지 확인합니다. 권장 수는 3 ~ 5입니다.
    - **시험 사용 기간** - 사용자의 시험 사용이 활성 상태로 유지되는 시간입니다. 시간이 만료되면 사용자는 테넌트에서 프로비저닝 해제됩니다. 앱의 복잡성에 따라 2 ~ 24시간을 권장합니다. 시간이 만료된 후 시험 사용에 다시 액세스하려는 사용자는 언제든지 시험 사용을 또다시 요청할 수 있습니다.
    - **인스턴스 URL**
        - *고객 참여* – 시험 사용을 시작할 때 시험 사용 사용자에게 전송되는 URL입니다. 일반적으로는 앱과 샘플 데이터가 설치된 Dynamics 365 인스턴스의 URL입니다. 값 예: `https://testdrive.crm.dynamics.com`.
        - *캔버스 앱(Power Apps)*
            1. **PowerApps 포털** 페이지를 열고 로그인합니다.
            2. **앱** 을 선택한 다음, 앱에서 줄임표를 선택합니다.
            4. **세부 정보** 를 선택합니다.
            5. **세부 정보** 탭에서 **웹 링크** 를 복사합니다.

                [![TestDrive Canvas 앱 창을 표시 합니다. ](./media/test-drive/testdrive-canvas-app.png) ](./media/test-drive/testdrive-canvas-app.png#lightbox)

    - **인스턴스 웹 API URL**
        - *고객 참여* – Dynamics 365 인스턴스의 웹 API URL입니다. Microsoft Dynamics 365 인스턴스에 로그인하고, **설정** > **사용자 지정** > **개발자 리소스** > **인스턴스 웹 API** 를 선택하고, 주소(URL)를 복사하여 이 값을 검색합니다. 예를 들어 값:

            :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="인스턴스 웹 API의 예":::

        - *캔버스 앱(Power Apps)* – CE/Dataverse를 캔버스 앱의 백 엔드로 사용하지 않는 경우 `https://localhost`를 자리 표시자로 사용합니다.

    - **역할 이름**
        - *고객 참여* – 시험 사용을 위해 만든 사용자 지정 Dynamics 365 보안 역할의 이름입니다. 또는 기존 역할을 사용할 수 있습니다. Customer Engagement 인스턴스에 로그인하려면 새 역할에 최소 필수 권한을 추가해야 합니다. [Microsoft Dynamics 365에 로그인하는 데 필요한 최소 권한](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365)을 참조하세요. 시험 사용 중에 사용자에게 이 역할이 할당됩니다. 값 예: `testdriverole`.
        - *캔버스 앱(Power Apps)* – CE/dataverse를 백 엔드 데이터 원본으로 사용하지 않는 경우 "NA"를 사용합니다.
    
    > [!IMPORTANT]
    > 보안 그룹 검사가 추가되지 않았는지 확인합니다. 이를 통해 사용자를 Customer Engagement 인스턴스와 동기화할 수 있습니다.

    - **Azure Active Directory 테넌트 ID** - Dynamics 365 인스턴스용 Azure 테넌트의 ID입니다. 이 값을 검색하려면 Azure Portal에 로그인하고 **Azure Active Directory** > **속성** 으로 이동하고 디렉터리 ID를 복사합니다. 값 예: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory 테넌트 이름** - Dynamics 365 인스턴스용 Azure 테넌트의 이름입니다. `<tenantname>.onmicrosoft.com` 형식을 사용합니다. 값 예: `testdrive.onmicrosoft.com`.
    - **Azure Active Directory 애플리케이션 ID** – 5단계에서 만든 Azure AD(Active Directory) 앱의 ID입니다. 값 예: `53852862-a2ae-4e43-9461-faa49650a096`.
    - **Azure Active Directory 애플리케이션 클라이언트 암호** – 5단계에서 만든 Azure AD 앱에 대한 비밀입니다. 값 예: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`.

7. 마켓플레이스 목록 세부 정보를 제공합니다. 추가 필수 필드를 보려면 **언어** 를 선택합니다.

    [![Marketplace 목록 세부 정보 페이지를 보여 줍니다. ](./media/test-drive/marketplace-listing-details-workspaces.png) ](./media/test-drive/marketplace-listing-details-workspaces.png#lightbox)

    - **설명** – 시험 사용의 개요입니다. 이 텍스트는 시험 사용을 프로비저닝하는 동안 사용자에게 표시됩니다. 서식 있는 콘텐츠를 제공 하려는 경우이 필드는 HTML을 지원 합니다 (필수).
    - **사용자 수동** – 테스트 드라이브 사용자가 앱을 사용 하는 방법을 이해 하는 데 도움이 되는 PDF 사용자 설명서입니다 (필수).
    - **시험 사용 데모 비디오**: 앱을 소개하는 비디오입니다(선택 사항).

---

## <a name="dynamics-365-operations-apps"></a>Dynamics 365 운영 앱

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)에 로그인합니다.
2. 위의 링크에 액세스할 수 없는 경우 애플리케이션을 게시하려면 [여기](https://appsource.microsoft.com/partners/list-an-app)에서 요청을 제출해야 합니다. 요청을 검토한 후 게시 프로세스를 시작할 수 있는 권한이 부여됩니다.
3. 기존 **dynamics 365 작업 앱** 을 찾거나 새 **Dynamics 365 operations apps** 제품을 만듭니다.
4. **제품 설치** 페이지에서 **테스트 드라이브 사용** 확인란을 선택 하 고 **테스트 드라이브의 종류** (아래 글머리 기호 참조)를 선택한 다음 **초안 저장** 을 선택 합니다.

    [![‘시험 사용 지원’ 확인란 선택](media/test-drive/enable-test-drive-check-box-operations-workspaces.png)](media/test-drive/enable-test-drive-check-box-operations-workspaces.png#lightbox)

    - **테스트 드라이브의 유형** – **Dynamics 365 작업 앱** 옵션을 선택 합니다. 이는 시험 사용 사용자 프로비저닝 및 프로비저닝 해제를 수행하는 서비스를 Microsoft에서 호스팅하고 유지 관리함을 의미합니다.

5. [이러한 지침](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)에 따라 테넌트의 시험 사용 사용자를 프로비저닝/프로비저닝 해제할 수 있는 권한을 Microsoft AppSource에 부여합니다. 이 단계에서는 아래에 나와 있는 **Azure AD 앱 ID** 및 **Azure AD 앱 키** 값을 생성합니다.
6. **Test drive**  >   **기술 구성** 페이지에서 이러한 필드를 완료 합니다.

    [![Marketplace 기술 구성 페이지를 보여 줍니다. ](./media/test-drive/technical-config-details-operations-workspaces.png) ](./media/test-drive/technical-config-details-operations-workspaces.png#lightbox)

    - **최대 동시 시험 사용 수** – 동시에 활성 시험 사용을 실행할 수 있는 동시 사용자 수입니다. 각 사용자는 시험 사용이 활성화된 상태에서 Dynamics 라이선스를 사용하므로 시험 사용 사용자가 사용할 수 있는 Dynamics 라이선스가 충분한지 확인합니다. 권장 수는 3 ~ 5입니다.
    - **시험 사용 기간** - 사용자의 시험 사용이 활성 상태로 유지되는 시간입니다. 시간이 만료되면 사용자는 테넌트에서 프로비저닝 해제됩니다. 앱의 복잡성에 따라 2 ~ 24시간을 권장합니다. 시간이 만료된 후 시험 사용에 다시 액세스하려는 사용자는 언제든지 시험 사용을 또다시 요청할 수 있습니다.
    - **인스턴스 URL** – 시험 사용을 시작할 때 시험 사용 사용자에게 전송되는 URL입니다. 일반적으로는 앱과 샘플 데이터가 설치된 Dynamics 365 인스턴스의 URL입니다. 값 예: `https://testdrive.crm.dynamics.com`.
    - **Azure Active Directory 테넌트 ID** - Dynamics 365 인스턴스용 Azure 테넌트의 ID입니다. 이 값을 검색하려면 Azure Portal에 로그인하고 **Azure Active Directory** > **속성** 으로 이동하고 디렉터리 ID를 복사합니다. 값 예: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory 테넌트 이름** - Dynamics 365 인스턴스용 Azure 테넌트의 이름입니다. `<tenantname>.onmicrosoft.com` 형식을 사용합니다. 값 예: `testdrive.onmicrosoft.com`.
    - **Azure Active Directory 애플리케이션 ID** – 5단계에서 만든 Azure AD(Active Directory) 앱의 ID입니다. 값 예: `53852862-a2ae-4e43-9461-faa49650a096`.
    - **Azure Active Directory 애플리케이션 클라이언트 암호** – 5단계에서 만든 Azure AD 앱에 대한 비밀입니다. 값 예: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`.
    - **평가판 법인** – 평가판 사용자를 할당할 법인을 입력합니다. [법인 만들기 또는 수정](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity)에서 새 법인을 만들 수 있습니다.
    - **역할 이름** – 시험 사용을 위해 만든 사용자 지정 Dynamics 365 보안 역할의 AOT(애플리케이션 개체 트리) 이름입니다. 시험 사용 중에 사용자에게 이 역할이 할당됩니다.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="보안 구성 페이지":::

7. **Drive marketplace 목록 테스트** 페이지의 필드를 완료 합니다.

    [![Marketplace 목록 세부 정보 페이지](media/test-drive/marketplace-listing-details-ops-workspaces.png)](media/test-drive/marketplace-listing-details-workspaces.png#lightbox)

    - **설명** – 시험 사용의 개요입니다. 이 텍스트는 시험 사용을 프로비저닝하는 동안 사용자에게 표시됩니다. 서식 있는 콘텐츠를 제공 하려는 경우이 필드는 HTML을 지원 합니다 (필수).
    - **사용자 수동** – 테스트 드라이브 사용자가 앱을 사용 하는 방법을 이해 하는 데 도움이 되는 PDF 사용자 설명서입니다 (필수).
    - **시험 사용 데모 비디오**: 앱을 소개하는 비디오입니다(선택 사항).

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

1. [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2165507)에 로그인합니다.
2. 위의 링크에 액세스할 수 없는 경우 애플리케이션을 게시하려면 [여기](https://appsource.microsoft.com/partners/list-an-app)에서 요청을 제출해야 합니다. 요청을 검토한 후 게시 프로세스를 시작할 수 있는 권한이 부여됩니다.
3. 기존 **dynamics 365 작업 앱** 을 찾거나 새 **Dynamics 365 operations apps** 제품을 만듭니다.
4. **시험 사용 지원** 확인란을 선택하고 **시험 사용 유형**(아래 글머리 기호 참조)을 선택한 다음 **초안 저장** 을 선택합니다.

    [![‘시험 사용 지원’ 확인란 선택](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **테스트 드라이브의 유형** – **Dynamics 365 작업 앱** 옵션을 선택 합니다. 이는 시험 사용 사용자 프로비저닝 및 프로비저닝 해제를 수행하는 서비스를 Microsoft에서 호스팅하고 유지 관리함을 의미합니다.

5. [이러한 지침](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)에 따라 테넌트의 시험 사용 사용자를 프로비저닝/프로비저닝 해제할 수 있는 권한을 Microsoft AppSource에 부여합니다. 이 단계에서는 아래에 나와 있는 **Azure AD 앱 ID** 및 **Azure AD 앱 키** 값을 생성합니다.
6. **시험 사용 기술 구성** 페이지에서 다음 필드를 완료합니다.

    [![Marketplace 기술 구성 페이지](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **최대 동시 시험 사용 수** – 동시에 활성 시험 사용을 실행할 수 있는 동시 사용자 수입니다. 각 사용자는 시험 사용이 활성화된 상태에서 Dynamics 라이선스를 사용하므로 시험 사용 사용자가 사용할 수 있는 Dynamics 라이선스가 충분한지 확인합니다. 권장 수는 3 ~ 5입니다.
    - **시험 사용 기간** - 사용자의 시험 사용이 활성 상태로 유지되는 시간입니다. 시간이 만료되면 사용자는 테넌트에서 프로비저닝 해제됩니다. 앱의 복잡성에 따라 2 ~ 24시간을 권장합니다. 시간이 만료된 후 시험 사용에 다시 액세스하려는 사용자는 언제든지 시험 사용을 또다시 요청할 수 있습니다.
    - **인스턴스 URL** – 시험 사용을 시작할 때 시험 사용 사용자에게 전송되는 URL입니다. 일반적으로는 앱과 샘플 데이터가 설치된 Dynamics 365 인스턴스의 URL입니다. 값 예: `https://testdrive.crm.dynamics.com`.
    - **Azure Active Directory 테넌트 ID** - Dynamics 365 인스턴스용 Azure 테넌트의 ID입니다. 이 값을 검색하려면 Azure Portal에 로그인하고 **Azure Active Directory** > **속성** 으로 이동하고 디렉터리 ID를 복사합니다. 값 예: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory 테넌트 이름** - Dynamics 365 인스턴스용 Azure 테넌트의 이름입니다. `<tenantname>.onmicrosoft.com` 형식을 사용합니다. 값 예: `testdrive.onmicrosoft.com`.
    - **Azure Active Directory 애플리케이션 ID** – 5단계에서 만든 Azure AD(Active Directory) 앱의 ID입니다. 값 예: `53852862-a2ae-4e43-9461-faa49650a096`.
    - **Azure Active Directory 애플리케이션 클라이언트 암호** – 5단계에서 만든 Azure AD 앱에 대한 비밀입니다. 값 예: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`.
    - **평가판 법인** – 평가판 사용자를 할당할 법인을 입력합니다. [법인 만들기 또는 수정](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity)에서 새 법인을 만들 수 있습니다.
    - **역할 이름** – 시험 사용을 위해 만든 사용자 지정 Dynamics 365 보안 역할의 AOT(애플리케이션 개체 트리) 이름입니다. 시험 사용 중에 사용자에게 이 역할이 할당됩니다.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="보안 구성 페이지":::

7. 마켓플레이스 목록 세부 정보를 제공합니다. 추가 필수 필드를 보려면 **언어** 를 선택합니다.

    [![Marketplace 목록 세부 정보 페이지](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **설명** – 시험 사용의 개요입니다. 이 텍스트는 시험 사용을 프로비저닝하는 동안 사용자에게 표시됩니다. 서식 있는 콘텐츠를 제공하려는 경우 이 필드는 HTML을 지원합니다.
    - **사용자 매뉴얼** – 시험 사용 사용자가 앱 사용 방법을 이해하는 데 도움이 되는 PDF 사용자 매뉴얼입니다.
    - **시험 사용 데모 비디오**: 앱을 소개하는 비디오입니다(선택 사항).

---

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->
