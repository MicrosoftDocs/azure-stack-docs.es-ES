---
title: Validación del estado del sistema con la herramienta de validación de Azure Stack Hub
description: Aprenda a usar la herramienta de validación de Azure Stack Hub para validar el estado del sistema.
author: justinha
ms.topic: article
ms.date: 01/10/2020
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: 4c91954e4a3a19640d519d16363c0d2742077d67
ms.sourcegitcommit: 30ea43f486895828710297967270cb5b8d6a1a18
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2020
ms.locfileid: "93415171"
---
# <a name="validate-azure-stack-hub-system-state"></a>Validación del estado del sistema con Azure Stack Hub

Como operador de Azure Stack Hub, es esencial tener la capacidad de determinar a petición el mantenimiento y el estado del sistema. La herramienta de validación de Azure Stack Hub ( **Test-AzureStack** ) es un cmdlet de PowerShell que le permite ejecutar una serie de pruebas en el sistema para identificar errores, si los hubiera. Cuando se ponga en contacto con los Servicios de atención al cliente y soporte técnico de Microsoft (Soporte técnico de Microsoft) con un problema, normalmente se le pedirá que ejecute esta herramienta a través del [punto de conexión con privilegios (PEP)](azure-stack-privileged-endpoint.md). Con la información sobre el mantenimiento y el estado en todo el sistema, Soporte técnico de Microsoft puede recopilar y analizar registros detallados, centrarse en el área donde se produjo el error y trabajar conjuntamente con usted para resolver el problema.

## <a name="running-the-validation-tool-and-accessing-results"></a>Ejecución de la herramienta de validación y acceso a los resultados

Como se indicó anteriormente, la herramienta de validación se ejecuta a través del PEP. Cada prueba devuelve un estado **PASS/FAIL** (correcto/incorrecto) en la ventana de PowerShell. Este es un esquema del proceso de prueba de validación de un extremo a otro:

1. Establezca la confianza. En un sistema integrado, ejecute el siguiente comando desde una sesión de Windows PowerShell con privilegios elevados para agregar el PEP como host de confianza a la máquina virtual protegida que se ejecuta en el host de ciclo de vida de hardware o en la estación de trabajo de acceso con privilegios.

   ```powershell
   winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
   ```

   Si está ejecutando el Kit de desarrollo de Azure Stack (ASDK), inicie sesión en el host del kit de desarrollo.

1. Acceda al PEP. Ejecute los comandos siguientes para establecer una sesión de PEP:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Para acceder al PEP en un equipo host del Kit de desarrollo de Azure Stack (ASDK), use AzS-ERCS01 para -ComputerName.

1. Una vez que esté en el PEP, ejecute:

   ```powershell
   Test-AzureStack
   ```

   Acuda a las secciones [Consideraciones sobre los parámetros](azure-stack-diagnostic-test.md#parameter-considerations) y [Ejemplos de caso de uso](azure-stack-diagnostic-test.md#use-case-examples) para más información.

1. Si cualquier informe de pruebas aparece como **FAIL** , ejecute `Get-AzureStackLog`. Para obtener instrucciones sobre un sistema integrado, consulte cómo ejecutar [Get-AzureStackLog en sistemas integrados de Azure Stack Hub](azure-stack-get-azurestacklog.md).

   El cmdlet recopila registros generados por Test-AzureStack. Se recomienda no recopilar registros y ponerse en contacto con Soporte técnico de Microsoft en su lugar si las pruebas envían una notificación **WARN**.

1. Si Soporte técnico de Microsoft le indicó que ejecutase la herramienta de validación, el representante de Soporte técnico de Microsoft le solicitará los registros que haya recopilado para continuar con la solución del problema.

## <a name="tests-available"></a>Pruebas disponibles

La herramienta de validación le permite ejecutar una serie de pruebas de nivel de sistema y escenarios de nube básicos que le proporcionan información sobre el estado actual y le permiten solucionar los problemas del sistema.

### <a name="cloud-infrastructure-tests"></a>Pruebas de infraestructura en la nube

Estas pruebas de bajo impacto trabajan a nivel de infraestructura y proporcionan información sobre los distintos componentes y funciones del sistema. Actualmente, las pruebas se agrupan en las siguientes categorías:

| Categoría de prueba                                        | Argumento de -Include and -Ignore |
| :--------------------------------------------------- | :-------------------------------- |
| Resumen de ACS de Azure Stack Hub                              | AzsAcsSummary                     |
| Resumen de Active Directory de Azure Stack Hub                 | AzsAdSummary                      |
| Resumen de alertas de Azure Stack Hub                            | AzsAlertSummary                   |
| Resumen de bloqueo de aplicación de Azure Stack Hub                | AzsApplicationCrashSummary        |
| Resumen de accesibilidad del recurso compartido de copia de seguridad de Azure Stack Hub       | AzsBackupShareAccessibility       |
| Resumen del BMC de Azure Stack Hub                              | AzsStampBMCSummary                |
| Resumen de la infraestructura de hospedaje en la nube de Azure Stack Hub     | AzsHostingInfraSummary            |
| Uso de la infraestructura de hospedaje en la nube de Azure Stack Hub | AzsHostingInfraUtilization        |
| Resumen de plano de Control de Azure Stack Hub                    | AzsControlPlane                   |
| Resumen Azure Stack Hub Defender                         | AzsDefenderSummary                |
| Resumen del firmware de la infraestructura de hospedaje de Azure Stack Hub  | AzsHostingInfraFWSummary          |
| Capacidad de la infraestructura de Azure Stack Hub                  | AzsInfraCapacity                  |
| Rendimiento de la infraestructura de Azure Stack Hub               | AzsInfraPerformance               |
| Resumen de los roles de la infraestructura de Azure Stack Hub              | AzsInfraRoleSummary               |
| Infraestructura de red de Azure Stack Hub                            | AzsNetworkInfra                   |
| Resumen de API y del portal de Azure Stack Hub                   | AzsPortalAPISummary               |
| Eventos de máquina virtual de la unidad de escalado de Azure Stack Hub                     | AzsScaleUnitEvents                |
| Recursos de máquina virtual de la unidad de escalado de Azure Stack Hub                  | AzsScaleUnitResources             |
| Escenarios de Azure Stack Hub                                | AzsScenarios                      |
| Resumen de validación de SDN de Azure Stack Hub                   | AzsSDNValidation                  |
| Resumen de roles de Azure Stack Hub Service Fabric              | AzsSFRoleSummary                  |
| Plano de datos de almacenamiento de Azure Stack Hub                       | AzsStorageDataPlane               |
| Resumen de los servicios de almacenamiento de Azure Stack Hub                 | AzsStorageSvcsSummary             |
| Resumen del almacén de SQL de Azure Stack Hub                        | AzsStoreSummary                   |
| Proceso de actualización de Azure Stack Hub                           | AzsInfraUpdateSummary             |
| Resumen de la selección de máquina virtual de Azure Stack Hub                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Pruebas de escenario de nube

Además de las pruebas de infraestructura anteriores, también puede ejecutar pruebas de escenario de nube para comprobar la funcionalidad en todos los componentes de la infraestructura. Las credenciales de administrador de la nube son necesarias para ejecutar estas pruebas, ya que implican la implementación de recursos.

> [!NOTE]
> Actualmente, no se pueden ejecutar pruebas de escenarios en la nube mediante las credenciales de los Servicios de federación de Active Directory (AD FS).

Los siguientes escenarios de nube han sido probados por la herramienta de validación:
- Creación de grupos de recursos
- Creación de planes
- Creación de ofertas
- Creación de cuentas de almacenamiento
- Creación de máquinas virtuales
- Operación de almacenamiento de blobs
- Operación de almacenamiento en cola
- Operación de almacenamiento en tabla

## <a name="parameter-considerations"></a>Consideraciones sobre los parámetros

- El parámetro **List** puede usarse para mostrar todas las categorías de prueba disponibles.

- Los parámetros **Include** e **Ignore** pueden usarse para incluir o excluir las categorías de pruebas. Para más información sobre estos argumentos, consulte la siguiente sección.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- Se implementa una máquina virtual de inquilino como parte de las pruebas de escenario de nube. Puede usar **DoNotDeployTenantVm** para deshabilitar esta implementación de máquina virtual.

- Tendrá que agregar el parámetro **ServiceAdminCredential** para ejecutar las pruebas de escenario de nube, tal y como se describe en la sección [Ejemplos de caso de uso](azure-stack-diagnostic-test.md#use-case-examples).

- **BackupSharePath** y **BackupShareCredential** se utilizan cuando se prueba la configuración de copia de seguridad de la infraestructura, tal y como se muestra en la sección [Ejemplos de caso de uso](azure-stack-diagnostic-test.md#use-case-examples).

- **DetailedResults** puede usarse para obtener información sobre las ejecuciones correctas, incorrectas o con advertencias de cada prueba, y para conocer la ejecución en general. Cuando no se especifica, **Test-AzureStack** devuelve **$true** si no hay errores, y **$false** si hay errores.
- **TimeoutSeconds** puede usarse para establecer una hora específica de finalización de cada grupo.

- La herramienta de validación también admite parámetros comunes de PowerShell: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable y OutVariable. Para más información, consulte [About Common Parameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters) (Acerca de parámetros habituales).  

## <a name="use-case-examples"></a>Ejemplos de caso de uso

### <a name="run-validation-without-cloud-scenarios"></a>Ejecución de la validación sin escenarios de nube

Ejecute la herramienta de validación sin el parámetro **ServiceAdminCredential** para omitir la ejecución de las pruebas de escenario de nube: 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Ejecución de la validación con escenarios de nube

Al proporcionar la herramienta de validación con el parámetro **ServiceAdminCredentials** se ejecutan las pruebas de escenario de nube de forma predeterminada: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Si desea ejecutar SOLO escenarios de nube sin ejecutar el resto de las pruebas, puede usar el parámetro **Include** para hacerlo: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

El nombre del usuario administrador de la nube tiene que escribirse en formato UPN: serviceadmin@contoso.onmicrosoft.com (Azure AD). Cuando se le solicite, escriba la contraseña en la cuenta de administrador de la nube.

### <a name="groups"></a>Grupos

Para mejorar la experiencia del operador, se ha habilitado un parámetro **Group** para ejecutar varias categorías de pruebas al mismo tiempo. Actualmente, hay tres grupos definidos: **Default** , **UpdateReadiness** y **SecretRotationReadiness**.

- **Valor predeterminado** : Se considera una ejecución estándar de **Test-AzureStack**. Si no se selecciona ningún otro grupo, este grupo se ejecuta de forma predeterminada.
- **UpdateReadiness** : una comprobación para ver si se puede actualizar la instancia de Azure Stack Hub. Cuando se ejecuta el grupo **UpdateReadiness** , se muestran advertencias como errores en la salida de la consola, y se deben considerar bloqueadores de la actualización. A partir de la versión 1910 de Azure Stack Hub, las siguientes categorías forman parte del grupo **UpdateReadiness** :

  - **AzsInfraFileValidation**
  - **AzsActionPlanStatus**
  - **AzsStampBMCSummary**

- **SecretRotationReadiness** : comprobación para ver si la instancia de Azure Stack Hub está en un estado en el que se pueda ejecutar la rotación de secretos. Cuando se ejecuta el grupo **SecretRotationReadiness** , se muestran advertencias como errores en la salida de la consola y se deben considerar bloqueadores de rotación de secretos. Las siguientes categorías forman parte del grupo SecretRotationReadiness:

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStorageSvcsSummary**
  - **AzsStoreSummary**

#### <a name="group-parameter-example"></a>Ejemplo de parámetro Group

El siguiente ejemplo ejecuta **Test-AzureStack** para probar la preparación del sistema antes de instalar una actualización o una revisión mediante **Group**. Antes de iniciar la instalación de una actualización o de una revisión, ejecute **Test-AzureStack** para comprobar el estado de la instancia de Azure Stack Hub:

```powershell
Test-AzureStack -Group UpdateReadiness
```

Si Azure Stack Hub ejecuta una versión anterior a 1811, use los siguientes comandos de PowerShell para ejecutar **Test-AzureStack** :

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Ejecución de la herramienta de validación para probar la configuración de copia de seguridad de la infraestructura

*Antes* de configurar la copia de seguridad de la infraestructura, puede probar la ruta de acceso y las credenciales del recurso compartido de copia de seguridad mediante la prueba **AzsBackupShareAccessibility** :

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential $using:backupcred
  ```

*Después* de configurar la copia de seguridad, puede ejecutar **AzsBackupShareAccessibility** para asegurarse de que el recurso compartido sea accesible desde ERCS:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

Para probar las nuevas credenciales con el recurso compartido de la copia de seguridad configurada, ejecute: 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```

### <a name="run-validation-tool-to-test-network-infrastructure"></a>Ejecución de la herramienta de validación para probar la infraestructura de red

Esta prueba comprueba la conectividad de la infraestructura de red que omite la red definida por software (SDN) de Azure Stack Hub. Muestra la conectividad de una VIP pública a los reenviadores DNS configurados, los servidores NTP y los puntos de conexión de autenticación. Esto incluye la conectividad a Azure cuando se usa Azure AD como proveedor de identidades o el servidor federado cuando se usa AD FS como proveedor de identidades.

Incluya el parámetro debug para obtener una salida detallada del comando:

```powershell
Test-AzureStack -Include AzsNetworkInfra -Debug
```

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de las herramientas de diagnóstico de Azure Stack Hub y el registro de problemas, consulte [Uso del punto de conexión con privilegios (PEP) para recopilar registros de diagnóstico](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002).

Para más información acerca de la solución de problemas, consulte [Solución de problemas de Microsoft Azure Stack Hub](azure-stack-troubleshooting.md).