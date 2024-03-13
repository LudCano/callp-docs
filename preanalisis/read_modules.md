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

## 1. Selección de lugar (Función `place_selected()`)
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

## 2. Funciones del archivo log
Las siguientes funciones son utilizadas para escribir en el archivo log, crearlo y crear su header.

### 2.1. `myprint(text, flg)`
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

### 2.2. `print_log(txt)`
Esta función es usada solamente en este módulo y sirve simplemente para generar una línea directamente en el log.
```python
def print_log(txt):
    log = open(log_fname, "a")
    log.write(txt + "\n")
    log.close()
```

### 2.3. `create_log(aux_dir, use_log)`
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

### 2.4. `create_header(p, flg)`
Genera el header (inicio) del archivo log, para eso necesita los parámetros iniciales (diccionario `p`) y `flg` nos indica si se usará en el log o solamente en pantalla de terminal.

```python
def create_header(p, flg):

    myprint("-------------------", flg)
    myprint("*******************", flg)
    myprint("CÓDIGO LIDAR LA PAZ", flg)
    myprint("*******************", flg)
    myprint("Ejecutado en:", flg)
    now = datetime.now()
    now = now.strftime("%d/%m/%Y %H:%M:%S")
    myprint(now, flg)
    myprint("*******************", flg)

    if flg:
        print_log("PARÁMETROS USADOS...")
        print_log("Dato escogido = " +p[0])
        print_log("Calibrador escogido = "+ p[1])
        print_log("Mostrar ruido =  "+ str(p[2]))
        print_log("Mostrar calibracion = "+ str(p[3]))
        print_log("Mostrar quicklook = "+ str(p[4]))
        print_log("Mostrar DEP = "+ str(p[5]))
        print_log("*******************")
```

El header del log muestra la siguiente información:
![](figs/header_log.png)





