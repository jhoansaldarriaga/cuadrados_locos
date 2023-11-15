# Cuadrados Locos

Este es un simple juego creado con Pygame en el que el jugador controla un cuadrado rojo y debe evitar colisionar con cuadrados azules que aparecen en la pantalla. El objetivo es sobrevivir a la aparición de cinco cuadrados azules para ganar el juego.

## Instrucciones

1. **Controles del Jugador:**
   - Usa las teclas direccionales (`↑`, `↓`, `←`, `→`) para mover el cuadrado rojo hacia arriba, abajo, izquierda o derecha, respectivamente.

2. **Mecánica del Juego:**
   - El jugador debe evitar colisionar con los cuadrados azules.
   - El juego finaliza si el cuadrado rojo colisiona con uno de los cuadrados azules.
   - El jugador gana al sobrevivir a la aparición de cinco cuadrados azules.

3. **Reiniciar el Juego:**
   - En caso de perder o ganar, puedes reiniciar el juego haciendo clic en el botón "Reiniciar".

## Código Fuente

```python
import pygame
import sys
import random

# ... (Código del juego)

# Llamada al juego
game()
