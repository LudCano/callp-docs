---
layout: default
title: Parámetros iniciales
nav_order: 3
has_children: true
has_toc: false
---

# Archivos de parámetros iniciales
El código cuenta con 3 archivos de parámetros iniciales, los cuales desarrollaremos a continuación.

## [`run_params.py`](run_params)
Este archivo tiene como objetivo parámetros predefinidos de la interfaz del código, como ser si queremos que se muestre alguna figura por defecto, alguna fecha por defecto, etc.

## [`params.py`](params)
Este archivo tiene como objetivo parámetros predefinidos para los cálculos del código.

## `place.py`
Este archivo solamente es un parámetro y se borra con cada actualización, su objetivo es que guarde localmente el lugar donde está instalado el código y así use las rutas establecidas en [`run_params.py`](run_params), se crea automáticamente mediante el módulo de pick place, que es el primero en ejecutarse en caso que no exista el lugar. Si ya existe este archivo el código se salta dicho módulo y entra directamente al menú de escoger dato.
