(---
title: read_modules.py
layout: default
nav_order: 1
parent: Pre análisis
---

# read_modules.py
Este módulo tiene varias funciones que son llamadas tanto por [lidar_master.py](lidar_master) (directamente) como por el módulo [interactivo.py](interactivo).

{: .librerias }
> Utiliza las siguientes librerías
> - `os` : Manejo de sistema operativo, búsqueda de carpetas y creación de paths.
> - `datetime` : Manejo de fechas y 
> - `numpy` : Librería numérica
> - `tqdm` : Barras de progreso en iteraciones

## Selección de lugar
Para definir en qué lugar está siendo ejecutado el código usamos una función llamada `place_selected()`. Esta función busca si existe el archivo llamado `place.py`, en caso cierto define ese como el lugar (para los parámetros predeterminados) y en caso contrario ejecuta la ventana [`pick_place()`](pick_place).

```python
def place_selected():
    if os.path.exists("place.py"):
        pass
    else:
        with open("pick_place.py") as f:
            exec(f.read())
    return
```
