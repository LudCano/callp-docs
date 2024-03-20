---
title: Promediado de señal (RCS)
layout: default
nav_order: 2
parent: Procesamiento de señal
---

# Señal promediada (RCS)
Este cálculo está controlado por una única función (`mkavesig`).
## `mkavesig(ruta_datos, ch1n, ch2n, cdep, fechas, carpeta_aux, use_log)`
La funcioń genera los datos corregidos por rango, ademas de ser la lectura original de los datos. A la vez nos apoyamos fuertemente en los parámetros de ejecución que están en [params](../preanalisis/params).

{: .parametros }
> Tiene los siguientes inputs:
> - `ruta_datos` : Ruta de los datos a procesar, obtenidos de [read_modules.py](../preanalisis/read_modules)
> - `ch1n` y `ch2n` : Vectores de ruido (corriente oscura) para los canales 1 y 2 respectivamente. Obtenidos de [dark_noise.py](../ruidocalib/dark_noise) 
> - `cdep` : Vector de calibración de polarización, obtenidos de [noise_calibration.py](../ruidocalib/calibracion)
> - `fechas` : Fecha del dato a analizar.
> - `carpeta_aux`: Carpeta de los datos a analizar, para el guardado de archivos temporales.
> - `use_log`: Si se desea manejar el archivo log (ya creado).
>   
> Devuelve:
> - `rcst` : Señal corregida sumada de ambos canales (`rcs1 + rcs2`).
> - `rcs1` y `rcs2` : Señal corregida por rango del canal 1 y 2, respectivamente.
> - `signal_read`: Señal (original) leída, consiste en una tupla de (tiempo, canal1, canal2).

Pssfafa
