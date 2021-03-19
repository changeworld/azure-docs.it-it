---
title: Configurare gli ambienti di sviluppo e distribuzione di bicipiti
description: Come configurare gli ambienti di sviluppo e distribuzione bicipite
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: d665a863affdec2009fc208f76b85a7f25de451d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594394"
---
# <a name="setup-bicep-development-and-deployment-environment"></a>Configurare l'ambiente di sviluppo e distribuzione bicipite

Informazioni su come configurare gli ambienti di sviluppo e distribuzione di bicipiti.

## <a name="development-environment"></a>Ambiente di sviluppo

Per ottenere la migliore esperienza di creazione del bicipite, sono necessari due componenti:

- **Estensione bicipite per Visual Studio Code**. Per creare i file bicipite, è necessario un editor bicipite valido. Si consiglia di [Visual Studio Code](https://code.visualstudio.com/) con l' [estensione bicipite](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Questi strumenti forniscono supporto per la lingua e il completamento automatico delle risorse. Consentono di creare e convalidare i file bicipite. Per altre informazioni sull'uso di Visual Studio Code e dell'estensione bicipite, vedere [Guida introduttiva: creare file bicipit con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- INTERFACCIA della riga di comando **bicipite**. Usare l'interfaccia della riga di comando bicipite per compilare i file bicipite nei modelli JSON ARM e decompilare i modelli JSON ARM nei file bicipite. Per altre informazioni, vedere Installare l'interfaccia della riga di comando di [bicipite](#install-bicep-cli).

## <a name="deployment-environment"></a>Ambiente di distribuzione

È possibile distribuire i file bicipite usando l'interfaccia della riga di comando di Azure o Azure PowerShell. Per l'interfaccia della riga di comando di Azure è necessaria la versione 2.20.0 o successiva. per Azure PowerShell, è necessaria la versione 5.6.0 o successiva. Per le istruzioni di installazione, vedere:

- [Installare Azure PowerShell](/powershell/azure/install-az-ps)
- [Installare l'interfaccia della riga di comando di Azure in Windows](/cli/azure/install-azure-cli-windows)
- [Installare l'interfaccia della riga di comando di Azure in Linux](/cli/azure/install-azure-cli-linux)
- [Installare l'interfaccia della riga di comando di Azure in macOS](/cli/azure/install-azure-cli-macos)

> [!NOTE]
> Attualmente, sia l'interfaccia della riga di comando di Azure che la Azure PowerShell possono distribuire solo file bicipiti locali. Per altre informazioni sulla distribuzione di file bicipite usando l'interfaccia della riga di comando di Azure, vedere [deploy-CLI](/deploy-cli.md#deploy-remote-template). Per altre informazioni sulla distribuzione di file bicipite usando Azure PowerShell, vedere [deploy-PowerShell](/deploy-powershell.md#deploy-remote-template).

Dopo l'installazione della versione supportata di Azure PowerShell o dell'interfaccia della riga di comando di Azure, è possibile distribuire un file bicipite con:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -storageAccountType Standard_GRS
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

---

## <a name="install-bicep-cli"></a>Installare l'interfaccia della riga di comando Bicipit

È possibile installare l'interfaccia della riga di comando bicipite usando l'interfaccia della riga di comando di Azure usando Azure PowerShell o manualmente

### <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

Con AZ CLI versione 2.20.0 o successiva installata, l'interfaccia della riga di comando di bicipite viene installata automaticamente quando viene eseguito un comando che dipende da esso. Ad esempio, `az deployment ... -f *.bicep` o `az bicep ...`.

È anche possibile installare manualmente l'interfaccia della riga di comando usando i comandi predefiniti:

```bash
az bicep install
```

Per eseguire l'aggiornamento alla versione più recente:

```bash
az bicep upgrade
```

Per installare una versione specifica:

```bash
az bicep install --version v0.2.212
```

> [!NOTE]
> AZ CLI installa una versione separata dell'interfaccia della riga di comando bicipite che non è in conflitto con altre installazioni bicipite che potrebbero avere e AZ CLI non aggiunge bicipite al percorso.

Per visualizzare le versioni installate:

```bash
az bicep version
```

Per elencare tutte le versioni disponibili dell'interfaccia della riga di comando bicipite:

```bash
az bicep list-versions
```

### <a name="use-azure-powershell"></a>Usare Azure PowerShell

Azure PowerShell non è ancora in grado di installare l'interfaccia della riga di comando bicipite. Azure PowerShell (v 5.6.0 o versione successiva) prevede che l'interfaccia della riga di comando del bicipite sia già installata e disponibile nel percorso. Seguire uno dei [metodi di installazione manuale](#install-manually). Una volta installata l'interfaccia della riga di comando bicipite, l'interfaccia della riga di comando bicipite viene chiamata ogni volta che è necessaria per un cmdlet Ad esempio: `New-AzResourceGroupDeployment ... -TemplateFile main.bicep`.

### <a name="install-manually"></a>Eseguire l'installazione manuale

I metodi seguenti installano l'interfaccia della riga di comando del bicipite e la aggiungono al percorso.

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>Via Homebrew

```sh
# Add the tap for bicep
brew tap azure/bicep https://github.com/azure/bicep

# Install the tool
brew install azure/bicep/bicep
```

##### <a name="macos-manual-install"></a>installazione manuale di macOS

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Windows Installer

Scaricare ed eseguire la [versione più recente di Windows Installer](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe). Il programma di installazione non richiede privilegi amministrativi. Al termine dell'installazione, viene aggiunta l'interfaccia della riga di comando del bicipite al percorso utente. Chiudere e riaprire le finestre della shell dei comandi aperte per rendere effettive le modifiche al percorso.

##### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>Manuale con PowerShell

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-the-nightly-builds"></a>Installare le compilazioni notturne

Se si vuole provare gli ultimi bit di versione non definitiva di bicipite prima di essere rilasciati, vedere [installare le compilazioni notturne](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md).

> [!WARNING]
> È molto più probabile che queste build di versioni non definitive includano bug noti o sconosciuti.

## <a name="next-steps"></a>Passaggi successivi

Inizia a usare la [Guida introduttiva di bicipite](./quickstart-create-bicep-use-visual-studio-code.md).
