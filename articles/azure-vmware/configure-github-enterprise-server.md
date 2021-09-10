---
title: Azure VMware Solution에서 GitHub Enterprise Server 구성
description: Azure VMware Solution 프라이빗 클라우드에서 GitHub Enterprise Server를 설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 07/07/2021
ms.openlocfilehash: 7b3289742c765e6b809cd96db3bfa51d8407a282
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538646"
---
# <a name="configure-github-enterprise-server-on-azure-vmware-solution"></a>Azure VMware Solution에서 GitHub Enterprise Server 구성

이 문서에서는 Azure VMware Solution 프라이빗 클라우드에 [GitHub.com](https://github.com/)의 “온-프레미스” 버전인 GitHub Enterprise Server를 설정합니다. 시나리오에서는 GitHub Actions에서 분당 최대 25개 작업을 실행하는 최대 3,000명의 개발자를 지원할 수 있는 GitHub Enterprise Server 인스턴스를 다룹니다. GitHub Actions와 같은 (작성 시점에서의) 미리 보기 기능 설정이 포함되어 있습니다. 특정 요구 사항에 맞게 설정을 사용자 지정하려면 [VMware에서 GitHub Enterprise Server 설치](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)에 나열된 요구 사항을 검토하세요.

## <a name="before-you-begin"></a>시작하기 전에

GitHub Enterprise Server에는 유효한 라이선스 키가 필요합니다. [무료 평가판 라이선스](https://enterprise.github.com/trial)에 등록할 수 있습니다. 통합을 통해 GitHub Enterprise Server의 기능을 확장하려는 경우 5개의 무료 개발자 라이선스 자격이 있을 수 있습니다. [GitHub의 파트너 프로그램](https://partner.github.com/)을 통해 이 라이선스를 신청하세요.

## <a name="install-github-enterprise-server-on-vmware"></a>VMware에 GitHub Enterprise Server 설치

1. VMware ESXi/vSphere(OVA)용 [GitHub Enterprise Server의 최신 릴리스](https://enterprise.github.com/releases/2.19.0/download)를 다운로드하고, 다운로드한 [OVA 템플릿을 배포](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html)합니다.

   :::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="VMware에 GitHub Enterprise Server 설치 옵션을 보여 주는 스크린샷"::: 

   :::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="OVA 템플릿 배포 메뉴 옵션을 보여 주는 스크린샷":::    

1. 새 가상 머신에 인식 가능한 이름을 제공합니다(예: GitHubEnterpriseServer). 인스턴스가 업그레이드될 때 이러한 세부 정보가 오래된 정보가 될 수 있으므로 VM 이름에 릴리스 정보를 포함할 필요가 없습니다. 

1. 지금은 모든 기본값을 선택하고(곧 이러한 세부 정보를 편집 함) OVA를 가져올 때까지 기다립니다.

1. 가져온 후에는 요구 사항에 따라 [하드웨어 구성을 조정](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance)합니다. 예제 시나리오에서는 다음과 같은 구성이 필요합니다.

   | 리소스 | 표준 설정 | 표준 설정 + "베타 기능"(Actions) |
   | --- | --- | --- |
   | vCPU | 4 | 8 |
   | 메모리 | 32GB | 61GB |
   | 연결된 스토리지 | 250GB | 300GB |
   | 루트 스토리지 | 200GB | 200GB |

   요구 사항이 달라질 수 있습니다. [VMware에 GitHub Enterprise Server 설치](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)에서 하드웨어 고려 사항에 대한 지침을 참조하세요. 또한 상황에 따라 하드웨어 구성을 사용자 지정하려면 [VMware에 대한 CPU 또는 메모리 리소스 추가](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware)를 참조하세요.

## <a name="configure-the-github-enterprise-server-instance"></a>GitHub Enterprise Server 인스턴스 구성

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="GitHub Enterprise 설치 창의 스크린샷"::: 

새로 프로비저닝된 VM(가상 머신)이 켜진 후 [브라우저를 통해 VM을 구성](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance)합니다. 라이선스 파일을 업로드하고 관리 콘솔 암호를 설정해야 합니다. 이 암호는 안전한 위치에 기록해 두어야 합니다.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="새 SSH 키를 추가하는 GitHub Enterprise SSH 액세스 화면의 스크린샷":::    

최소한 다음 단계를 수행하는 것이 좋습니다.

1. [SSH를 통해 관리 셸에 액세스](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)할 수 있도록 퍼블릭 SSH 키를 관리 콘솔에 업로드합니다. 

2. 신뢰할 수 있는 인증 기관에서 서명한 인증서를 사용할 수 있도록 [인스턴스에서 TLS를 구성](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls)합니다. 설정을 적용합니다.

   :::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="인스턴스에 적용되는 설정을 보여 주는 스크린샷":::

1. 인스턴스가 다시 시작되는 동안 GitHub Actions에 사용할 Blob Storage를 구성합니다.

   >[!NOTE]
   >GitHub Actions는 [현재 GitHub Enterprise Server 릴리스 2.22에 대한 제한된 베타 버전으로 제공](https://docs.github.com/en/enterprise/admin/github-actions)됩니다.
    
   GitHub Enterprise Server(현재 "베타" 기능으로 제공)에서 GitHub Actions를 사용하려면 외부 Blob storage가 필요합니다. Actions는 이 외부 Blob Storage를 사용하여 아티팩트와 로그를 저장합니다. GitHub Enterprise Server의 Actions는 [Azure Blob Storage를 스토리지 공급자로 지원](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements)합니다(기타 일부). 따라서 [스토리지 계정 유형](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts)의 BlobStorage를 사용하여 새 Azure 스토리지 계정을 프로비저닝합니다.
    
   :::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Azure Blob Storage 계정을 프로비저닝하기 위해 입력할 인스턴스 세부 정보를 보여 주는 스크린샷":::
    
1. 새 BlobStorage 리소스 배포가 완료되면 연결 문자열(액세스 키에서 사용 가능)을 저장합니다. 곧 이 문자열이 필요합니다.

1. 인스턴스가 다시 시작된 후 인스턴스에서 새 관리자 계정을 만듭니다. 이 사용자의 암호를 기록해 두어야 합니다.

   :::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="GitHub Enterprise의 관리자 계정 만들기를 보여 주는 스크린샷":::

### <a name="other-configuration-steps"></a>다른 구성 단계

프로덕션에 사용하도록 인스턴스를 강화하려면 다음과 같은 선택적 설정 단계를 수행하는 것이 좋습니다.

1. 보호를 위한 [고가용성](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) 구성:

    - 소프트웨어 충돌(OS 또는 애플리케이션 수준)
    - 하드웨어 오류(스토리지, CPU, RAM 등)
    - 가상화 호스트 시스템 오류
    - 논리적으로 또는 물리적으로 손상된 네트워크

2. [backup-utilities](https://github.com/github/backup-utils)를 [구성](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance)하여 주 인스턴스와 별개인 가용성에 호스팅된 재해 복구용 버전 지정된 스냅샷을 제공합니다.
3. 유효한 TLS 인증서를 사용하여 [하위 도메인 격리를 설정](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)하고 사이트 간 스크립팅 및 기타 관련 취약성을 완화합니다.


## <a name="set-up-the-github-actions-runner"></a>GitHub Actions 실행기 설정

> [!NOTE]
> GitHub Actions는 [현재 GitHub Enterprise Server 릴리스 2.22에 대한 제한된 베타 버전으로 제공](https://docs.github.com/en/enterprise/admin/github-actions)됩니다.

이 시점에서 관리자 계정이 생성된 GitHub Enterprise Server 인스턴스를 실행해야 합니다. GitHub Actions에서 지속성을 위해 사용할 외부 Blob Storage도 있어야 합니다.

GitHub Actions를 실행할 위치를 만듭니다. 다시 Azure VMware Solution을 사용합니다.

1. 클러스터에서 새 VM을 프로비저닝하고 [Ubuntu Server의 최신 릴리스](http://releases.ubuntu.com/20.04.1/)를 기준으로 사용합니다.

   :::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="새 VM을 프로비저닝할 가상 머신 이름과 위치를 보여 주는 스크린샷":::

1. 컴퓨팅 리소스, 스토리지, 호환성을 선택하여 설정을 계속 진행합니다.

1. VM에 설치하려는 게스트 OS를 선택합니다.

   :::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="VM에 설치할 게스트 OS 제품군과 게스트 OS 버전을 보여 주는 스크린샷":::

1. VM을 만든 후에는 전원을 켜고 SSH를 통해 연결합니다.

1. GitHub Actions 워크플로에서 작업을 실행하는 [Actions 실행기](https://github.com/actions/runner) 애플리케이션을 설치합니다. [릴리스 페이지](https://github.com/actions/runner/releases)에서 또는 다음 빠른 스크립트를 실행하여 Actions 실행기의 최신 Linux x64 릴리스를 찾고 다운로드합니다. 이 스크립트를 사용하려면 사용자의 VM에는 curl 및 [jq](https://stedolan.github.io/jq/)가 모두 있어야 합니다.

   ```bash
   LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \
    
   jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )
    
   DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \
    
   jq -r '.browser\_download\_url' )
    
   curl -OL $DOWNLOAD\_URL
   ```
    
   이제 VM에 actions-runner-linux-arm64-\*.tar.gz 파일이 로컬로 있어야 합니다. 이 tarball를 로컬로 추출합니다.
    
   ```bash
   tar xzf actions-runner-linux-arm64-\*.tar.gz
   ```
    
   이 추출은 `config.sh` 및 `run.sh` 스크립트를 포함하여 몇몇 파일의 압축을 로컬로 풉니다.

## <a name="enable-github-actions"></a>GitHub Actions 사용

>[!NOTE]
>GitHub Actions는 [현재 GitHub Enterprise Server 릴리스 2.22에 대한 제한된 베타 버전으로 제공](https://docs.github.com/en/enterprise/admin/github-actions)됩니다.

GitHub Enterprise Server 인스턴스에서 GitHub Actions를 구성하고 사용하도록 설정합니다. 

1. [SSH를 통해 GitHub Enterprise Server 인스턴스의 관리 셸에 액세스](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)하고 다음 명령을 실행합니다.

1. Blob Storage 연결 문자열을 포함하는 환경 변수를 설정합니다.

   ```bash
   export CONNECTION\_STRING="<your connection string from the blob storage step>"
   ```    

1. Actions 스토리지를 구성합니다.
    
   ```bash
   ghe-config secrets.actions.storage.blob-provider azure
  
   ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING`      
   ```

1. 설정을 적용합니다.

   ```bash
   ghe-config-apply
   ```    

1. 사전 검사를 실행하여 GitHub Enterprise Server에서 Actions에 필요한 추가 소프트웨어를 설치합니다.
    
   ```bash
   ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"
   ```

1. Actions를 사용하도록 설정하고 구성을 다시 적용합니다.
 
   ```bash
   ghe-config app.actions.enabled true
    
   ghe-config-apply      
   ```

1. Blob Storage의 상태를 확인합니다.

   ```bash
   ghe-actions-check -s blob
   ```

   ‘Blob Storage가 정상 상태입니다.’라는 출력이 표시됩니다.

1. 이제 **GitHub Actions** 가 구성되었으므로 사용자가 사용할 수 있도록 설정합니다. GitHub Enterprise Server 인스턴스에 관리자 권한으로 로그인하고 페이지의 오른쪽 위에서 :::image type="icon" source="media/github-enterprise-server/rocket-icon.png":::을 선택합니다. 

1. 왼쪽 사이드바에서 **Enterprise 개요**, **정책**, **Actions** 를 차례로 선택하고 **모든 조직에 대해 Actions를 사용하도록 설정** 옵션을 선택합니다.

1. **자체 호스팅 실행기** 탭에서 실행기를 구성합니다. 드롭다운에서 **새로 추가** 를 선택하고 **새 실행기** 를 선택합니다. 실행할 명령 세트가 표시됩니다.

1. 명령을 복사하여 실행기를 **구성** 합니다. 예를 들면 다음과 같습니다.

   ```bash
   ./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA
   ```

1. `config.sh` 명령을 복사하고 Actions 실행기(이전에 만들었음)의 세션에 붙여넣습니다.

   :::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="GitHub Actions 실행기 등록과 설정을 보여 주는 스크린샷":::

1. `./run.sh` 명령을 사용하여 실행기를 ‘실행’합니다.

   >[!TIP]
   >엔터프라이즈의 조직에서 이 실행기를 사용할 수 있도록 설정하려면 조직 액세스를 편집합니다. 조직의 하위 집합과 특정 리포지토리로 액세스를 제한할 수도 있습니다.
   >
   >:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="자체 호스팅 실행기의 액세스를 편집하는 방법 스크린샷":::


## <a name="optional-configure-github-connect"></a>(선택 사항) GitHub Connect 구성

이 단계는 선택 사항이지만 GitHub.com에서 사용할 수 있는 오픈 소스 작업을 사용하려는 경우에 권장됩니다. 이를 통해 워크플로에서 재사용 가능한 작업을 참조하여 다른 사용자의 작업을 빌드할 수 있습니다.

GitHub Connect를 사용하도록 설정하려면 [GitHub Connect를 사용하여 GitHub.com 작업에 대한 자동 액세스 사용 설정](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect)의 단계를 수행합니다.

GitHub Connect가 사용 설정되면 **GitHub.com에서 작업을 사용할 서버** 를 선택합니다.

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="서버가 워크플로 실행 사용에서 GitHub.com의 작업을 사용할 수 있음을 보여 주는 스크린샷":::

## <a name="set-up-and-run-your-first-workflow"></a>첫 번째 워크플로 설정 및 실행

이제 Actions 및 GitHub Connect가 설정되었으므로 이 모든 작업을 사용하도록 하겠습니다. 다음은 [octokit/request-action](https://github.com/octokit/request-action)을 참조하는 훌륭한 워크플로 예제이며, GitHub Actions를 통해 지원되는 GitHub API를 사용하는 상호 작용을 통해 GitHub를 "스크립팅"할 수 있습니다.

이 기본 워크플로에서는 `octokit/request-action`을 사용하여 API를 사용하는 GitHub에서 이슈를 엽니다.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="예제 워크플로의 스크린샷":::

>[!NOTE]
>GitHub.com은 작업을 호스트하지만 GitHub Enterprise Server에서 실행될 때 자동으로 GitHub Enterprise Server API를 사용합니다.

GitHub Connect를 사용하도록 선택하지 않은 경우 다음 대체 워크플로를 사용할 수 있습니다.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="대체 예제 워크플로의 스크린샷":::

1. 인스턴스의 리포지토리로 이동하여 위의 워크플로를 `.github/workflows/hello-world.yml`과 같이 추가합니다.

   :::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="또 다른 대체 예제 워크플로의 스크린샷":::

1. 리포지토리의 **작업** 탭에서 워크플로가 실행되기를 기다립니다.

   :::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="실행된 대체 예제 워크플로의 스크린샷":::

   실행기에서 처리되는 것을 확인할 수 있습니다.

   :::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="실행기에서 처리된 워크플로의 스크린샷":::

모든 사항이 성공적으로 실행되면 리포지토리에 "Hello world"라는 새 이슈가 표시될 것입니다.

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="github-actions에서 생성된 GitHub의 Hello world 이슈의 스크린샷":::

축하합니다! Azure VMware Solution 프라이빗 클라우드에서 실행되는 GitHub Enterprise Server에서 첫 번째 Actions 워크플로를 완료했습니다.

이 문서에서는 Azure VMware Solution 프라이빗 클라우드 위에 GitHub Enterprise Server(자체 호스팅 GitHub.com)의 새 인스턴스를 설정합니다. 이 인스턴스에는 GitHub Actions에 대한 지원이 포함되며 로그와 아티팩트의 지속성을 위해 Azure Blob Storage를 사용합니다. 하지만 이제 GitHub Actions로 수행할 수 있는 작업의 일부만 살펴보았습니다. [GitHub의 마켓플레이스](https://github.com/marketplace)에서 Actions의 목록을 살펴보거나 [직접 만들어 보세요](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>다음 단계

Azure VMware Solution 프라이빗 클라우드에서 GitHub Enterprise Server를 설정하는 방법을 알아보았습니다. 이제 다음에 대해 알아보고자 할 수 있습니다. 

- [GitHub Actions를 시작하는 방법](https://docs.github.com/en/actions)
- [베타 프로그램에 조인하는 방법](https://resources.github.com/beta-signup/)
- [GitHub Enterprise Server 관리](https://githubtraining.github.io/admin-training/#/00_getting_started)
