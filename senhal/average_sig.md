---
title: Promediado de señal (RCS)
layout: default
nav_order: 2
parent: Procesamiento de señal
---

# Señal promediada (RCS)
Este cálculo está controlado por una única función (`mkavesig`).
## `mkavesig(ruta_datos, ch1n, ch2n, cdep, fechas, carpeta_aux, use_log)`
La funcioń genera los datos corregidos por rango, ademas de ser la lectura original de los datos. A la vez nos apoyamos fuertemente en los parámetros de ejecución que están en [params](../preanalisis/params).

{: .parametros }
> Tiene los siguientes inputs:
> - `ruta_datos` : Ruta de los datos a procesar, obtenidos de [read_modules.py](../preanalisis/read_modules)
> - `ch1n` y `ch2n` : Vectores de ruido (corriente oscura) para los canales 1 y 2 respectivamente. Obtenidos de [dark_noise.py](../ruidocalib/dark_noise) 
> - `cdep` : Vector de calibración de polarización, obtenidos de [noise_calibration.py](../ruidocalib/calibracion)
> - `fechas` : Fecha del dato a analizar.
> - `carpeta_aux`: Carpeta de los datos a analizar, para el guardado de archivos temporales.
> - `use_log`: Si se desea manejar el archivo log (ya creado).
>   
> Devuelve:
> - `rcst` : Señal corregida sumada de ambos canales (`rcs1 + rcs2`).
> - `rcs1` y `rcs2` : Señal corregida por rango del canal 1 y 2, respectivamente.
> - `signal_read`: Señal (original) leída, consiste en una tupla de (tiempo, canal1, canal2).

La primera parte del código se dedica a importar y asignar variables a los parámetros de `params.py` y buscar si el archivo con las señales corregidas existe. El formato que busca el archivo `carpeta_aux/YYYY_MM_DD_rcsX.txt`, donde `X` puede ser `1`, `2` para los canales 1 y 2 respectivamente o `t` para la suma. Por ejemplo `carpeta_aux/2024_10_18_rcs1.txt`.

En caso encuentre a los 3 archivos asigna `flg = True` y procede a leer estos archivos encontrados, caso contrario procede a realizar el cálculo y generarlos.

```python
def mkavesig(ruta_datos, ch1n, ch2n, cdep, fechas, 
             carpeta_aux, use_log):
    ## Importando parámetros desde params.py
    rz = params.rz
    rdz = params.rdz
    anz = params.anz
    rnz = params.rnz
    bshift = params.bshift

    myprint("Buscando si ya existen los archivos...", use_log)
    rcs1_fname = f"{carpeta_aux}/{fechas}_rcs1.txt"
    rcs2_fname = f"{carpeta_aux}/{fechas}_rcs2.txt"
    rcst_fname = f"{carpeta_aux}/{fechas}_rcst.txt"
    flg1 = os.path.exists(rcs1_fname)
    flg2 = os.path.exists(rcs2_fname)
    flg3 = os.path.exists(rcst_fname)
    if flg1 and flg2 and flg3:
        myprint("Se encontraron RCS, saltamos cálculos", use_log)
        flg = True
        signal_read = None
    else:
        myprint("No se encontraron señales RCS, calculando...", use_log)
        flg = False
```

De nuevo, si no encontramos los archivos, tenemos `flg = False` y reailzamos el cálculo leyendo primero la señal del archivo en la ruta `ruta_datos`.. NOta: Apenas leemos el archivo procedemos a guardar los datos leídos en una tupla llamada `signal_read`.

```python
    if flg == False:
        # read signals
        t,ch1,ch2 = read_sig(ruta_datos)
        signal_read = (t, ch1, ch2) #datos leídos
    

        myprint("CALCULANDO SEÑAL PROMEDIO...", use_log)
        dn1 = ch1n
        dn2 = ch2n    
        cdep = cdep
        
        ch1_ = ch1.copy()
        ch2_ = ch2.copy()
        for i in tqdm(range(len(t)),"Promediando"):
            s1 = ch1[:,i] - dn1
            ch1_[:,i] = s1 - np.average(s1[900:1000])
            s2 = ch2[:,i] - dn2
            ch2_[:,i] = s2 - np.average(s2[900:1000])
            
        # average and background subtract
        ch1a = np.average(ch1_[:,:],axis=1)
        ch2a = np.average(ch2_[:,:],axis=1)
        
        z = rz
        if rdz == 15.0:
            rcs1_ = np.roll(ch1a, bshift) * z * z
            rcs2_ = np.roll(ch2a, bshift) * z * z * cdep
            #rcs1 = np.array([sum(rcs1_[j:j+2])/2.0 
            #                        for j in range(0, rnz,2)])
            #rcs2 = np.array([sum(rcs2_[j:j+2])/2.0 
            #                        for j in range(0, rnz,2)])
            rcs1 = rcs1_
            rcs2 = rcs2_
        elif rdz == 30.0:
            cdep_ = np.array([sum(cdep[j:j+2])/2.0 for j in range(0,1000,2)])
            rcs1_ = np.roll(ch1a,bshift) * z * z
            rcs2_ = np.roll(ch2a,bshift) * z * z
            rcs1 = rcs1_[0:anz]
            rcs2 = rcs2_[0:anz] * cdep_
        
        rcst = rcs1 + rcs2
```

Una vez realizado el cálculo, procedemos a guardar los resultados con el mismo formato deseado, así lo podremos encontrar en una siguiente ejecución del código y por consiguiente saltarnos este paso.

```python
        myprint("Correcto!", use_log)
        myprint("Guardando archivos de RCS... ", use_log)
        np.savetxt(rcs1_fname, rcs1)
        myprint(f"Archivo {rcs1_fname} creado", use_log)
        np.savetxt(rcs2_fname, rcs2)
        myprint(f"Archivo {rcs2_fname} creado", use_log)
        np.savetxt(rcst_fname, rcst)
        myprint(f"Archivo {rcst_fname} creado", use_log)

    else:
        rcst = np.genfromtxt(rcst_fname)
        rcs1 = np.genfromtxt(rcs1_fname)
        rcs2 = np.genfromtxt(rcs2_fname)

    signal_read = (t, ch1, ch2)

```

Finalmente retornamos los valores deseados de la función con `return rcst, rcs1, rcs2, signal_read`



