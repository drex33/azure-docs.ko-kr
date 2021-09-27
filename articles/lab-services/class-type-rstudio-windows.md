---
title: Azure Lab Services를 사용하여 Windows에서 R 및 RStudio로 랩 설정
description: Windows에서 RStudio를 사용하여 R을 가르치기 위해 랩을 설정하는 방법 알아보기
author: emaher
ms.topic: article
ms.date: 08/26/2021
ms.author: enewman
ms.openlocfilehash: 5d081e21723fa73d84beec12e8602234ad5e8074
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757385"
---
# <a name="set-up-a-lab-to-teach-r-on-windows"></a>Windows에서 R을 가르칠 랩 설정

[R](https://www.r-project.org/about.html)은 통계 컴퓨팅 및 그래픽에 사용되는 오픈 소스 언어입니다.  유전학부터 자연어 처리, 금융 데이터 분석까지의 통계 분석에 사용됩니다.  R은 [대화형 명령줄](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line) 환경을 제공합니다.  [RStudio](https://www.rstudio.com/products/rstudio/)는 R 언어에 사용할 수 있는 IDE(대화형 개발 환경)입니다.  무료 버전은 코드 편집 도구, 통합 디버깅 환경 및 패키지 개발 도구를 제공합니다.

이 문서에서는 통계 컴퓨팅을 사용해야 하는 클래스의 구성 요소로서 RStudio와 R에만 중점을 둡니다.  [딥 러닝](class-type-deep-learning-natural-language-processing.md)과 [Python 및 Jupyter Notebooks](class-type-jupyter-notebook.md) 클래스 유형은 RStudio를 다르게 설정합니다.  각 문서에서는 RStudio를 비롯한 많은 [데이터 과학 관련 도구](../machine-learning/data-science-virtual-machine/tools-included.md)가 사전 설치된 [Linux(Ubuntu)용 Data Science Virtual Machine](https://azuremarketplace.microsoft.com/en-US/marketplace/apps/microsoft-dsvm.ubuntu-1804) 마켓플레이스 이미지를 사용하는 방법을 설명합니다.  

## <a name="lab-account-configuration"></a>랩 계정 구성

이 랩을 설정하려면 Azure 구독과 랩 계정으로 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정 만들기에 대한 자세한 내용은 [랩 계정 설정 방법](./tutorial-setup-lab-account.md)의 자습서를 참조하세요. 기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

다음 표에서 설명하는 것과 같이 랩 계정 설정을 사용합니다.

| 랩 계정 설정 | Instructions |
| -------------------- | ----- |
| [피어 가상 네트워크 사용](how-to-connect-peer-virtual-network.md) | 다음과 같은 경우 사용하도록 설정합니다.<ul><li>클래스에는 공유 R 서버가 필요합니다.</li><li>클래스에는 학생 VM이 아닌 외부에 저장하려는 대용량 데이터 파일이 필요합니다.</li></ul> |

> [!IMPORTANT]
> 피어 가상 네트워크를 사용하도록 선택하는 경우 랩을 만들기 전에 이 작업을 수행해야 합니다.

## <a name="lab-configuration"></a>랩 구성

새 랩을 만들고 필요한 설정을 적용하는 방법에 대한 지침은 [자습서: 클래스룸 랩 설정](tutorial-setup-classroom-lab.md)을 참조하세요.  랩을 만들 때 다음 설정을 적용합니다.

| 랩 설정 | 값 및 설명 |
| ------------ | ------------------ |
| Virtual Machine 크기 | 소형 GPU(컴퓨팅)|
| VM 이미지 | Windows 10 Pro. 버전 2004 |

## <a name="external-resource-configuration"></a>외부 리소스 구성

일부 클래스에서는 대용량 데이터 파일과 같은 파일을 외부에 저장해야 합니다.  옵션 및 설정 지침은 [Azure Lab Services에서 외부 파일 스토리지 사용](how-to-attach-external-storage.md)을 참조하세요.

학생을 위한 공유 RStudio 서버를 선택하는 경우 랩이 만들어지기 전에 서버를 설정해야 합니다.  공유 서버를 설정하는 방법에 대한 자세한 내용은 [Azure Lab Services에서 공유 리소스로 랩을 만드는 방법](how-to-create-a-lab-with-shared-resource.md)을 참조하세요.  RStudio Server를 만드는 방법에 대한 지침은 [Debian & Ubuntu에 대한 RStudio Server 다운로드](https://www.rstudio.com/products/rstudio/download-server/debian-ubuntu/) 및 [RStudio Server 오픈 소스 액세스](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started)를 참조하세요.

## <a name="template-configuration"></a>템플릿 구성

템플릿 컴퓨터가 만들어지면 컴퓨터를 시작하고 연결하여 [R](https://docs.rstudio.com/resources/install-r/) 및 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)을 설치합니다.  

### <a name="install-r"></a>R 설치

1. [Windows용 R 최신 설치 프로그램](https://cran.r-project.org/bin/windows/base/release.html)을 다운로드합니다.  사용 가능한 전체 버전 목록은 [Windows용 R 다운로드 페이지](https://cran.r-project.org/bin/windows/base/)를 참조하세요.
2. 설치 관리자를 실행합니다.
    1. **설정 언어 선택** 프롬프트에서 원하는 언어를 선택하고 **확인** 을 선택합니다.
    2. 설치 프로그램의 **정보** 페이지에서 라이선스 계약을 읽습니다.  계약에 동의하고 계속하려면 **다음** 을 선택합니다.
    3. **대상 위치 선택** 페이지에서 기본 설치 위치를 수락하고 **다음** 을 선택합니다.
    4. **구성 요소 선택** 페이지에서 선택적으로 **32비트 파일** 옵션을 선택 취소합니다.  32비트 및 62비트 버전의 R을 모두 실행하는 방법에 대한 자세한 내용은 [32비트 및 64비트 R을 모두 동일한 컴퓨터에 설치할 수 있나요?](https://cran.r-project.org/bin/windows/base/rw-FAQ.html#Can-both-32_002d-and-64_002dbit-R-be-installed-on-the-same-machine_003f) FAQ를 참조하세요.
    5. **시작 옵션** 페이지에서 시작 옵션을 **아니요(기본값 적용)** 로 둡니다.  R GUI(그래픽 사용자 인터페이스)에서 별도의 창(SDI) 또는 일반 텍스트 도움말을 사용하도록 하려면 **예(시작 사용자 지정)** 라디오 단추를 선택하고 다음에서 시작 옵션을 마법사 페이지로 변경합니다.
    6. **시작 메뉴 폴더 선택** 페이지에서 **다음** 을 선택합니다.
    7. **추가 작업 선택** 페이지에서 선택적으로 **바탕화면 바로가기 만들기** 를 선택합니다.  **다음** 을 선택합니다.
    8. **설치** 페이지에서 설치가 완료될 때까지 기다립니다.
    9. **Windows용 R 완료** 페이지에서 **마침** 을 선택합니다.

PowerShell을 사용하여 R 설치를 실행할 수도 있습니다.  코드 예는 32비트 구성 요소 없이 R을 설치하고 최신 버전의 R에 대한 바탕 화면 아이콘을 추가하는 방법을 보여 줍니다. 설치 프로그램에 대한 명령줄 옵션의 전체 목록을 보려면 [명령줄 매개 변수 설정](https://jrsoftware.org/ishelp/index.php?topic=setupcmdline)을 참조하세요.

```powershell
#Avoid prompt to setup Internet Explorer if we must parse download page
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Internet Explorer\Main" -Name "DisableFirstRunCustomize" -Value 2

$outputfile = "R-win.exe"

$result = Invoke-WebRequest "https://cran.r-project.org/bin/windows/base/release.html" -OutFile $outputfile -PassThru

#Check if we need to parse the result ourselves, to find the latest version of R
if ($result.StatusCode -eq '200' -and $result.Headers["Content-Type"] -eq 'text/html')
{
    $metaTag = $result.ParsedHtml.Head.children | Where-Object {$_.nodeName -eq 'META'}
    if ($metaTag.content  -match "R-\d+\.\d+\.\d+-win.exe"){
        $outputfile = $Matches.0

        #Download latest version
        Invoke-WebRequest "https://cran.r-project.org/bin/windows/base/$outputfile" -OutFile $outputfile
    }else{
        Write-Error "Unable to find latest version of R installer.  Go to https://cran.r-project.org/bin/windows/base/release.html to download manually."
    }
}

#Install Silently
$installPath = Get-Item -Path $outputfile
Start-Process -FilePath $installPath.FullName -ArgumentList "/VERYSILENT /LOG=r-install.log /NORESTART /COMPONENTS=""main,x64,translations"" /MERGETASKS=""desktopicon"" /LANG=""en""" -NoNewWindow -Wait
```

### <a name="install-rstudio"></a>RStudio 설치

R이 로컬에 설치되었으므로 RStudio IDE를 설치할 수 있습니다.  RStudio Desktop의 무료 버전을 설치하겠습니다.  사용 가능한 모든 버전은 [RStudio 다운로드](https://www.rstudio.com/products/rstudio/download/)를 참조하세요.

1. Windows 10용 [R Studio 설치 프로그램](https://www.rstudio.com/products/rstudio/download/#download)을 다운로드합니다.  설치 프로그램 파일은 `rstudio-{version}.exe` 형식입니다.  
2. RStudio 설치 프로그램을 실행합니다.
    1. **RStudio 설정** 마법사의 **RStudio 설정에 오신 것을 환영합니다** 페이지에서 **다음** 을 선택합니다.
    2. **설치 위치 선택** 페이지에서 **다음** 을 선택합니다.
    3. **시작 메뉴 폴더 선택** 페이지에서 **설치** 를 선택합니다.
    4. **설치** 페이지에서 설치가 완료될 때까지 기다립니다.
    5. **RStudio 설정 완료** 페이지에서 **마침** 을 선택합니다.

PowerShell을 사용하여 RStudio 설치 단계를 실행하려면 다음 명령을 실행합니다.  명령을 실행하기 전에 RStudio 버전을 사용할 수 있는지 확인하려면 [RStudio 다운로드](https://www.rstudio.com/products/rstudio/download/)를 참조하세요.

```powershell
$rstudiover="1.4.1717"
$outputfile = "RStudio-$rstudiover.exe"

#Download installer executable
Invoke-WebRequest "https://download1.rstudio.org/desktop/windows/RStudio-$rstudiover.exe" -OutFile $outputfile

#Install RStudio silently
$installPath = Get-Item -Path $outputfile
Start-Process -FilePath $installPath.FullName -ArgumentList "/S" -NoNewWindow -Wait
```

### <a name="cran-packages"></a>CRAN 패키지

[유용한 R 패키지의 빠른 목록](https://support.rstudio.com/hc/articles/201057987-Quick-list-of-useful-R-packages) 문서에 표시된 대로 R 대화형 세션에서 `install.packages(“package name”)` 명령을 사용합니다.  또는 도구 -> RStudio의 패키지 메뉴 항목 설치를 활용합니다.

패키지를 찾는 데 도움이 필요하면 [작업별 패키지 목록](https://cran.r-project.org/web/views/) 또는 [알파벳순 패키지 목록](https://cloud.r-project.org/web/packages/available_packages_by_name.html)을 참조하세요.

## <a name="cost"></a>Cost

이 수업의 예상 비용 예시를 살펴보겠습니다.  25명의 학생으로 구성된 클래스가 있다고 가정합니다. 각 학생에게는 20시간 일정의 클래스 시간이 있습니다.  학생에게는 일정에 있는 클래스 시간 외에도 과제를 위해 추가적으로 10시간이 할당됩니다.  선택한 가상 머신 크기는 **소형 GPU(컴퓨팅)** 로, 랩 유닛 139개입니다.

학생 25명 &times;(예약 시간 20시간 + 할당 시간 10시간) &times; 랩 단위 139개 &times; 시간당 0.01 USD = 1042.5 USD

> [!IMPORTANT]
> 예상 비용은 예제 용도로만 사용됩니다.  최신 가격 책정 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 템플릿 이미지를 랩에 게시할 수 있습니다. 추가 지침은 [템플릿 VM 게시](how-to-create-manage-template.md#publish-the-template-vm)를 참조하세요.

랩을 설정할 때 다음 문서를 참조하세요.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)