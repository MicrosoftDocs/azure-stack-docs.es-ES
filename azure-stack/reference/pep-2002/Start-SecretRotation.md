---
title: Cmdlet del punto de conexión con privilegios de Start-SecretRotation para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack Hub para PowerShell: Start-SecretRotation'
author: mattbriggs
ms.topic: reference
ms.date: 07/29/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 8a3dee9ad26c37626c0f3383b874f149be4fac01
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742601"
---
# <a name="start-secretrotation"></a>Start-SecretRotation

## <a name="synopsis"></a>Sinopsis
Desencadena la rotación de secretos en una unidad de escalado.

## <a name="syntax"></a>Sintaxis

```
Start-SecretRotation [-PathAccessCredential <Object>] [-ReRun] [-CertificatePassword <Object>] [-Internal]
 [-PfxFilesPath <Object>] [-AsJob]
```

## <a name="description"></a>Descripción
Invoca el proceso de rotación de los secretos de la infraestructura de un sistema de Azure Stack Hub. De forma predeterminada, solo se cambian los certificados de los puntos de conexión de la infraestructura de red externa. Consulte [Cambio de secretos en Azure Stack Hub](../../operator/azure-stack-rotate-secrets.md) para más información.

## <a name="parameters"></a>Parámetros

### <a name="-internal"></a>-Internal
Cambie los secretos de los puntos de conexión de la infraestructura de red interna.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-pfxfilespath"></a>-PfxFilesPath
Ruta de acceso de los nuevos archivos pfx compartidos para la rotación de certificados externos.
Es obligatorio si se desea la rotación de certificados externos.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-pathaccesscredential"></a>-PathAccessCredential
Credenciales para acceder a PfxFilesPath.
Es obligatorio si se desea la rotación de certificados externos.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-certificatepassword"></a>-CertificatePassword
Contraseña para los nuevos archivos pfx proporcionados.
Es obligatorio si se desea la rotación de certificados externos.
Puede ser diferente de la contraseña pfx original proporcionada en el momento de la implementación inicial.
Se volverán a generar los archivos pfx con la contraseña de CA correcta.

Uso:

```console
    # Rotates external certificates only
    Start-SecretRotation -PfxFilesPath \<String\> -PathAccessCredential \<PSCredential\> -CertificatePassword \<SecureString\>

    # Rotates internal secrets only
    Start-SecretRotation -Internal  

    # Reruns internal secrets only
    Start-SecretRotation -Internal -ReRun 

    # Reruns external certificates only
    Start-SecretRotation -ReRun
```

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-rerun"></a>-ReRun
 

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-asjob"></a>-AsJob


```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el acceso y el uso de un punto de conexión con privilegios, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
