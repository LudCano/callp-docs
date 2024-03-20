---
layout: default
title: Procesamiento de señal
nav_order: 7
has_children: true
has_toc: false
---

# Procesamiento de señales

El procesamiento de señal consiste en dos grandes pasos, la creación de la señal que genera la señal corregida por rango (_Range Corrected Signal_ o RCS), y la señal promediada que será usada en el quicklook. Ambas funciones se encuentran en un script llamado `signal_ql.py`.


{: .librerias }
> Utiliza las siguientes librerías
> - `numpy` : Librería numérica
> - `matplotlib` : Librería para gráficos
> - `read_sig` y `myprint`: Módulos de apoyo que se encuentran en [read_modules.py](../preanalisis/read_modules)
> - `params` : Parámetros de ejecución [params.py](../preanalisis/params)
> - `os`: Uso del sistema operativo, creación de carpetas
> - `tqdm`: Barras de progreso en terminal

## [Creación de la señal](make_sig)

## [Promediado de la señal](average_sig)
