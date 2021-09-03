---
title: Azure IoT Edge 디바이스에 연속 통합 및 지속적인 배포 - Azure IoT Edge
description: YAML을 사용하여 연속 통합 및 지속적인 배포 설정 - Azure IoT Edge 및 Azure DevOps, Azure Pipelines
author: kgremban
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c6cc3f1f49150be53d2da4e1dfc7b40d291999f5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528099"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Azure IoT Edge 디바이스에 연속 통합 및 지속적인 배포

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Pipelines의 기본 제공 Azure IoT Edge 작업과 함께 Azure IoT Edge 애플리케이션을 사용하여 DevOps를 손쉽게 채택할 수 있습니다. 이 문서에서는 Azure Pipelines를 사용하여 YAML을 사용하여 Azure IoT Edge 모듈을 빌드, 테스트 및 배포하는 방법을 보여 줍니다. 또는 [클래식 편집기](how-to-continuous-integration-continuous-deployment-classic.md)를 사용할 수 있습니다.

![다이어그램 - 개발 및 프로덕션을 위한 CI 및 CD 분기](./media/how-to-continuous-integration-continuous-deployment/model.png)

이 문서에서는 Azure Pipelines의 기본 제공 [Azure IoT Edge 작업](/azure/devops/pipelines/tasks/build/azure-iot-edge)을 사용하여 IoT Edge 솔루션을 위한 빌드 및 릴리스 파이프라인을 만드는 방법을 알아봅니다. 파이프라인에 추가된 각 Azure IoT Edge 작업은 다음 네 가지 작업 중 하나를 구현합니다.

 | 작업 | 설명 |
 | --- | --- |
 | 모듈 이미지 빌드 | IoT Edge 솔루션 코드를 가져와 컨테이너 이미지를 빌드합니다.|
 | 모듈 이미지 푸시 | 지정한 컨테이너 레지스트리에 모듈 이미지를 푸시합니다. |
 | 배포 매니페스트 생성 | deployment.template.json 파일 및 변수를 가져와 최종 IoT Edge 배포 매니페스트 파일을 생성합니다. |
 | IoT Edge 디바이스에 배포 | 하나 이상의 IoT Edge 디바이스에 대한 IoT Edge 배포를 만듭니다. |

달리 지정하지 않는 한, 이 문서의 절차에서는 작업 매개 변수를 통해 사용할 수 있는 모든 기능을 검색하지는 않습니다. 자세한 내용은 다음 자료를 참조하세요.

* [작업 버전](/azure/devops/pipelines/process/tasks?tabs=yaml#task-versions)
* [제어 옵션](/azure/devops/pipelines/process/tasks?tabs=yaml#task-control-options)
* [환경 변수](/azure/devops/pipelines/process/variables?tabs=yaml#environment-variables)
* [출력 변수](/azure/devops/pipelines/process/variables?tabs=yaml#use-output-variables-from-tasks)

## <a name="prerequisites"></a>필수 구성 요소

* Azure Repos 리포지토리. 이 리포지토리가 없는 경우 [프로젝트에서 새 Git 리포지토리를 만들](/azure/devops/repos/git/create-new-repo) 수 있습니다. 이 문서의 경우 **IoTEdgeRepo** 라는 리포지토리를 만들었습니다.
* 리포지토리에 커밋되고 푸시된 IoT Edge 솔루션. 이 문서를 테스트하기 위한 새 샘플 솔루션을 만들려면 [Visual Studio Code에서 모듈 개발 및 디버그](how-to-vs-code-develop-module.md) 또는 [Visual Studio에서 C# 모듈 개발 및 디버그](./how-to-visual-studio-develop-module.md)의 단계를 따릅니다. 이 문서에서는 **filtermodule** 이라는 모듈에 대한 코드를 포함하는 **IoTEdgeSolution** 이라는 리포지토리에 솔루션을 만들었습니다.

  이 문서의 경우에는 Visual Studio Code 또는 Visual Studio에서 IoT Edge 템플릿으로 솔루션 폴더만 만들면 됩니다. 계속하기 전에 이 코드를 빌드, 푸시, 배포 또는 디버그할 필요가 없습니다. 해당 프로세스는 Azure Pipelines에서 설정합니다.

  여러 단계에서 사용되는 솔루션의 **deployment.template.json** 파일 경로를 알고 있습니다. 배포 템플릿의 역할에 대해 잘 모르는 경우 [모듈 배포 및 경로 설정 방법 알아보기](module-composition.md)를 참조하세요.

  >[!TIP]
  >새 솔루션을 만드는 경우에는 먼저 로컬로 리포지토리를 복제합니다. 그런 다음, 솔루션을 만들 때 리포지토리 폴더에서 직접 만들도록 선택할 수 있습니다. 여기에서 새 파일을 쉽게 커밋하고 푸시할 수 있습니다.

* 모듈 이미지를 푸시할 수 있는 컨테이너 레지스트리. [Azure Container Registry](../container-registry/index.yml) 또는 타사 레지스트리를 사용할 수 있습니다.
* 개별 테스트 및 프로덕션 배포 스테이지를 테스트하기 위한 IoT Edge 디바이스가 2개 이상 있는 활성 Azure [IoT 허브](../iot-hub/iot-hub-create-through-portal.md). 빠른 시작 문서에 따라 [Linux](quickstart-linux.md) 또는 [Windows](quickstart.md)에서 IoT Edge 디바이스를 만들 수 있습니다.

Azure Repos를 사용하는 방법에 대한 자세한 내용은 [Share your code with Visual Studio and Azure Repos](/azure/devops/repos/git/share-your-code-in-git-vs)(Visual Studio 및 Azure Repos와 코드 공유)를 참조하세요.

## <a name="create-a-build-pipeline-for-continuous-integration"></a>연속 통합을 위한 빌드 파이프라인 만들기

이 섹션에서는 새 빌드 파이프라인을 만듭니다. 샘플 IoT Edge 솔루션에 변경 사항을 체크 인하면 자동으로 실행되도록 파이프라인을 구성하고 빌드 로그를 게시합니다.

1. Azure DevOps 조직(`https://dev.azure.com/{your organization}`)에 로그인하고 IoT Edge 솔루션 리포지토리가 포함된 프로젝트를 엽니다.

   ![DevOps 프로젝트 열기](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. 프로젝트의 왼쪽 창 메뉴에서 **파이프라인** 을 선택합니다. 페이지 중앙에서 **파이프라인 만들기** 를 선택합니다. 또는 빌드 파이프라인이 이미 있는 경우 오른쪽 위에 있는 **새 파이프라인** 단추를 선택합니다.

    ![새 파이프라인 단추를 사용하여 새 빌드 파이프라인 만들기](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. **코드는 어디에 있나요?** 페이지에서 **Azure Repos Git `YAML`** 을 선택합니다. 클래식 편집기를 사용하여 프로젝트의 빌드 파이프라인을 만들려면 [클래식 편집기 가이드](how-to-continuous-integration-continuous-deployment-classic.md)를 참조하세요.

4. 파이프라인을 만들 리포지토리를 선택합니다.

    ![빌드 파이프라인을 위한 리포지토리 선택](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. **파이프라인 구성** 페이지에서 **시작 파이프라인** 을 선택합니다. 이 파이프라인을 만드는 데 사용하고자 하는 기존 Azure Pipelines YAML 파일이 있는 경우 **기존 Azure Pipelines YAML 파일** 을 선택하고 리포지토리의 분기 및 경로를 파일에 제공할 수 있습니다.

    ![시작 파이프라인 또는 기존 Azure Pipelines YAML 파일을 선택하여 빌드 파이프라인 시작](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. **파이프라인 YAML 검토** 페이지에서 기본 이름 `azure-pipelines.yml`을 선택하여 파이프라인의 구성 파일 이름을 바꿀 수 있습니다.

   **도우미 표시** 를 선택하여 **작업** 팔레트를 엽니다.

    ![도우미 표시를 선택하여 작업 팔레트 열기](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. 작업을 추가하려면 YAML의 끝에 커서를 놓거나 작업에 대한 지침을 추가하려는 곳에 커서를 놓습니다. **Azure IoT Edge** 를 검색하여 선택합니다. 작업의 매개 변수를 다음과 같이 입력합니다. 그런 후 **추가** 를 선택합니다.

   | 매개 변수 | 설명 |
   | --- | --- |
   | 작업 | **모듈 이미지 빌드** 를 선택합니다. |
   | .template.json 파일 | IoT Edge 솔루션이 포함된 리포지토리의 **deployment.template.json** 파일 경로를 제공합니다. |
   | 기본 플랫폼 | 대상으로 하는 IoT Edge 디바이스에 따라 모듈에 적합한 운영 체제를 선택합니다. |

   이 작업 및 해당 매개 변수에 대한 자세한 내용은 [Azure IoT Edge 작업](/azure/devops/pipelines/tasks/build/azure-iot-edge)을 참조하세요.

   ![작업 팔레트를 사용하여 파이프라인에 작업 추가](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > 각 작업이 추가되면 편집기는 추가된 줄을 자동으로 강조 표시합니다. 실수로 덮어쓰는 일을 방지하려면 작업을 추가하기 전에 줄을 선택 취소하고 다음 작업을 위한 새 공간을 제공합니다.

8. 다음 매개 변수를 사용하여 이 프로세스를 반복하여 작업 3개를 더 추가합니다.

   * 작업: **Azure IoT Edge**

     | 매개 변수 | 설명 |
     | --- | --- |
     | 작업 | **모듈 이미지 푸시** 를 선택합니다. |
     | 컨테이너 레지스트리 유형 | 기본 유형인 **Azure Container Registry** 를 사용합니다. |
     | Azure 구독 | 구독을 선택합니다. |
     | Azure Container Registry | 파이프라인에 사용하려는 레지스트리를 선택합니다. |
     | .template.json 파일 | IoT Edge 솔루션이 포함된 리포지토리의 **deployment.template.json** 파일 경로를 제공합니다. |
     | 기본 플랫폼 | 대상으로 하는 IoT Edge 디바이스에 따라 모듈에 적합한 운영 체제를 선택합니다. |

     이 작업 및 해당 매개 변수에 대한 자세한 내용은 [Azure IoT Edge 작업](/azure/devops/pipelines/tasks/build/azure-iot-edge)을 참조하세요.

   * 작업: **파일 복사**

     | 매개 변수 | 설명 |
     | --- | --- |
     | 원본 폴더 | 데이터를 복사할 원본 폴더입니다. 리포지토리의 루트는 Empty입니다. 파일이 리포지토리에 없으면 변수를 사용합니다. 예: `$(agent.builddirectory)`.
     | 콘텐츠 | `deployment.template.json` 및 `**/module.json`의 두 줄을 추가합니다. |
     | 대상 폴더 | 변수 `$(Build.ArtifactStagingDirectory)`를 지정합니다. 설명에 대한 자세한 내용은 [빌드 변수](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables)를 참조하세요. |

     이 작업 및 매개 변수에 대한 자세한 내용은 [파일 복사 작업](/azure/devops/pipelines/tasks/utility/copy-files)을 참조하세요.

   * 작업: **빌드 아티팩트 게시**

     | 매개 변수 | 설명 |
     | --- | --- |
     | 게시할 경로 | 변수 `$(Build.ArtifactStagingDirectory)`를 지정합니다. 설명에 대한 자세한 내용은 [빌드 변수](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables)를 참조하세요. |
     | 아티팩트 이름 | 기본 이름 `drop`을 지정합니다. |
     | 아티팩트 게시 위치 | 기본 위치 `Azure Pipelines`를 사용합니다. |

     이 작업 및 해당 매개 변수에 대한 자세한 내용은 [빌드 아티팩트 게시 작업](/azure/devops/pipelines/tasks/utility/publish-build-artifacts)을 참조하세요.

9. 오른쪽 위의 **저장 및 실행** 드롭다운에서 **저장** 을 선택합니다.

10. 연속 통합 트리거는 YAML 파이프라인에 대해 기본적으로 사용 설정되어 있습니다. 해당 설정을 편집하려면 파이프라인을 선택하고 오른쪽 위에 있는 **편집** 을 클릭합니다. 오른쪽 위에 있는 **실행** 단추 옆의 **추가 작업** 을 선택하고 **트리거** 로 이동합니다. 파이프라인 이름 아래에 **연속 통합** 이 사용 설정되었다고 표시됩니다. 트리거의 세부 정보를 보려면 **여기에서 YAML 연속 통합 트리거 재정의** 상자를 선택합니다.

    ![파이프라인의 트리거 설정을 검토하려면 추가 작업의 트리거를 참조하세요.](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

계속해서 다음 섹션에서 릴리스 파이프라인을 빌드합니다.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>다음 단계

* [IoT Edge용 Azure DevOps Starter](how-to-devops-starter.md)의 IoT Edge DevOps 샘플
* [단일 디바이스 또는 대규모 IoT Edge 배포에 대한 이해](module-deployment-monitoring.md)를 통해 IoT Edge 배포 이해
* [대규모 IoT Edge 모듈 배포 및 모니터링](how-to-deploy-at-scale.md)에서 배포를 생성, 업데이트 또는 삭제하는 단계를 연습합니다.