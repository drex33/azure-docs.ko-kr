---
title: Azure Arc 지원 Logic Apps를 사용하여 워크플로 만들기 및 배포
description: Kubernetes가 실행할 수 있는 모든 위치에서 실행되는 단일 테넌트 기반 논리 앱 워크플로를 만들고 배포합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, reylons, archidda, sopai, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ca9458581468ea359ae1ca2f7e034c7459f87fea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082439"
---
# <a name="create-and-deploy-single-tenant-based-logic-app-workflows-with-azure-arc-enabled-logic-apps-preview"></a>Azure Arc 사용 Logic Apps (미리 보기)를 사용 하 여 단일 테 넌 트 기반 논리 앱 워크플로 만들기 및 배포

> [!NOTE]
> 이 기능은 미리 보기로 제공되고 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)이 적용됩니다.

Azure Arc 사용 Logic Apps 및 Azure Portal를 사용 하 여 단일 테 넌 트 기반 논리 앱 워크플로를 만들고, 운영 및 관리 하는 Kubernetes 인프라에 배포할 수 있습니다. 논리 앱은 Azure App Service 플랫폼 확장 번들을 설치 하 고 사용 하도록 설정한 Azure Arc 사용 Kubernetes 클러스터에 매핑된 *사용자 지정 위치* 에서 실행 됩니다.

예를 들어 이 클러스터는 Azure Kubernetes Service, 미설치 Kubernetes 또는 다른 설정일 수 있습니다. 확장 번들을 사용하면 Kubernetes 클러스터에서 Azure Logic Apps, Azure App Service 및 Azure Functions와 같은 플랫폼 서비스를 실행할 수 있습니다. 

자세한 내용은 다음 설명서를 검토하세요.

- [Azure Arc 지원 Logic Apps란?](azure-arc-enabled-logic-apps-overview.md)
- [단일 테넌트 대 다중 테넌트 및 통합 서비스 환경](../logic-apps/single-tenant-overview-compare.md)
- [Azure Arc 개요](../azure-arc/overview.md)
- [Azure Kubernetes Service 개요](../aks/intro-kubernetes.md)
- [Azure Arc 지원 Kubernetes란?](../azure-arc/kubernetes/overview.md)
- [Azure Arc 지원 Kubernetes의 사용자 지정 위치](../azure-arc/kubernetes/conceptual-custom-locations.md)
- [Azure Arc의 App Service, Functions 및 Logic Apps(미리 보기)](../app-service/overview-arc-integration.md)
- [Azure Arc 지원 Kubernetes 클러스터를 설정하여 App Service, Functions 및 Logic Apps 실행(미리 보기)](../app-service/manage-create-arc-environment.md)

## <a name="prerequisites"></a>전제 조건

이 섹션에서는 논리 앱 워크플로를 만들고 배포하는 데 사용할 수 있는 모든 방식 및 도구의 일반적인 필수 구성 요소에 대해 설명합니다. 도구별 필수 구성 요소는 해당 단계와 함께 표시됩니다.

- 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Azure Logic Apps, Azure App Service 및 Azure Functions를 호스트 하 고 실행할 수 있는 Azure Arc 사용 Kubernetes 클러스터와 *사용자 지정 위치* 를 사용 하는 Kubernetes 환경입니다.

  > [!IMPORTANT]
  > Kubernetes 환경, 사용자 지정 위치 및 논리 앱에 대해 동일한 리소스 위치를 사용해야 합니다.

  Kubernetes 클러스터에서 App Service 번들 확장을 만들 때 논리 앱 워크플로를 실행하기 위한 [기본 크기 조정 동작을 변경](#change-scaling)할 수 있습니다. Azure CLI 명령 [ **`az k8s-extension create`**](/cli/azure/k8s-extension)를 사용하여 확장을 만들 때 구성 설정 `keda.enabled=true`를 포함해야 합니다.

  `az k8s-extension create {other-command-options} --configuration-settings "keda.enabled=true"`

  자세한 내용은 다음 설명서를 검토하세요.

  - [Azure Arc의 App Service, Functions 및 Logic Apps(미리 보기)](../app-service/overview-arc-integration.md)
  - [Azure Arc의 클러스터 확장-사용 Kubernetes](../azure-arc/kubernetes/conceptual-extensions.md)
  - [Azure Arc 지원 Kubernetes 클러스터를 설정하여 App Service, Functions 및 Logic Apps 실행(미리 보기)](../app-service/manage-create-arc-environment.md)
  - [기본 크기 조정 동작 변경](#change-scaling)

- 자체 Azure AD(Azure Active Directory) ID

  워크플로에서 Office 365 Outlook 또는 Azure Storage와 같은 Azure에서 호스트되는 연결을 사용해야 하는 경우 논리 앱은 인증을 위해 Azure AD ID를 사용해야 합니다. Azure Arc 사용 Logic Apps은 모든 인프라에서 실행할 수 있지만 Azure에서 호스트 되는 연결을 사용할 수 있는 권한이 있는 id가 필요 합니다. 이 ID를 설정하려면 논리 앱에서 필수 ID로 사용하는 Azure AD에서 앱 등록을 만듭니다.

  > [!NOTE]
  > 관리 id 지원은 현재 Azure Arc 사용 Logic Apps에서 사용할 수 없습니다.

  Azure CLI를 사용하여 Azure AD(Azure Active Directory) 앱 등록을 만들려면 다음 단계를 수행합니다.

  1. [`az ad sp create`](/cli/azure/ad/sp#az_ad_sp_create) 명령을 사용하여 앱 등록을 만듭니다.

  1. 모든 세부 정보를 검토하려면 [`az ad sp show`](/cli/azure/ad/sp#az_ad_sp_show) 명령을 실행합니다.

  1. 두 명령의 출력에서 클라이언트 ID, 개체 ID, 테넌트 ID 및 클라이언트 암호 값을 찾아 저장합니다. 이 값은 나중에 사용하기 위해 보관해야 합니다.

  Azure Portal을 사용하여 Azure AD(Azure Active Directory) 앱 등록을 만들려면 다음 단계를 수행합니다.

  1. [Azure Portal](../active-directory/develop/quickstart-register-app.md)을 사용하여 새 Azure AD 앱 등록을 만듭니다.

  1. 만들기가 완료되면 포털에서 새 앱 등록을 찾습니다.

  1. 등록 메뉴에서 **개요** 를 선택하고 클라이언트 ID, 테넌트 ID 및 클라이언트 암호 값을 저장합니다.

  1. 개체 ID를 찾으려면 **로컬 디렉터리의 관리형 애플리케이션** 필드 옆에서 앱 등록의 이름을 선택합니다. 속성 보기에서 개체 ID를 복사합니다.

## <a name="create-and-deploy-logic-apps"></a>논리 앱 만들기 및 배포

Azure CLI, Visual Studio Code 또는 Azure Portal 중 무엇을 사용할지에 따라 일치하는 탭을 선택하여 특정 필수 구성 요소 및 단계를 검토합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

시작하기 전에 다음 항목이 있어야 합니다.

- 로컬 컴퓨터에 설치된 최신 Azure CLI 확장입니다.

  - 이 확장이 없으면 [운영 체제 또는 플랫폼에 대한 설치 가이드](/cli/azure/install-azure-cli)를 검토하세요.

  - 최신 버전이 있는지 확실하지 않은 경우 [환경 및 CLI 버전을 확인하는 단계](#check-environment-cli-version)를 수행합니다.

- Azure CLI용 *미리 보기* Azure Logic Apps(표준) 확장입니다.

  단일 테넌트 Azure Logic Apps가 일반 공급되지만 Azure Logic Apps 확장은 아직 미리 보기로 제공됩니다.

- 논리 앱을 만들 위치에 대한 [Azure 리소스 그룹](#create-resource-group)입니다.

  이 리소스 그룹이 없는 경우 [리소스 그룹을 만드는 단계](#create-resource-group)를 수행합니다.

- 논리 앱에서 데이터 및 실행 기록 보존에 사용할 Azure 스토리지 계정입니다.

  이 스토리지 계정이 없는 경우 논리 앱을 만들 때 이 계정을 만들거나 단계에 따라 [스토리지 계정을 만들](/cli/azure/storage/account#az_storage_account_create) 수 있습니다.

<a name="check-environment-cli-version"></a>

#### <a name="check-environment-and-cli-version"></a>환경 및 CLI 버전 확인

1. Azure Portal에 로그인합니다. 다음 명령을 실행하여 구독이 활성 상태인지 확인합니다.

   ```azurecli-interactive
   az login
   ```

1. 터미널 또는 명령 창에서 다음 명령을 실행하여 Azure CLI 버전을 확인합니다.

   ```azurecli-interactive
   az --version
   ```

   최신 버전은 [최신 릴리스 정보](/cli/azure/release-notes-azure-cli?tabs=azure-cli)를 참조하세요.

1. 최신 버전이 없는 경우 [사용 중인 운영 체제 또는 플랫폼의 설치 가이드](/cli/azure/install-azure-cli)에 따라 설치를 업데이트합니다.

<a name="install-logic-apps-cli-extension"></a>

##### <a name="install-azure-logic-apps-standard-extension-for-azure-cli"></a>Azure CLI용 Azure Logic Apps(표준) 확장 설치

다음 명령을 실행하여 Azure CLI용 *미리 보기* 단일 테넌트 Azure Logic Apps(표준) 확장을 설치합니다.

```azurecli-interactive
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

<a name="create-resource-group"></a>

#### <a name="create-resource-group"></a>리소스 그룹 만들기

논리 앱에 대한 리소스 그룹이 아직 없는 경우 `az group create` 명령을 실행하여 그룹을 만듭니다. Azure 계정에 대한 기본 구독을 아직 설정하지 않은 경우 `--subscription` 매개 변수를 구독 이름 또는 식별자와 함께 사용해야 합니다. 그렇지 않으면 `--subscription` 매개 변수를 사용할 필요가 없습니다.

> [!TIP]
> 기본 구독을 설정하려면 다음 명령을 실행하고 `MySubscription`을 구독 이름 또는 식별자로 대체합니다.
>
> `az account set --subscription MySubscription`

예를 들어 다음 명령은 위치 `eastus`에서 `MySubscription`이라는 Azure 구독을 사용하여 `MyResourceGroupName`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name MyResourceGroupName 
   --subscription MySubscription 
   --location eastus
```

리소스 그룹이 성공적으로 만들어지면 출력에서 `provisioningState`가 `Succeeded`로 표시됩니다.

```output
<...>
   "name": "testResourceGroup",
   "properties": {
      "provisioningState": "Succeeded"
    },
<...>
```

#### <a name="create-logic-app"></a>논리 앱 만들기

Azure Arc 사용 논리 앱을 만들려면 `az logicapp create` 다음 필수 매개 변수를 사용 하 여 명령을 실행 합니다. 논리 앱, 사용자 지정 위치 및 Kubernetes 환경의 리소스 위치는 모두 동일해야 합니다.

| 매개 변수 | Description |
|------------|-------------|
| `--name -n` | 논리 앱의 고유한 이름 |
| `--resource-group -g` | 논리 앱을 만들 [리소스 그룹](../azure-resource-manager/management/manage-resource-groups-cli.md)의 이름입니다. 사용할 리소스 그룹이 없는 경우 [리소스 그룹을 만듭니다](#create-resource-group). |
| `--storage-account -s` | 논리 앱에 사용할 [스토리지 계정](/cli/azure/storage/account)입니다. 동일한 리소스 그룹에 있는 스토리지 계정의 경우 문자열 값을 사용합니다. 다른 리소스 그룹에 있는 스토리지 계정의 경우 리소스 ID를 사용합니다. |
|||

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation
```

ACR (private Azure Container Registry) 이미지를 사용 하 여 Azure Arc 사용 논리 앱을 만들려면 `az logicapp create` 다음 필수 매개 변수를 사용 하 여 명령을 실행 합니다.

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation 
   --deployment-container-image-name myacr.azurecr.io/myimage:tag
   --docker-registry-server-password MyPassword 
   --docker-registry-server-user MyUsername
```

#### <a name="show-logic-app-details"></a>논리 앱 세부 정보 표시

Azure Arc 사용 논리 앱에 대 한 세부 정보를 표시 하려면 `az logicapp show` 다음 필수 매개 변수를 사용 하 여 명령을 실행 합니다.

```azurecli
az logicapp show --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="deploy-logic-app"></a>논리 앱 배포

[Azure App Service의 Kudu zip 배포](../app-service/resources-kudu.md)를 사용 하 여 Azure Arc 사용 논리 앱을 배포 하려면 `az logicapp deployment source config-zip` 다음과 같은 필수 매개 변수를 사용 하 여 명령을 실행 합니다.

> [!IMPORTANT]
> zip 파일에 루트 수준의 프로젝트 아티팩트가 포함되어 있는지 확인합니다. 이러한 아티팩트에는 모든 워크플로 폴더, host.js, connections.json과 같은 구성 파일 및 기타 관련 파일이 포함됩니다. 추가 폴더를 추가하거나 프로젝트 구조에 아직 없는 폴더에 아티팩트를 배치하지 마세요. 예를 들어 이 목록은 MyBuildArtifacts.zip 파일 구조의 예제를 보여줍니다.
>
> ```output
> MyStatefulWorkflow1-Folder
> MyStatefulWorkflow2-Folder
> connections.json
> host.json
> ```

```azurecli
az logicapp deployment source config-zip --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --src MyBuildArtifact.zip
```

#### <a name="start-logic-app"></a>논리 앱 시작

Azure Arc 사용 논리 앱을 시작 하려면 `az logicapp start` 다음 필수 매개 변수를 사용 하 여 명령을 실행 합니다.

```azurecli
az logicapp start --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="stop-logic-app"></a>논리 앱 중지

Azure Arc 사용 논리 앱을 중지 하려면 `az logicapp stop` 다음 필수 매개 변수를 사용 하 여 명령을 실행 합니다.

```azurecli
az logicapp stop --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="restart-logic-app"></a>논리 앱 다시 시작

Azure Arc 사용 논리 앱을 다시 시작 하려면 `az logicapp restart` 다음 필수 매개 변수를 사용 하 여 명령을 실행 합니다.

```azurecli
az logicapp restart --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="delete-logic-app"></a>논리 앱 삭제

Azure Arc 지원 논리 앱을 삭제하려면 `az logicapp delete` 다음 필수 매개 변수를 사용하여 명령을 실행합니다.

```azurecli
az logicapp delete --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Visual Studio Code 끝에서 끝까지 논리 앱 워크플로를 만들고, 배포하고, 모니터링할 수 있습니다. 단일 테넌트 Azure Logic Apps 실행되는 논리 앱 워크플로 개발과 Azure Arc 지원 Logic Apps 간에 디자이너 환경이 변경되거나 차이가 없습니다.

1. 논리 앱 프로젝트를 만들려면 [Visual Studio Code를 사용하여 단일 테넌트 Azure Logic Apps에서 통합 워크플로 만들기](create-single-tenant-workflows-visual-studio-code.md) 설명서의 필수 구성 요소 및 단계를 따르세요.

1. Azure에 배포할 준비가 되면 **논리 앱에 배포** 환경을 사용하여 이전에 만든 사용자 지정 위치에 논리 앱 리소스를 만들고 동일한 위치에 워크플로를 배포합니다.

   1. 논리 앱 프로젝트 창의 빈 영역에서 바로 가기 메뉴를 열고 **논리 앱에 배포** 를 선택합니다.

   1. 사용자 지정 위치와 연결된 Azure 구독을 선택합니다.

   1. 새 Azure Arc 지원 Logic Apps 리소스를 만들려면 **Azure에서 새 논리 앱 만들기(고급)** 를 선택합니다. 또는 목록에서 기존 논리 앱 리소스를 선택하고 다음 단계를 건너뛸 수 있습니다.

   1. 논리 앱에 전역적으로 고유한 이름을 제공합니다.

   1. 배포하려는 Azure Arc 지원 Kubernetes 환경의 사용자 지정 위치를 선택합니다. 대신 일반 Azure 지역을 선택하는 경우 단일 테넌트 Azure Logic Apps 실행되는 Azure Arc 지원되지 않는 논리 앱 리소스를 만듭니다.

   1. 논리 앱을 배포하려는 위치에 새 리소스 그룹을 만들거나 선택합니다.

   1. 논리 앱의 상태 및 메타데이터를 저장하기 위한 새 스토리지 계정을 만들거나 선택합니다.

   1. 논리 앱에 대한 애플리케이션 로그를 저장하기 위한 새 Application Insights 리소스를 만들거나 선택합니다.

   1. 아직 수행하지 않은 경우 논리 앱이 관리형 API 연결을 인증할 수 있도록 Azure AD(Azure Active Directory) ID를 설정합니다. 자세한 내용은 최상위 [필수 구성 요소](#prerequisites)를 참조하세요.

   1. Azure AD ID에 대한 클라이언트 ID, 테넌트 ID, 개체 ID 및 클라이언트 암호를 입력합니다.

      > [!NOTE]
      > 이 단계는 한 번만 완료하면 됩니다. Visual Studio Code에서 프로젝트의 connections.json 파일 및 관리형 API 연결의 액세스 정책이 업데이트됩니다.

1. 완료되면 논리 앱이 라이브 상태이며 Azure Arc 지원 Kubernetes 클러스터에서 실행되고 테스트할 준비가 됩니다.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

포털 기반 디자이너의 편집 기능은 현재 Azure Arc 지원 Logic Apps 개발 중입니다. 포털 기반 디자이너를 사용하여 논리 앱을 만들고, 배포하고, 볼 수 있지만 배포 후에는 포털에서 편집할 수 없습니다. 지금은 Visual Studio Code에서 로컬로 논리 앱 프로젝트를 만들고 편집한 다음, Visual Studio Code, Azure CLI 또는 자동화된 배포를 사용하여 배포할 수 있습니다.

1. Azure Portal에서 [**논리 앱(표준)** 리소스](create-single-tenant-workflows-azure-portal.md)를 만듭니다. 그러나 **게시** 대상의 경우 **Docker 컨테이너** 를 선택합니다. **지역** 의 경우 이전에 만든 사용자 지정 위치를 앱의 위치로 선택합니다.

   기본적으로 **논리 앱(표준)** 리소스는 단일 테넌트 Azure Logic Apps에서 실행됩니다. 그러나 Azure Arc 지원 Logic Apps 경우 논리 앱 리소스는 Kubernetes 환경에 대해 만든 사용자 지정 위치에서 실행됩니다. 또한 사용자를 위해 생성되는 App Service 계획을 만들 필요가 없습니다.

   > [!IMPORTANT]
   > 논리 앱, 사용자 지정 위치 및 Kubernetes 환경의 리소스 위치는 모두 동일해야 합니다.

1. [Visual Studio Code를 사용하여 논리 앱을 편집하고 배포합니다](create-single-tenant-workflows-visual-studio-code.md).

1. 논리 앱을 빌드하고 배포한 후에는 포털 또는 Application Insights를 사용하여 워크플로를 평소처럼 모니터링하고 볼 수 있습니다.

   배포된 논리 앱에 대한 포털 환경은 현재 읽기 전용 모드에서 사용할 수 있습니다. 즉, 워크플로 또는 앱 설정을 변경할 수 없습니다. 그러나 실행 기록, 트리거 기록 및 앱에 대한 기타 정보는 계속 볼 수 있습니다. 현재 논리 앱을 업데이트하려면 Azure CLI, Visual Studio Code 또는 자동화된 배포를 사용하면 됩니다.

---

## <a name="set-up-connection-authentication"></a>연결 인증 설정

현재 Azure Arc 지원 Kubernetes 클러스터는 논리 앱의 관리 ID를 사용하여 관리되는 API 연결을 인증하는 것을 지원하지 않습니다. 워크플로에서 관리형 커넥터를 사용하는 경우 이러한 Azure에서 호스트 및 관리형 연결을 만듭니다.

대신 Azure AD(Azure Active Directory)에서 자체 앱 등록을 만들어야 합니다. 그런 다음, 이 앱 등록을 Azure Arc 지원 Logic Apps 배포되고 실행되는 논리 앱의 ID로 사용할 수 있습니다. 자세한 내용은 [최상위 필수 구성 요소](#prerequisites)를 검토하세요.

앱 등록에서 클라이언트 ID, 개체 ID, 테넌트 ID 및 클라이언트 암호가 필요합니다. Visual Studio Code를 사용하여 배포하는 경우 Azure AD ID를 사용하여 논리 앱을 설정하기 위한 기본 제공 환경이 있습니다. 자세한 내용은 [논리 앱 워크플로 만들기 및 배포 - Visual Studio Code](#create-and-deploy-logic-apps)를 참조하세요.

단, 개발에 Visual Studio Code를 사용하지만 배포에는 Azure CLI 또는 자동화된 파이프라인을 사용하는 경우 다음 단계를 수행합니다.

### <a name="configure-connection-and-app-settings-in-your-project"></a>프로젝트에서 연결 및 앱 설정 구성

1. 논리 앱 프로젝트의 **connections.js** 파일에서 관리형 연결의 `authentication` 개체를 찾습니다. 이 개체의 콘텐츠를 이전에 [최상위 필수 구성 요소](#prerequisites)에서 생성한 앱 등록 정보로 바꿉니다.

   ```json
   "authentication": {
      "type": "ActiveDirectoryOAuth",
      "audience": "https://management.core.windows.net/",
      "credentialType": "Secret",
      "clientId": "@appsetting('WORKFLOWAPP_AAD_CLIENTID')",
      "tenant": "@appsetting('WORKFLOWAPP_AAD_TENANTID')",
      "secret": "@appsetting('WORKFLOWAPP_AAD_CLIENTSECRET')"
   } 
   ```

1. 논리 앱 프로젝트의 **local.settings.js** 파일에서 클라이언트 ID, 개체 ID, 테넌트 ID 및 클라이언트 암호를 추가합니다. 배포 후 이러한 설정은 논리 앱 설정이 됩니다.

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         <...>
         "WORKFLOWAPP_AAD_CLIENTID":"<my-client-ID>",
         "WORKFLOWAPP_AAD_OBJECTID":"<my-object-ID",
         "WORKFLOWAPP_AAD_TENANTID":"<my-tenant-ID>",
         "WORKFLOWAPP_AAD_CLIENTSECRET":"<my-client-secret>"
      }
   }
   ```

> [!IMPORTANT]
> 프로덕션 시나리오 또는 환경의 경우 [키 자격 증명 모음](../app-service/app-service-key-vault-references.md)을 사용하는 등의 방법으로 이러한 비밀과 중요한 정보를 보호하고 안전하게 지켜야 합니다.

### <a name="add-access-policies"></a>액세스 정책 추가

단일 테넌트 Azure Logic Apps에서 각 논리 앱에는 Azure에서 호스트 및 관리형 연결을 사용하기 위해 액세스 정책에 의해 권한이 부여된 ID가 있습니다. 이러한 액세스 정책은 Azure Portal 또는 인프라 배포를 사용하여 설정할 수 있습니다.

#### <a name="arm-template"></a>ARM 템플릿

ARM 템플릿(Azure Resource Manager 템플릿)에서 *각* 관리형 API 연결에 대한 다음 리소스 정의를 포함하고 다음 정보를 제공합니다.

| 매개 변수 | 설명 |
|-----------|-------------|
| <*connection-name*> | 관리형 API 연결의 이름, 예: `office365` |
| <*object-ID*> | 이전에 앱 등록에서 저장한 Azure AD ID의 개체 ID |
| <*tenant-ID*> | 이전에 앱 등록에서 저장한 Azure AD ID의 테넌트 ID |
|||

```json
{
   "type": "Microsoft.Web/connections/accessPolicies",
   "apiVersion": "2016-06-01",
   "name": "[concat('<connection-name>'),'/','<object-ID>')]",
   "location": "<location>",
   "dependsOn": [
      "[resourceId('Microsoft.Web/connections', parameters('connection_name'))]"
   ],
   "properties": {
      "principal": {
         "type": "ActiveDirectory",
         "identity": {
            "objectId": "<object-ID>",
            "tenantId": "<tenant-ID>"
         }
      }
   }
}
```

자세한 내용은 [Microsoft.Web/connections/accesspolicies(ARM 템플릿)](/azure/templates/microsoft.web/connections?tabs=json) 설명서를 검토하세요.

#### <a name="azure-portal"></a>Azure portal

이 작업의 경우 이전에 저장한 클라이언트 ID를 *애플리케이션 ID* 로 사용합니다.

1. Azure Portal에서 논리 앱을 찾아서 엽니다. 논리 앱 메뉴의 **워크플로** 아래에서 **연결** 을 선택합니다. 그러면 논리 앱의 워크플로에 있는 모든 연결이 나열됩니다.

1. **API 연결** 아래에서 연결(이 예제의 경우 `office365`)을 선택합니다.

1. 연결 메뉴의 **설정** 아래에서 **액세스 정책** > **추가** 를 선택합니다.

1. **액세스 정책 추가** 창의 검색 상자에서 이전에 저장한 클라이언트 ID를 찾아서 선택합니다.

1. 완료되면 **추가** 를 선택합니다.

1. 논리 앱에서 Azure에서 호스트되는 각 연결에 대해 이러한 단계를 반복합니다.

## <a name="automate-devops-deployment"></a>DevOps 배포 자동화

Azure Arc 지원 논리 앱을 빌드하고 배포하려면 [단일 테넌트 기반 논리 앱과](single-tenant-overview-compare.md)동일한 파이프라인 및 프로세스를 사용할 수 있습니다. DevOps용 파이프라인을 사용하여 인프라 배포를 자동화하려면 비컨테이너 및 컨테이너 배포 모두에 대해 인프라 수준에서 다음과 같은 변경을 수행합니다.

### <a name="standard-deployment-non-container"></a>표준 배포(비컨테이너)

논리 앱 배포에 zip 배포를 사용하는 경우 컨테이너 이미지를 호스트하기 위한 Docker 레지스트리를 설정할 필요가 없습니다. Kubernetes의 논리 앱은 기술적으로 컨테이너에서 실행되지만 Azure Arc 지원 Logic Apps 이러한 컨테이너를 자동으로 관리합니다. 이 시나리오의 경우 인프라를 설정할 때 다음 작업을 완료합니다.

- Kubernetes에서 논리 앱을 만들고 있음을 리소스 공급자에게 알립니다.
- 배포에 App Service 계획을 포함합니다. 자세한 내용은 [배포에 App Service 계획 포함](#include-app-service-plan)을 검토하세요.

[ARM 템플릿(Azure Resource Manager 템플릿)](../azure-resource-manager/templates/overview.md)에 다음 값을 포함합니다.

| 항목 | JSON 속성 | Description |
|------|---------------|-------------|
| 위치 | `location` | 사용자 지정 위치 및 Kubernetes 환경과 동일한 리소스 위치(Azure 지역)를 사용해야 합니다. 논리 앱, 사용자 지정 위치 및 Kubernetes 환경의 리소스 위치는 모두 동일해야 합니다. <p><p>**참고**: 이 값은 사용자 지정 위치의 *이름* 과 동일하지 않습니다. |
| 앱 종류 | `kind` | Azure 플랫폼에서 앱을 식별할 수 있도록 배포하는 앱의 유형입니다. Azure Logic Apps의 경우 이 정보는 다음 예제와 같습니다. `kubernetes,functionapp,workflowapp,linux` |
| 확장 위치 | `extendedLocation` | 이 개체는 Kubernetes 환경에 대한 *사용자 지정 위치* 의 `"name"`이 필요하며 `"type"`이 `"CustomLocation"`으로 설정되어 있어야 합니다. |
| 호스팅 계획 리소스 ID | `serverFarmId` | 연결된 App Service 계획의 리소스 ID이며, 다음과 같은 형식입니다. <p><p>`"/subscriptions/{subscriptionID}/resourceGroups/{groupName}/providers/Microsoft.Web/serverfarms/{appServicePlanName}"` |
| 스토리지 연결 문자열 | `AzureWebJobsStorage` | 스토리지 계정의 연결 문자열 <p><p>**중요**: ARM 템플릿에서 스토리지 계정에 대한 연결 문자열을 제공해야 합니다. 프로덕션 시나리오 또는 환경의 경우 키 자격 증명 모음을 사용하는 등의 방법으로 이러한 비밀과 중요한 정보를 보호하고 안전하게 지켜야 합니다. |
||||

#### <a name="arm-template"></a>ARM 템플릿

다음 예제에서는 ARM 템플릿에서 사용할 수 있는 샘플 Azure Arc 지원 Logic Apps 리소스 정의에 대해 설명합니다. 자세한 내용은 [Microsoft.Web/sites 템플릿 형식(JSON)](/azure/templates/microsoft.web/sites?tabs=json) 설명서를 검토하세요.

```json
{
   "type": "Microsoft.Web/sites",
   "apiVersion": "2020-12-01",
   "name": "[parameters('name')]",
   "location": "[parameters('location')]",
   "kind": "kubernetes,functionapp,workflowapp,linux",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
    },
   "properties": {
      "clientAffinityEnabled": false,
      "name": "[parameters('name')]",
      "serverFarmId": "<hosting-plan-ID>",
      "siteConfig": {
         "appSettings": [
            {
               "name": "FUNCTIONS_EXTENSION_VERSION",
               "value": "~3"
            },
            {
               "name": "FUNCTIONS_WORKER_RUNTIME",
               "value": "node"
            },
            {
               "name": "AzureWebJobsStorage",
               "value": "<storage-connection-string>"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__id",
               "value": "Microsoft.Azure.Functions.ExtensionBundle.Workflows"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__version",
               "value": "[1.*, 2.0.0)"
            },
            {
               "name": "APP_KIND",
               "value": "workflowapp"
            }
         ],
         "use32BitWorkerProcess": "[parameters('use32BitWorkerProcess')]",
         "linuxFxVersion": "Node|12"
      }
   }
}
```

### <a name="container-deployment"></a>컨테이너 배포

컨테이너 도구 및 배포 프로세스를 사용하려는 경우 논리 앱을 컨테이너화하고 Azure Arc 지원 Logic Apps 배포할 수 있습니다. 이 시나리오의 경우 인프라를 설정할 때 다음 같은 상위 수준 작업을 완료합니다.

- 컨테이너 이미지를 호스트하기 위한 Docker 레지스트리를 설정합니다.

- 논리 앱을 컨테이너화하려면 논리 앱 프로젝트의 루트 폴더에 다음 Dockerfile을 추가하고 Docker 레지스트리에 이미지를 빌드하고 게시하는 단계를 따릅니다. 예를 들어 자습서: Azure Container Registry 작업 사용하여 [클라우드에서 컨테이너 이미지 빌드 및 배포를](../container-registry/container-registry-tutorial-quick-task.md)검토합니다.

  > [!NOTE]
  > SQL [스토리지 공급자로 사용하는](set-up-sql-db-storage-single-tenant-standard-workflows.md)경우 Azure Functions 이미지 버전 3.3.1 이상 버전을 사용해야 합니다.

  ```text
  FROM mcr.microsoft.com/azure-functions/node:3.3.1
  ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
  AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
  FUNCTIONS_V2_COMPATIBILITY_MODE=true
  COPY . /home/site/wwwroot
  RUN cd /home/site/wwwroot
  ```

- Kubernetes에서 논리 앱을 만들고 있음을 리소스 공급자에게 알립니다.

- 배포 템플릿에서 배포할 Docker 레지스트리 및 컨테이너 이미지를 가리킵니다. 단일 테넌트 Azure Logic Apps는 이 정보를 사용하여 Docker 레지스트리에서 컨테이너 이미지를 가져옵니다.

- 배포에 App Service 계획을 포함합니다. 자세한 내용은 [배포에 App Service 계획 포함](#include-app-service-plan)을 검토하세요.

[ARM 템플릿(Azure Resource Manager 템플릿)](../azure-resource-manager/templates/overview.md)에 다음 값을 포함합니다.

| 항목 | JSON 속성 | Description |
|------|---------------|-------------|
| 위치 | `location` | 사용자 지정 위치 및 Kubernetes 환경과 동일한 리소스 위치(Azure 지역)를 사용해야 합니다. 논리 앱, 사용자 지정 위치 및 Kubernetes 환경의 리소스 위치는 모두 동일해야 합니다. <p><p>**참고**: 이 값은 사용자 지정 위치의 *이름* 과 *동일하지 않습니다*. |
| 앱 종류 | `kind` | Azure 플랫폼에서 앱을 식별할 수 있도록 배포하는 앱의 유형입니다. Azure Logic Apps의 경우 이 정보는 다음 예제와 같습니다. `kubernetes,functionapp,workflowapp,container` |
| 확장 위치 | `extendedLocation` | 이 개체는 Kubernetes 환경에 대한 *사용자 지정 위치* 의 `"name"`이 필요하며 `"type"`이 `"CustomLocation"`으로 설정되어 있어야 합니다. |
| 컨테이너 이름 | `linuxFxVersion` | 컨테이너의 이름이며, 다음과 같은 형식입니다. `DOCKER\|<container-name>` |
| 호스팅 계획 리소스 ID | `serverFarmId` | 연결된 App Service 계획의 리소스 ID이며, 다음과 같은 형식입니다. <p><p>`"/subscriptions/{subscriptionID}/resourceGroups/{groupName}/providers/Microsoft.Web/serverfarms/{appServicePlanName}"` |
| 스토리지 연결 문자열 | `AzureWebJobsStorage` | 스토리지 계정의 연결 문자열 <p><p>**중요**: Docker 컨테이너에 배포할 때 ARM 템플릿에서 스토리지 계정에 대한 연결 문자열을 제공해야 합니다. 프로덕션 시나리오 또는 환경의 경우 키 자격 증명 모음을 사용하는 등의 방법으로 이러한 비밀과 중요한 정보를 보호하고 안전하게 지켜야 합니다. |
||||

Docker 레지스트리 및 컨테이너 이미지를 참조하려면 템플릿에 다음 값을 포함합니다.

| 항목 | JSON 속성 | Description |
|------|---------------|-------------|
| Docker 레지스트리 서버 URL | `DOCKER_REGISTRY_SERVER_URL` | Docker 레지스트리 서버의 URL |
| Docker 레지스트리 서버 | `DOCKER_REGISTRY_SERVER_USERNAME` | Docker 레지스트리 서버에 액세스하기 위한 사용자 이름 |
| Docker 레지스트리 서버 암호 | `DOCKER_REGISTRY_SERVER_PASSWORD` | Docker 레지스트리 서버에 액세스하기 위한 암호 |
||||

#### <a name="arm-template"></a>ARM 템플릿

다음 예제에서는 ARM 템플릿에서 사용할 수 있는 샘플 Azure Arc 지원 Logic Apps 리소스 정의에 대해 설명합니다. 자세한 내용은 [Microsoft.Web/sites 템플릿 형식(ARM 템플릿)](/azure/templates/microsoft.web/sites?tabs=json) 설명서를 검토하세요.

```json
{
   "type": "Microsoft.Web/sites",
   "apiVersion": "2020-12-01",
   "name": "[parameters('name')]",
   "location": "[parameters('location')]",
   "kind": " kubernetes,functionapp,workflowapp,container",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
    },
   "properties": {
      "name": "[parameters('name')]",
      "clientAffinityEnabled": false,
      "serverFarmId": "<hosting-plan-ID>",
      "siteConfig": {
         "appSettings": [
            {
               "name": "FUNCTIONS_EXTENSION_VERSION",
               "value": "~3"
            },
            {
               "name": "FUNCTIONS_WORKER_RUNTIME",
               "value": "node"
            },
            {
               "name": "AzureWebJobsStorage",
               "value": "<storage-connection-string>"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__id",
               "value": "Microsoft.Azure.Functions.ExtensionBundle.Workflows"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__version",
               "value": "[1.*, 2.0.0)"
            },
            {
               "name": "APP_KIND",
               "value": "workflowapp"
            }, 
            {
               "name": "DOCKER_REGISTRY_SERVER_URL",
               "value": "<docker-registry-server-URL>"
            },
            { 
               "name": "DOCKER_REGISTRY_SERVER_USERNAME",
               "value": "<docker-registry-server-username>"
            },
            {
               "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
               "value": "<docker-registry-server-password>"
            }
         ],
         "use32BitWorkerProcess": "[parameters('use32BitWorkerProcess')]",
         "linuxFxVersion": "DOCKER|<container-name>"
      }
   }
}
```

<a name="include-app-service-plan"></a>

### <a name="include-app-service-plan-with-deployment"></a>배포에 App Service 계획 포함

표준 또는 컨테이너 배포 중 무엇을 사용하든 배포에 App Service 계획을 포함해야 합니다. 이 계획은 Kubernetes 환경과 관련성이 떨어지지만 표준 및 컨테이너 배포에는 여전히 App Service 계획이 필요합니다.

다른 만들기 옵션은 일반적으로 이 계획에 대한 Azure 리소스 프로비전을 처리하지만 배포에서 "IaC(infrastructure-as-code)" 템플릿을 사용하는 경우 계획에 대한 Azure 리소스를 명시적으로 만들어야 합니다. 호스팅 계획 리소스는 변경되지 않으며 `sku` 정보만 변경됩니다.

[ARM 템플릿(Azure Resource Manager 템플릿)](../azure-resource-manager/templates/overview.md)에 다음 값을 포함합니다.

| 항목 | JSON 속성 | Description |
|------|---------------|-------------|
| 위치 | `location` | 사용자 지정 위치 및 Kubernetes 환경과 동일한 리소스 위치(Azure 지역)를 사용해야 합니다. 논리 앱, 사용자 지정 위치 및 Kubernetes 환경의 리소스 위치는 모두 동일해야 합니다. <p><p>**참고**: 이 값은 사용자 지정 위치의 *이름* 과 동일하지 않습니다. |
| 종류 | `kind` | `kubernetes,linux`가 필요한 배포 중인 앱 서비스 계획의 종류 |
| 확장 위치 | `extendedLocation` | 이 개체는 Kubernetes 환경에 대한 *사용자 지정 위치* 의 `"name"`이 필요하며 `"type"`이 `"CustomLocation"`으로 설정되어 있어야 합니다. |
| 호스팅 계획 이름 | `name` | App Service 계획의 이름 |
| 계획 계층 | `sku: tier` | App Service 계획 계층(`K1`) |
| 계획 이름 | `sku: name` | App Service 계획 이름(`Kubernetes`) |
||||

#### <a name="arm-template"></a>ARM 템플릿

다음 예제에서는 앱 배포에 사용할 수 있는 샘플 App Service 계획 리소스 정의에 대해 설명합니다. 자세한 내용은 [Microsoft.Web/serverfarms 템플릿 형식(ARM 템플릿)](/azure/templates/microsoft.web/serverfarms?tabs=json) 설명서를 검토하세요.

```json
{
   "type": "Microsoft.Web/serverfarms",
   "apiVersion": "2020-12-01",
   "location": "<location>",
   "name": "<hosting-plan-name>",
   "kind": "kubernetes,linux",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
   },
   "sku": {
      "tier": "K1",
      "name": "Kubernetes", 
      "capacity": 1
   },
   "properties": {
      "kubeEnvironmentProfile": {
         "id": "[parameters('kubeEnvironmentId')]"
      }
   }
}
```

<a name="change-scaling"></a>

## <a name="change-default-scaling-behavior"></a>기본 크기 조정 동작 변경

Azure Arc 지원 Logic Apps 백 엔드 스토리지 큐의 *작업* 수에 따라 논리 앱의 크기 조정을 자동으로 관리합니다. 단, 기본 크기 조정 동작을 변경할 수 있습니다.

논리 앱에서 워크플로 정의는 실행할 작업 시퀀스를 지정합니다. 워크플로 실행이 트리거될 때마다 Azure Logic Apps 런타임은 워크플로 정의의 각 작업 유형에 대한 *작업* 을 만듭니다. 그런 다음, 런타임은 이러한 작업을 *작업 시퀀서* 로 구성합니다. 이 시퀀서가 워크플로 정의에 대한 작업 실행을 오케스트레이션하지만 기본 Azure Logic Apps 작업 오케스트레이션 엔진이 각 작업을 실행합니다.

상태 저장 워크플로의 경우 작업 오케스트레이션 엔진은 스토리지 큐 메시지를 사용하여 작업 시퀀서에서 작업을 예약합니다. 백그라운드에서 *작업 디스패처*(또는 *디스패처 작업자 인스턴스*)는 이러한 작업 큐를 모니터링합니다. 오케스트레이션 엔진은 기본 최소 및 최대 작업자 인스턴스 수를 사용하여 작업 큐를 모니터링합니다. 상태 비저장 워크플로의 경우 오케스트레이션 엔진은 작업 상태를 메모리에 완전히 유지합니다.

기본 크기 조정 동작을 변경하려면 작업 큐를 모니터링하는 작업자 인스턴스의 최소 및 최대 수를 다르게 지정합니다.

### <a name="prerequisites-to-change-scaling"></a>크기 조정을 변경하기 위한 필수 구성 요소

Azure Arc 지원 Kubernetes 클러스터에서 이전에 만든 App Service 번들 확장에는 속성이 로 설정되어 있어야 `keda.enabled` `true` 합니다. 자세한 내용은 [최상위 필수 구성 요소](#prerequisites)를 검토하세요.

### <a name="change-scaling-threshold"></a>크기 조정 임계값 변경

Azure Arc 지원 Logic Apps 작업 큐의 길이는 크기 조정 이벤트를 트리거하고 논리 앱에 대해 크기 조정이 발생하는 빈도에 대한 임계값을 설정합니다. 기본값이 `20`개 작업으로 설정된 큐 길이를 변경할 수 있습니다. 크기 조정 빈도를 줄이려면 큐 길이를 늘립니다. 크기 조정 빈도를 늘리려면 큐 길이를 줄입니다. 이 프로세스에는 시행착오가 필요할 수 있습니다.

큐 길이를 변경하려면 논리 앱 프로젝트의 루트 수준 **host.js** 파일에서 `Runtime.ScaleMonitor.KEDA.TargetQueueLength` 속성을 설정합니다. 예를 들면 다음과 같습니다.

```json
"extensions": {
   "workflow": {
      "settings": {
         "Runtime.ScaleMonitor.KEDA.TargetQueueLength": "10"
      }
   }
}
```

### <a name="change-throughput-maximum"></a>처리량 최댓값 변경

기존 논리 앱 리소스에서 최대 작업자 인스턴스 수를 변경할 수 있습니다. 기본값은 `2`로 설정되어 있습니다. 이 값은 작업 큐를 모니터링할 수 있는 작업자 인스턴스 수의 상한을 제어합니다.

이 최댓값을 변경하려면 Azure CLI(논리 앱 생성만) 및 Azure Portal을 사용합니다.

#### <a name="azure-cli"></a>Azure CLI

새 논리 앱을 만들려면 다음 매개 변수와 함게 `az logicapp create` 명령을 실행합니다.

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation 
   [--plan MyHostingPlan] [--min-worker-count 1] [--max-worker-count 4]
```

최대 인스턴스 수를 구성하려면 `--settings` 매개 변수를 사용합니다.

```azurecli
az logicapp config appsettings set --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
   --settings "K8SE_APP_MAX_INSTANCE_COUNT=10"
```

#### <a name="azure-portal"></a>Azure portal

단일 테넌트 기반 논리 앱의 설정에서 다음 단계에 따라 `K8SE_APP_MAX_INSTANCE_COUNT` 설정 값을 추가하거나 편집합니다.

1. Azure Portal에서 단일 테넌트 기반 논리 앱을 찾아서 엽니다.

1. 논리 앱 메뉴의 **설정** 에서 **구성** 을 선택합니다.

1. **구성** 창의 **애플리케이션 설정** 에서 새 애플리케이션 설정을 추가하거나 기존 값(이미 추가된 경우)을 편집합니다.

   1. **새 애플리케이션 설정** 을 선택하고 원하는 최댓값으로 `K8SE_APP_MAX_INSTANCE_COUNT` 설정을 추가합니다.

   1. `K8SE_APP_MAX_INSTANCE_COUNT` 설정에 대한 기존 값을 편집합니다.

1. 완료되면 변경 사항을 저장합니다.

### <a name="change-throughput-minimum"></a>처리량 최솟값 변경

기존 논리 앱 리소스에서 최소 작업자 인스턴스 수를 변경할 수 있습니다. 기본값은 `1`로 설정되어 있습니다. 이 값은 작업 큐를 모니터링할 수 있는 작업자 인스턴스 수의 하한을 제어합니다. 고가용성 또는 성능을 위해서는 이 값을 늘입니다.

이 최솟값을 변경하려면 Azure CLI 또는 Azure Portal을 사용합니다.

#### <a name="azure-cli"></a>Azure CLI

기존 논리 앱 리소스의 경우 다음 매개 변수를 통해 `az logicapp scale` 명령을 실행합니다.

```azurecli
az logicapp scale --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --instance-count 5 
```

새 논리 앱을 만들려면 다음 매개 변수와 함게 `az logicapp create` 명령을 실행합니다.

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation 
   [--plan MyHostingPlan] [--min-worker-count 2] [--max-worker-count 4]
```

#### <a name="azure-portal"></a>Azure Portal

단일 테넌트 기반 논리 앱의 설정에서 다음 단계에 따라 **스케일 아웃** 속성 값을 변경합니다.

1. Azure Portal에서 단일 테넌트 기반 논리 앱을 찾아서 엽니다.

1. 논리 앱 메뉴의 **설정** 에서 **스케일 아웃** 을 선택합니다.

1. **스케일 아웃** 창에서 최소 인스턴스 슬라이더를 원하는 값으로 끕니다.

1. 완료되면 변경 사항을 저장합니다.

## <a name="troubleshoot-problems"></a>문제 해결

배포된 논리 앱에 대한 자세한 내용을 얻으려면 다음 옵션을 사용해보세요.

### <a name="access-app-settings-and-configuration"></a>앱 설정 및 구성에 액세스

앱 설정에 액세스하려면 다음 매개 변수와 함께 `az logicapp config appsettings` 명령을 실행합니다.

```azurecli
az logicapp config appsettings list --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

앱 설정을 구성하려면 다음 매개 변수와 함께 `az logicapp config appsettings set` 명령을 실행합니다. `--settings` 매개 변수를 설정의 이름 및 값과 함께 사용해야 합니다.

```azurecli
az logicapp config appsettings set --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --settings "MySetting=1"
```

앱 설정을 삭제하려면 다음 매개 변수와 함께 `az logicapp config appsettings delete` 명령을 실행합니다. `--setting-names` 매개 변수는 삭제하려는 설정의 이름과 함께 사용해야 합니다.

```azurecli
az logicapp config appsettings delete --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
   --setting-names MySetting
```

### <a name="view-logic-app-properties"></a>논리 앱 속성 보기

앱의 정보 및 속성을 보려면 다음 매개 변수와 함께 `az logicapp show` 명령을 실행합니다.

```azurecli
az logicapp show --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

### <a name="monitor-workflow-activity"></a>워크플로 활동 모니터링

논리 앱에서 워크플로에 대한 활동을 보려면 다음 단계를 수행합니다. 

1. Azure Portal에서 배포된 논리 앱을 찾아서 엽니다.

1. 논리 앱 메뉴에서  **워크플로** 를 선택한 다음, 워크플로를 선택합니다. 

1. 워크플로 메뉴에서  **모니터** 를 선택합니다.

### <a name="collect-logs"></a>로그 수집

논리 앱에 대해 로그된 데이터를 얻으려면 논리 앱에서 Application Insights를 사용하도록 설정합니다(아직 사용하도록 설정되지 않은 경우).

## <a name="next-steps"></a>다음 단계

- [Azure Arc 지원 Logic Apps 정보](azure-arc-enabled-logic-apps-overview.md)
