---
title: interactivo.py
layout: default
nav_order: 1
parent: Pre análisis
---

# interactivo.py
_Última actualización: 27 feb 2024_
Como dicho en la página principal, este módulo se centra en crear la interfaz para el menú principal, es llamado como un ejecutable a parte debido a particularidades de la librería usada (`tkinter`). Se apoya de varias funciones en el módulo [read_modules.py](read_modules).

{: .librerias }
> Utiliza las siguientes librerías
> - `tkinter` : Librería de interfaces.
> - `PIL` : (pillow) Para mostrar imágenes.

El menú principal del código se ve como la siguiente figura:  
![](figs/menu.png)

En la primera columna (Carpetas) se observan los siguientes parámetros:
 - **Carpeta de datos**: Ruta general donde se encuentran los datos (éstos pueden estar muy anidados, es decir, encontrarse dentro de varias subcarpetas).
 - **Carpeta de calibs**: Ruta de calibradores
 - **Carpeta auxiliar**: Para algunos outputs, los archivos `log` se escriben acá.
 - **Carpeta figuras**: Lugar donde se almacenan las figuras generadas por el código.
 - **Generar LOG** (checkbox): Si se desea que se genere el archivo log (se encuentra almacenado en la carpeta auxiliar).

Posteriormente tenemos un botón **ACTUALIZAR** que busca los datos y genera el archivo de compilado de información de los datos disponibles.

{ .note }
Es recomendado que después de usar el botón ACTUALIZAR se reinicie el programa, este bug será reparado en futuras versiones.

