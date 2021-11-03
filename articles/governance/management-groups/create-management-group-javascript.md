---
title: '빠른 시작: JavaScript를 사용하여 관리 그룹 만들기'
description: 이 빠른 시작에서는 JavaScript를 사용하여 리소스를 리소스 계층 구조로 구성하는 관리 그룹을 만듭니다.
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: devx-track-js, mode-api
ms.openlocfilehash: a05eb48e4513ef1eca7f92a50351cbc371e8dc2b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131081223"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>빠른 시작: JavaScript를 사용하여 관리 그룹 만들기

관리 그룹은 여러 구독에서 액세스, 정책 및 규정 준수를 관리하는 데 도움이 되는 컨테이너입니다. 이러한 컨테이너를 만들어 [Azure 정책](../policy/overview.md) 및 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)와 함께 사용할 수 있는 효과적이고 효율적인 계층을 구축합니다. 관리 그룹에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](overview.md)을 참조하세요.

디렉터리에서 만드는 첫 번째 관리 그룹을 완료하려면 최대 15분이 소요될 수 있습니다. 디렉터리에 대해 Azure 내의 관리 그룹 서비스를 설정하기 위해 처음으로 실행되는 프로세스가 있습니다. 프로세스가 완료되면 알림이 수신됩니다. 자세한 내용은 [관리 그룹의 초기 설정](./overview.md#initial-setup-of-management-groups)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

- 시작하기 전에 [Node.js](https://nodejs.org/) 12 이상 버전이 설치되어 있는지 확인합니다.

- 테넌트의 모든 Azure AD 사용자는 [계층 구조 보호](./how-to/protect-resource-hierarchy.md#setting---require-authorization)가 활성화되지 않은 경우 해당 사용자에게 할당된 관리 그룹 쓰기 권한 없이 관리 그룹을 만들 수 있습니다. 이 새 관리 그룹은 루트 관리 그룹 또는 [기본 관리 그룹](./how-to/protect-resource-hierarchy.md#setting---default-management-group)의 자식이 되며 작성자에게 "소유자" 역할 할당이 부여됩니다. 관리 그룹 서비스는 이 기능을 허용하므로 루트 수준에서 역할 할당이 필요하지 않습니다. 루트 관리 그룹이 생성될 때 사용자는 액세스할 수 있는 권한이 없습니다. 관리 그룹 사용을 시작하기 위해 Azure AD 전역 관리자를 찾는 장애물을 방지하기 위해 루트 수준에서 초기 관리 그룹을 만들 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>애플리케이션 설정

관리 그룹을 관리할 수 있도록 JavaScript를 활성화하려면 환경을 설정해야 합니다. 이 설정은 [Windows 10의 bash](/windows/wsl/install-win10)를 비롯하여 JavaScript를 사용할 수 있는 모든 위치에서 작동합니다.

1. 다음 명령을 실행하여 새 Node.js 프로젝트를 설정합니다.

   ```bash
   npm init -y
   ```

1. yargs 모듈에 참조를 추가합니다.

   ```bash
   npm install yargs
   ```

1. Azure Resource Graph 모듈에 참조를 추가합니다.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Azure 인증 라이브러리에 참조를 추가합니다.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > _package.json_ `@azure/arm-managementgroups`가 버전 **1.1.0** 이상이고 `@azure/ms-rest-nodeauth`가 버전 **3.0.5** 이상인지 확인합니다.

## <a name="create-the-management-group"></a>관리 그룹 만들기

1. _index.js_ 라는 새 파일을 만들고 다음 코드를 입력합니다.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.managementGroups.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. 터미널에서 다음 명령을 입력합니다.

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   각 토큰 `<>` 자리 표시자는 각각 _관리 그룹 ID_ 및 _관리 그룹 식별 이름_ 으로 바꾸어야 합니다.

   스크립트가 인증을 시도할 때 다음 메시지와 유사한 메시지가 터미널에 표시됩니다.

   > 로그인하려면 웹 브라우저를 사용하여 https://microsoft.com/devicelogin 페이지를 열고 FGB56WJUGK 코드를 입력하여 인증합니다.

   브라우저에서 인증하면 스크립트가 계속 실행됩니다.

관리 그룹 생성 결과는 콘솔에 출력됩니다.

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션에서 설치된 라이브러리를 제거하려는 경우 다음 명령을 실행합니다.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 리소스 계층 구조를 구성하기 위한 관리 그룹을 만들었습니다. 관리 그룹은 구독 또는 기타 관리 그룹을 포함할 수 있습니다.

관리 그룹 및 리소스 계층 구조를 관리하는 방법에 대해 자세히 알아보려면 다음을 계속 진행하세요.

> [!div class="nextstepaction"]
> 관리 그룹으로 리소스 관리
