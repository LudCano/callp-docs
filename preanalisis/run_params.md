---
title: run_params.py
layout: default
nav_order: 1
parent: Parámetros iniciales
---

# run_params.py
Este archivo tiene dos partes, la primera se encarga de utilizar las rutas por defecto para el código, que se basa en el módulo `place.py`, y depende fuertemente del lugar donde se ejecuta el código. Mientras la segunda consta de parámetros regulares que son los de defecto.

## Parámetros regulares
```python
data_picked = "2024_02_26" #dato escogido por defecto
calib_picked = "2023_09_07" #calibrador escogido por defecto
# Banderas por defecto
plot_dark_noise = False #mostrar gráfico de ruido
plot_cdep = False       #mostrar calibrador
plot_ql = True          #mostrar quicklook
plot_dep = False        #mostrar depolarizador
use_log = True          #crear log
tiempo_promedio = 10    #tiempo a promediar
plot_molecular = True   #hacer ajuste molecular
```
