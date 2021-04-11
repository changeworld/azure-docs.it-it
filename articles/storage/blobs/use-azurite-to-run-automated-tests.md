---
title: Eseguire test automatizzati tramite azzurrite
titleSuffix: Azure Storage
description: Informazioni su come scrivere test automatizzati sugli endpoint privati per l'archiviazione BLOB di Azure usando azzurrite.
services: storage
author: ikivanc
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 03/31/2021
ms.author: ikivanc
ms.openlocfilehash: c4e8a11e0c46cb9a138a1a66060d9fdcc72c192e
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111817"
---
# <a name="run-automated-tests-by-using-azurite"></a>Eseguire test automatizzati tramite azzurrite

Informazioni su come scrivere test automatizzati sugli endpoint privati per l'archiviazione BLOB di Azure usando l'emulatore di archiviazione azzurrite.

## <a name="run-tests-on-your-local-machine"></a>Eseguire test nel computer locale

1. Installare la versione più recente di [Python](https://www.python.org/)

1. Installa [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

1. Installare ed eseguire [azzurrite](../common/storage-use-azurite.md):

   **Opzione 1:** Usare NPM per installare, quindi eseguire azzurrite in locale

   ```bash
   # Install Azurite
   npm install -g azurite
   
   # Create an Azurite directory
   mkdir c:/azurite
   
   # Launch Azurite locally
   azurite --silent --location c:\azurite --debug c:\azurite\debug.log
   ```

   **Opzione 2:** Usare Docker per eseguire azzurrite

   ```bash
   docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
   ```

1. In Azure Storage Explorer selezionare **Connetti a un emulatore locale**

    :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection.png" alt-text="Screenshot del Azure Storage Explorer la connessione all'origine di archiviazione di Azure.":::

1. Fornire un **nome visualizzato** e un numero di **porta BLOB** per connettere azzurrite e usare Azure Storage Explorer per gestire l'archiviazione BLOB locale.

   :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection-attach.png" alt-text="Screenshot di Azure Storage Explorer la connessione a un emulatore locale.":::

1. Creare un ambiente Python virtuale

   ```bash
   python -m venv .venv
   ```

1. Creare un contenitore e inizializzare le variabili di ambiente. Usare un file [PyTest](https://docs.pytest.org/) [conftest.py](https://docs.pytest.org/en/2.1.0/plugins.html) per generare i test. Di seguito è riportato un esempio di un file conftest.py:

   ```python
   from azure.storage.blob import BlobServiceClient
   import os

   def pytest_generate_tests(metafunc):
      os.environ['AZURE_STORAGE_CONNECTION_STRING'] = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;'
      os.environ['STORAGE_CONTAINER'] = 'test-container'

      # Create a container for Azurite for the first run
      blob_service_client = BlobServiceClient.from_connection_string(os.environ.get("AZURE_STORAGE_CONNECTION_STRING"))
      try:
         blob_service_client.create_container(os.environ.get("STORAGE_CONTAINER"))
      except Exception as e:
         print(e)
   ```

   > [!NOTE]
   > Il valore visualizzato per `AZURE_STORAGE_CONNECTION_STRING` è il valore predefinito per azzurrite, non è una chiave privata.

1. Installare le dipendenze elencate in un file di [requirements.txt](https://github.com/Azure-Samples/automated-testing-with-azurite/blob/main/requirements.txt)

   ```bash
   pip install -r requirements.txt
   ```

1. Eseguire i test:

   ```bash
   python -m pytest ./tests
   ```

Dopo l'esecuzione dei test, è possibile visualizzare i file nell'archivio BLOB azzurrite usando Azure Storage Explorer.

:::image type="content" source="media/use-azurite-to-run-automated-tests/http-local-blob-storage.png" alt-text="Screenshot del Azure Storage Explorer che mostra i file generati dai test.":::

## <a name="run-tests-on-azure-pipelines"></a>Esegui test su Azure Pipelines

Dopo aver eseguito i test localmente, verificare che i test vengano superati [Azure Pipelines](/azure/devops/pipelines). Usare un'immagine Docker azzurrite come agente ospitato in Azure o usare NPM per installare azzurrite. Nell'esempio di Azure Pipelines seguente viene usato NPM per installare azzurrite.
  
```yaml
trigger:
- master

steps:
- task: UsePythonVersion@0
  displayName: 'Use Python 3.7'
  inputs:
    versionSpec: 3.7

- bash: |
    pip install -r requirements_tests.txt
  displayName: 'Setup requirements for tests'
  
- bash: |
    sudo npm install -g azurite
    sudo mkdir azurite
    sudo azurite --silent --location azurite --debug azurite\debug.log &
  displayName: 'Install and Run Azurite'

- bash: |
    python -m pytest --junit-xml=unit_tests_report.xml --cov=tests --cov-report=html --cov-report=xml ./tests
  displayName: 'Run Tests'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'
    reportDirectory: '$(System.DefaultWorkingDirectory)/**/htmlcov'

- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/*_tests_report.xml'
    failTaskOnFailedTests: true
```

Dopo aver eseguito i test di Azure Pipelines, verrà visualizzato un output simile al seguente:

:::image type="content" source="media/use-azurite-to-run-automated-tests/azure-pipeline.png" alt-text="Screenshot dei risultati del test Azure Pipelines.":::

## <a name="next-steps"></a>Passaggi successivi

- [Usare l'emulatore di azzurrite per lo sviluppo locale di archiviazione di Azure](../common/storage-use-azurite.md)
- [Esempio: uso di azzurrite per l'esecuzione di test di archiviazione BLOB nella pipeline di Azure DevOps](https://github.com/Azure-Samples/automated-testing-with-azurite)
