---
title: '문제 해결 Azure Digital Twins Explorer: 인증 오류'
titleSuffix: Azure Digital Twins
description: Azure Digital Twins Explorer 인증 오류를 진단하고 해결하는 방법을 알아봅니다.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 10/7/2021
ms.openlocfilehash: f03c85f912271dbd5d1cb7904f159d89f4a09ba6
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132629"
---
# <a name="troubleshooting-azure-digital-twins-explorer-authentication-error"></a>문제 해결 Azure Digital Twins Explorer: 인증 오류

이 문서에서는 로컬 컴퓨터에서 [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 샘플을 실행하는 동안 '인증 실패' 오류를 수신하는 원인 및 해결 단계를 설명합니다. 

## <a name="symptoms"></a>증상

Azure Digital Twins Explorer 애플리케이션을 설정하고 실행할 때 앱에서 인증을 시도하면 다음과 같은 오류 메시지가 표시됩니다.

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Azure Digital Twins Explorer의 인증 실패 오류 메시지 스크린샷.":::

## <a name="causes"></a>원인

### <a name="cause-1"></a>원인 #1

이 오류는 Azure 계정에 Azure Digital Twins 인스턴스에 대해 설정된 필수 Azure RBAC(역할 기반 액세스 제어) 권한이 없는 경우에 발생할 수 있습니다. 인스턴스의 데이터에 액세스하려면 각각 읽거나 관리하려는 인스턴스에 대한 **Azure Digital Twins 데이터 판독기** 또는 **Azure Digital Twins 데이터 소유자** 역할이 있어야 합니다. 

Azure Digital Twins의 보안 및 역할에 대한 자세한 내용은 [Azure Digital twins 솔루션의 보안](concepts-security.md)을 참조하세요.

## <a name="solutions"></a>솔루션

### <a name="solution-1"></a>해결 방법 #1

데이터를 읽으려고 하는 경우 Azure 사용자가 Azure Digital twins 인스턴스에서 **Azure Digital Twins 데이터 판독기** 역할을 가지고 있는지 확인하고, 데이터를 관리하려는 경우 인스턴스에서 **Azure Digital Twins 데이터 소유자** 역할이 맞는지 확인합니다.

이 역할은...
* 미리 보기 중에 이 역할의 이전 이름인 *Azure Digital Twins 소유자(미리 보기)* 와는 다릅니다(역할은 동일 하지만 이름이 변경됨).
* 전체 Azure 구독에서의 *소유자* 역할과는 다릅니다. *Azure Digital Twins 데이터 소유자* 는 Azure Digital Twins 내에서의 역할이며, 이 개별 Azure Digital Twins 인스턴스로 범위가 지정됩니다.
* Azure Digital Twins의 *소유자* 역할과는 다릅니다. 이것들은 서로 다른 두 가지 Azure Digital Twins 관리 역할이며, *Azure Digital Twins 데이터 소유자* 는 관리에 사용해야 하는 역할입니다.

 이 역할이 없으면 이를 설정하여 문제를 해결합니다.

#### <a name="check-current-setup"></a>현재 설정 확인

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>문제 해결 

이 역할 할당이 없는 경우 **Azure 구독** 에 소유자 역할이 있는 사용자는 다음 명령을 실행하여 Azure 사용자에게 **Azure Digital Twins 인스턴스** 에서 적절한 역할을 제공해야 합니다. 

구독에 대한 소유자인 경우 이 명령을 직접 실행할 수 있습니다. 그렇지 않은 경우 소유자에게 문의하여 대신 이 명령을 실행하도록 합니다. 역할 이름은 편집 액세스를 위한 **Azure Digital Twins 데이터 소유자** 이거나 읽기 액세스를 위한 **Azure Digital Twins 데이터 판독기** 입니다.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

이 역할 요구 사항과 할당 프로세스에 대한 자세한 내용은 *방법: 인스턴스 및 인증 설정(CLI 또는 포털)* 의 [사용자 액세스 권한 설정 섹션](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)을 참조하세요.

## <a name="next-steps"></a>다음 단계

새 Azure Digital Twins 인스턴스를 만들고 인증하는 방법에 대한 설치 단계를 읽어보세요.
* [인스턴스 및 인증 설정(CLI)](how-to-set-up-instance-cli.md)

Azure Digital Twins의 보안 및 사용 권한에 대해 자세히 알아보세요.
* [Azure Digital Twins 솔루션에 대한 보안](concepts-security.md)
