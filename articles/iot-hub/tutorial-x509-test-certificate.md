---
title: 'Esercitazione: Testare la capacità dei certificati X.509 di autenticare i dispositivi in un hub IoT di Azure | Microsoft Docs'
description: "Esercitazione: Testare i certificati X.509 per l'autenticazione hub IoT di Azure"
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 7d1900782fce6b84ed79014e985393f3626d171b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379435"
---
# <a name="tutorial-testing-certificate-authentication"></a>Esercitazione: Test dell'autenticazione del certificato

È possibile usare l'esempio di codice C# seguente per verificare che il certificato possa autenticare il dispositivo nell'hub IoT. Si noti che è necessario eseguire le operazioni seguenti prima di eseguire il codice di test:

* Creare un certificato CA radice o subordinato.
* Caricare il certificato della CA nell'hub IoT.
* Dimostrare di essere in possesso del certificato della CA.
* Aggiungere un dispositivo all'hub IoT.
* Creare un certificato del dispositivo con lo stesso ID dispositivo del dispositivo.

## <a name="code-example"></a>Esempio di codice

L'esempio di codice seguente illustra come creare un'applicazione C# per simulare il dispositivo X.509 registrato per l'hub IoT. L'esempio invia i valori di temperatura e umidità dal dispositivo simulato all'hub. In questa esercitazione verrà creata solo l'applicazione del dispositivo. Viene lasciato ai lettori un esercizio per creare l'applicazione del servizio Hub IoT che invierà risposte agli eventi inviati da questo dispositivo simulato.

1. Aprire Visual Studio, selezionare **Crea un nuovo progetto** e quindi scegliere il modello di progetto **App console (.NET Framework).** Selezionare **Avanti**.

1. In **Configurare il nuovo progetto** assegnare al progetto il nome *SimulateX509Device* e quindi selezionare **Crea**.

   ![Creare il progetto di dispositivo X.509 in Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul **progetto SimulateX509Device** e quindi scegliere **Gestisci pacchetti NuGet**.

1. Nel Gestione pacchetti **NuGet** selezionare **Sfoglia** e cercare e scegliere **Microsoft.Azure.Devices.Client**. Selezionare **Installa**.

   ![Aggiungere il pacchetto NuGet SDK per dispositivi in Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Questo passaggio scarica, installa e aggiunge un riferimento al pacchetto NuGet Azure IoT SDK per dispositivi e alle relative dipendenze.

    Immettere ed eseguire il codice seguente:

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

                if (deviceClient == null)
                {
                    Console.WriteLine("Failed to create DeviceClient!");
                }
                else
                {
                    Console.WriteLine("Successfully created DeviceClient!");
                    SendEvent(deviceClient).Wait();
                }

                Console.WriteLine("Exiting...\n");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
         }
    }
}
```