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

### Cálculo de calibración de polarización
En caso que no encuentre el archivo ya calibrado, entonces procedemos a calcular el calibrador.

```python
# ============ PARTE 2 ================
    else:
        myprint("CALCULO DE CALIBRACIÓN POLARIZADORES...", use_log)
        t0,ch1,ch2=read_sig(ruta_data)

        ch1n = np.average(ch1,axis=1) #son ruidos ch1 from darks
        ch2n = np.average(ch2,axis=1) #ruido ch2

        t1,ch1_1,ch2_1 = read_sig(ruta_p45) # +45 deg
        #t1 de 45 grados +


        t2,ch1_2,ch2_2 = read_sig(ruta_m45) # -45 deg
        #t2 de 45 grados -

        # SE QUITAN LA CORRIENTE OSCURA DE LOS DARKS
        ch1_1a = subtract_bg(np.average(ch1_1,axis=1),ch1n)#+45 #corregido y promediado
        ch2_1a = subtract_bg(np.average(ch2_1,axis=1),ch2n)#-45 #
        ch1_2a = subtract_bg(np.average(ch1_2,axis=1),ch1n)#+45
        ch2_2a = subtract_bg(np.average(ch2_2,axis=1),ch2n)#-45
        cdep_bshift = -50
        p45a = np.roll(ch1_1a / ch2_1a, cdep_bshift) #correcciones
        m45a = np.roll(ch1_2a / ch2_2a, cdep_bshift) #correcciones


        mult=p45a*m45a
        mult[mult < 0] = -mult[mult < 0]
        cdep= np.sqrt(mult) #stdev necesario
        cdep_ = np.sqrt(mult)
        
        rnz     = 1000
        rdz     = 15.0 # 15 metros de resolución para Rawdata
        for j in range(20,100):
            cdep[j] = np.average(cdep_[j-3:j+3])
        cdep[36:] = np.average(cdep[33:39]) #<< constante de pol

        ## ESCRIBIENDO EL ARCHIVO EN LA CARPETA DESIGNADA
        with open(file=cdep_fname,mode='w') as fo:
            for j in range(rnz):
                fo.write('{0:>17.10E}\n'.format(cdep[j]))
        myprint(f"Archivo {cdep_fname} creado", use_log)
        myprint("Correcto!", use_log)        


```

## Gráfico de calibradores
Una vez calculado `cdep`, podemos graficarlo siempre y cuando la bandera `plot_cdep == True`

```python
# ======= PARTE 3 - Figura ===============
        rz = np.arange(rnz) * rdz
        plt.figure()
        plt.plot(rz, p45a, label = "+45")
        plt.plot(rz, m45a, label = "-45")
        plt.plot(rz, cdep, label = "mult")
        plt.xlabel("Altura [m]")
        plt.xlim(0,3000) #altura arbitraria hasta los 3km
        plt.ylabel('Signal [mV]')
        plt.title('Calibration plot')
        plt.ylim(1.7,2.5)
        plt.legend()
        plt.show()

```

Finalmente retornamos el cdep calculado (`return cdep`) y salimos del código.
