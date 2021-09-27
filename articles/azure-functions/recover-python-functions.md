---
title: Azure Functions에서 Python 함수 앱 문제 해결
description: Python 함수 문제를 해결하는 방법을 알아봅니다.
ms.topic: article
ms.date: 07/29/2020
ms.custom: devx-track-python
ms.openlocfilehash: 3d6be539763e655405a1e46b628e68cb5690e4f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654350"
---
# <a name="troubleshoot-python-errors-in-azure-functions"></a>Azure Functions에서 Python 오류 문제 해결

다음은 Python 함수의 일반적인 문제에 대한 문제 해결 가이드 목록입니다.

* [ModuleNotFoundError 및 ImportError](#troubleshoot-modulenotfounderror)
* ['cygrpc'를 가져올 수 없음](#troubleshoot-cannot-import-cygrpc)
* [Python이 코드 137로 종료](#troubleshoot-python-exited-with-code-137)
* [Python이 코드 139로 종료](#troubleshoot-python-exited-with-code-139)

## <a name="troubleshoot-modulenotfounderror"></a>ModuleNotFoundError 문제 해결

이 섹션은 Python 함수 앱에서 모듈 관련 오류를 해결하는 데 도움이 됩니다. 이러한 오류는 일반적으로 다음과 같은 Azure Functions 오류 메시지를 생성합니다.

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

이 오류는 Python 함수 앱에서 Python 모듈을 로드하지 못하는 경우 발생합니다. 이 오류의 근본 원인은 다음과 같은 문제 중 하나입니다.

* [패키지를 찾을 수 없습니다.](#the-package-cant-be-found)
* [적절한 Linux 휠을 사용하는 패키지가 해결되지 않았습니다.](#the-package-isnt-resolved-with-proper-linux-wheel)
* [패키지가 Python 인터프리터 버전과 호환되지 않습니다.](#the-package-is-incompatible-with-the-python-interpreter-version)
* [패키지가 다른 패키지와 충돌합니다.](#the-package-conflicts-with-other-packages)
* [패키지가 Windows 또는 macOS 플랫폼만 지원합니다.](#the-package-only-supports-windows-or-macos-platforms)

### <a name="view-project-files"></a>프로젝트 파일 보기

문제의 실제 원인을 확인하려면 함수 앱에서 실행되는 Python 프로젝트 파일을 가져와야 합니다. 로컬 컴퓨터에 프로젝트 파일이 없는 경우 다음 방법 중 하나로 가져올 수 있습니다.

* 함수 앱에 `WEBSITE_RUN_FROM_PACKAGE` 앱 설정이 있고 해당 값이 URL인 경우 해당 URL을 복사하여 브라우저에 붙여넣어 파일을 다운로드합니다.
* 함수 앱에 `WEBSITE_RUN_FROM_PACKAGE`가 있고 `1`로 설정된 경우 `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages`로 이동하여 최신 `href` URL에서 파일을 다운로드합니다.
* 함수 앱에 위에서 언급한 앱 설정이 없는 경우 `https://<app-name>.scm.azurewebsites.net/api/settings`로 이동하여 `SCM_RUN_FROM_PACKAGE`에서 URL을 찾습니다. URL을 복사하여 브라우저에 붙여넣어 파일을 다운로드합니다.
* 이러한 작업을 수행할 수 없는 경우 `https://<app-name>.scm.azurewebsites.net/DebugConsole`로 이동하여 `/home/site/wwwroot`에 콘텐츠를 표시합니다.

이 문서의 나머지 부분은 함수 앱의 콘텐츠를 검사하고 근본 원인을 파악하고 특정 문제를 해결하여 이 오류의 잠재적 원인을 해결하는 데 도움이 됩니다.

### <a name="diagnose-modulenotfounderror"></a>ModuleNotFoundError 진단

이 섹션에서는 모듈 관련 오류의 잠재적 근본 원인을 자세히 설명합니다. 가능한 근본 원인을 파악한 후 관련된 완화 방법으로 이동할 수 있습니다.

#### <a name="the-package-cant-be-found"></a>패키지를 찾을 수 없습니다.

`.python_packages/lib/python3.6/site-packages/<package-name>` 또는 `.python_packages/lib/site-packages/<package-name>`으로 이동합니다. 파일 경로가 없으면 이 누락 경로가 근본 원인일 수 있습니다.

배포 중에 타사 또는 오래된 도구를 사용하면 이 문제가 발생할 수 있습니다.

완화를 위해 [원격 빌드 사용](#enable-remote-build) 또는 [네이티브 종속성 빌드](#build-native-dependencies)를 참조하세요.

#### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>적절한 Linux 휠을 사용하는 패키지가 해결되지 않았습니다.

`.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` 또는 `.python_packages/lib/site-packages/<package-name>-<version>-dist-info`로 이동합니다. 자주 사용하는 텍스트 편집기를 사용하여 **휠** 파일을 열고 **태그:** 섹션을 확인합니다. 태그 값에 **linux** 가 포함되지 않은 경우 이 문제가 발생할 수 있습니다.

Python 함수는 Azure의 Linux에서만 실행됩니다. 함수 런타임 v2.x는 Debian Buster의 v3.x 런타임 및 Debian Stretch에서 실행됩니다. 아티팩트에는 올바른 Linux 이진 파일이 포함되어야 합니다. 핵심 도구, 타사 또는 오래된 도구에서 `--build local` 플래그를 사용하면 이전 이진 파일이 사용될 수 있습니다.

완화를 위해 [원격 빌드 사용](#enable-remote-build) 또는 [네이티브 종속성 빌드](#build-native-dependencies)를 참조하세요.

#### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>패키지가 Python 인터프리터 버전과 호환되지 않습니다.

`.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` 또는 `.python_packages/lib/site-packages/<package-name>-<version>-dist-info`로 이동합니다. 텍스트 편집기를 사용하여 메타데이터 파일을 열고 **분류자:** 섹션을 확인합니다. 섹션에 `Python :: 3`, `Python :: 3.6`, `Python :: 3.7` 또는 `Python :: 3.8`이 포함되지 않은 경우 패키지 버전이 너무 오래 되었거나 패키지가 이미 유지 관리되지 않는 것입니다.

[Azure Portal](https://portal.azure.com)에서 함수 앱의 Python 버전을 확인할 수 있습니다. 함수 앱으로 이동하여 **리소스 탐색기** 를 선택하고 **이동** 을 선택합니다.

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="포털에서 함수 앱에 대한 리소스 탐색기를 엽니다.":::

탐색기가 로드된 후 Python 버전을 표시하는 **LinuxFxVersion** 을 검색합니다.

완화를 위해 [패키지를 최신 버전으로 업데이트](#update-your-package-to-the-latest-version) 또는 [패키지를 해당 항목으로 바꾸기](#replace-the-package-with-equivalents)를 참조하세요.

#### <a name="the-package-conflicts-with-other-packages"></a>패키지가 다른 패키지와 충돌합니다.

적절한 Linux 휠을 사용하는 패키지가 올바르게 해결됨을 확인한 경우 다른 패키지와 충돌하는 것일 수 있습니다. 특정 패키지에서는 PyPi 설명서에 호환되지 않는 모듈이 명시될 수 있습니다. 예를 들어 [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/)에는 다음과 같은 명령문이 있습니다.

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

`https://pypi.org/project/<package-name>/<package-version>`에서 패키지 버전에 대한 설명서를 찾을 수 있습니다.

완화를 위해 [패키지를 최신 버전으로 업데이트](#update-your-package-to-the-latest-version) 또는 [패키지를 해당 항목으로 바꾸기](#replace-the-package-with-equivalents)를 참조하세요.

#### <a name="the-package-only-supports-windows-or-macos-platforms"></a>패키지가 Windows 또는 macOS 플랫폼만 지원합니다.

텍스트 편집기를 사용하여 `requirements.txt`를 열고 `https://pypi.org/project/<package-name>`에서 패키지를 확인합니다. 일부 패키지는 Windows 또는 macOS 플랫폼에서만 실행됩니다. 예를 들어 pywin32는 Windows에서만 실행됩니다.

로컬 개발에 Windows 또는 macOS를 사용하는 경우 `Module Not Found` 오류가 발생하지 않을 수 있습니다. 단, 런타임 시 Linux를 사용하는 Azure Functions에서 패키지를 가져오지 못합니다. 이는 `pip freeze`를 사용하여 프로젝트를 초기화하는 동안 Windows 또는 macOS 컴퓨터에서 가상 환경을 requirements.txt로 내보내는 경우에 발생할 수 있습니다.

완화를 위해 [패키지를 해당 항목으로 바꾸기](#replace-the-package-with-equivalents) 또는 [수작업 requirements.txt](#handcraft-requirementstxt)를 참조하세요.

### <a name="mitigate-modulenotfounderror"></a>ModuleNotFoundError 완화

모듈 관련 문제에 대한 잠재적 완화 방법은 다음과 같습니다. [위의 진단](#diagnose-modulenotfounderror)을 사용하여 이러한 완화 방법을 확인하여 시도해 보세요.

#### <a name="enable-remote-build"></a>원격 빌드 사용

원격 빌드를 사용하도록 설정했는지 확인합니다. 이 작업을 수행하는 방법은 배포 방법에 따라 달라집니다.

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)
[Visual Studio Code에 대한 Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)의 최신 버전이 설치되어 있는지 확인합니다. `.vscode/settings.json`이 있는지와 `"azureFunctions.scmDoBuildDuringDeployment": true` 설정을 포함하는지 확인합니다. 그렇지 않은 경우 이 파일을 만들고 `azureFunctions.scmDoBuildDuringDeployment` 설정을 사용하도록 설정한 후, 프로젝트를 다시 배포합니다.

## <a name="azure-functions-core-tools"></a>[Azure Functions 핵심 도구](#tab/coretools)

최신 버전의 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases)가 설치되어 있는지 확인합니다. 로컬 함수 프로젝트 폴더로 이동하고 배포에 `func azure functionapp publish <app-name>`을 사용합니다.

## <a name="manual-publishing"></a>[수동 게시](#tab/manual)

`https://<app-name>.scm.azurewebsites.net/api/zipdeploy` 엔드포인트에 패키지를 수동으로 게시하는 경우 **SCM_DO_BUILD_DURING_DEPLOYMENT** 및 **ENABLE_ORYX_BUILD** 가 모두 **true** 로 설정되었는지 확인합니다. 자세한 내용은 [애플리케이션 설정을 사용하는 방법](functions-how-to-use-azure-function-app-settings.md#settings)을 참조하세요.

---

#### <a name="build-native-dependencies"></a>네이티브 종속성 빌드

**docker** 및 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases)가 모두 최신 버전으로 설치되어 있는지 확인합니다. 로컬 함수 프로젝트 폴더로 이동하고 배포에 `func azure functionapp publish <app-name> --build-native-deps`를 사용합니다.

#### <a name="update-your-package-to-the-latest-version"></a>패키지를 최신 버전으로 업데이트

`https://pypi.org/project/<package-name>`에서 최신 패키지 버전을 찾아보고 **분류자:** 섹션을 확인합니다. 패키지는 `OS Independent`이거나, **운영 체제** 에서 `POSIX` 또는 `POSIX :: Linux`와 호환되어야 합니다. 또한 프로그래밍 언어에 `Python :: 3`, `Python :: 3.6`, `Python :: 3.7` 또는 `Python :: 3.8`이 포함되어야 합니다.

이러한 항목이 올바른 경우 requirements.txt의 줄 `<package-name>~=<latest-version>`을 변경하여 패키지를 최신 버전으로 업데이트할 수 있습니다.

#### <a name="handcraft-requirementstxt"></a>수작업 requirements.txt

일부 개발자는 `pip freeze > requirements.txt`를 사용하여 개발 환경에 대한 Python 패키지 목록을 생성합니다. 이 편의성은 대부분의 경우에 작동하긴 하지만, Windows 또는 macOS에서 로컬로 함수를 개발하지만 Linux에서 실행되는 함수 앱에 게시하는 것처럼 플랫폼 간 배포 시나리오에서는 문제가 있을 수 있습니다. 이 시나리오에서 `pip freeze`는 로컬 개발 환경에 대한 예기치 않은 운영 체제별 종속성을 제공할 수 있습니다. 이러한 종속성은 Linux에서 실행될 때 Python 함수 앱을 중단시킬 수 있습니다.

모범 사례는 프로젝트 소스 코드의 각 .py 파일에서 import 문을 확인하고, requirements.txt 파일의 해당 모듈만 체크 인하는 것입니다. 이렇게 하면 다른 운영 체제에서 패키지 문제를 제대로 처리할 수 있습니다.

#### <a name="replace-the-package-with-equivalents"></a>패키지를 해당 항목으로 바꾸기

먼저 `https://pypi.org/project/<package-name>`에서 최신 버전의 패키지를 확인해야 합니다. 일반적으로 이 패키지에는 고유한 GitHub 페이지가 있습니다. GitHub의 **문제** 섹션으로 이동하여 문제가 해결되었는지 검색합니다. 해결되었다면 패키지를 최신 버전으로 업데이트합니다.

경우에 따라 패키지가 [Python 표준 라이브러리](https://docs.python.org/3/library/)(예: pathlib)에 통합될 수 있습니다. 통합되어 있는 경우 Azure Functions(Python 3.6, Python 3.7 및 Python 3.8)에서 특정 Python 배포를 제공하기 때문에 requirements.txt의 패키지를 제거해야 합니다.

단, 문제가 고쳐지지 않고 마감 기한에 쫓기고 있다면 일부 연구를 수행하고 프로젝트에 대한 유사한 패키지를 찾는 것이 좋습니다. 일반적으로 Python 커뮤니티에는 사용 가능한 유사한 라이브러리가 다양하게 제공됩니다.

---

## <a name="troubleshoot-cannot-import-cygrpc"></a>문제 해결에서 'cygrpc'를 가져올 수 없음

이 섹션은 Python 함수 앱에서 'cygrpc' 관련 오류를 해결하는 데 도움이 됩니다. 이러한 오류는 일반적으로 다음과 같은 Azure Functions 오류 메시지를 생성합니다.

> `Cannot import name 'cygrpc' from 'grpc._cython'`

해당 오류는 Python 함수 앱이 적절한 Python 인터프리터를 시작하지 못하는 경우에 발생합니다. 이 오류의 근본 원인은 다음과 같은 문제 중 하나입니다.

- [Python 인터프리터가 OS 아키텍처와 일치하지 않음](#the-python-interpreter-mismatches-os-architecture)
- [Azure Functions Python Worker가 Python 인터프리터를 지원하지 않음](#the-python-interpreter-is-not-supported-by-azure-functions-python-worker)

### <a name="diagnose-cygrpc-reference-error"></a>'cygrpc' 참조 오류 진단

#### <a name="the-python-interpreter-mismatches-os-architecture"></a>Python 인터프리터가 OS 아키텍처와 일치하지 않음

이는 64비트 운영 체제에 32비트 Python 인터프리터가 설치되어서 발생할 수 있습니다.

x64 운영 체제에서 실행하는 경우 Python 3.6, 3.7 또는 3.8 인터프리터가 64비트 버전에도 있는지 확인하세요.

다음 명령을 수행하여 Python 인터프리터 비트를 확인할 수 있습니다.

PowerShell의 Windows: `py -c 'import platform; print(platform.architecture()[0])'`

Unix와 유사한 셸: `python3 -c 'import platform; print(platform.architecture()[0])'`

Python 인터프리터 비트 수와 운영 체제 아키텍처가 일치하지 않는 경우 [Python Software Foundation](https://www.python.org/downloads)에서 적절한 Python 인터프리터를 다운로드하세요.

#### <a name="the-python-interpreter-is-not-supported-by-azure-functions-python-worker"></a>Azure Functions Python Worker가 Python 인터프리터를 지원하지 않음

Azure Functions Python Worker는 Python 3.6, 3.7, 3.8만 지원합니다.
Python 인터프리터가 Windows의 `py --version` 또는 Unix와 유사한 시스템에서 `python3 --version`으로 예상 버전과 일치하는지 확인하세요. 반환 결과가 Python 3.6.x, Python 3.7.x 또는 Python 3.8.x인지 확인합니다.

[Python](https://www.python.org/downloads) 인터프리터 버전이 예상과 다른 경우 에서 Python 3.6, 3.7 또는 3.8 인터프리터를 다운로드하세요.

---

## <a name="troubleshoot-python-exited-with-code-137"></a>코드 137로 종료된 Python 문제 해결

코드 137 오류는 일반적으로 Python 함수 앱의 메모리 부족 문제로 인해 발생합니다. 그 결과 다음과 같은 Azure Functions 오류 메시지가 표시됩니다.

> `Microsoft.Azure.WebJobs.Script.Workers.WorkerProcessExitException : python exited with code 137`

해당 오류는 SIGKILL 신호를 사용하여 운영 체제에서 Python 함수 앱을 강제로 종료하는 경우에 발생합니다. 해당 신호는 일반적으로 Python 프로세스의 메모리 부족 오류를 나타냅니다. Azure Functions 플랫폼에는 제한을 초과하는 모든 함수 앱을 종료하는 [서비스 제한](functions-scale.md#service-limits)이 있습니다.

함수 앱의 메모리 병목 상태를 분석하려면 [Python 함수의 메모리 프로파일링](python-memory-profiler-reference.md#memory-profiling-process)의 자습서 섹션을 참조하세요.

---

## <a name="troubleshoot-python-exited-with-code-139"></a>코드 139로 종료된 Python 문제 해결

이 섹션은 Python 함수 앱에서 구분 오류를 해결하는 데 도움이 됩니다. 이러한 오류는 일반적으로 다음과 같은 Azure Functions 오류 메시지를 생성합니다.

> `Microsoft.Azure.WebJobs.Script.Workers.WorkerProcessExitException : python exited with code 139`

해당 오류는 SIGSEGV 신호를 사용하여 운영 체제에서 Python 함수 앱을 강제로 종료하는 경우에 발생합니다. 해당 신호는 메모리 구분 위반을 나타내며, 이는 제한된 메모리 영역에서 예기치 않게 읽거나 쓰는 동안 발생할 수 있습니다. 다음 섹션에서는 일반적인 근본 원인의 목록을 제공합니다.

### <a name="a-regression-from-third-party-packages"></a>타사 패키지로부터의 회귀

함수 앱의 requirements.txt에서 고정 해제된 패키지는 모든 Azure Functions 배포에서 최신 버전으로 업그레이드됩니다. 해당 패키지의 공급 기업은 최신 릴리스에서 회귀를 도입할 수 있습니다. 이 문제를 복구하려면 import 문을 주석으로 처리하거나 패키지 참조를 사용하지 않도록 설정하거나 requirements.txt에서 이전 버전으로 패키지를 고정하세요.

### <a name="unpickling-from-a-malformed-pkl-file"></a>잘못된 형식의 .pkl 파일에서 언피클링

함수 앱이 Python 피클 라이브러리를 사용하여 .pkl 파일에서 Python 개체를 로드하는 경우 .pkl에 잘못된 형식의 바이트 문자열이 포함되어 있거나 유효하지 않은 주소 참조가 있을 수 있습니다. 해당 문제를 복구하려면 pickle.load() 함수를 주석으로 처리하세요.

### <a name="pyodbc-connection-collision"></a>Pyodbc 연결 충돌

함수 앱이 많이 사용되는 ODBC 데이터베이스 드라이버인 [pyodbc](https://github.com/mkleehammer/pyodbc)를 사용하는 경우 단일 함수 앱 내에서 여러 연결을 열 수 있습니다. 해당 문제를 방지하려면 싱글톤 패턴을 사용하고 함수 앱에서 하나의 pyodbc 연결만 사용하는지 확인하세요.

---

## <a name="next-steps"></a>다음 단계

문제를 해결할 수 없는 경우 Functions 팀에 보고하세요.

> [!div class="nextstepaction"]
> [해결되지 않은 문제 보고](https://github.com/Azure/azure-functions-python-worker/issues)
