# Plan de Mejoras: Relatividad Especial
## De "funcional" a "exhibition-ready"

*Objetivo: Transformar la simulación en una experiencia visual inmersiva que "venda" la física relativista*

---

## Estado Actual

```
✓ Contracción de Lorentz en la nave
✓ Dilatación temporal (relojes comparados)
✓ Factor γ en gráfico
✓ Estrellas con stretch básico
✗ Motor estático (gradiente radial)
✗ Estrellas son elipses, no streaks
✗ Sin Doppler shift
✗ Sin grid espacio-temporal
✗ Sin efectos de post-procesado
```

---

## MEJORAS PROPUESTAS

### 1. WARP SPEED - Estelas de Estrellas (ALTA PRIORIDAD)

**Problema actual:** Las estrellas son elipses que se estiran. A 99%c parecen huevos, no lluvia de luz.

**Solución:**
```javascript
// En lugar de ellipse, dibujar líneas cuya longitud dependa de v
const streakLength = 1 + velocity * 100; // hasta 100px a 99%c
ctx.strokeStyle = `rgba(255, 255, 255, ${brightness})`;
ctx.lineWidth = 1;
ctx.beginPath();
ctx.moveTo(x, y);
ctx.lineTo(x - streakLength, y); // estela hacia atrás
ctx.stroke();
```

**Resultado esperado:** A baja velocidad = puntos. A alta velocidad = lluvia horizontal de líneas.

---

### 2. DOPPLER SHIFT - Color de Estrellas (MEDIA PRIORIDAD)

**Física real:** Luz que viene hacia ti → blueshift. Luz que se aleja → redshift.

**Simplificación (vista lateral):** A mayor velocidad, las estrellas tienden a azul/violeta (energía visual).

```javascript
// Interpolar de blanco (0%c) a azul-violeta (99%c)
const hue = 60 - velocity * 120; // 60 (amarillo) → -60 (violeta)
const saturation = velocity * 80; // 0% → 80%
ctx.strokeStyle = `hsla(${hue}, ${saturation}%, ${70 + brightness * 30}%, ${brightness})`;
```

**Resultado esperado:** Estrellas pasan de blanco-amarillento a azul-violeta con la velocidad.

---

### 3. SISTEMA DE PARTÍCULAS - Motor (ALTA PRIORIDAD)

**Problema actual:** El motor es un gradiente radial estático. Muy aburrido.

**Solución:** Array de partículas con ciclo de vida.

```javascript
class Particle {
  constructor(x, y) {
    this.x = x;
    this.y = y + (Math.random() - 0.5) * 20;
    this.vx = -5 - Math.random() * 10; // hacia atrás
    this.vy = (Math.random() - 0.5) * 2;
    this.life = 1.0;
    this.decay = 0.02 + Math.random() * 0.02;
    this.size = 3 + Math.random() * 5;
  }

  update() {
    this.x += this.vx * velocity;
    this.y += this.vy;
    this.life -= this.decay;
    this.size *= 0.98;
  }

  draw(ctx) {
    // Color: blanco → rosa → violeta → transparente
    const hue = 300 + (1 - this.life) * 60; // 300 (magenta) → 360 (rojo)
    ctx.fillStyle = `hsla(${hue}, 100%, ${50 + this.life * 30}%, ${this.life})`;
    ctx.beginPath();
    ctx.arc(this.x, this.y, this.size * this.life, 0, Math.PI * 2);
    ctx.fill();
  }
}

// En el loop principal:
if (velocity > 0.1) {
  // Spawn rate proporcional a velocidad
  for (let i = 0; i < velocity * 5; i++) {
    particles.push(new Particle(engineX, engineY));
  }
}
particles = particles.filter(p => p.life > 0);
particles.forEach(p => { p.update(); p.draw(ctx); });
```

**Resultado esperado:** Chorro de partículas que se desvanecen, como un motor de fusión real.

---

### 4. GRID ESPACIO-TEMPORAL (MEDIA PRIORIDAD)

**Concepto:** Mostrar que el ESPACIO se contrae, no solo la nave.

**Implementación:**
```javascript
function drawSpaceGrid() {
  ctx.strokeStyle = 'rgba(100, 100, 255, 0.15)';
  ctx.lineWidth = 1;

  const gridSpacing = 50;
  const contractedSpacing = gridSpacing / gamma(velocity);

  // Líneas verticales (se contraen)
  for (let x = 0; x < W; x += contractedSpacing) {
    ctx.beginPath();
    ctx.moveTo(x, 0);
    ctx.lineTo(x, H);
    ctx.stroke();
  }

  // Líneas horizontales (no se contraen - perpendiculares al movimiento)
  for (let y = 0; y < H; y += gridSpacing) {
    ctx.beginPath();
    ctx.moveTo(0, y);
    ctx.lineTo(W, y);
    ctx.stroke();
  }
}
```

**Resultado esperado:** La cuadrícula vertical se comprime visualmente con la velocidad.

---

### 5. EFECTO BLOOM / GLOW (BAJA PRIORIDAD)

**Problema actual:** `shadowBlur` es costoso y no muy convincente.

**Solución:** Doble canvas con blur CSS.

```html
<div class="canvas-wrapper">
  <canvas id="glowCanvas"></canvas> <!-- Capa de glow -->
  <canvas id="canvas"></canvas>     <!-- Capa principal -->
</div>

<style>
.canvas-wrapper { position: relative; }
#glowCanvas {
  position: absolute;
  top: 0; left: 0;
  filter: blur(15px);
  opacity: 0.7;
  mix-blend-mode: screen;
  pointer-events: none;
}
</style>
```

En JS: Dibujar solo elementos brillantes (motor, estrellas) en `glowCanvas`.

**Resultado esperado:** Efecto neón real sin matar el rendimiento.

---

### 6. ABERRACIÓN CROMÁTICA (BAJA PRIORIDAD - POLISH)

**Concepto:** A >90%c, separar canales RGB en los bordes para sensación de inestabilidad.

**Implementación:**
```javascript
if (velocity > 0.9) {
  const shift = (velocity - 0.9) * 50; // 0-5px de shift

  // Guardar frame actual
  const imageData = ctx.getImageData(0, 0, W, H);

  // Aplicar offset a canales (simplificado - solo bordes)
  // Esto es costoso, usar con moderación o solo en los bordes
  ctx.globalCompositeOperation = 'screen';
  ctx.drawImage(canvas, -shift, 0); // Red channel shift
  ctx.drawImage(canvas, shift, 0);  // Blue channel shift
  ctx.globalCompositeOperation = 'source-over';
}
```

**Nota:** Esto puede ser costoso. Alternativa: vignette + slight blur en bordes.

---

## ORDEN DE IMPLEMENTACIÓN RECOMENDADO

| Fase | Feature | Impacto Visual | Dificultad | Tiempo |
|------|---------|----------------|------------|--------|
| **1** | Warp Speed (streaks) | ⭐⭐⭐⭐⭐ | Baja | 30min |
| **2** | Partículas motor | ⭐⭐⭐⭐⭐ | Media | 45min |
| **3** | Doppler shift color | ⭐⭐⭐ | Baja | 15min |
| **4** | Grid espacio-temporal | ⭐⭐⭐⭐ | Baja | 20min |
| **5** | Bloom (dual canvas) | ⭐⭐⭐ | Media | 30min |
| **6** | Aberración cromática | ⭐⭐ | Alta | 45min |

**Tiempo total estimado: ~3 horas**

---

## DECISIONES A DISCUTIR

### 1. ¿Bloom sí o no?
- **Pro:** Efecto visual impresionante, sensación de energía
- **Contra:** Complejidad de dual-canvas, posible impacto en rendimiento
- **Mi opinión:** Sí, pero solo para motor y estrellas muy brillantes

### 2. ¿Grid siempre visible o toggle?
- **Pro toggle:** Menos ruido visual cuando no interesa
- **Pro siempre:** Refuerza constantemente el mensaje "el espacio se contrae"
- **Mi opinión:** Siempre visible pero muy tenue (opacity 0.1)

### 3. ¿Aberración cromática?
- **Pro:** Efecto "película de ciencia ficción", sensación de límite físico
- **Contra:** Puede parecer un bug si no se explica, costoso
- **Mi opinión:** Solo si el resto está perfecto. Es polish, no core.

### 4. ¿Añadir sonido?
- No está en tus propuestas, pero: un drone que sube de pitch con la velocidad sería potente
- **Mi opinión:** Dejarlo para una fase 2, concentrarse en visual primero

---

## PREGUNTAS PARA TI

1. ¿Quieres las 6 mejoras o empezamos con las 4 de alto impacto (1-4)?

2. ¿El grid debería moverse con las estrellas o ser estático? (Moverse = más inmersivo, estático = más didáctico)

3. ¿Prefieres Bloom simple (solo motor) o completo (motor + estrellas)?

4. ¿Three.js está en el horizonte o queremos maximizar Canvas 2D primero?

---

*Creado: 15/12/2025*
*Physics Visual Lab - Relatividad Especial v2*
