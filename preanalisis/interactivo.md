---
title: interactivo.py
layout: default
nav_order: 2
parent: Pre análisis
---

# interactivo.py
_Última actualización: 27 feb 2024_  
Como dicho en la página principal, este módulo se centra en crear la interfaz para el menú principal, es llamado como un ejecutable a parte debido a particularidades de la librería usada (`tkinter`). Se apoya de varias funciones en el módulo [read_modules.py](read_modules).

{: .librerias }
> Utiliza las siguientes librerías
> - `tkinter` : Librería de interfaces.
> - `PIL` : (pillow) Para mostrar imágenes.
> - `pandas` : Manejo de tablas .csv.

El menú principal del código se ve como la siguiente figura:  
![](figs/menu.png)

### Primera columna: Carpetas
En la primera columna se observan los siguientes parámetros:
 - **Carpeta de datos**: Ruta general donde se encuentran los datos (éstos pueden estar muy anidados, es decir, encontrarse dentro de varias subcarpetas).
 - **Carpeta de calibs**: Ruta de calibradores
 - **Carpeta auxiliar**: Para algunos outputs, los archivos `log` se escriben acá.
 - **Carpeta figuras**: Lugar donde se almacenan las figuras generadas por el código.
 - **Generar LOG** (checkbox): Si se desea que se genere el archivo log (se encuentra almacenado en la carpeta auxiliar).

Posteriormente tenemos un botón **ACTUALIZAR** que busca los datos y genera el archivo de compilado de información de los datos disponibles.

{: .note }
Es recomendado que después de usar el botón ACTUALIZAR se reinicie el programa, este bug será reparado en futuras versiones.

### Segunda columna: Escoger dato
En esta columna escogeremos el dato deseado a través de 3 listas desplegables. El manejo de las listas superiores (gestión y mes) filtran automáticamente los datos disponibles en la última lista desplegable (Dato a escoger)  
 - **GESTION**: Nos permite escoger el año.
 - **MES**: Filtra por mes en formato `YYYY_MM` (por ejemplo `2022_10` es Octubre de 2023).
 - **Dato a escoger**: Fechas disponibles de datos. En caso que aparezca algún duplicado la elección es indiferente. Por defecto existe un dato ya seleccionado, se puede cambiar este parámetro en [run_params.py](run_params)

### Tercera columna: Opciones
Acá tenemos parámetros a usar durante el código, la mayoría son checkboxes para decidir si mostraremos algún gráfico en específico. Todos los parámetros por defecto se encuentran (y se pueden modificar en [run_params.py](run_params)).
 - **Segundos a promediar**: Cantidad de segundos a promediar los datos.
 - **Mostrar ruido**: Mostrar gráfico de ruido (detalles en [noise_calibration.py](noise_calibration)).
 - **Mostrar CDEP**: Mostrar gráfico de calibradores (detalles en [noise_calibration.py](noise_calibration)).
 - **Calibrador**: Calibradores disponibles, es recomendado usar el más reciente, deberían estar almacenados en la carpeta de calibs de la primera columna.
 - **Mostrar Quicklook**: Mostrar quicklook (detalles en [quicklook](../quicklook)).
 - **Mostrar DEP**: Mostrar depolarización (detalles en [depolarización](../depolarizacion)).
 - **Mostrar Molecular**: Realizar y mostrar el ajuste molecular (detalles en [Ajuste Molecular](../ajustemolecular)).

# Código
Ahora seccionaremos el código para mostrar lo que hace, en realidad, en Tkinter creamos objetos como botones, listas, etc. y posteriormente obtenemos los parámetros puestos a través de algún método.

Primero las librerías a utilizar
```python
import tkinter as tk
from tkinter import ttk
from PIL import Image, ImageTk
import pandas as pd
import params
import run_params
from lidar_master import get_from_interactive
```

Notemos que llamamos al master, específicamente llamamos a una función ahí que se llama `get_from_interactive` y es la función que comunica este módulo con el master.  
Ésta función toma los valores (mandados por el interactivo) y genera una variable global (que se sale de la función), lo que hace que estos valores pasen directamente al master.
```python
#------------ en lidar_master.py -----------------
def get_from_interactive(vals):
    global vals_from_interac
    vals_from_interac = vals
```

Posteriormente necesitamos los valores predeterminados de las listas desplegables, entonces leemos el archivo `data.csv` generado por [read_modules](read_modules) y lo trabajamos con pandas
```python
## Obteniendo los datos
lst_data = pd.read_csv("./data.csv")                             #leemos el archivo
lst_data["anho"] = [i[:4] for i in lst_data.fecha.to_list()]     #obtenemos años
lst_data["mes"] = [i[:7] for i in lst_data.fecha.to_list()]      #obtenemos meses 
anho_unique = list(lst_data.anho.unique())                       #generamos la lista unica de años
mes_unique = list(lst_data.mes.unique())                         #generamos la lista unica de meses
dark_lst = lst_data[lst_data.tipo == "dark"].fecha.to_list()     #generamos las opciones de calibradores (darks)
```

Usaremos funciones auxiliares para el evento de las listas desplegables, éstas simplemente usan un evento para filtrar los datos
```python
#se obtiene el mes escogido y se actualiza con el bottón
def buscar_mes(event):
    m = dropdown_mes_lst.get()
    d = lst_data[lst_data.mes == m]
    data_lst = d[d.tipo == "data"].fecha.to_list()
    
    dato_escoger_dropdwn['values'] = data_lst

#se obtiene el mes escogido y se actualiza con el bottón
def buscar_anho(event):
    m = dropdown_anho_lst.get()
    d = lst_data[lst_data.anho == m]
    nuevos_meses = list(d.mes.unique())
    
    dropdown_mes_lst['values'] = nuevos_meses
    
#función del botón actualizar
def actualizar_csv():
    from read_modules import get_data_list #usa una función que se encuentra en read_mocules
    get_data_list(data_dir_entry.get(), pola_dir_entry.get())
```

## Ventana
Ahora la ventana es bastante directa, el código está ordenado por columnas, esta sección es la creación.
```python
## WINDOW AND GENERAL FRAME
app = tk.Tk()
app.geometry("500x430")
app.title("LIDAR ANALYSIS LFA")

# FRAME 
frame = tk.Frame(app)
frame.pack()

# OPTIONS FRAME
opt_frame = tk.Frame(frame)
opt_frame.grid(row = 0, column = 0)

# Frame de escoger dato
data_pick_frame = tk.LabelFrame(opt_frame, text = "Dato a analizar")
data_pick_frame.grid(row = 0, column= 1)

dropdown_label = tk.Label(data_pick_frame, text = "GESTION")
dropdown_label.grid(row = 0, column = 0)
dropdown_anho_lst = ttk.Combobox(data_pick_frame, values = anho_unique)
dropdown_anho_lst.grid(row = 1, column = 0)
dropdown_anho_lst.bind("<<ComboboxSelected>>", buscar_anho)

dropdown_label2 = tk.Label(data_pick_frame, text = "MES")
dropdown_label2.grid(row = 2, column = 0)
dropdown_mes_lst = ttk.Combobox(data_pick_frame, values = mes_unique)
dropdown_mes_lst.grid(row = 3, column = 0)
dropdown_mes_lst.bind("<<ComboboxSelected>>", buscar_mes)


dato_escoger_label = tk.Label(data_pick_frame, text="Dato a escoger")
dato_escoger_label.grid(row = 4, column = 0)
dato_escoger_dropdwn = ttk.Combobox(data_pick_frame, values=[run_params.data_picked])
dato_escoger_dropdwn.current(0)
dato_escoger_dropdwn.grid(row = 5, column=0)

# Frame de carpetas
file_frame = tk.LabelFrame(opt_frame, text = "Carpetas")
file_frame.grid(row = 0, column= 0)
data_dir_lab = tk.Label(file_frame, text = "Carpeta de datos")
data_dir_lab.grid(row = 0, column=0)
data_dir_entry = tk.Entry(file_frame)
data_dir_entry.insert(0, run_params.data_dir)
data_dir_entry.grid(row = 1, column=0)
pola_dir_lab = tk.Label(file_frame, text = "Carpeta de calibs")
pola_dir_lab.grid(row = 2, column=0)
pola_dir_entry = tk.Entry(file_frame)
pola_dir_entry.insert(0, run_params.pola_dir)
pola_dir_entry.grid(row = 3, column=0)
aux_dir_lab = tk.Label(file_frame, text = "Carpeta auxiliar")
aux_dir_lab.grid(row = 4, column=0)
aux_dir_entry = tk.Entry(file_frame)
aux_dir_entry.insert(0, run_params.carpeta_aux)
aux_dir_entry.grid(row = 5, column=0)
fig_dir_lab = tk.Label(file_frame, text = "Carpeta figuras")
fig_dir_lab.grid(row = 6, column=0)
fig_dir_entry = tk.Entry(file_frame)
fig_dir_entry.insert(0, run_params.carpeta_figs)
fig_dir_entry.grid(row = 7, column=0)
update_csv_but = tk.Button(file_frame, text="ACTUALIZAR", command=actualizar_csv)
update_csv_but.grid(row = 8, column=0, sticky='news')
gen_log = tk.StringVar(value = run_params.use_log)
log_check = tk.Checkbutton(file_frame, text="Generar LOG", variable=gen_log, onvalue=True, offvalue=False)
log_check.grid(row = 9, column = 0)


# Opciones de datos
data_opt_frame = tk.LabelFrame(opt_frame, text= "Opciones")
data_opt_frame.grid(row = 0, column= 2)
stoav_label = tk.Label(data_opt_frame, text = "Segundos a promediar")
stoav_label.pack()
stoav_entry = tk.Entry(data_opt_frame)
stoav_entry.insert(0,params.dt)
stoav_entry.pack()
show_noise = tk.StringVar(value = run_params.plot_dark_noise)
noise_check = tk.Checkbutton(data_opt_frame, text="Mostrar ruido", variable=show_noise, onvalue=True, offvalue=False)
noise_check.pack()
show_cdep = tk.StringVar(value = run_params.plot_cdep)
cdep_check = tk.Checkbutton(data_opt_frame, text="Mostrar CDEP", variable=show_cdep, onvalue=True, offvalue=False)
cdep_check.pack()
cali_pkd_label = tk.Label(data_opt_frame, text="Calibrador")
cali_pkd_label.pack()
calib_lst = ["2023_09_07", "2023_09_07"] ## MODIFICAR CUANDO HAYA MEJORES CALIBRADORES
calib_dropdwn = ttk.Combobox(data_opt_frame, values=calib_lst)
calib_dropdwn.current(0)
calib_dropdwn.pack()
show_ql = tk.StringVar(value = run_params.plot_ql)
ql_check = tk.Checkbutton(data_opt_frame, text="Mostrar Quicklook", variable=show_ql, onvalue=True, offvalue=False)
ql_check.pack()
show_dep = tk.StringVar(value = run_params.plot_dep)
dep_check = tk.Checkbutton(data_opt_frame, text="Mostrar DEP", variable=show_dep, onvalue=True, offvalue=False)
dep_check.pack()
show_mole = tk.StringVar(value = run_params.plot_molecular)
dep_check = tk.Checkbutton(data_opt_frame, text="Mostrar Molecular", variable=show_mole, onvalue=True, offvalue=False)
dep_check.pack()

### ACÁ VIENE LA FUNCIÓN DEL BOTÓN (VER MÁS ABAJO)

## BOTON
run_button = tk.Button(frame, text="RUN", command=correr)
run_button.grid(row = 2, column= 0, sticky='news')


## FOTO
image = Image.open("portrait.png")
resiz = image.resize((500,200))
img = ImageTk.PhotoImage(resiz)
tk.Label(app, image = img).pack()

tk.mainloop()
```

Ahora el botón **RUN** llama las variables creadas con la ventana, es decir, recopila todo, genera un diccionario que será enviado al master.
```python
## -------------------------
## Botón run
## -------------------------
def correr():
    vals = {
        "data_picked" : dato_escoger_dropdwn.get(),
        "calib_picked" : calib_dropdwn.get(),
        "plot_dark" : bool(int(show_noise.get())),
        "plot_cdep" : bool(int(show_cdep.get())),
        "ave_time" : float(stoav_entry.get()),
        "show_ql" : bool(int(show_ql.get())),
        "data_dir" : data_dir_entry.get(),
        "pola_dir" : pola_dir_entry.get(),
        "aux_dir" : aux_dir_entry.get(),
        "show_dep" : bool(int(show_dep.get())),
        "gen_log" : bool(int(gen_log.get())),
        "fig_dir" : fig_dir_entry.get(),
        "plot_mole" : bool(int(show_mole.get()))
    }
    get_from_interactive(vals)
    app.destroy()
```
Finalmente, se destruye la ventana pues ya tenemos los parámetros para ejecutar nuestro código, empecemos con el análisis!
