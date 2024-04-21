---
layout: post
title: Desvelando las aplicaciones instaladas en Windows 10
---

`appwiz.cpl` es el elemento del Panel de Control que todos conocen como **Programas y características**. Es la herramienta que permite desinstalar aplicaciones desde que se implementó en Windows 98 (¡hoy es el 26º aniversario de aquel famoso BSOD, felicítenlos!). Técnicamente, este elemento interactúa con el registro de Windows, específicamente con las claves relacionadas con la información de instalación de programas. Los valores de estas claves dependen de muchos factores como el tipo de arquitectura, tipo de instalación o donde se haya instalado la aplicación que informa el registro.

Con algo de ingeniería inversa, podemos llegar a entender casi completamente cómo este elemento del Panel de Control recupera la información sobre los programas instalados, y así descubrir que puede faltar mucha información que podría ser relevante, por ejemplo, para administradores de sistemas o usuarios más exigentes. Conociendo las fuentes de datos, podemos intentar interrogarlas nosotros mismos mediante Powershell.

## Consultando el registro

La primera fuente es la clave de registro `HKLM\Software\Microsoft\Windows\CurrentVersion\Uninstall`. Dentro de esta clave, cada subclave representa una aplicación instalada en el sistema. Cada una de estas subclaves contiene información sobre la aplicación, como su `DisplayName`, `DisplayVersion`, `Publisher` y más. Veamos cómo sacar la información con el cmdlet `Get-ItemProperty`. 

{% highlight powershell %}
Get-ItemProperty "HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*" | Select-Object DisplayName, DisplayVersion, Publisher
{% endhighlight %}

Hemos utilizado `Select-Object` para que el output apareciera inteligible, pero se puede omitir para revisar los valores de cada subclave. Es posible también utilizar `Format-Table -AutoSize` si no se requieren valores específicos.

Otra fuente es la clave de registro `HKLM:\Software\Wow6432node\Microsoft\Windows\CurrentVersion\Uninstall` donde podemos encontrar las aplicaciones de 32bits. En esta ocasión hemos creado un array y agrupado los outputs.

{% highlight powershell %}
# creamos un array donde almacenaremos los valores
$output = @()

# informamos el array con los valores del registro
$output += Get-ItemProperty "HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*"
$output += Get-ItemProperty "HKLM:\Software\Wow6432node\Microsoft\Windows\CurrentVersion\Uninstall\*"

# filtramos, ordenamos y mostramos los resultados sin duplicados
$output | Select-Object DisplayName, DisplayVersion, Publisher | Sort-Object DisplayName -Unique
{% endhighlight %}

Hay otras fuentes como la `HKLM\Software\Classes\Installer\Products`, donde se almacenan los valores de las aplicaciones instaladas mediante MSI, o `HKCU\Software\Microsoft\Windows\CurrentVersion\Uninstall` y `HKCU\Software\Microsoft\Installer\Products`, donde podemos encontrar información sobre las aplicaciones instaladas solamente para el current user.

## Clases WMI
A través de las Clases WMI, podemos acceder a una amplia gama de información sobre el software instalado en un sistema, desde el nombre y la versión de las aplicaciones hasta detalles más específicos como la ubicación de instalación y la fecha de instalación. Cabe destacar que algunas, para su uso, requieren software preinstalado como, por ejemplo, clientes SMS/SCCM. Los cmdlets que se suelen utilizar en estos casos son `Get-CIMInstance` y `Get-WmiObject`. WMI es la versión de Microsoft de CIM. CIM es un estándar open-source para la recopilación y visualización de información de un ordenador. ¿La diferencia? A nivel local casi ninguna, sin embargo, en términos de **remoting**, cuando hablamos de WMI, el equipo remoto debe permitir el tráfico de red entrante en los puertos TCP 135, 445 y otros puertos adicionales asignados dinámicamente entre 1024 y 1034 (¡pero esto lo dejamos para otro post!). Una última nota importante es que estos cmdlets son más lentos respecto a las interrogaciones del registro porque, al parecer, pueden realizar comprobaciones de consistencia contra cada una de las aplicaciones.

{% highlight powershell %}
Get-CIMInstance Win32_Product | Select-Object Name, Version, Vendor | Sort-Object Name -Unique
{% endhighlight %}

{% highlight powershell %}
Get-WmiObject -Class Win32_Product | Select-Object Name, Version, Vendor | Sort-Object Name -Unique
{% endhighlight %}

{% highlight powershell %}
Get-WmiObject -Class Win32_SoftwareFeature | Select-Object ProductName, Version, Vendor | Sort-Object ProductName -Unique
{% endhighlight %}

Otras clases que se pueden utilizar en entornos SCCM son `Win32_AddRemovePrograms`, `Win32_InstalledSoftwareElement` o `Win32_ProductSoftwareFeatures`.

## Updates 
Por último, si se necesitáran consultar las últimas actualizaciones del sistema operativo, sería posible utilizando la clase `Win32_QuickFixEngineering`.

{% highlight powershell %}
Get-WmiObject -Class Win32_QuickFixEngineering
{% endhighlight %}
