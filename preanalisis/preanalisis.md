---
layout: default
title: Pre análisis
nav_order: 5
has_children: true
has_toc: false
---

# Módulos de pre análisis
Los siguientes módulos se ejecutan al inicio y tienen como objetivo reconocer los datos existentes en un ordenador, obtener su información relevante y darle la opción al usuario de escoger cualquiera de estos parámetros.

{: .librerias }
> Utiliza las siguientes librerías
> - `os` : Para manejo de archivos, rutas, y búsqueda en carpetas.
> - `tkinter` : Librería de interfaces.
> - `pandas`: Manejo de tablas



A continuación un resumen de todos los módulos que comprenden el pre-análisis.

## [interactivo.py](interactivo)
Este módulo es la base del menú interfaz del código. Es llamado por el master como un ejecutable aparte, es decir:

```python
#-------------- (en lidar_master.py) ----------------------
with open("interactivo.py") as f:
    exec(f.read())
```

Nos devuelve todos los parámetros específicos de esta ejecución. Más detalles en la página dedicada a este módulo.

## [read_modules.py](interactivo)
Este es un script que contiene varias funciones y algoritmos, desde buscar los datos, presentarlos, crear el log y su respectivo header (encabezado) e incluso leer la primera parte de los datos. Es llamado por el master múltiples veces para usar funciones principales que se apoyan de otras.
```python
#-------------- (en lidar_master.py) ----------------------
#=======================================================================
## 1. CREACIÓN DEL HEADER
#=======================================================================
from read_modules import create_header, create_log
log_fname = create_log(carpeta_aux, use_log)
paramet = [data_picked, calib_picked, plot_dark, plot_cdep, plot_ql, plot_dep]
create_header(paramet, use_log)

#=======================================================================
## 2. ELECCIÓN DE DATOS Y ENCONTRAR LOS MISMOS
#=======================================================================
from read_modules import pick_calib, pick_data
# ESCOGER UN DATO
ruta_datos, ruta_dark, fecha, carpeta_gen = pick_data(use_log, False, data_picked)
# ESCOGER DATOS DE DEPOLARIZACION
ruta_p45, ruta_m45, calib_dark = pick_calib(pola_dir, False, calib_picked, use_log)
```


