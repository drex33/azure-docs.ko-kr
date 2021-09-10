---
title: Azure AD의 앱 및 서비스 사용자
titleSuffix: Microsoft identity platform
description: Azure Active Directory의 애플리케이션 및 서비스 주체 개체 간의 관계에 대해 알아봅니다.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/23/2021
ms.author: ryanwi
ms.custom: contperf-fy21q4-portal, aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: b8748bc5f628db654f67d68176e5cd10a1f02ec3
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539037"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Azure Active Directory의 애플리케이션 및 서비스 주체 개체

이 문서에서는 Azure Active Directory(Azure AD) 애플리케이션 등록, 애플리케이션 개체 및 서비스 사용자에 대해 설명합니다. 즉, 애플리케이션의 용도, 사용 방법 및 서로 관련되는 방식을 설명합니다. 다중 테넌트 예제 시나리오는 애플리케이션의 애플리케이션 개체와 해당 서비스 사용자 개체 간의 관계를 설명하기 위해 제공됩니다.

## <a name="application-registration"></a>애플리케이션 등록

ID 및 액세스 관리 기능을 Azure AD에 위임할 수 있도록 허용하기 위해 Azure AD 테넌트를 사용하여 등록해야 합니다. Azure AD에 애플리케이션을 등록할 때 Azure AD와 통합하는 데 사용할 수 있는 애플리케이션의 ID 구성을 생성합니다. Azure Portal에 앱을 등록하는 경우, 단일 테넌트인지(테넌트에서만 액세스할 수 있음) 또는 다중 테넌트인지(다른 테넌트에서 액세스할 수 있음) 선택하고, 필요에 따라 액세스 토큰이 전송되는 리디렉션 URI를 설정할 수 있습니다. 앱 등록에 대한 단계별 지침은 [앱 등록 빠른 시작](quickstart-register-app.md)을 참조하세요.

앱 등록을 완료하면 홈 테넌트 또는 디렉터리 내에 상주하는 앱의 전역적으로 고유한 인스턴스(애플리케이션 개체)가 확보됩니다. 앱의 전역적으로 고유한 ID(앱 또는 클라이언트 ID)도 있습니다. 그런 다음, 포털에서 비밀 또는 인증서 및 범위를 추가하여 앱이 작동하도록 하고, 로그인 대화 상자에서 앱의 브랜딩을 사용자 지정하는 등의 작업을 수행할 수 있습니다.

포털에 애플리케이션을 등록하는 경우 애플리케이션 개체 및 서비스 사용자 개체가 홈 테넌트에 자동으로 만들어집니다. Microsoft Graph API를 사용하여 애플리케이션을 등록/생성할 경우 별도의 단계를 통해 서비스 사용자 개체를 만들 수 있습니다.

## <a name="application-object"></a>애플리케이션 개체

Azure AD 애플리케이션은 애플리케이션의 "홈" 테넌트라고도 알려진, 애플리케이션이 등록된 Azure AD 테넌트에 상주하는 하나의 애플리케이션 개체에 의해서만 정의됩니다.  애플리케이션 개체는 템플릿 또는 청사진으로 사용되어 하나 이상의 서비스 사용자 개체를 만듭니다.  애플리케이션이 사용되는 모든 테넌트에 서비스 사용자가 생성됩니다. 개체 지향 프로그래밍의 클래스와 유사하게 애플리케이션 개체에는 생성된 모든 서비스 사용자(또는 애플리케이션 인스턴스)에 적용되는 일부 정적 속성이 있습니다.

애플리케이션 개체는 애플리케이션의 세 가지 측면, 즉 서비스가 애플리케이션에 액세스하기 위해 토큰을 발행하는 방법, 애플리케이션이 액세스해야 할 수도 있는 리소스 및 애플리케이션이 취할 수 있는 작업을 설명합니다.

[Azure Portal][AZURE-Portal]의 **앱 등록** 블레이드는 홈 테넌트에서 애플리케이션 개체를 나열하고 관리하는 데 사용됩니다.

![앱 등록 블레이드](./media/app-objects-and-service-principals/app-registrations-blade.png)

Microsoft Graph [애플리케이션 엔터티][MS-Graph-App-Entity]는 애플리케이션 개체의 속성에 대한 스키마를 정의합니다.

## <a name="service-principal-object"></a>서비스 주체 개체

Azure AD 테넌트에 의해 보안이 유지되는 리소스에 액세스하려면 액세스해야 하는 엔터티를 보안 주체로 나타내야 합니다. 이 요구 사항은 사용자(사용자 주체) 및 애플리케이션(서비스 사용자) 둘 다 마찬가지입니다. 보안 주체는 Azure AD 테넌트의 사용자/애플리케이션에 대한 액세스 정책 및 권한을 정의합니다. 이를 통해 로그인 동안의 사용자/애플리케이션의 인증 및 리소스 액세스 동안의 권한 부여 같은 핵심 기능이 허용됩니다.

서비스 사용자 유형은 다음 3가지입니다.

- **애플리케이션** - 이 서비스 사용자 유형은 단일 테넌트 또는 디렉터리에 있는 전역 애플리케이션 개체의 로컬 표현 또는 애플리케이션 인스턴스입니다. 이 경우 서비스 사용자는 애플리케이션 개체에서 만든 구체적인 인스턴스이고 해당 애플리케이션 개체의 특정 속성을 상속합니다. 서비스 주체는 애플리케이션이 사용되는 각 테넌트에 만들어지고 전역적으로 고유한 앱 개체를 참조합니다.  서비스 사용자 개체는 앱이 특정 테넌트에서 실제로 수행할 수 있는 작업, 앱에 액세스할 수 있는 사용자 및 앱이 액세스할 수 있는 리소스를 정의합니다.

   애플리케이션이 테넌트의 리소스에 액세스하기 위한 권한을 받으면(등록 또는 동의 시) 서비스 주체 개체가 만들어집니다. Azure 포털을 사용하여 애플리케이션을 등록할 때 서비스 사용자가 자동으로 생성됩니다. Azure PowerShell, Azure CLI, Microsoft Graph, 기타 도구를 사용하여 테넌트에서 서비스 사용자 개체를 생성할 수도 있습니다.

- **관리 ID** - 이 서비스 사용자 유형은 [관리 ID](../managed-identities-azure-resources/overview.md)를 나타내는 데 사용합니다. 관리 ID를 통해 개발자는 자격 증명을 관리할 필요가 없습니다. 관리 ID는 Azure AD 인증을 지원하는 리소스에 연결할 때 사용할 애플리케이션의 ID를 제공합니다. 관리 ID를 사용할 경우 해당 관리 ID를 나타내는 서비스 사용자가 테넌트에 만들어집니다. 관리 ID를 나타내는 서비스 사용자에게 액세스 및 사용 권한을 부여할 수는 있으나 직접 업데이트하거나 수정할 수는 없습니다.

- **레거시** - 이 서비스 사용자 유형은 레거시 앱(앱 등록 도입 전에, 또는 레거시 환경을 통해 만들어진 앱)을 나타냅니다. 레거시 서비스 사용자는 자격 증명, 서비스 사용자 이름, 회신 URL 및 기타 권한 있는 사용자가 편집할 수 있는 속성을 가질 수 있으나 연결된 앱 등록은 없습니다. 서비스 사용자는 해당 서비스 사용자를 만든 테넌트에서만 사용할 수 있습니다.

Microsoft Graph [서비스 사용자 엔터티][MS-Graph-Sp-Entity]는 서비스 사용자 개체의 속성에 대한 스키마를 정의합니다.

Azure 포털의 **엔터프라이즈 애플리케이션** 블레이드는 테넌트에서 서비스 사용자를 나열하고 관리하는 데 사용됩니다. 서비스 사용자의 권한, 사용자가 동의한 권한, 해당 동의를 수행한 사용자, 로그인 정보 등을 볼 수 있습니다.

![엔터프라이즈 앱 블레이드](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

## <a name="relationship-between-application-objects-and-service-principals"></a>애플리케이션 개체와 서비스 사용자 간의 관계

애플리케이션 개체는 모든 테넌트에서 사용하기 위한 애플리케이션에 대한 *글로벌* 표현으로 간주되고, 서비스 사용자는 특정 테넌트에서 사용하기 위한 *로컬* 표현으로 간주됩니다. 애플리케이션 개체는 해당 서비스 주체 개체 만들기에 사용하기 위해 공통의 기본 속성이 *파생* 되는 템플릿으로 제공됩니다.

애플리케이션 개체는 다음과 같은 속성을 갖습니다.

- 소프트웨어 애플리케이션과의 1:1 관계
- 해당 서비스 개체와의 일대다 관계.

애플리케이션이 사용될 각 테넌트에 서비스 주체를 만들어서 테넌트가 보호하는 리소스에 대한 로그인 및/또는 액세스를 위한 ID를 설정할 수 있어야 합니다. 단일 테넌트 애플리케이션에는 해당 홈 테넌트에 하나의 서비스 주체만 있으며 애플리케이션 등록 중에 생성하고 동의합니다. 다중 테넌트 애플리케이션에도 해당 테넌트의 사용자가 사용을 동의한 각 테넌트에 생성된 하나의 서비스 사용자가 있습니다.

### <a name="consequences-of-modifying-and-deleting-applications"></a>애플리케이션 수정 및 삭제 결과

애플리케이션 개체에 대해 이뤄진 모든 변경은 또한 애플리케이션의 홈 테넌트(그것이 등록된 테넌트)에만 있는 해당 서비스 사용자 개체에도 반영됩니다. 즉, 애플리케이션 개체를 삭제하면 홈 테넌트 서비스 사용자 개체도 삭제됩니다. 그러나 해당 애플리케이션 개체를 복원해도 해당 서비스 사용자는 복원되지 않습니다. 다중 테넌트 애플리케이션의 경우, [애플리케이션 액세스 패널](https://myapps.microsoft.com)을 통해 액세스를 제거하고 다시 액세스 권한이 부여될 때까지 애플리케이션 개체의 변경 내용은 모든 소비자 테넌트의 서비스 주체 개체에 반영되지 않습니다.

## <a name="example"></a>예

다음 다이어그램은 **HR 앱** 이라는 샘플 다중 테넌트 애플리케이션의 컨텍스트에서 애플리케이션의 애플리케이션 개체와 해당 서비스 주체 개체를 보여줍니다. 이 예제 시나리오에는 다음 세 가지 Azure AD 테넌트가 있습니다.

- **Adatum** - **HR 앱** 을 개발한 회사에서 사용하는 테넌트
- **Contoso** - **HR 앱** 의 소비자인 Contoso 조직에서 사용하는 테넌트
- **Fabrikam** - **HR 앱** 의 또 다른 소비자인 Fabrikam 조직에서 사용하는 테넌트

![앱 개체와 서비스 사용자 간의 관계](./media/app-objects-and-service-principals/application-objects-relationship.svg)

이 예제 시나리오는 다음과 같이 이루어져 있습니다.

| 단계 | 설명 |
|------|-------------|
| 1    | 애플리케이션의 홈 테넌트에서 애플리케이션 및 서비스 주체 개체를 만드는 과정입니다. |
| 2    | Contoso 관리자와 Fabrikam 관리자가 전적으로 동의한 경우 서비스 주체 개체가 회사의 Azure AD 테넌트에 생성되고 관리자가 부여한 사용 권한이 할당됩니다. 또한 사용자가 개별 사용에 대한 동의를 할 수 있게 HR 앱이 구성/설계될 수 있습니다. |
| 3    | HR 애플리케이션의 각 소비자 테넌트(예: Contoso 및 Fabrikam)에 고유한 서비스 주체 개체가 제공됩니다. 각각은 런타임에 애플리케이션 인스턴스의 사용을 나타내며, 이는 해당 관리자가 동의한 사용 권한으로 관리됩니다. |

## <a name="next-steps"></a>다음 단계

서비스 주체를 생성하는 방법을 알아봅니다.

- [Azure Portal 사용](howto-create-service-principal-portal.md)
- [Azure PowerShell 사용](howto-authenticate-service-principal-powershell.md)
- [Azure CLI 사용](/cli/azure/create-an-azure-service-principal-azure-cli)
- [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http)를 사용한 후 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 사용하여 애플리케이션과 서비스 사용자 개체를 둘 다 쿼리할 수 있습니다.

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com