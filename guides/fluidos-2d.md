# Fluidos 2D: Ecuaciones de Navier-Stokes

## Descripción

Esta simulación implementa las **ecuaciones de Navier-Stokes** para fluidos incompresibles en 2D usando el método de "Stable Fluids" de Jos Stam (SIGGRAPH 1999). Permite visualizar humo, tinta, fuego y otros fenómenos fluidos en tiempo real.

## Conceptos Físicos

### Ecuaciones de Navier-Stokes

Las ecuaciones que gobiernan el movimiento de fluidos viscosos incompresibles:

```
∂u/∂t = −(u·∇)u − (1/ρ)∇p + ν∇²u + f
∇·u = 0
```

Donde:
- **u**: Campo de velocidad del fluido
- **p**: Presión
- **ρ**: Densidad del fluido
- **ν**: Viscosidad cinemática
- **f**: Fuerzas externas

### Términos de la ecuación

| Término | Significado | Efecto |
|---------|-------------|--------|
| `−(u·∇)u` | Advección | El fluido se transporta a sí mismo |
| `−∇p/ρ` | Presión | Mantiene incompresibilidad |
| `ν∇²u` | Difusión | Suaviza el campo de velocidad |
| `f` | Fuerzas | Input del usuario |

### Condición de incompresibilidad

`∇·u = 0` significa que el fluido no puede comprimirse ni expandirse. Esto se asegura mediante la **proyección de presión**.

## Método Numérico

### Stable Fluids (Stam, 1999)

El algoritmo divide cada paso temporal en etapas:

1. **Add Forces**: Aplicar fuerzas externas
2. **Advect**: Transportar el campo por el flujo
3. **Diffuse**: Difundir velocidad (viscosidad)
4. **Project**: Hacer el campo divergencia-cero

### Advección Semi-Lagrangiana

Para advectar un campo φ de manera estable:

```
φ_new(x) = φ_old(x − u·dt)
```

Se "rastrea hacia atrás" para encontrar de dónde vino el valor.

### Proyección de Presión

Para hacer ∇·u = 0:

1. Calcular divergencia: `div = ∇·u`
2. Resolver ecuación de Poisson: `∇²p = div`
3. Restar gradiente: `u = u − ∇p`

## Parámetros

### Viscosidad (ν)

- **0**: Fluido ideal (no viscoso)
- **0.0001**: Aire/humo
- **0.001**: Agua
- **0.01+**: Miel/lava

### Vorticidad

El **confinamiento de vorticidad** (Fedkiw et al.) añade pequeños remolinos que se perderían por difusión numérica:

```
f_vort = ε(N × ω)
```

Donde ω es la vorticidad (curl de velocidad) y N es su gradiente normalizado.

### Disipación

- **Velocidad**: Cuánto se frena el flujo (0.99 = pierde 1% por frame)
- **Densidad**: Cuánto se desvanece el tinte/humo

## Presets

| Preset | Características |
|--------|-----------------|
| **Humo** | Baja viscosidad, alta vorticidad, desvanece rápido |
| **Agua** | Alta viscosidad, baja vorticidad, persistente |
| **Fuego** | Sin viscosidad, alta vorticidad, desvanece muy rápido |
| **Tinta** | Media difusión, efectos artísticos |
| **Turbulento** | Máxima vorticidad, remolinos intensos |

## Visualización

### Modos

- **Color**: Muestra tinte RGB inyectado
- **Densidad**: Escala de grises por concentración
- **Velocidad**: Color por dirección, brillo por magnitud
- **Vorticidad**: Rojo = giro antihorario, Azul = horario

## Aplicaciones

### Gráficos por computadora
- Efectos de humo y fuego en videojuegos
- Simulación de explosiones
- Animación de líquidos

### Ingeniería
- Aerodinámica (flujo alrededor de objetos)
- Diseño de sistemas de ventilación
- Análisis de turbulencia

### Meteorología
- Modelado de sistemas climáticos
- Predicción de dispersión de contaminantes

## Experimentos sugeridos

1. **Vórtice de von Kármán**: Coloca un obstáculo y observa los remolinos alternantes
2. **Mezcla de colores**: Inyecta dos colores y observa cómo se mezclan
3. **Turbulencia**: Aumenta la vorticidad y observa el caos
4. **Flujo laminar vs turbulento**: Compara baja vs alta velocidad

## Referencias

- Stam, J. (1999). "Stable Fluids". SIGGRAPH '99
- Fedkiw, R., Stam, J., Jensen, H. W. (2001). "Visual Simulation of Smoke"
- Bridson, R. (2015). "Fluid Simulation for Computer Graphics"

## Conexiones con otras simulaciones

- **Difusión de calor**: Usa ecuaciones similares de difusión
- **Formación de galaxias**: Fluidos gravitacionales
- **Ondas**: Ecuación de onda vs Navier-Stokes
