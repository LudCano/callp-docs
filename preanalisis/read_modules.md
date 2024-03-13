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

## 3. Buscando datos (`get_data_list(data_rut, p)`)
**Genera el archivo `data.csv`**  
Esta función de forma general busca los datos que cumplan los parámetros deseados dentro de la carpeta `data_rut` (ruta de datos que proviene de [interactivo](interactivo)). Para esto usa la función `os.walk()` que busca tanto en carpetas como en subcarpetas, así los datos se pueden encontrar en cualquier anidación (cada lugar de ejecución tiene diferentes formas de ordenar los datos) y serán encontrados.

```python
def get_data_list(data_rut, p):

    def get_time_date(x):
        # OBTIENE LA FECHA Y HORA A PARTIR DE UN TEXTO
        f = x[:10]
        h = x[13:15]
        m = x[15:17]
        hora = h+":"+m
        return f, hora

    from place import place
    # LOS SEPARADORES SON USADOS DE FORMA DIFERENTE
    # EN WINDOWS Y LINUX
    if place == "Linux":
        separador = "/"
    else:
        separador = "\\"

    df = open("data.csv", "w")
    df.write("fecha,hora,tipo,ruta,carpeta\n")
    c = 0
    for root, dirs, files in os.walk(data_rut):
        # BUSQUEDA DE DATOS EN LA CARPETA data_rut
        if root == p:
            #exluding looking into the calib files
            pass
        elif len(root) > len(p) and root[:len(p)] == p:
            #excluding entering the calib files directory
            pass
        elif len(files) == 2:
            for i in files:
                if i[-12:] == "data_2CH.txt":
                    f, h = get_time_date(i)
                    r = os.path.join(root, i)
                    
                    dr = root.split(separador)
                    ddr = ""
                    for i in dr[:-1]:
                        ddr  = ddr +separador+ i
                    ddr = ddr[1:]
                    linea = f + "," + h + ",data," + r + "," + ddr + "\n"
                    #print(linea)
                    c = c+1
                    df.write(linea)
                if i[-12:] == "dark_2CH.txt":
                    k = "dark"
                    f, h = get_time_date(i)
                    r = os.path.join(root, i)

                    dr = root.split(separador)
                    ddr = ""
                    for i in dr[:-1]:
                        ddr  = ddr +separador+ i
                    ddr = ddr[1:]
                    
                    linea = f + "," + h + ",dark," + r + "," + ddr + "\n"
                    c = c+1
                    df.write(linea)
    print(f"Se escribieron {c} lineas")
    df.close()

```



