---
title: 이미지 저장 및 배포
description: 이 문서에서는 Azure DevTest Labs에 이미 만들어진 VM(가상 머신)에서 사용자 지정 이미지를 저장하는 단계를 설명합니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 0c2ecd3f64fbc6292563446e701e306815714d1e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397285"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>사용자 지정 이미지 저장 및 여러 랩에 배포
이 문서에서는 이미 만들어진 VM(가상 머신)에서 사용자 지정 이미지를 저장하는 단계를 설명합니다. 사용자 지정 이미지를 조직의 다른 DevTest Labs에 배포하는 방법도 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소
다음 항목이 이미 준비되어 있어야 합니다.

- Azure DevTest Labs의 이미지 팩터리 랩
- 이미지 팩터리를 자동화하는 데 사용되는 Azure DevOps 프로젝트
- 스크립트 및 구성이 포함된 소스 코드 위치(이 예제에서는 이전 단계에서 언급한 것과 동일한 DevOps 프로젝트)
- Azure PowerShell 작업을 오케스트레이션 하는 빌드 정의입니다.

필요한 경우 [Azure DevOps에서 이미지 팩터리 실행](image-factory-set-up-devops-lab.md)의 단계에 따라 이러한 항목을 만들거나 설정합니다. 

## <a name="save-vms-as-generalized-vhds"></a>VM을 일반화된 VHD로 저장
기존 VM을 일반화된 VHD로 저장합니다.  기존 VM을 일반화된 VHD로 저장하는 샘플 PowerShell 스크립트가 있습니다. 이 스크립트를 사용하려면 먼저 다음 이미지와 같이 다른 **Azure Powershell** 작업을 빌드 정의에 추가합니다.

![Azure PowerShell 추가 단계](./media/save-distribute-custom-images/powershell-step.png)

목록에 새 작업이 있으면 다음 이미지와 같이 모든 세부 정보를 채울 수 있도록 해당 항목을 선택합니다. 

![PowerShell 설정](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>일반화된 사용자 지정 이미지 및 특수화된 사용자 지정 이미지 비교
[Azure Portal](https://portal.azure.com)에서 가상 머신의 사용자 지정 이미지를 만드는 경우 일반화된 사용자 지정 이미지 또는 특수화된 사용자 지정 이미지를 포함하도록 선택할 수 있습니다.

- **특수화된 사용자 지정 이미지:** 머신에서 Sysprep/프로비전 해제가 실행되지 않았습니다. 즉, 이미지는 기존 가상 머신(스냅샷)에 있는 OS 디스크와 정확하게 일치하는 복사본입니다.  새 컴퓨터에는이 사용자 지정 이미지와 동일한 파일, 응용 프로그램, 사용자 계정 및 컴퓨터 이름이 있습니다.
- **일반화된 사용자 지정 이미지:** 머신에서 Sysprep/프로비전 해제가 실행되었습니다.  이 프로세스는 사용자 계정을 제거 하 고, 컴퓨터 이름을 제거 하 고, 사용자 레지스트리 하이브를 제거 합니다. 다른 가상 컴퓨터를 만들 때 이미지를 사용자 지정할 수 있도록 이미지를 일반화 하는 것이 목표입니다.  Sysprep을 실행 하 여 가상 컴퓨터를 일반화 하면 프로세스에서 현재 가상 컴퓨터를 제거 합니다. 현재 가상 컴퓨터는 더 이상 작동 하지 않습니다.

이미지 팩터리의 사용자 지정 이미지를 맞추기 위한 스크립트는 이전 단계에서 만든 가상 컴퓨터에 대 한 Vhd를 저장 합니다. 이 스크립트는 Azure의 리소스에 대 한 태그를 기반으로 Vhd를 식별 합니다.

## <a name="update-configuration-for-distributing-images"></a>이미지 배포에 대한 구성 업데이트
프로세스의 다음 단계에서는 이미지 팩터리 랩의 사용자 지정 이미지를 해당 이미지를 필요로 하는 다른 랩으로 푸시합니다. 이 프로세스의 핵심적인 부분은 **labs.json** 구성 파일입니다. 이 파일은 이미지 팩터리에 포함된 **구성** 폴더에서 찾을 수 있습니다.

labs.json 구성 파일에 나열되는 두 가지 핵심 사항은 다음과 같습니다.

- 구독 ID 및 랩 이름을 사용하여 특정 대상 랩을 고유하게 식별합니다.
- 구성 루트의 상대 경로로서 랩으로 푸시해야 하는 특정 이미지 세트입니다. 전체 폴더를 지정하여 해당 폴더의 모든 이미지를 가져올 수도 있습니다.

다음은 두 개의 랩을 나열 하는 예제 labs 파일입니다. 이 경우 두 개의 다른 랩에 이미지를 배포 하 게 됩니다.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>빌드 작업 만들기
이 문서의 앞에서 설명한 것과 동일한 단계를 사용 하 여 빌드 정의에 **Azure PowerShell** 빌드 작업을 추가 합니다. 다음 이미지에 표시된 것처럼 세부 정보를 채웁니다. 

![이미지를 배포 하는 빌드 작업의 스크린샷](./media/save-distribute-custom-images/second-build-task-powershell.png)

매개 변수는 다음과 같습니다. `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

이 작업에서는 이미지 팩터리에 있는 모든 사용자 지정 이미지를 사용하며 Labs.json 파일에 정의된 모든 랩으로 이미지를 푸시합니다.

## <a name="queue-the-build"></a>빌드 큐 대기
배포 빌드 작업이 완료 되 면 새 빌드를 큐에 대기 하 여 모든 것이 작동 하는지 확인 합니다. 빌드가 성공적으로 완료 된 후에는 새 사용자 지정 이미지가 실습실. json 구성 파일에 입력 한 대상 랩에 표시 됩니다.

## <a name="next-steps"></a>다음 단계
이 시리즈의 다음 문서에서는 [보존 정책 설정 및 정리 스크립트 실행](image-factory-set-retention-policy-cleanup.md)의 보존 정책 및 정리 단계를 사용하여 이미지 팩터리를 업데이트합니다.
