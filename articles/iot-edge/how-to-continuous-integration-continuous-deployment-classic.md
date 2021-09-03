---
title: Azure IoT Edge 디바이스에 연속 통합 및 지속적인 배포(클래식 편집기) - Azure IoT Edge
description: 클래식 편집기를 사용하여 연속 통합 및 지속적인 배포 설정 - Azure IoT Edge 및 Azure DevOps, Azure Pipelines
author: kgremban
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 757ae7f71e5b03a5dc38b6e5438cdf22a0083965
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528883"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Azure IoT Edge 디바이스에 연속 통합 및 지속적인 배포(클래식 편집기)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Pipelines의 기본 제공 Azure IoT Edge 작업과 함께 Azure IoT Edge 애플리케이션을 사용하여 DevOps를 손쉽게 채택할 수 있습니다. 이 문서에서는 Azure Pipelines의 연속 통합 및 지속적인 배포 기능을 사용하여 애플리케이션을 빠르고 효율적으로 빌드하고 테스트한 다음, 클래식 편집기를 사용하여 Azure IoT Edge에 배포하는 방법을 설명합니다. 또는 [YAML을 사용](how-to-continuous-integration-continuous-deployment.md)할 수 있습니다.

![다이어그램 - 개발 및 프로덕션을 위한 CI 및 CD 분기](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

이 문서에서는 Azure Pipelines의 기본 제공 [Azure IoT Edge 작업](/azure/devops/pipelines/tasks/build/azure-iot-edge)을 사용하여 IoT Edge 솔루션을 위한 빌드 및 릴리스 파이프라인을 만드는 방법을 알아봅니다. 파이프라인에 추가된 각 Azure IoT Edge 작업은 다음 네 가지 작업 중 하나를 구현합니다.

 | 작업 | 설명 |
 | --- | --- |
 | 모듈 이미지 빌드 | IoT Edge 솔루션 코드를 가져와 컨테이너 이미지를 빌드합니다.|
 | 모듈 이미지 푸시 | 지정한 컨테이너 레지스트리에 모듈 이미지를 푸시합니다. |
 | 배포 매니페스트 생성 | deployment.template.json 파일 및 변수를 가져와 최종 IoT Edge 배포 매니페스트 파일을 생성합니다. |
 | IoT Edge 디바이스에 배포 | 하나 이상의 IoT Edge 디바이스에 대한 IoT Edge 배포를 만듭니다. |

달리 지정하지 않는 한, 이 문서의 절차에서는 작업 매개 변수를 통해 사용할 수 있는 모든 기능을 검색하지는 않습니다. 자세한 내용은 다음 자료를 참조하세요.

* [작업 버전](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **고급** -해당하는 경우 빌드하지 않으려는 모듈을 지정합니다.
* [제어 옵션](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [환경 변수](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [출력 변수](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>필수 구성 요소

* Azure Repos 리포지토리. 이 리포지토리가 없는 경우 [프로젝트에서 새 Git 리포지토리를 만들](/azure/devops/repos/git/create-new-repo) 수 있습니다. 이 문서의 경우 **IoTEdgeRepo** 라는 리포지토리를 만들었습니다.
* 리포지토리에 커밋되고 푸시된 IoT Edge 솔루션. 이 문서를 테스트하기 위한 새 샘플 솔루션을 만들려면 [Visual Studio Code에서 모듈 개발 및 디버그](how-to-vs-code-develop-module.md) 또는 [Visual Studio에서 C# 모듈 개발 및 디버그](./how-to-visual-studio-develop-module.md)의 단계를 따릅니다. 이 문서에서는 **filtermodule** 이라는 모듈에 대한 코드를 포함하는 **IoTEdgeSolution** 이라는 리포지토리에 솔루션을 만들었습니다.

   이 문서의 경우에는 Visual Studio Code 또는 Visual Studio에서 IoT Edge 템플릿으로 솔루션 폴더만 만들면 됩니다. 계속하기 전에 이 코드를 빌드, 푸시, 배포 또는 디버그할 필요가 없습니다. 해당 프로세스는 Azure Pipelines에서 설정합니다.

  여러 단계에서 사용되는 솔루션의 **deployment.template.json** 파일의 경로를 알아야 합니다. 배포 템플릿의 역할에 대해 잘 모르는 경우 [모듈을 배포하고 경로를 설정하는 방법 알아보기](module-composition.md)를 참조하세요.

  >[!TIP]
  >새 솔루션을 만드는 경우에는 먼저 로컬로 리포지토리를 복제합니다. 그런 다음, 솔루션을 만들 때 리포지토리 폴더에서 직접 만들도록 선택할 수 있습니다. 여기에서 새 파일을 쉽게 커밋하고 푸시할 수 있습니다.

* 모듈 이미지를 푸시할 수 있는 컨테이너 레지스트리. [Azure Container Registry](../container-registry/index.yml) 또는 타사 레지스트리를 사용할 수 있습니다.
* 개별 테스트 및 프로덕션 배포 스테이지를 테스트하기 위한 IoT Edge 디바이스가 2개 이상 있는 활성 Azure [IoT 허브](../iot-hub/iot-hub-create-through-portal.md). 빠른 시작 문서에 따라 [Linux](quickstart-linux.md) 또는 [Windows](quickstart.md)에서 IoT Edge 디바이스를 만들 수 있습니다.

## <a name="create-a-build-pipeline-for-continuous-integration"></a>연속 통합을 위한 빌드 파이프라인 만들기

이 섹션에서는 새 빌드 파이프라인을 만듭니다. IoT Edge 솔루션에 대한 변경 사항을 체크 인할 때마다 자동으로 실행되고 빌드 로그를 게시하도록 파이프라인을 구성합니다.

1. Azure DevOps 조직(`https://dev.azure.com/{your organization}`)에 로그인하고 IoT Edge 솔루션 리포지토리가 포함된 프로젝트를 엽니다.

    ![DevOps 프로젝트 열기](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. 프로젝트의 왼쪽 창 메뉴에서 **파이프라인** 을 선택합니다. 페이지 중앙에서 **파이프라인 만들기** 를 선택합니다. 또는 빌드 파이프라인이 이미 있는 경우 오른쪽 위에 있는 **새 파이프라인** 단추를 선택합니다.

    ![새 빌드 파이프라인 만들기](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. **코드는 어디에 있나요?** 페이지의 아래쪽에서 **클래식 편집기 사용** 을 선택합니다. YAML을 사용하여 프로젝트의 빌드 파이프라인을 만들려면 [YAML 가이드](how-to-continuous-integration-continuous-deployment.md)를 참조하세요.

    ![클래식 편집기 사용 선택](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. 프롬프트에 따라 파이프라인을 만듭니다.

   1. 새 빌드 파이프라인의 원본 정보를 제공합니다. **Azure Repos Git** 을 원본으로 선택한 다음, IoT Edge 솔루션 코드가 있는 프로젝트, 리포지토리 및 분기를 선택합니다. 그런 다음, **계속** 을 선택합니다.

      ![파이프라인 원본 선택](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. 템플릿 대신 **빈 작업** 을 선택합니다.

      ![빌드 파이프라인에 대한 빈 작업으로 시작](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. 파이프라인이 만들어지면 파이프라인 편집기로 이동합니다. 여기에서 파이프라인의 이름, 에이전트 풀 및 에이전트 사양을 변경할 수 있습니다.

   Microsoft에서 호스트하는 풀 또는 사용자가 관리하는 자체 호스팅 풀을 선택할 수 있습니다.

   파이프라인 설명에서 대상 플랫폼에 따라 올바른 에이전트 사양을 선택합니다.

   * Linux 컨테이너용 플랫폼 amd64에서 모듈을 빌드하려는 경우 **ubuntu-16.04** 를 선택합니다.

   * Windows 1809 컨테이너용 플랫폼 amd64에서 모듈을 빌드하려는 경우 [Windows에서 자체 호스팅 에이전트를 설치](/azure/devops/pipelines/agents/v2-windows)해야 합니다.

   * Linux 컨테이너용 플랫폼 arm32v7 또는 arm64에서 모듈을 빌드하려는 경우에는 [Linux에서 자체 호스팅 에이전트를 설치](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent)해야 합니다.

    ![빌드 에이전트 사양 구성](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. 파이프라인은 **에이전트 작업 1** 이라는 작업으로 미리 구성됩니다. 더하기 기호( **+** )를 선택하여 작업에 4개의 작업을 추가합니다. **Azure IoT Edge** 를 두 번 추가하고, **파일 복사** 를 한 번 추가하고, **빌드 아티팩트 게시** 를 한 번 추가합니다. 각 작업을 검색하고 작업 이름 위로 마우스를 이동하면 **추가** 단추를 표시할 수 있습니다.

   ![Azure IoT Edge 작업 추가](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   네 개의 작업(task)이 모두 추가되면 에이전트 작업(job)이 다음 예제와 같이 표시됩니다.

   ![빌드 파이프라인에 있는 네 개의 작업](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. 첫 번째 **Azure IoT Edge** 작업을 선택하여 편집합니다. 이 작업은 사용자가 지정한 대상 플랫폼을 사용하여 솔루션의 모든 모듈을 빌드합니다. 다음 값을 사용하여 작업을 편집합니다.

   | 매개 변수 | 설명 |
   | --- | --- |
   | 표시 이름 | 작업 필드가 변경되면 표시 이름이 자동으로 업데이트됩니다. |
   | 작업 | **모듈 이미지 빌드** 를 선택합니다. |
   | .template.json 파일 | 줄임표(**...**)를 선택하고 IoT Edge 솔루션이 포함된 리포지토리의 **deployment.template.json** 파일로 이동합니다. |
   | 기본 플랫폼 | 대상으로 하는 IoT Edge 디바이스에 따라 모듈에 적합한 운영 체제를 선택합니다. |
   | 출력 변수 | deployment.json 파일이 생성하는 파일의 경로와 연결할 참조 이름(예: **edge**)을 제공합니다. |

   이 작업 및 해당 매개 변수에 대한 자세한 내용은 [Azure IoT Edge 작업](/azure/devops/pipelines/tasks/build/azure-iot-edge)을 참조하세요.

   해당 구성은 `module.json` 파일에 정의된 이미지 리포지토리 및 태그를 사용하여 모듈 이미지의 이름 및 태그를 지정합니다. 또한 **모듈 이미지 빌드** 를 사용하면 변수를 `module.json` 파일에서 정의한 정확한 값으로 바꿀 수 있습니다. Visual Studio 또는 Visual Studio Code에서 `.env` 파일의 실제 값을 지정합니다. Azure Pipelines에서 **파이프라인 변수** 탭의 값을 설정합니다. 파이프라인 편집기 메뉴에서 **변수** 탭을 선택하고 이름 및 값을 다음과 같이 구성합니다.

   * **ACR_ADDRESS**: 실제 Azure Container Registry **로그인 서버** 이름으로 바꿉니다. Azure Portal에 있는 컨테이너 레지스트리의 개요 페이지에서 Login 서버 값을 검색할 수 있습니다.

   프로젝트에 다른 변수가 있는 경우 이 탭에서 이름 및 값을 지정할 수 있습니다. **모듈 이미지 빌드** 는 `${VARIABLE}` 형식의 변수만 인식합니다. `**/module.json` 파일에서 이 형식을 사용하는지 확인합니다.

8. 두 번째 **Azure IoT Edge** 작업을 선택하여 편집합니다. 이 작업은 모든 모듈 이미지를 선택된 컨테이너 레지스트리로 푸시합니다.

   | 매개 변수 | 설명 |
   | --- | --- |
   | 표시 이름 | 작업 필드가 변경되면 표시 이름이 자동으로 업데이트됩니다. |
   | 작업 | **모듈 이미지 푸시** 를 선택합니다. |
   | 컨테이너 레지스트리 유형 | 기본 형식인 `Azure Container Registry`를 사용합니다. |
   | Azure 구독 | 구독을 선택합니다. |
   | Azure Container Registry | 모듈 이미지를 저장하는 데 사용할 컨테이너 레지스트리 유형을 선택합니다. 선택한 레지스트리 유형에 따라 양식이 변경됩니다. **Azure Container Registry** 를 선택하는 경우에는 드롭다운 목록을 사용하여 Azure 구독 및 컨테이너 레지스트리 이름을 선택합니다. **일반 컨테이너 레지스트리** 를 선택하는 경우에는 **새로 만들기** 를 선택하여 레지스트리 서비스 연결을 만듭니다. |
   | .template.json 파일 | 줄임표(**...**)를 선택하고 IoT Edge 솔루션이 포함된 리포지토리의 **deployment.template.json** 파일로 이동합니다. |
   | 기본 플랫폼 | 대상으로 하는 IoT Edge 디바이스에 따라 모듈에 적합한 운영 체제를 선택합니다. |
   | 배포 매니페스트에 레지스트리 자격 증명 추가 | Docker 이미지를 배포 매니페스트에 푸시하는 데 사용할 레지스트리 자격 증명을 추가하려면 true를 지정합니다. |

   이 작업 및 해당 매개 변수에 대한 자세한 내용은 [Azure IoT Edge 작업](/azure/devops/pipelines/tasks/build/azure-iot-edge)을 참조하세요.

   모듈 이미지를 호스트하는 컨테이너 레지스트리가 여러 개 있는 경우 이 작업을 복제하고 다른 컨테이너 레지스트리를 선택한 다음, **고급** 설정의 **모듈 무시** 를 사용하여 이 특정 레지스트리에 해당되지 않는 이미지를 무시해야 합니다.

9. **파일 복사** 작업을 선택하여 편집합니다. 이 작업을 사용하여 아티팩트 스테이징 디렉터리에 파일을 복사합니다.

   | 매개 변수 | 설명 |
   | --- | --- |
   | 표시 이름 | 기본 이름을 사용하거나 사용자 지정 |
   | 소스 폴더 | 복사할 파일이 포함된 폴더입니다. |
   | 콘텐츠 | `deployment.template.json` 및 `**/module.json`의 두 줄을 추가합니다. 해당 두 파일은 IoT Edge 배포 매니페스트 생성을 위한 입력으로 사용됩니다. |
   | 대상 폴더 | 변수 `$(Build.ArtifactStagingDirectory)`를 지정합니다. 설명에 대한 자세한 내용은 [빌드 변수](/azure/devops/pipelines/build/variables#build-variables)를 참조하세요. |

   이 작업 및 매개 변수에 대한 자세한 내용은 [파일 복사 작업](/azure/devops/pipelines/tasks/utility/copy-files?tabs=classic)을 참조하세요.

10. **빌드 아티팩트 게시** 작업을 선택하여 편집합니다. 경로를 릴리스 파이프라인에 게시할 수 있도록 작업에 아티팩트 스테이징 디렉터리 경로를 제공합니다.

    | 매개 변수 | 설명 |
    | --- | --- |
    | 표시 이름 | 기본 이름을 사용하거나 사용자 지정합니다. |
    | 게시할 경로 | 변수 `$(Build.ArtifactStagingDirectory)`를 지정합니다. 자세히 알아보려면 [빌드 변수](/azure/devops/pipelines/build/variables#build-variables)를 참조하세요. |
    | 아티팩트 이름 | 기본 이름인 **drop** 을 사용합니다. |
    | 아티팩트 게시 위치 | 기본 위치인 **Azure Pipelines** 를 사용합니다. |

    이 작업 및 해당 매개 변수에 대한 자세한 내용은 [빌드 아티팩트 게시 작업](/azure/devops/pipelines/tasks/utility/publish-build-artifacts)을 참조하세요.

11. **트리거** 탭을 열고 **연속 통합 사용** 상자를 선택합니다. 코드를 포함하는 분기가 포함되어 있는지 확인합니다.

    ![연속 통합 트리거 켜기](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. **저장 및 큐** 드롭다운에서 **저장** 을 선택합니다.

이제 이 파이프라인은 리포지토리에 새 코드를 푸시하면 자동으로 실행되도록 구성됩니다. 파이프라인 아티팩트를 게시하는 마지막 작업은 릴리스 파이프라인을 트리거합니다. 계속해서 다음 섹션에서 릴리스 파이프라인을 빌드합니다.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>계층화된 배포는 Azure DevOps의 Azure IoT Edge 작업에서 아직 지원되지 않습니다.
>
>그러나 [Azure DevOps에서 Azure CLI 작업](/azure/devops/pipelines/tasks/deploy/azure-cli)을 사용하여 배포를 계층화된 배포로 만들 수 있습니다. **인라인 스크립트** 값의 경우 [az iot edge deployment create 명령](/cli/azure/iot/edge/deployment)을 사용할 수 있습니다.
>
>```azurecli-interactive
>az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>다음 단계

* [IoT Edge용 Azure DevOps Starter](how-to-devops-starter.md)의 IoT Edge DevOps 샘플
* [단일 디바이스 또는 대규모 IoT Edge 배포에 대한 이해](module-deployment-monitoring.md)를 통해 IoT Edge 배포 이해
* [대규모 IoT Edge 모듈 배포 및 모니터링](how-to-deploy-at-scale.md)에서 배포를 생성, 업데이트 또는 삭제하는 단계를 연습합니다.