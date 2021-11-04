---
title: Azure DevOps를 사용한 지속적인 배포(미리 보기)
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 애플리케이션에 대한 연속 배포를 설정하는 방법을 알아봅니다. 연속 배포 워크플로를 지원하는 스크립트를 만듭니다.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: c83c52efb8ff6eb59d49fb930ae4edf0b2f9c335
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011848"
---
# <a name="continuous-deployment-with-azure-devops"></a>Azure DevOps를 사용한 지속적인 배포

이 문서에서는 사용자 지정 명령 애플리케이션에 대한 연속 배포를 설정하는 방법을 알아봅니다. CI/CD 워크플로를 지원하는 스크립트가 제공됩니다.

## <a name="prerequisite"></a>필수 요소
> [!div class = "checklist"]
> * 개발용 사용자 지정 명령 애플리케이션(DEV)
> * 프로덕션용 사용자 지정 명령 애플리케이션(PROD)
> * [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up) 가입

## <a name="exportimportpublish"></a>내보내기/가져오기/게시

스크립트는 [Cognitive Services 음성 도우미 - 사용자 지정 명령](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)에서 호스팅됩니다. bash 디렉터리의 스크립트를 리포지토리에 복제합니다. 동일한 경로를 유지 관리해야 합니다.

### <a name="set-up-a-pipeline"></a>파이프라인 설정 

1. **Azure DevOps - 파이프라인** 으로 이동하고 "새 파이프라인"을 클릭합니다.
1. **연결** 섹션에서 이러한 스크립트가 있는 리포지토리의 위치를 선택합니다.
1. **선택** 섹션에서 리포지토리를 선택합니다.
1. **구성** 섹션에서 "시작 파이프라인"을 선택합니다.
1. 다음에는 YAML 파일로 편집기가 표시되고 "steps" 섹션을 이 스크립트로 바꿉니다.

    ```yaml
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. 이러한 스크립트에서는 `westus2` 지역을 사용하고 있다고 가정합니다. 그렇지 않은 경우 적절히 작업 인수를 업데이트합니다.

    > [!div class="mx-imgBorder"]
    > ![인수의 지역 값을 강조하는 스크린샷](media/custom-commands/cicd-new-pipeline-yaml.png)

1. "저장 후 실행" 단추에서 드롭다운을 열고 "저장"을 클릭합니다.

### <a name="hook-up-the-pipeline-with-your-application"></a>애플리케이션에 파이프라인 연결

1. 파이프라인의 기본 페이지로 이동합니다.
1. 오른쪽 상단 모서리 드롭다운에서 **파이프라인 편집** 을 선택합니다. YAML 편집기가 표시됩니다. 
1. 오른쪽 상단에서 "실행" 단추 옆의 **변수** 를 선택합니다. **새 변수** 를 클릭합니다.
1. 다음 변수를 추가합니다.
    
    | 변수 | Description |
    | ------- | --------------- | ----------- |
    | SourceAppId | DEV 애플리케이션의 ID |
    | TargetAppId | PROD 애플리케이션의 ID |
    | SubscriptionKey | 두 애플리케이션 모두에 사용되는 구독 키 |
    | 문화권 | 애플리케이션의 문화권(예: en-us) |

    > [!div class="mx-imgBorder"]
    > ![작업 보내기 페이로드](media/custom-commands/cicd-edit-pipeline-variables.png)

1. "실행"을 클릭한 다음 실행 중인 "작업"을 클릭합니다. 

    "원본 앱 내보내기", "대상 앱으로 가져오기" 및 "대상 앱 학습 및 게시"가 포함된 실행 중인 작업 목록이 표시되어야 합니다.

## <a name="deploy-from-source-code"></a>소스 코드에서 배포

애플리케이션 정의를 리포지토리에 유지하려는 경우 소스 코드에서 배포를 위한 스크립트를 제공합니다. 스크립트는 bash에 있으므로 Windows를 사용하는 경우 [Linux 하위 시스템](/windows/wsl/install-win10)을 설치해야 합니다.

스크립트는 [Cognitive Services 음성 도우미 - 사용자 지정 명령](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)에서 호스팅됩니다. bash 디렉터리의 스크립트를 리포지토리에 복제합니다. 동일한 경로를 유지 관리해야 합니다.

### <a name="prepare-your-repository"></a>리포지토리 준비

1. 애플리케이션을 위한 디렉터리를 만듭니다. 이 예에서는 "apps"라는 디렉터리를 만듭니다.
1. 아래 bash 스크립트의 인수를 업데이트하고 실행합니다. 애플리케이션의 대화 모델을 myapp.json 파일로 가져옵니다.
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | 인수 | Description |
    | ------- | --------------- | ----------- |
    | region | 애플리케이션 지역(예: westus2) |
    | subscriptionkey | 음성 리소스의 구독 키 |
    | appid | 내보내려는 사용자 지정 명령의 애플리케이션 ID입니다. |

1. 이러한 변경 내용을 리포지토리에 푸시합니다.

### <a name="set-up-a-pipeline"></a>파이프라인 설정 

1. **Azure DevOps - 파이프라인** 으로 이동하고 "새 파이프라인"을 클릭합니다.
1. **연결** 섹션에서 이러한 스크립트가 있는 리포지토리의 위치를 선택합니다.
1. **선택** 섹션에서 리포지토리를 선택합니다.
1. **구성** 섹션에서 "시작 파이프라인"을 선택합니다.
1. 다음에는 YAML 파일로 편집기가 표시되고 "steps" 섹션을 이 스크립트로 바꿉니다.

    ```yaml
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > 이러한 스크립트에서는 westus2 지역을 사용하고 있다고 가정합니다. 그렇지 않은 경우 적절히 작업 인수를 업데이트합니다.

1. "저장 후 실행" 단추에서 드롭다운을 열고 "저장"을 클릭합니다.

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>대상 애플리케이션과 파이프라인 연결

1. 파이프라인의 기본 페이지로 이동합니다.
1. 오른쪽 상단 모서리 드롭다운에서 **파이프라인 편집** 을 선택합니다. YAML 편집기가 표시됩니다. 
1. 오른쪽 상단에서 "실행" 단추 옆의 **변수** 를 선택합니다. **새 변수** 를 클릭합니다.
1. 다음 변수를 추가합니다.

    | 변수 | Description |
    | ------- | --------------- | ----------- |
    | TargetAppId | PROD 애플리케이션의 ID |
    | SubscriptionKey | 두 애플리케이션 모두에 사용되는 구독 키 |
    | 문화권 | 애플리케이션의 문화권(예: en-us) |

1. "실행"을 클릭한 다음 실행 중인 "작업"을 클릭합니다.
    "앱 가져오기" 및 "앱 학습 및 게시"가 포함된 실행 중인 작업 목록이 표시됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub 샘플 참조](https://aka.ms/speech/cc-samples)
