---
title: params.py
layout: default
nav_order: 3
parent: Parámetros iniciales
---

# params.py
Acá tenemos parámetros importantes para el código del LIDAR, fuera de los datos escogidos, éstos no cambian.

{: .warning }
No cambiar estos parámetros sin revisar sus implicaciones, de ser necesario contactar al desarrollador.

```python
rnz = 1000                        #numero de bins
rdz = 15.0                        # rawdata range resolution meter #del log
rz = np.arange(rnz) * rdz         # m
bshift = -50                      # -50 for rdz = 15.0m, -25 for rdz = 30.0m
anz    = 500                      # largo del vector a finalizar
dt     = 10.0                     # segundos a promediar
nt     = int(24*3600/dt)          #segundos en un día
t = np.arange(nt) * dt / 3600.0 + dt / 2.0 / 3600.0 # hour

adz     = 30.0                     # average range resolution meter
az = np.arange(anz) * adz          # m


S1 = 50.0 #lidar ratio (sr)
```
