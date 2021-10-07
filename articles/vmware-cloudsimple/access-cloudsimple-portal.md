---
title: Azure VMware Solution by CloudSimple에 액세스 - 포털
description: Azure Portal에서 VMware Solution by CloudSimple 포털에 액세스하는 방법 설명
author: suzizuber
ms.author: v-szuber
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 669141627d4665e3c44c44463232ca2d4883fc99
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618350"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Azure Portal에서 VMware Solution by CloudSimple 포털에 액세스합니다.

Single Sign-On은 CloudSimple 포털에 대한 액세스를 위해 지원됩니다. Azure Portal에 로그인하면 다시 로그인하지 않고 CloudSimple 포털에 액세스할 수 있습니다. CloudSimple 포털에 처음 액세스할 때 [CloudSimple 서비스 권한 부여](#consent-to-cloudsimple-service-authorization-application) 애플리케이션에 권한을 부여할 것인지 묻는 메시지가 표시됩니다.  권한 부여는 일회성 작업입니다.

## <a name="before-you-begin"></a>시작하기 전에

기본 제공 **소유자** 및 **기여자** 역할을 가진 사용자는 CloudSimple 포털에 액세스할 수 있습니다.  CloudSimple 서비스가 배포되는 리소스 그룹에 역할을 구성해야 합니다.  CloudSimple 서비스 개체에도 역할을 구성할 수 있습니다.  역할을 확인하는 방법에 대한 자세한 내용은 [역할 할당 보기](../role-based-access-control/check-access.md) 문서를 참조하세요. 기본 제공 **소유자** 및 **기여자** 역할을 가진 사용자만 CloudSimple 포털에 액세스할 수 있습니다.  구독에서 역할을 구성해야 합니다.  역할을 확인하는 방법에 대한 자세한 내용은 [역할 할당 보기](../role-based-access-control/check-access.md) 문서를 참조하세요.

사용자 지정 역할을 사용하는 경우 해당 역할에 다음 작업 중 하나가 ```Actions```에 있어야 합니다.  사용자 지정 역할에 대한 자세한 내용은 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.  작업이 ```NotActions```의 일부인 경우 사용자는 CloudSimple 포털에 액세스할 수 없습니다.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

1. **모든 서비스** 를 선택합니다.

2. **CloudSimple 서비스** 를 검색합니다.

3. 프라이빗 클라우드를 만들려는 CloudSimple 서비스를 선택합니다.

4. **개요** 페이지에서 **Cloudsimple 포털로 이동** 을 클릭합니다.  Azure Portal에서 CloudSimple 포털에 처음 액세스하는 경우 [CloudSimple 서비스 권한 부여](#consent-to-cloudsimple-service-authorization-application) 애플리케이션에 권한을 부여할 것인지 묻는 메시지가 표시됩니다. 

    ![CloudSimple 포털 시작](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Azure Portal에서 직접 프라이빗 클라우드 작업(예: 프라이빗 클라우드 만들기 또는 확장)을 선택하는 경우 CloudSimple 포털이 표시된 페이지에 열립니다.

CloudSimple 포털의 측면 메뉴에서 **홈** 을 선택하여 프라이빗 클라우드에 대한 요약 정보를 표시합니다. 주의가 필요한 경고 및 작업과 함께 프라이빗 클라우드의 리소스 및 용량이 표시됩니다. 일반 작업의 경우 페이지 맨 위에 있는 명명된 아이콘을 클릭합니다.

![홈 페이지](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>CloudSimple 서비스 권한 부여 애플리케이션에 동의

Azure Portal에서 처음으로 CloudSimple 포털을 시작하려면 CloudSimple 서비스 권한 부여 애플리케이션에 대한 동의가 필요합니다.  **수락** 을 선택하여 요청된 권한을 부여하고 CloudSimple 포털에 액세스합니다.

![CloudSimple 서비스 권한 부여에 대한 동의 - 관리자](media/cloudsimple-azure-consent.png)

전역 관리자 권한이 있는 경우 조직에 대해 동의할 수 있습니다.  **조직을 대신하여 동의** 를 선택합니다.

![CloudSimple 서비스 권한 부여에 동의 - 전역 관리자](media/cloudsimple-azure-consent-global-admin.png)

사용자의 권한으로 CloudSimple 포털에 액세스할 수 없는 경우 테넌트 전역 관리자에게 문의하여 필요한 권한을 부여합니다.  전역 관리자가 조직을 대신하여 동의할 수 있습니다.

![CloudSimple 서비스 권한 부여에 동의 - 관리자 필요](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드 생성](./create-private-cloud.md) 방법 알아보기
* [프라이빗 클라우드 환경 구성](quickstart-create-private-cloud.md) 방법 알아보기
