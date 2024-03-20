---
title: Crear señal
layout: default
nav_order: 2
parent: Procesamiento de señal
---

# Crear señal (ch1 y ch2)
El objetivo de este algoritmo es calcular vectores promedio para visualización (Quicklook). Está manejado primordialmente por la función `mksig`.

`mksig(ruta_datos, ch1n, ch2n, cdep, use_log, carpeta_aux, fechas, dtt, signal_read, all_day = False)`

{: .parametros }
> Tiene los siguientes inputs:
> - `ruta_datos` : Ruta de los datos a procesar, obtenidos de [read_modules.py](../preanalisis/read_modules)
> - `ch1n` y `ch2n` : Vectores de ruido (corriente oscura) para los canales 1 y 2 respectivamente. Obtenidos de [dark_noise.py](../ruidocalib/dark_noise) 
> - `cdep` : Vector de calibración de polarización, obtenidos de [noise_calibration.py](../ruidocalib/calibracion)
> - `fechas` : Fecha del dato a analizar.
> - `carpeta_aux`: Carpeta de los datos a analizar, para el guardado de archivos temporales.
> - `use_log`: Si se desea manejar el archivo log (ya creado).
> - `dtt`: Delta tiempo a promediar (intervalo, por defecto son 10 segundos).
> - `signal_read`: Tupla de datos ya leídos por la función [`mkavesig()`](average_sig.md), si no fueron leídos adopta el valor None.
>   
> Devuelve:
> - `t0` : Vector de tiempo.
> - `ch1` y `ch2` : Señal promediada (matrices).


De una forma similar a cuando promediamos los datos y obtuvimos el RCS, primero revisamos si ya se realizó este cálculo previamente, en caso positivo nos saltamos el paso y leemos los archivos existentes, caso contrario los calculamos y guardado.

```python
    ## Buscando si ya existe el archivo
    myprint("Buscando si ya existen los archivos...", use_log)
    
    ch1_fname = f"{carpeta_aux}/{fechas}_CH1_d{fdt}.txt"
    ch2_fname = f"{carpeta_aux}/{fechas}_CH2_d{fdt}.txt"
    t0_fname = f"{carpeta_aux}/{fechas}_t0_d{fdt}.txt"
    flg1 = os.path.exists(ch1_fname)
    flg2 = os.path.exists(ch2_fname)
    flg3 = os.path.exists(t0_fname) 
    if flg1 and flg2 and flg3:
        myprint("Se encontraron señales CH, saltamos cálculos", use_log)
        flg = True
    else:
        myprint("No se encontraron señales CH, calculando...", use_log)
        dt = dtt
        flg = False
```

En caso que no existan los archivos temporales y tampoco se haya leído los datos previamente procedemos a hacerlo.

El algoritmo consiste en promediar cada `dtt` segundos, como tenemos varios datos por segundo también se toma en cuenta eso, luego quitamos el ruido mediante la función [subtract_bg()](../ruidocalib/dark_noise), donde igual hacemos el recorrido de datos (shift). Este algoritmo crea una matriz para todo el día, lo cual nos serviría, pero la mayoría de los datos son en un rango dado, por lo que se recorta la matriz y la guardamos temporalmente.

```python
        # read signals
        myprint("PROMEDIANDO DATOS...", use_log)
        # No se leen de nuevo, esto está en la tupla signal_read
        if signal_read != None:
            t0   = signal_read[0]
            ch1v = signal_read[1]
            ch2v = signal_read[2]
        else:
            #en caso sí sea None entonces sí leemos los datos de nuevo
            t0,ch1v,ch2v = read_sig(ruta_datos)
        
        dn1 = ch1n
        dn2 = ch2n    
        cdep = cdep
        
        t  = t
        dt = dt / 3600.0
        nz = anz
        nt = nt
        ch1 = np.full([nz,nt],-9999.0)
        ch2 = np.full([nz,nt],-9999.0)
        z  = rz
        from noise_calibration import subtract_bg
        for i in tqdm(range(nt), "Calculando..."):
            flag = 0
            st   = -1
            ed   = -1
            if t0[0] > t[i]+dt/2.0: continue
            if t0[len(t0)-1] < t[i]-dt/2.0: break
            for ii in range(len(t0)):
                if t0[ii] >= t[i]-dt/2.0 and t0[ii] < t[i]+dt/2.0:
                    if flag == 0:
                        st = ii
                        flag = 1
                    if flag == 1:
                        ed = ii
                if t0[ii] >= t[i]+dt/2.0: break
            if st == -1 or ed == -1: continue
            ch1_ = subtract_bg(np.average(ch1v[:,st:ed+1],axis=1),dn1)
            ch2_ = subtract_bg(np.average(ch2v[:,st:ed+1],axis=1),dn2)


            rcs1 = np.roll(ch1_,bshift) * z * z
            rcs2 = np.roll(ch2_,bshift) * z * z * cdep
            np.savetxt("rcs1.txt", rcs1)
            np.savetxt("rcs2.txt", rcs2)
            ch1[:,i] = np.array([sum(rcs1[j:j+2])/2.0 
                                        for j in range(0,rnz,2)])
            ch2[:,i] = np.array([sum(rcs2[j:j+2])/2.0 
                                        for j in range(0,rnz,2)])
            
        ## CORTANDO LAS MATRICES PORQUE NORMALMENTE SON DE TODO EL DÍA
        sta = int(t0[0]/dt); fin = int(t0[-1]/dt)
        if all_day:
            pass
        else:
            ch1 = ch1[:,sta:fin]
            ch2 = ch2[:,sta:fin]

        myprint("Correcto!", use_log)
        myprint("Guardando archivos de CH... ", use_log)
        ch1_fname = f"{carpeta_aux}/{fechas}_CH1_d{fdt}.txt"
        ch2_fname = f"{carpeta_aux}/{fechas}_CH2_d{fdt}.txt"
        t0_fname = f"{carpeta_aux}/{fechas}_t0_d{fdt}.txt"
        np.savetxt(ch1_fname, ch1)
        myprint(f"Archivo {ch1_fname} creado", use_log)
        np.savetxt(ch2_fname, ch2)
        myprint(f"Archivo {ch2_fname} creado", use_log)
        np.savetxt(t0_fname, t0)
        myprint(f"Archivo {t0_fname} creado", use_log)
    else:
        t0 = np.genfromtxt(t0_fname)
        ch1 = np.genfromtxt(ch1_fname)
        ch2 = np.genfromtxt(ch2_fname)
```

Y finalmente devolvemos los parámetros deseados `return t0, ch1, ch2`.



