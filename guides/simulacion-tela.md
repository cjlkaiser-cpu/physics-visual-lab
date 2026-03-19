# Simulacion de Tela: Dinamica de Cuerpos Blandos

## Descripcion

Esta simulacion implementa **fisica de tela y cuerpos blandos** usando el metodo de **Dinamica Basada en Posiciones (PBD)** con integracion de Verlet. Es la tecnica estandar en videojuegos, animacion y efectos visuales para simular telas, cuerdas, cabello y materiales deformables.

## Fundamentos Fisicos

### Integracion de Verlet

A diferencia de la integracion de Euler que almacena velocidad explicitamente, Verlet usa la diferencia entre posiciones:

```
x_nuevo = 2x_actual - x_anterior + a * dt²
```

**Ventajas:**
- La velocidad esta implicita (no hay error de acumulacion)
- Muy estable para sistemas de restricciones
- Conserva energia mejor que Euler
- Simple y eficiente

### Sistema Masa-Resorte

La tela se modela como una grilla de **particulas** conectadas por **resortes**:

```
    o---o---o---o     Cada 'o' es una particula
    |\ /|\ /|\ /|     Las lineas son resortes
    | X | X | X |
    |/ \|/ \|/ \|
    o---o---o---o
```

## Tipos de Resortes

### 1. Resortes Estructurales

Conectan vecinos directos (horizontal y vertical).

```
o---o
|   |
o---o
```

- Resisten el **estiramiento**
- Son los mas importantes
- Alta rigidez (1.0)

### 2. Resortes de Corte (Shear)

Conectan vecinos diagonales.

```
o   o
 \ /
  X
 / \
o   o
```

- Resisten la **deformacion angular**
- Evitan que el cuadrado se convierta en rombo
- Rigidez media (0.7-0.8)

### 3. Resortes de Flexion (Bend)

Conectan particulas saltando una.

```
o-------o
    |
    o (particula intermedia)
```

- Resisten el **doblado**
- Dan rigidez al material
- Rigidez baja para telas (0.1-0.3)
- Rigidez alta para carton (0.7-0.9)

## Restricciones de Distancia

Cada resorte impone una restriccion: mantener la distancia de reposo.

### Algoritmo de Satisfaccion

```
delta = posB - posA
distancia = |delta|
diferencia = (distancia - distanciaReposo) / distancia
correccion = delta * diferencia * 0.5 * rigidez

posA += correccion
posB -= correccion
```

### Iteraciones Multiples

Una sola pasada no satisface todas las restricciones simultaneamente. Se necesitan **multiples iteraciones**:

- 4-6 iteraciones: Tela suave, mas elastica
- 8-12 iteraciones: Tela rigida, mantiene forma
- 15-20 iteraciones: Material casi rigido

## Parametros de Simulacion

### Gravedad (200-2000 cm/s²)

Fuerza hacia abajo.

| Valor | Efecto |
|-------|--------|
| 200 | Material muy ligero (seda) |
| 980 | Gravedad terrestre estandar |
| 1500 | Material pesado (denim) |
| 2000 | Ultra pesado |

### Amortiguacion (0.9-1.0)

Cuanta energia se conserva en cada frame.

- **0.95**: Mucho amortiguamiento, se detiene rapido
- **0.99**: Poca perdida, oscila mucho tiempo
- **1.0**: Sin perdida (inestable)

### Rigidez (0.1-1.0)

Fuerza de las restricciones por iteracion.

- **0.2**: Muy elastico (goma)
- **0.5**: Tela normal
- **0.9**: Muy rigido (carton)

## Presets de Materiales

### Seda
```
gravedad: 600
amortiguacion: 0.97
rigidez: 0.3
flexion: 0.1
viento: si, turbulento
```
Tela ligera y fluida que ondea con el viento.

### Mezclilla (Denim)
```
gravedad: 1200
amortiguacion: 0.995
rigidez: 0.9
flexion: 0.8
viento: no
```
Material pesado y rigido, cae rapido.

### Goma (Rubber)
```
gravedad: 800
amortiguacion: 0.98
rigidez: 0.2
flexion: 0.1
iteraciones: 4
```
Muy elastico, rebota y se estira.

### Bandera (Flag)
```
gravedad: 200
rigidez: 0.6
viento_x: 200
turbulencia: 80
```
Optimizada para ondear con el viento.

### Red (Net)
```
rigidez_estructural: 1.0
rigidez_corte: 0.1
rigidez_flexion: 0.05
```
Solo resortes estructurales visibles, como una malla.

### Telarana (Cobweb)
```
gravedad: 100
rigidez: 0.5
desgarro: 0.5
```
Fragil, se rompe facilmente al estirar.

## Desgarro (Tearing)

Cuando el umbral de desgarro es mayor que 0, los resortes se rompen si se estiran demasiado:

```
if (distancia > distanciaReposo * (1 + umbral)) {
    resorte.activo = false
}
```

- **Umbral 0**: Sin desgarro
- **Umbral 0.5**: Se rompe al estirar 50% extra
- **Umbral 1.0**: Se rompe al doblar longitud
- **Umbral 2.0**: Muy dificil de romper

## Colision con Esfera

Se implementa colision simple con una esfera movil:

```
distancia = |particula - centroEsfera|
if (distancia < radio) {
    // Empujar particula fuera de la esfera
    direccion = normalizar(particula - centro)
    particula = centro + direccion * radio * 1.1
}
```

El factor 1.1 evita que la particula quede exactamente en la superficie (evita jitter).

## Viento

### Modelo de Viento

El viento aplica una fuerza constante mas turbulencia aleatoria:

```
fuerzaX = vientoBaseX + aleatorio(-turbulencia, turbulencia)
fuerzaY = vientoBaseY + aleatorio(-turbulencia, turbulencia)
```

### Turbulencia Realista

Para viento mas realista, se puede usar ruido Perlin:

```
turb = noise(tiempo * 0.5) * turbulencia
```

Esto crea rafagas suaves en lugar de cambios bruscos aleatorios.

## Partículas Fijadas (Pinned)

Algunas particulas tienen **masa infinita** y no se mueven:

```
if (!particula.fijada) {
    particula.posicion += velocidad
}
```

En la restriccion de distancia:
- Si una particula esta fijada, la otra toma toda la correccion
- Si ambas estan fijadas, no hay correccion

## Optimizaciones

### Substeps

Para mayor estabilidad, dividir el paso de tiempo:

```
for (int i = 0; i < 2; i++) {
    integracion(dt/2)
    resolverRestricciones()
}
```

### Paralelizacion

Los resortes se pueden resolver en paralelo usando coloreado de grafos:

1. Colorear resortes para que ninguno comparta particulas
2. Resolver todos los resortes del mismo color en paralelo
3. Repetir para cada color

### GPU Compute

Para miles de particulas, mover la simulacion a GPU:
- Position Buffer Objects
- Compute Shaders para Verlet y restricciones
- Renderizado directo desde GPU

## Aplicaciones

### Videojuegos
- Capas, banderas, cortinas
- Cabello y ropa de personajes
- Cuerdas y cadenas

### Animacion
- Efectos de tela en peliculas
- Simulacion de vestimenta
- Pelo y plumas

### Ingenieria
- Simulacion de estructuras textiles
- Pruebas de materiales
- Diseno de paracaidas

### Medicina
- Simulacion de tejidos blandos
- Entrenamiento quirurgico
- Modelado de organos

## Experimentos Sugeridos

1. **Comparar materiales**: Cambiar entre presets y observar diferencias
2. **Estirar hasta romper**: Activar desgarro y tirar de la tela
3. **Bandera al viento**: Preset bandera con diferentes turbulencias
4. **Red de pesca**: Ver como la red envuelve la esfera
5. **Efecto domino**: Soltar puntos fijados uno por uno

## Limitaciones del Modelo

1. **Sin auto-colision**: La tela puede atravesarse a si misma
2. **Sin friccion de aire real**: Solo viento como fuerza constante
3. **Restricciones aproximadas**: No se satisfacen exactamente
4. **Sin torsion**: Los resortes no resisten la torsion

## Extension: Cuerpos Blandos 3D

Para volumenes deformables (softbodies 3D):

1. Usar tetraedros en lugar de cuadrilateros
2. Agregar restricciones de volumen
3. Implementar presion interna
4. Manejar auto-colision

## Referencias

- Muller, M. et al. (2007). "Position Based Dynamics"
- Jakobsen, T. (2001). "Advanced Character Physics" (GDC)
- Provot, X. (1995). "Deformation Constraints in a Mass-Spring Model"
- Baraff, D. & Witkin, A. (1998). "Large Steps in Cloth Simulation"

## Conexiones con Otras Simulaciones

- **Particulas con resortes**: Base del sistema masa-resorte
- **Pendulos acoplados**: Caso especial de restricciones de distancia
- **Difusion de calor**: Ecuaciones similares para propagar fuerzas
- **Fluidos SPH**: Particulas interactuando con fuerzas
