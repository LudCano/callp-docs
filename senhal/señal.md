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

## [Promediado de la señal](average_sig)

En el [lidar_master.py](../preanalisis/lidar_master) es llamado como:

```python
# --------------- en lidar_master.py ---------------------

#=======================================================================
## 5. PROMEDIADO DE DATOS
#=======================================================================
from signal_ql import mkavesig, mksig
rcst, rcs1, rcs2, signal_read = mkavesig(ruta_datos, ch1n, ch2n, cdep, fecha, carpeta_gen, use_log)
```

## [Creación de la señal](make_sig)

En el [lidar_master.py](../preanalisis/lidar_master) es llamado como:

```python
# --------------- en lidar_master.py ---------------------
t0, ch1, ch2 = mksig(ruta_datos, ch1n, ch2n, cdep, use_log, carpeta_gen, fecha, tiempo_promedio, signal_read)
```
