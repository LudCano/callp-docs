---
layout: default
title: Ruido y calibración
nav_order: 6
has_children: true
has_toc: false
---

# Ruido y calibración
Scripts que leen los datos y obtienen señales de ruido y calibración. Todo el código relacionado a esta sección se encuentra en un único módulo `noise_calibration.py`. Sin embargo, es conveniente separar a estos dos procesos, los cuales son:

## [Corriente oscura](dark_noise)

En el [lidar_master.py](../preanalisis/lidar_master) es llamado como:

```python
# --------------- en lidar_master.py ---------------------
#=======================================================================
## 3. DARK NOISE (CORRIENTE OSCURA)
#=======================================================================
from noise_calibration import dark_noise
ch1n, ch2n = dark_noise(ruta_dark, False, plot_dark, use_log)
```


## [Calibradores](calibracion)


En el [lidar_master.py](../preanalisis/lidar_master) es llamado como:

```python
# --------------- en lidar_master.py ---------------------
#=======================================================================
## 4. CALIBRACIÓN
#=======================================================================
from noise_calibration import mkcdep
cdep = mkcdep(calib_dark, ruta_p45, ruta_m45, False, plot_cdep, use_log, carpeta_gen)
```
