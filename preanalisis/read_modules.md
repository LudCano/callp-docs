---
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

## Selección de lugar (Función `place_selected()`)
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

## Funciones del archivo log
Las siguientes funciones son utilizadas para escribir en el archivo log, crearlo y crear su header.

### `myprint(text, flg)`
Esta función (o mas bien, un proceso) es usada en módulos externos, y nos permite imprimir cualquier cadena de texto directamente en el log actual, para eso necesita el nombre del log una variable global creadad con otra función y una bandera que nos dice si imprimir en log o no (usado para debudding).
```python
def myprint(text, flg):
    if flg:
        log = open(log_fname, "a")
        print(text)
        log.write(text + "\n")
        log.close()
    else:
        print(text)
```

### `print_log(txt)`
Esta función es usada solamente en este módulo y sirve simplemente para generar una línea directamente en el log.
```python
def print_log(txt):
    log = open(log_fname, "a")
    log.write(txt + "\n")
    log.close()
```

### `create_log(aux_dir, use_log)`
**Retorna `log_fname`**
Genera el log, usando `aux_dir` (carpeta donde se almacenará) y una bandera (`use_log`) para decidir si crear el archivo o no, usa la fecha y hora actual para generar el archivo con el nombre de la hora actual, retorna el nombre de archivo de log `log_fname`.
```python
def create_log(aux_dir, use_log):
    global log_fname
    if use_log:
        now = datetime.now()
        log_fname = now.strftime("%d_%m_%Y_%H_%M_%S")
        log_fname = log_fname + ".txt"
        log_fname = os.path.join(aux_dir, "logs",log_fname)
        print(log_fname)
    else:
        log_fname = None
    return log_fname
```





