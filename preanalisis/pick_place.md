---
title: pick_place.py
layout: default
nav_order: 3
parent: Parámetros iniciales
---
# Archivos de configuración de lugar
Necesitamos dos, el primero genera al otro.

## `pick_place.py`
Este pequeño código genera una ventana con una lista desplegable para escoger el lugar donde se ejecuta el código, una vez seleccionado se genera el archivo `place.py` que tiene como única línea el lugar escogido, este segundo archivo es posteriormente leido por [run_params.py](run_params) para decidir las rutas a utilizar.

{: .librerias }
> Utiliza los siguientes módulos 
> - Tkinter

```python
### pick_place.py
import tkinter as tk
from tkinter import ttk

## WINDOW AND GENERAL FRAME
global app
app = tk.Tk()
app.geometry("200x130")
app.title("ESCOGER LUGAR DE EJECUCIÓN")

#lista de opciones por ahora
lst = ["Cota Cota", "Dev", "Linux"]

# FRAME 
frame = tk.Frame(app)
frame.pack()
dato_escoger_label = tk.Label(frame, text="Lugar donde se ejecuta")
dato_escoger_label.pack()
global lugar_escoger_dropdwn
lugar_escoger_dropdwn = ttk.Combobox(frame, values=lst)
lugar_escoger_dropdwn.current(0)
lugar_escoger_dropdwn.pack()

def ejecutar():
    f = open("place.py", "w")
    p = lugar_escoger_dropdwn.get()
    t = f"place = '{p}'"
    f.write(t)
    f.close()
    app.destroy()
    return

run_button = tk.Button(frame, text="LANZAR CALLP", command=ejecutar)
run_button.pack()

tk.mainloop()

```


## `place.py`
Este archivo solamente es un parámetro y se borra con cada actualización, su objetivo es que guarde localmente el lugar donde está instalado el código y así use las rutas establecidas en [`run_params.py`](run_params), se crea automáticamente mediante el módulo de pick place, que es el primero en ejecutarse en caso que no exista el lugar. Si ya existe este archivo el código se salta dicho módulo y entra directamente al menú de escoger dato.

```python
place = "Cota Cota" #para cuando se ejecuta en campus
```
