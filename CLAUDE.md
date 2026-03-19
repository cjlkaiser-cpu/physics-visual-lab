# Physics Visual Lab - Documentación Técnica

## Descripción

El **Physics Visual Lab** es el laboratorio de física más completo de EigenLab, con **23 simulaciones** que cubren mecánica clásica, ondas, termodinámica, electromagnetismo y física moderna. Implementa métodos numéricos rigurosos (RK4, diferencias finitas) y visualización en tiempo real con Canvas 2D y WebAssembly para simulaciones masivas.

**Filosofía:** Cada simulación traduce ecuaciones físicas reales en experiencias interactivas, permitiendo explorar fenómenos desde proyectiles hasta relatividad especial.

## Simulaciones (23 Total)

### Mecánica Clásica (5)
1. **Movimiento Proyectil** - Balística con resistencia del aire
2. **Colisiones 2D** - Conservación de momento y energía
3. **Péndulo Simple** - Modelo no lineal con RK4
4. **Péndulos Desacoplados** - 5 péndulos con diferentes longitudes
5. **Mecánica Lagrangiana** - Formalismo L = T - V

### Oscilaciones y Resonancia (2)
6. **Oscilador Forzado** - Resonancia cuando ω = ω₀
7. **Atractor Multi-Cuerpo** - Caos determinista

### Ondas (5)
8. **Ondas Mecánicas** - Transversales y longitudinales
9. **Efecto Doppler** - Con sonificación en tiempo real
10. **Interferencia de Ondas** - Experimento de Young
11. **Transformada de Fourier** - Síntesis aditiva visual y sonora
12. **Óptica (Ley de Snell)** - Reflexión y refracción

### Termodinámica (6)
13. **Gas Ideal** - Teoría cinética, distribución Maxwell-Boltzmann
14. **Gas Ideal WASM** - Versión acelerada (10-100x) con WebAssembly
15. **Difusión del Calor** - Ecuación: ∂T/∂t = α∇²T
16. **Difusión del Calor WASM** - Versión optimizada
17. **Ciclos Termodinámicos** - Carnot, Otto, Diesel
18. **Entropía** - Segunda Ley, ΔS ≥ 0

### Astrofísica (2)
19. **Sistema Solar** - 8 planetas con períodos reales
20. **Órbitas de Kepler** - Tres leyes, Newton-Raphson

### Electromagnetismo (2)
21. **Campo Eléctrico** - Líneas de campo y potencial
22. **Circuitos RLC** - Impedancia, resonancia

### Física Moderna (2)
23. **Relatividad Especial** - Factor de Lorentz, dilatación temporal
24. **Efecto Túnel** - Mecánica cuántica, T ≈ e^{-2κa}

## Métodos Numéricos

### Runge-Kutta 4 (RK4)
Integrador principal para EDOs, precisión O(h⁵):
- Péndulos, órbitas, atractores
- 4 evaluaciones por paso
- Ideal para sistemas caóticos

### Newton-Raphson
Ecuación de Kepler: E - e·sin(E) = M
- Convergencia cuadrática
- Precisión de 10⁻⁶ típica

### Diferencias Finitas
Ecuación del calor: ∂T/∂t = α∇²T
- Grillas 2D (30x30 a 500x500)
- Euler explícito para tiempo
- Laplaciano en espacio

### Monte Carlo
Distribución de Maxwell-Boltzmann en gases
- Muestreo aleatorio
- Colisiones estocásticas

## Tecnología

### WebAssembly
Módulos compilados para cálculos masivos:
- `eigenlab-core.wasm` (158 KB)
- `eigenlab-core.js` (50 KB)
- Gas Ideal: 10,000+ partículas
- Difusión Calor: grillas 500x500
- Aceleración: 10-100x vs JS puro

### Web Audio API
Sonificación de física:
- Efecto Doppler en tiempo real
- Transformada de Fourier (síntesis aditiva)
- Metrónomo en péndulo simple
- ADSR envelopes

### Canvas 2D
Renderizado principal:
- Device Pixel Ratio handling
- Gradientes radiales/lineales
- 60 FPS con requestAnimationFrame
- 22/23 simulaciones

## Ecuaciones Destacadas

```
Péndulo Simple (no lineal):
θ̈ + (g/L)sin(θ) = 0

Oscilador Forzado:
mẍ + γẋ + kx = F₀cos(ωt)

Maxwell-Boltzmann:
f(v) = (m/2πkT)^(3/2) · 4πv² · exp(-mv²/2kT)

Ecuación del Calor:
∂T/∂t = α∇²T

Lorentz (Relatividad):
γ = 1/√(1 - v²/c²)

Ley de Snell:
n₁sin(θ₁) = n₂sin(θ₂)
```

## Paleta de Colores

- **Primaria**: Verde `#22c55e`
- **Energía cinética**: Rojo
- **Energía potencial**: Azul
- **Temperatura**: Azul (frío) → Rojo (caliente)
- **Campos**: Gradientes radiales

## Referencias

**Archivos de código:**
- Péndulo RK4: `pendulo-simple.html:250-310`
- Gas Ideal WASM: `gas-ideal-wasm.html:180-220`
- Transformada Fourier: `fourier.html:400-480`

**Documentación completa:** 23 simulaciones, ~500-800 líneas cada una

---

**Última actualización:** 2026-01-10
**Total:** 23 simulaciones, ~15,000 líneas de código
