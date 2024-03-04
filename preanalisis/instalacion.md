---
title: Instalación
layout: default
nav_order: 2
---

# Instalación del código CALLP

## Tabla de contenidos
1. TOC
{:toc}

## Descargar repositorio
Pueden descargar el [repositorio](http://github.com/LFAUMSA/LIDAR_PYTHON), una vez descargado deben configurar las carpetas que serán usadas para datos, salidas de datos y archivos auxiliares.

## Carpetas auxiliares
El código debe enlazar las siguientes 4 carpetas, para realizar cambios pueden utilizar el módulo `run_params.py`

### 1. `lidar_data`
Esta carpeta contiene solamente los datos del lidar, puede estar ordenada de la forma que se guste, el código busca directamente en cada carpeta, subcarpeta y consiguiente en archivos de formato válido (más de esto en el módulo [read_modules.py](read_modules)

### 2. `lidar_calib`
Esta carpeta contiene los calibradores p45 y m45. Ésta puede estar contenida en la carpeta de datos, y será ignorada por el código.

### 3. `lidar_aux`
Carpeta para archivos auxiliares, como ser logs.

### 4. `lidar_figs`
Carpeta de almacenamiento de figuras.

