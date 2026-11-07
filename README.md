# 3D Mapping Car 🚗

Carrito robot con **cámara estéreo** que recorre un espacio, se controla a distancia y reconstruye un **mapa 3D** (nube de puntos) del lugar con odometría visual y SLAM.

## Overview

El sistema tiene dos partes que se comunican por sockets TCP:

**En el carrito (Raspberry Pi), `server_2.py`**
- Recibe comandos de movimiento (puerto 9999) y mueve los 4 motores con un driver PWM PCA9685 (`motor.py`, `pca9685.py`).
- Transmite el video de la cámara estéreo (puerto 9998).

**En la computadora, `client_2.py`**, en dos fases:

1. **Captura en vivo**: muestra el video rectificado, controla el carrito con el teclado y guarda los cuadros en disco.
2. **SLAM offline** (al terminar):
   - Calcula la disparidad estéreo con **RAFT-Stereo** en GPU (PyTorch).
   - Estima el movimiento cuadro a cuadro con **odometría visual** (`visual_odometry.py`): ORB + matching + `solvePnPRansac`.
   - Detecta **loop closures** con FAISS y optimiza el grafo de poses.
   - Genera las nubes de puntos `mapa_final.ply` y `mapa_paredes.ply` con Open3D.

**Calibración**: `camera_calibration_dualCamera.py` calibra las dos lentes con un tablero de ajedrez y guarda los parámetros en `Calibracion_Camara/estereo_cal.json`.

## Tecnologías

Python · OpenCV · NumPy · PyTorch (RAFT-Stereo) · FAISS · SciPy · Open3D · Raspberry Pi · PCA9685

## Cómo correrlo

1. **En la Raspberry Pi** (con la cámara estéreo y los motores conectados):
   ```bash
   python server_2.py
   ```
2. **En la computadora** (con GPU para RAFT), indica la IP del carrito:
   ```bash
   PI_IP=10.42.0.146 python client_2.py
   ```
3. Controles: **W/A/S/D** para mover, **Q** para frenar y **X** para salir y procesar el mapa.
4. Abre los `.ply` generados con Open3D, MeshLab o CloudCompare.

## Ramas

`main` tiene la versión estable. También hay ramas con experimentos: `SLAM_offline`, `SLAM_offline_alex`, `StereoSGBM` y `Reorganizacion_V2`.

## Equipo

Repositorio original: [ivanfmm/3D_mapping_Car](https://github.com/ivanfmm/3D_mapping_Car).

- Christofer Castañeda
- [ivanfmm](https://github.com/ivanfmm)
- [alexorduna](https://github.com/alexorduna)
- [EstebanColautti](https://github.com/EstebanColautti)
