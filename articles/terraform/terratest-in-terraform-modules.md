---
title: 在 Azure 中使用 Terratest 測試 Terraform 模組
description: 了解如何使用 Terratest 來測試 Terraform 模組。
services: terraform
ms.service: terraform
keywords: terraform, devops, 儲存體帳戶, azure, terratest, 單元測試, 整合測試
author: JunyiYi
manager: jeconnoc
ms.author: junyi
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: 7feee063c7b311934f7d157a9dff62d803a041b0
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638700"
---
# <a name="test-terraform-modules-in-azure-using-terratest"></a>在 Azure 中使用 Terratest 測試 Terraform 模組

Terraform 模組可用來建立可重複使用、可組合和可測試的元件。 其可在「基礎結構即程式碼」的世界中合併封裝功能。

和在其他軟體元件一樣，品質保證也會在 Terraform 模組中扮演重要的角色。 不幸的是，說明如何在 Terraform 模組中撰寫單元測試和整合測試的可用文件並不多。 本教學課程會介紹測試基礎結構，以及我們在建置 [Azure Terraform 模組](https://registry.terraform.io/browse?provider=azurerm)時所採用的最佳做法。

在考量過所有最受歡迎的測試基礎結構之後，我們選擇使用 [Terratest](https://github.com/gruntwork-io/terratest)。 Terratest 可實作為 Go 程式庫。 其集合了常見基礎結構測試工作的協助程式函式和模式，例如，對特定虛擬機器提出 HTTP 要求和建立 SSH。 Terratest 的部分主要優點包括：

- **可提供便利的協助程式來檢查基礎結構。** 當您想要在實際環境中驗證實際基礎結構時，此功能非常有用。
- **資料夾結構的組織方式很清楚。** 測試案例的組織方式會很清楚，並遵循[標準的 Terraform 模組資料夾結構](https://www.terraform.io/docs/modules/create.html#standard-module-structure)。
- **所有測試案例均以 Go 撰寫。** 由於大部分的 Terraform 開發人員都已使用 Go，因此若使用 Terratest，他們就不需要再學習一種程式設計語言。 此外，在 Terratest 中執行測試案例所需的相依性，只有 Go 與 Terraform。
- **此基礎結構具有高度擴充性。** 在 Terratest 的基礎上另外新增功能並不困難，例如 Azure 特定的功能。

## <a name="prerequisites"></a>必要條件

這份實際操作指南沒有平台限制；在 Windows、Linux 或 MacOS 上均可執行。 在繼續之前，請先安裝下列軟體：

- **Go 程式設計語言**：Terraform 測試案例會以 [Go](https://golang.org/dl/) 撰寫。
- **dep**：[dep](https://github.com/golang/dep#installation) 是適用於 Go 的相依性管理工具。
- **Azure CLI**：[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 是命令列工具，可用於管理 Azure 資源。 (Terraform 支援透過服務主體或[透過 Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html) 來對 Azure 進行驗證)。
- **mage**：我們會使用 [mage 可執行檔](https://github.com/magefile/mage/releases)來了解如何簡化 Terratest 案例的執行。 

## <a name="create-a-static-webpage-module"></a>建立靜態網頁模組

在本教學課程中，您會建立一個 Terraform 模組，其會藉由將單一 HTML 檔案上傳至 Azure 儲存體 Blob，來佈建靜態網頁。 此模組可讓世界各地的使用者透過模組所傳回的 URL 存取此網頁。

> [!NOTE]
> 本節所述的所有檔案皆應建立在 [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) 底下。

首先，在 GoPath 的 `src` 資料夾底下，建立名為 `staticwebpage` 的新資料夾。 本教學課程的整體資料夾結構如下所示。 (以星號 `(*)` 標示的檔案是本節的關注重點)。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

靜態網頁模組接受三個輸入，並已於 `./variables.tf` 中宣告：

```hcl
variable "location" {
  description = "The Azure region in which all resources will be created."
}

variable "website_name" {
  description = "The website name which will be used to create a bunch of related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static homepage HTML in your local filesystem."
  default     = "index.html"
}
```

如前所述，此模組也會輸出 `./outputs.tf` 中所宣告的 URL：

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

這帶領我們來到此模組的主要邏輯。 此模組總共會佈建四個資源：
- 名稱為 `website_name` 輸入加上 `-staging-rg` 的資源群組。
- 名稱為 `website_name` 輸入加上 `data001` 的儲存體帳戶。 不過，為了遵守儲存體帳戶的名稱限制，此模組會移除所有特殊字元並將完整名稱變為小寫。
- 於上述儲存體帳戶中所建立的固定名稱容器 `wwwroot`。
- 從 `html_path` 輸入讀取並上傳至 `wwwroot/index.html` 的單一 HTML 檔案。

靜態網頁模組邏輯會實作在 `./main.tf` 中：

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>單元測試

傳統上，Terratest 是專為進行整合測試所設計的工具，這表示它會在實際環境中佈建實際資源。 這類作業有時候會變得特別巨大，當您要佈建大量資源時更會如此。 上一節所述的儲存體帳戶命名轉換邏輯便是絕佳範例：我們不需要真的佈建任何資源；我們只想要確定命名轉換邏輯是否正確。

由於 Terratest 具有彈性，因此透過使用單元測試便能輕鬆辦到。 單元測試是只要執行 `terraform init` 和 `terraform plan` 命令就能進行的本機執行測試案例 (不過仍需要網際網路連線)，單元測試案例會剖析 `terraform plan` 的輸出並尋找要比較的屬性值。

本節其餘部分將會說明如何使用 Terratest 來實作單元測試，以確定儲存體帳戶命名轉換邏輯正確無誤。 我們只會對以星號 `(*)` 標出的檔案感興趣。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

首先，空的 HTML 檔案 `./test/fixtures/storage-account-name/empty.html` 只是預留位置。

`./test/fixtures/storage-account-name/main.tf` 檔案是測試案例的基本架構。 它會接受一個輸入 `website_name`，這也是單元測試的輸入。 其邏輯如下所示：

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

最後，主要元件是單元測試的實作：`./test/storage_account_name_unit_test.go`

如果您是 Go 開發人員，您會發現其會藉由接受 `*testing.T` 類型的引數，來比對傳統 Go 測試函式的簽章。

在單元測試的本文中，我們在 `testCases` 變數中總共定義了五個案例 (金鑰作為輸入，值則作為預期的輸出)。 針對每個單元測試案例，我們會先執行 `terraform init`，其目標為測試固件資料夾 (`./test/fixtures/storage-account-name/`)。 

接下來，`terraform plan` 命令搭配特定的測試案例輸入 (請看 `tfOptions` 中的 `website_name` 定義) 會將結果儲存至 `./test/fixtures/storage-account-name/terraform.tfplan` (未在整體資料夾結構中列出)。

接下來，系統會使用官方的 Terraform 方案剖析器，將這個結果檔案剖析為可由程式碼讀取的結構。

現在，我們會尋找我們感興趣的屬性 (在此案例中為 `azurerm_storage_account` 的 `name`)，並將這些屬性與預期的輸出做比較。

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

若要執行單元測試，您必須在命令列中完成下列步驟。

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

大約一分鐘後，您就會看到傳統的 Go 測試結果。

### <a name="integration-test"></a>整合測試

從端對端的觀點來看，整合測試不像單元測試需要在實際環境中佈建資源。 Terratest 很擅長執行這類作業。 既然 Terraform 模組的最佳做法同樣建議使用包含某些端對端範例的 `examples` 資料夾，為何不直接以整合測試的形式來測試這些範例呢？ 在本節中，我們將聚焦在三個檔案 (均以星號 `(*)` 標出)。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

讓我們從這些範例來開始。 在 `./examples/` 資料夾中建立了名為 `hello-world/` 的新範例資料夾。 我們在此提供了簡單的 HTML 網頁 `./examples/hello-world/index.html` 供您上傳：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demostrate Terratest.</p>
</body>
</html>
```

Terraform 範例 `./examples/hello-world/main.tf` 與單元測試所示的範例類似，但有一個極大的差異：前者還會列印出所上傳 HTML (名為 `homepage`) 的 URL。

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

Terratest 和傳統的 Go 測試函式會再次出現在整合測試檔案 `./test/hello_world_example_test.go` 中。

不同於單元測試，整合測試會在 Azure 中建立實際資源，因此您必須小心避免名稱發生衝突。 (請特別注意某些全域唯一的名稱，例如儲存體帳戶名稱)。 因此，測試邏輯的第一個步驟就是使用 TerraTest 所提供的 `UniqueId()` 函式，來產生隨機的 `websiteName`。 此函式會產生隨機名稱，且其中包含小寫字母、大寫字母或數字。 `tfOptions` 會讓所有 Terraform 命令以 `./examples/hello-world/` 資料夾作為目標，並會確保 `website_name` 設定為隨機的 `websiteName`。

然後，系統會逐一執行 `terraform init`、`terraform apply` 和 `terraform output`。 我們已使用 Terratest 所提供的另一個協助程式函式 `HttpGetWithCustomValidation()`，藉由比較 HTTP Get 狀態碼與 `200` 並尋找 HTML 內容中的一些關鍵字，來確定該 HTML 會上傳至 `terraform output` 所傳回的輸出 `homepage` URL。 最後，系統會利用 Go 的 `defer` 功能來「保證」`terraform destroy` 的執行。

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demostrate Terratest.")
    })
}
```

若要執行整合測試，您必須在命令列中完成下列步驟。

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

大約兩分鐘後，您就會看到傳統的 Go 測試結果。 當然，您也可以藉由執行下列命令，來同時執行單元測試以及整合測試：

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

您可以看得出來，整合測試所需的進行時間比單元測試還久 (一個整合案例需要兩分鐘，五個單元案例則需要一分鐘)。 但單元測試和整合測試各自的使用時機，仍需由您自行決定。 一般而言，如果是使用 Terraform HCL 函式的複雜邏輯，我們喜歡使用單元測試；若從使用者的端對端觀點來看，則會使用整合測試。

## <a name="use-mage-to-simplify-running-terratest-cases"></a>使用 mage 來簡化 Terratest 案例的執行 

如您所見，在殼層中執行測試案例並不容易，因為您必須瀏覽至不同的目錄，並執行不同的命令。 因此，我們在專案中引入了建置系統。 在本節中，我們會使用 Go 建置系統 mage 來執行這項作業。

mage 唯一的要求是專案根目錄中有 `magefile.go` (在下圖中以 `(+)` 標出)。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

以下是 `./magefile.go` 的一個範例。 在這個以 Go 撰寫的建置指令碼中，我們實作了五個建置步驟：
- `Clean`：這個步驟會在測試執行期間移除所有產生的/暫存的檔案。
- `Format`：這個步驟會執行 `terraform fmt` 和 `go fmt` 來設定程式碼基底的格式。
- `Unit`：這個步驟會在 `./test/` 資料夾底下執行所有單元測試 (使用函式名稱慣例 `TestUT_*`)。
- `Integration`：類似於 `Unit`，但它不會執行單元測試，而是會執行整合測試 (`TestIT_*`)。
- `Full`：這個步驟會依序執行 `Clean`、`Format`、`Unit', and `Integration`。

```go
// +build mage

// Build script to format and run tests of a Terraform module project.
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// Default target when execute `mage` in shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build/test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

類似於之前執行的步驟，您可以使用下列命令來執行完整的測試套件：

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only requied when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage$ mage
```

您可以隨意地將最後一個命令列替換為任何 mage 步驟，例如 `mage unit` 或 `mage clean`。 現在，您可能會認為這裡還是有許多命令列，將 `dep` 命令以及 `az login` 內嵌到 magefile 或許是個好主意。 但我們不會在此說明該程式碼。 使用 mage 還有一個步驟，那就是可以使用 Go 套件系統來共用步驟。 如此一來，只要藉由參考通用實作並宣告相依性 (`mg.Deps()`)，便能簡化所有模組的 magefiles。

> [!NOTE]
> **選項：設定服務主體的環境變數來執行接受度測試**
> 
> 您不必在測試之前執行 `az login`，反而可以藉由設定服務主體的環境變數來完成 Azure 驗證。 Terraform 會發佈[環境變數名稱的清單](https://www.terraform.io/docs/providers/azurerm/index.html#testing)。 (在這些環境變數中，只有前四個是必要變數)。Terraform 也會發佈詳細指示，說明如何[取得這些環境變數的值。](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)

## <a name="next-steps"></a>後續步驟

如需 Terratest 的詳細資訊，請參閱[其 GitHub 頁面](https://github.com/gruntwork-io/terratest)。 您可以在[其 GitHub 頁面](https://github.com/magefile/mage)和[其首頁](https://magefile.org/)找到 mage 的一些實用資訊。
