# Physics Visual Lab

> 16 simulaciones de física interactivas donde las ecuaciones cobran vida.

## Simulaciones

| Categoría | Simulación | Ecuación Principal |
|-----------|------------|-------------------|
| **Cinemática** | Movimiento Proyectil | y = v₀sin(θ)t - ½gt² |
| **Dinámica** | Colisiones 2D | p₁ + p₂ = p₁' + p₂' |
| **Oscilaciones** | Péndulo Simple | θ'' + (g/L)sin(θ) = 0 |
| **Oscilaciones** | Péndulos Desacoplados | T = 2π√(L/g) |
| **Física Teórica** | Mecánica Lagrangiana | L = T - V |
| **Ondas** | Efecto Doppler | f' = f(v ± v_obs)/(v ∓ v_src) |
| **Ondas** | Interferencia | d·sin(θ) = nλ |
| **Ondas** | Transformada Fourier | f(t) = Σ aₙsin(nωt + φₙ) |
| **Electromagnetismo** | Campo Eléctrico | E = kq/r² |
| **Termodinámica** | Difusión del Calor | ∂T/∂t = α∇²T |
| **Termodinámica** | Gas Ideal | PV = NkT |
| **Astrofísica** | Sistema Solar | F = GMm/r² |
| **Astrofísica** | Órbitas de Kepler | T² = (4π²/GM)a³ |
| **Oscilaciones** | Oscilador Forzado | mẍ + γẋ + kx = F₀cos(ωt) |
| **Óptica** | Ley de Snell | n₁sin(θ₁) = n₂sin(θ₂) |
| **Física Moderna** | Relatividad Especial | γ = 1/√(1 - v²/c²) |

## Stack

- HTML5 (archivos autocontenidos)
- CSS inline (tema oscuro)
- JavaScript vanilla
- Canvas 2D con devicePixelRatio
- Tailwind CSS via CDN (solo index)

## Características

- Controles interactivos en tiempo real
- Presets de configuración (planetas, materiales)
- Ecuaciones y valores calculados visibles
- Responsive (mobile-friendly)
- Sin dependencias externas

## Uso

Abre `index.html` en cualquier navegador moderno.

---

<details>
<summary><strong>Prompt de Reproducción</strong></summary>

## Physics Visual Lab - Prompt de Reproducción

Crea un proyecto de simulaciones de física interactivas con las siguientes características:

### Stack
- HTML5 (archivos autocontenidos, sin bundler)
- CSS inline (estilos en cada archivo)
- JavaScript vanilla (sin frameworks)
- Canvas 2D para renderizado
- Tailwind CSS via CDN (solo para index.html)
- Google Fonts: Inter

### Estructura
```
physics-visual-lab/
├── index.html              # Landing page con grid de tarjetas y previews animados
├── proyectil.html          # Movimiento parabólico con gravedad variable
├── colisiones.html         # Colisiones 2D (elásticas/inelásticas)
├── pendulo-simple.html     # Péndulo con RK4 (ecuación no lineal)
├── pendulos-desacoplados.html # Múltiples péndulos con T = 2π√(L/g)
├── mecanica-lagrangiana.html  # Formalismo lagrangiano L = T - V
├── efecto-doppler.html     # Ondas con fuente móvil
├── interferencia.html      # Patrón Young, d·sin(θ) = nλ
├── fourier.html            # Síntesis aditiva (cuadrada, triangular, diente de sierra)
├── campo-electrico.html    # Líneas de campo, E = kq/r²
├── difusion-calor.html     # Ecuación del calor 2D (diferencias finitas)
├── sistema-solar.html      # 8 planetas con períodos reales
├── orbitas-kepler.html     # 3 leyes de Kepler, Newton-Raphson
├── gas-ideal.html          # Teoría cinética, Maxwell-Boltzmann
├── oscilador-forzado.html  # Resonancia, curva de respuesta
├── optica.html             # Ley de Snell, reflexión total interna
└── relatividad.html        # Lorentz γ, dilatación temporal, contracción
```

### Diseño Visual
- Tema oscuro (bg: #030712 o #0a0a1a)
- Acentos por categoría:
  - Cinemática/Dinámica: verde (#22c55e)
  - Oscilaciones: púrpura (#a855f7)
  - Ondas: cyan (#06b6d4)
  - Electromagnetismo: rojo (#ef4444)
  - Termodinámica: naranja (#f97316)
  - Astrofísica: índigo (#6366f1)
  - Física Moderna: ámbar (#fbbf24)
- Ecuaciones en Times New Roman italic
- Canvas con devicePixelRatio para retina

### Patrón de Simulación (cada archivo)
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>[Nombre] - Physics Visual Lab</title>
    <style>/* CSS inline completo */</style>
</head>
<body>
    <div class="container">
        <!-- Canvas principal -->
        <canvas id="canvas"></canvas>

        <!-- Panel de controles -->
        <div class="controls">
            <!-- Sliders con labels y valores en tiempo real -->
            <!-- Botones play/pause/reset -->
            <!-- Display de ecuaciones y valores calculados -->
        </div>
    </div>
    <script>/* JS inline completo */</script>
</body>
</html>
```

### Convenciones de Código
- Idioma: Español (UI y comentarios)
- Variables: camelCase
- Constantes físicas: MAYÚSCULAS (const G = 9.81)
- Animación: requestAnimationFrame con dt variable
- Integración numérica: Euler o RK4 según precisión requerida
- Sin dependencias externas (excepto Tailwind CDN en index)

### Features del Index
- Grid responsive (1-2-3 columnas)
- Tarjetas con hover lift y borde gradient animado
- Mini canvas con preview animado de cada simulación
- Tags de categoría coloreados
- Ecuación principal visible en cada tarjeta
- Header sticky con blur backdrop

### Física Implementada
1. **Mecánica**: RK4, conservación momento/energía, Lagrangiano
2. **Ondas**: Interferencia, Doppler, Fourier, Snell
3. **Termodinámica**: Difusión (∇²T), Maxwell-Boltzmann, PV=NkT
4. **Electromagnetismo**: Campo E, líneas de fuerza, Coulomb
5. **Relatividad**: γ = 1/√(1-v²/c²), dilatación, contracción
6. **Astrofísica**: Kepler (Newton-Raphson), órbitas elípticas

### Interactividad
- Sliders en tiempo real (sin necesidad de reiniciar)
- Drag & drop para posicionar objetos
- Presets (planetas, materiales, configuraciones)
- Play/Pause/Reset
- Valores calculados actualizados cada frame

</details>

---

*Physics Visual Lab - Donde las ecuaciones cobran vida*
