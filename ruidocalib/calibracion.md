---
title: Calibración
layout: default
nav_order: 2
parent: Ruido y calibración
has_toc: true
---
# Calibración
La calibración en el sistema LIDAR depende de dos archivos, que varían en 45mm el instrumento. Esta metodología debe ser mejor explicada.

Para esto dependemos de dos archivos: `*_p45.txt` y `*_m45.txt`, que tienen su archivo dark ([corriente oscura](dark_noise)) correspondiente.

{: .note }
Hasta la fecha (marzo 2024) sólo se cuentan con los calibradores tomados en fecha 7 de septiembre, siendo los archivos `2023_09_07_HR2032_A90_p45_2CH.txt` y `2023_09_07_HR2046_A90_m45_2CH.txt`.

El menú interactivo da la opción a escoger una fecha de calibradores, como por el momento sólo hay 1 set de los mismos, entonces sólo hay una opción.

Para evitar recalcular la calibración cada vez, se guarda un archivo temporal con este resultado, en caso ya exista el programa se salta un paso de cálculo y directamente se procede a graficar (si así se desea).

## Hacer calibrador de polarizador [`mkcdep(ruta_data, ruta_p45, ruta_m45, interactivo, plot_cdep, use_log, carpeta_aux)`]
A este proceso lo maneja una función grande llamada `mkcdep`. Que cuenta con los siguientes parámetros:

{: .parametros }
> Tiene los siguientes inputs:
> - `ruta_data` : Ruta de los datos a trabajar, obtenidos de [read_modules.py](../preanalisis/read_modules)
> - `ruta_p45` : Ruta a calibrador p45.
> - `ruta_m45` : Ruta a calibrador m45.
> - `interactivo` : [no usado] Si se desea hacer preguntas por terminal.
> - `plot_cdep` : Mostrar gráfico de calibradores? Se obtiene de la interfaz [interactivo.py](../preanalisis/interactivo)
> - `use_log` : Imprimir información en el log?
> - `carpeta_aux`: Carpeta auxiliar donde guardar los datos (que ahora es la carpeta de los datos originales).
>   
> Devuelve:
> - `cdep` : Calibrador calculado.

Seccionaremos la función, primero busca si el archivo cdep ya existe en la carpeta auxiliar.

```python
# ======= PARTE 1 ========
    import os
    from place import place
    if place == "Linux":
        # Como se usan distitnos separadores para distintos OS, entonces se hace esta diferencia
        ddate = ruta_data.split("/")[-1][:10]
    else:
        ddate = ruta_data.split("\\")[-1][:10] 
    
    cdep_fname = os.path.join(carpeta_aux, f"cdep{ddate}.txt")

    #Revisando si ya existe para saltarse el cálculo
    flg = os.path.exists(cdep_fname)
    if flg:
        myprint("Se encontró archivo CDEP", use_log)
        cdep = np.genfromtxt(cdep_fname)
    else:
        myprint("CALCULO DE CALIBRACIÓN POLARIZADORES...", use_log)


```

