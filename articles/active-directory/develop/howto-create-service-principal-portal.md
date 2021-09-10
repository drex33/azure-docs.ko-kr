---
title: 포털에서 Azure AD 앱 및 서비스 주체 만들기
titleSuffix: Microsoft identity platform
description: Azure Resource Manager에서 역할 기반 액세스 제어를 사용하여 리소스에 대한 액세스를 관리할 수 있도록 새 Azure Active Directory 앱 및 서비스 주체를 만듭니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.date: 06/16/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, subject-rbac-steps
ms.openlocfilehash: b4589f451894e328a27b67ac19be4ea91374bee5
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112579957"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기

이 문서에서는 역할 기반 액세스 제어와 함께 사용할 수 있는 새 Azure Active Directory(Azure AD) 애플리케이션 및 서비스 주체를 만드는 방법을 보여 줍니다. 리소스에 액세스하거나 리소스를 수정해야 하는 애플리케이션, 호스팅된 서비스 또는 자동화 도구가 있다면 앱의 ID를 만들면 됩니다. 이 ID를 서비스 주체라고 합니다. 리소스에 대한 액세스는 서비스 주체에 할당된 역할로 제한되므로 액세스할 수 있는 리소스와 해당 수준을 제어할 수 있습니다. 보안상의 이유로 사용자 ID를 통해 로그인할 수 있게 하는 대신, 항상 자동화된 도구에서 서비스 주체를 사용하는 것이 좋습니다.

이 문서에서는 Azure Portal에서 포털을 사용하여 서비스 주체를 만드는 방법을 보여 줍니다. 여기서는 애플리케이션을 하나의 조직 내에서만 실행하게 되는 단일 테넌트 애플리케이션을 중점적으로 다룹니다. 일반적으로 단일 조직 내에서 실행되는 LOB(기간 업무) 애플리케이션에 대해 단일 테넌트 애플리케이션을 사용하게 됩니다.  [Azure PowerShell을 사용하여 서비스 주체를 만들 수도 있습니다](howto-authenticate-service-principal-powershell.md).

> [!IMPORTANT]
> 서비스 주체를 만드는 대신 애플리케이션 ID에 Azure 리소스에 대한 관리 ID를 사용하는 것이 좋습니다. 코드가 관리 ID를 지원하는 서비스에서 실행되고 Azure AD 인증을 지원하는 리소스에 액세스하는 경우에는 관리 ID를 사용하는 방법이 더 좋습니다. Azure 리소스에 대한 관리 ID 및 이것이 지원되는 서비스를 알아보려면 [Azure 리소스에 대한 관리 ID란?](../managed-identities-azure-resources/overview.md)을 참조하세요.

## <a name="app-registration-app-objects-and-service-principals"></a>앱 등록, 앱 개체 및 서비스 주체

Azure Portal을 사용하여 서비스 주체를 바로 만들 수는 없습니다.  Azure Portal를 통해 애플리케이션을 등록하면 애플리케이션 개체와 서비스 주체가 홈 디렉터리 또는 테넌트에서 자동으로 생성됩니다.  Azure Active Directory의 애플리케이션 등록, 애플리케이션 개체 및 서비스 주체 간 관계에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

## <a name="permissions-required-for-registering-an-app"></a>앱을 등록하는 데 필요한 권한

Azure AD 테넌트에 애플리케이션을 등록하고 Azure 구독의 역할에 애플리케이션을 할당할 수 있는 충분한 권한이 있어야 합니다.

### <a name="check-azure-ad-permissions"></a>Azure AD 권한 확인

1. **Azure Active Directory** 를 선택합니다.
1. 역할을 기록해 둡니다. **사용자** 역할이 있는 경우 비관리자가 애플리케이션을 등록할 수 있는지 확인해야 합니다.

   ![자신의 역할을 찾습니다. 사용자인 경우 관리자가 아닌 사람이 앱을 등록할 수 있는지 확인하세요.](./media/howto-create-service-principal-portal/view-user-info.png)

1. 왼쪽 창에서 **사용자 설정** 을 선택합니다.
1. **앱 등록** 설정을 확인합니다. 이 값은 관리자만 설정할 수 있습니다. **예** 로 설정하면 Azure AD 테넌트의 모든 사용자가 앱을 등록할 수 있습니다.

앱 등록 설정이 **아니요** 로 설정되어 있으면 관리자 역할이 있는 사용자만 이러한 유형의 애플리케이션을 등록할 수 있습니다. Azure AD에서 사용 가능한 관리자 역할 및 각 역할에 제공되는 특정 권한에 대한 자세한 내용은 [Azure AD 기본 제공 역할](../roles/permissions-reference.md#all-roles)을 참조하세요. 계정이 사용자 역할에 할당되어 있으나 앱 등록 설정이 관리자로 제한되어 있다면, 관리자에게 앱 등록과 관련된 모든 요소를 만들고 관리할 수 있는 관리자 역할 중 하나를 할당해 달라고 요청하거나 사용자가 앱을 등록할 수 있게 해달라고 요청하세요.

### <a name="check-azure-subscription-permissions"></a>Azure 구독 권한 확인

Azure 구독에서 사용자 계정은 AD 앱을 역할에 할당할 수 있는 `Microsoft.Authorization/*/Write` 액세스 권한이 있어야 합니다. 이 작업에 대한 권한은 [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할 또는 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할을 통해 부여됩니다. 계정에 **기여자** 역할이 할당되었다면 적절한 권한을 가질 수 없습니다. 서비스 주체에 역할을 할당하려고 하면 오류가 발생합니다.

Azure 구독 권한을 확인하려면

1. **구독** 을 검색하고 선택하거나 **홈** 페이지에서 **구독** 을 선택합니다.

   ![검색](./media/howto-create-service-principal-portal/select-subscription.png)

1. 드롭다운 목록에서 서비스 주체를 만들 구독을 선택합니다.

   ![할당을 위한 구독 선택](./media/howto-create-service-principal-portal/select-one-subscription.png)

   찾고 있는 구독이 표시되지 않으면 **전역 구독 필터** 를 선택합니다. 포털에 대해 원하는 구독이 선택되었는지 확인합니다.

1. **내 사용 권한** 을 선택합니다. 그런 다음, **이 구독에 대한 전체 액세스 세부 정보를 보려면 여기를 클릭합니다.** 를 선택합니다.

   ![서비스 주체를 만들 구독을 선택합니다.](./media/howto-create-service-principal-portal/view-details.png)

1. **역할 할당** 에서 **보기** 를 선택하여 할당된 역할을 확인하고, AD 앱에 역할을 할당할 수 있는 적절한 권한이 있는지 확인합니다. 이러한 권한이 없으면 구독 관리자에게 사용자 액세스 관리자 역할에 사용자를 추가할 것을 요청합니다. 다음 이미지에서는 사용자에게 소유자 역할이 할당됩니다. 따라서 사용자는 적절한 권한이 있습니다.

   ![이 예제에서는 사용자에게 소유자 역할을 할당하는 작업을 보여 줍니다.](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="register-an-application-with-azure-ad-and-create-a-service-principal"></a>Azure AD를 이용한 애플리케이션 등록 및 서비스 주체 만들기

이제 ID를 만들어 보겠습니다. 문제가 발생하면 [필요한 권한](#permissions-required-for-registering-an-app)을 확인하여 계정이 ID를 만들 수 있는지 확인합니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>을 통해 Azure 계정에 로그인합니다.
1. **Azure Active Directory** 를 선택합니다.
1. **앱 등록** 을 선택합니다.
1. **새 등록** 을 선택합니다.
1. 애플리케이션의 이름을 지정합니다. 애플리케이션을 사용할 수 있는 사용자를 결정하는, 지원되는 계정 유형을 선택합니다. **URI 리디렉션** 에서, 생성할 애플리케이션 형식에 **웹** 을 선택합니다. 액세스 토큰이 전송되는 URI를 입력합니다. [네이티브 애플리케이션](../app-proxy/application-proxy-configure-native-client-application.md)의 자격 증명을 만들 수 없습니다. 자동화된 애플리케이션에 해당 형식을 사용할 수 없습니다. 값을 설정한 후 **등록** 을 선택합니다.

   ![애플리케이션의 이름을 입력합니다.](./media/howto-create-service-principal-portal/create-app.png)

Azure AD 애플리케이션 및 서비스 주체를 만들었습니다.

> [!NOTE]
> Azure AD에 이름이 같은 애플리케이션을 여러 개 등록할 수 있지만, 애플리케이션(클라이언트) ID는 달라야 합니다.

## <a name="assign-a-role-to-the-application"></a>애플리케이션에 역할 할당

구독의 리소스에 액세스하려면 역할을 애플리케이션에 할당해야 합니다. 애플리케이션에 적합한 권한을 제공하는 역할을 결정합니다. 사용 가능한 역할에 대한 자세한 내용을 알아보려면 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어 애플리케이션에 리소스 그룹에 대한 읽기 권한자 역할을 추가하면 애플리케이션이 리소스 그룹과 그 안에 포함된 모든 리소스를 읽을 수 있습니다.

1. Azure Portal에서 애플리케이션을 할당할 범위 수준을 선택합니다. 예를 들어 역할을 구독 범위에서 할당하려면 **구독** 을 검색하고 선택하거나, **홈** 페이지에서 **구독** 을 선택합니다.

   ![예를 들어 구독 범위에서 역할을 할당합니다.](./media/howto-create-service-principal-portal/select-subscription.png)

1. 애플리케이션을 할당할 특정 구독을 선택합니다.

   ![할당을 위한 구독 선택](./media/howto-create-service-principal-portal/select-one-subscription.png)

   찾고 있는 구독이 표시되지 않으면 **전역 구독 필터** 를 선택합니다. 포털에 대해 원하는 구독이 선택되었는지 확인합니다.

1. **액세스 제어(IAM)** 를 선택합니다.
1. **추가** > **역할 할당 추가** 를 선택하여 **역할 할당 추가** 페이지를 엽니다.
1. 애플리케이션에 할당할 역할을 선택합니다. 예를 들어 애플리케이션이 **다시 부팅**, **시작** 및 **중지** 인스턴스 같은 작업을 실행하게 하려면 **기여자** 역할을 선택합니다.  [사용 가능한 역할](../../role-based-access-control/built-in-roles.md)에 대해 자세히 알아보세요. 기본적으로 Azure AD 애플리케이션은 사용 가능한 옵션에 표시되지 않습니다. 애플리케이션을 찾으려면 이름을 검색하여 선택합니다. 

    구독 범위에서 애플리케이션에 기여자 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

서비스 주체가 설정되었습니다. 서비스 주체 사용을 시작하여 스크립트나 앱을 실행할 수 있습니다. 서비스 주체를 관리하려면(권한, 사용자가 동의한 권한, 사용자가 동의한 항목 확인, 권한 검토, 로그인 정보 확인 등) **엔터프라이즈 애플리케이션** 으로 이동하세요.

다음 섹션에서는 프로그래밍 방식으로 로그인할 때 필요한 값을 가져오는 방법을 보여 줍니다.

## <a name="get-tenant-and-app-id-values-for-signing-in"></a>로그인에 사용할 테넌트 및 앱 ID 값 가져오기

프로그래밍 방식으로 로그인하는 경우에는 인증 요청과 함께 테넌트 ID와 애플리케이션 ID를 전달하세요.  인증서 또는 인증 키도 필요합니다(다음 섹션에서 자세하게 설명합니다). 이러한 값을 가져오려면 다음 단계를 사용합니다.

1. **Azure Active Directory** 를 선택합니다.
1. Azure AD의 **앱 등록** 에서 애플리케이션을 선택합니다.
1. 디렉터리(테넌트 ID)를 복사하여 애플리케이션 코드에 저장합니다.

    ![디렉터리(테넌트 ID)를 복사하여 앱 코드에 저장](./media/howto-create-service-principal-portal/copy-tenant-id.png)

    디렉터리(테넌트) ID는 기본 디렉터리 개요 페이지에서도 확인할 수 있습니다.

1. **애플리케이션 ID** 를 복사하고 애플리케이션 코드에 저장합니다.

   ![애플리케이션(클라이언트) ID 복사](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="authentication-two-options"></a>인증: 두 가지 옵션

서비스 주체에 사용할 수 있는 인증 유형에는 암호 기반 인증(애플리케이션 비밀) 및 인증서 기반 인증의 두 가지 유형이 있습니다. 인증서 사용을 권장하지만 애플리케이션 암호를 만드는 방법도 가능합니다.

### <a name="option-1-upload-a-certificate"></a>옵션 1: 인증서 업로드

인증서가 이미 있다면 이 인증서를 사용할 수 있습니다.  원한다면 테스트 용도로만 사용해야 하는 자체 서명된 인증서를 만들어도 됩니다. 자체 서명된 인증서를 만들려면 PowerShell을 열고 다음 매개 변수를 사용하여 [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate)를 실행하여 컴퓨터의 사용자 인증서 저장소에 인증서를 만듭니다.

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Windows 제어판에서 액세스할 수 있는 [사용자 인증서 관리](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC 스냅인을 사용하여 이 인증서를 파일로 내보냅니다.

1. **시작** 메뉴에서 **실행** 을 선택한 다음 **certmgr.msc** 를 입력합니다.

   현재 사용자에 대한 인증서 관리자 도구가 표시됩니다.

1. 인증서를 보려면 왼쪽 창의 **인증서 - 현재 사용자** 에서 **개인** 디렉터리를 확장합니다.
1. 생성한 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업->내보내기** 를 선택합니다.
1. 인증서 내보내기 마법사의 지시를 따릅니다.  개인 키를 내보내지 말고 .CER 파일로 내보냅니다.

인증서를 업로드하는 방법:

1. **Azure Active Directory** 를 선택합니다.
1. Azure AD의 **앱 등록** 에서 애플리케이션을 선택합니다.
1. **인증서 및 비밀** 을 선택합니다.
1. **인증서 업로드** 를 선택하고 인증서(기존 인증서 또는 내보낸 자체 서명된 인증서)를 선택합니다.

    ![인증서 업로드를 선택하고 추가할 인증서를 선택합니다.](./media/howto-create-service-principal-portal/upload-cert.png)

1. **추가** 를 선택합니다.

애플리케이션 등록 포털에서 애플리케이션에 인증서를 등록한 후, 클라이언트 애플리케이션 코드가 인증서를 사용하도록 설정합니다.

### <a name="option-2-create-a-new-application-secret"></a>옵션 2: 새 애플리케이션 비밀 만들기

인증서를 사용하지 않도록 선택했다면 새 애플리케이션 암호를 만들 수 있습니다.

1. **Azure Active Directory** 를 선택합니다.
1. Azure AD의 **앱 등록** 에서 애플리케이션을 선택합니다.
1. **인증서 및 비밀** 을 선택합니다.
1. **클라이언트 비밀 -> 새 클라이언트 비밀** 을 차례로 선택합니다.
1. 비밀에 대한 설명과 기간을 제공합니다. 완료되면 **추가** 를 선택합니다.

   클라이언트 비밀이 저장되면 클라이언트 비밀의 값이 표시됩니다. 나중에 키를 검색할 수 없으므로 이 값을 복사해둡니다. 애플리케이션으로 로그인할 때 애플리케이션 ID와 함께 키 값을 제공해야 합니다. 애플리케이션에서 검색할 수 있는 위치에 키 값을 저장합니다.

   ![나중에 이 키를 검색할 수 없으므로 비밀 값 복사](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>리소스에 대한 액세스 정책 구성
애플리케이션에서 액세스해야 하는 리소스에 대한 추가 권한을 구성해야 할 수도 있음을 명심해야 합니다. 예를 들어 키, 비밀 또는 인증서에 대한 액세스 권한을 애플리케이션에 부여하려면 [키 자격 증명 모음의 액세스 정책을 업데이트](../../key-vault/general/security-features.md#privileged-access)해야 합니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에서 키 자격 증명 모음으로 이동하고 **액세스 정책** 을 선택합니다.
1. **액세스 정책 추가** 를 선택하고 애플리케이션에 부여할 키, 비밀과 인증서 권한을 선택합니다.  앞에서 만든 서비스 주체를 선택합니다.
1. **추가** 를 선택하여 액세스 정책을 추가하고 **저장** 을 선택하여 변경 내용을 커밋합니다.
    ![액세스 정책 추가](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="next-steps"></a>다음 단계
* [Azure PowerShell을 사용하여 서비스 주체를 만드는](howto-authenticate-service-principal-powershell.md) 방법을 알아보세요.
* 보안 정책 지정에 대해 자세히 알아보려면 [Azure 역할 기반 액세스 제어(Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.
* 권한이 부여되거나 사용자에 대해 거부될 수 있는 작업 목록은 [Azure Resource Manager 리소스 공급자 작업](../../role-based-access-control/resource-provider-operations.md)을 참조하세요.
* **Microsoft Graph** 를 사용한 앱 등록 작업에 대한 자세한 내용은 [애플리케이션](/graph/api/resources/application) API 참조를 확인하세요.
