---
title: Punto de conexión con privilegios de Close-PrivilegedEndpoint para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Close-PrivilegedEndpoint'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b61955c6fce760654430b999c3b393e9093db535
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564019"
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
Invoca el proceso de rotación de secretos.

## <a name="parameters"></a>Parámetros

### <a name="-internal"></a>-Internal
 

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
    # Rotates external certificates only
    Start-SecretRotation -PfxFilesPath \<String\> -PathAccessCredential \<PSCredential\> -CertificatePassword \<SecureString\>

    # Rotates internal secrets only
    Start-SecretRotation -Internal  

    # Reruns internal secrets only
    Start-SecretRotation -Internal -ReRun 

    # Reruns external certificates only
    Start-SecretRotation -ReRun

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

Para más información sobre el acceso y el uso de un punto de conexión con privilegios, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).