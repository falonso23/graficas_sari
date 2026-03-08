# Documento de Desarrollo - Generador de Graficas WISC-V

**Proyecto:** Graficas Sari
**Fecha:** 2026-03-07
**Version:** 1.0

---

## 1. Descripcion General

Aplicacion web standalone para generar graficas de perfil WISC-V (Wechsler Intelligence Scale for Children - Fifth Edition). Permite a psicologos visualizar e interactuar con las puntuaciones de indices de un nino evaluado, y exportar la grafica como imagen PNG.

---

## 2. Alcance

### Incluido
- Grafica interactiva con 6 indices WISC-V
- Inputs numericos sincronizados con puntos draggables
- Zonas de sombreado por rango de puntuacion
- Exportacion a PNG
- Archivo unico HTML (sin backend, sin build tools)

### Excluido
- Datos del paciente (nombre, edad, etc.)
- Almacenamiento o base de datos
- Autenticacion de usuarios
- Reportes PDF

---

## 3. Stack Tecnologico

| Componente       | Tecnologia                        |
|------------------|-----------------------------------|
| Estructura       | HTML5 (archivo unico)             |
| Estilos          | CSS3 inline                       |
| Logica           | JavaScript vanilla                |
| Grafica          | Chart.js 4.x (CDN)               |
| Drag & Drop      | chartjs-plugin-dragdata (CDN)     |
| Zonas sombreadas | chartjs-plugin-annotation (CDN)   |
| Fuente           | Google Fonts - Quicksand          |
| Exportacion      | Canvas API nativa (toDataURL)     |

---

## 4. Especificaciones de la Grafica

### 4.1 Eje Vertical (Y) - Coeficientes
- **Rango:** 40 a 160
- **Intervalos principales:** cada 10 unidades (gridlines visibles)
- **Sub-intervalos:** cada 5 unidades (gridlines tenues)
- **Linea media:** 100 (destacada con mayor grosor o color distinto)

### 4.2 Eje Horizontal (X) - Indices
Categorias fijas en orden:

| Posicion | Indice | Nombre Completo                     |
|----------|--------|-------------------------------------|
| 1        | ICV    | Indice de Comprension Verbal        |
| 2        | IVE    | Indice Visoespacial                 |
| 3        | IRF    | Indice de Razonamiento Fluido       |
| 4        | IMT    | Indice de Memoria de Trabajo        |
| 5        | IVP    | Indice de Velocidad de Procesamiento|
| 6        | CIT    | Coeficiente Intelectual Total       |

### 4.3 Zonas Sombreadas
| Rango     | Significado         | Color                          |
|-----------|---------------------|--------------------------------|
| 40 - 75   | Por debajo promedio | Azul claro, opacidad ~0.15     |
| 125 - 160 | Por encima promedio | Azul claro, opacidad ~0.15     |

### 4.4 Linea y Puntos
- **Puntos:** circulos de radio ~6px, color violeta pastel con borde
- **Linea:** curva bezier suave (tension: 0.4) conectando los 6 puntos
- **Drag:** los puntos se arrastran solo en eje Y (X bloqueado)

---

## 5. Paleta de Colores

| Elemento                | Color                    | Hex/RGBA                        |
|-------------------------|--------------------------|---------------------------------|
| Fondo pagina            | Gris muy claro           | #F8F9FA                         |
| Acento primario         | Verde agua pastel         | #A8E6CF                         |
| Acento secundario       | Violeta pastel            | #C3B1E1                         |
| Bordes inputs           | Verde agua pastel         | #A8E6CF                         |
| Boton exportar          | Violeta pastel            | #C3B1E1                         |
| Puntos grafica          | Violeta pastel            | #C3B1E1                         |
| Linea curva             | Violeta medio             | #9B7FC4                         |
| Zonas sombreadas        | Azul claro                | rgba(173, 216, 230, 0.15)       |
| Linea media (100)       | Gris medio                | #999999                         |
| Fuente                  | Quicksand (Google Fonts)  | -                               |

---

## 6. Layout de Interfaz

```
+─────────────────────────────────────────+
|          Perfil WISC-V                  |   <- titulo, Quicksand bold, violeta
+─────────────────────────────────────────+
| [ICV:___] [IVE:___] [IRF:___]          |
| [IMT:___] [IVP:___] [CIT:___]          |   <- inputs numericos, borde verde agua
+─────────────────────────────────────────+
|                                         |
|                                         |
|            GRAFICA CANVAS               |   <- area principal
|                                         |
|                                         |
+─────────────────────────────────────────+
|         [ Exportar PNG ]                |   <- boton violeta pastel
+─────────────────────────────────────────+
```

---

## 7. Tasks de Desarrollo

### TASK 1: Estructura HTML y estilos base
**Prioridad:** Alta
**Dependencias:** Ninguna

**Subtareas:**
- [ ] Crear archivo `index.html`
- [ ] Agregar meta tags y viewport responsive
- [ ] Incluir CDN de Chart.js, plugin-dragdata, plugin-annotation, Google Fonts
- [ ] Crear estructura HTML: titulo, contenedor de inputs, canvas, boton exportar
- [ ] Aplicar estilos CSS: paleta de colores, fuente Quicksand, layout flexbox
- [ ] Estilizar inputs (borde verde agua, border-radius, padding)
- [ ] Estilizar boton exportar (fondo violeta, hover effect)

**Criterio de aceptacion:**
La pagina se renderiza correctamente con todos los elementos visibles y estilizados. No hay funcionalidad aun.

---

### TASK 2: Configuracion de la grafica Chart.js
**Prioridad:** Alta
**Dependencias:** Task 1

**Subtareas:**
- [ ] Inicializar instancia de Chart.js tipo `line` sobre el canvas
- [ ] Configurar eje Y: min 40, max 160, step 10, sub-step 5
- [ ] Diferenciar gridlines principales (cada 10, opacidad mayor) de sub-gridlines (cada 5, tenues)
- [ ] Destacar linea media en Y=100 con estilo diferenciado
- [ ] Configurar eje X con las 6 categorias: ICV, IVE, IRF, IMT, IVP, CIT
- [ ] Configurar dataset con puntos iniciales (valor default 100 para todos)
- [ ] Aplicar tension 0.4 para curva bezier
- [ ] Estilizar puntos (violeta pastel, radio 6, borde)

**Criterio de aceptacion:**
La grafica se muestra con ejes correctos, gridlines visibles, 6 puntos en 100 unidos por curva suave.

---

### TASK 3: Zonas sombreadas con plugin annotation
**Prioridad:** Alta
**Dependencias:** Task 2

**Subtareas:**
- [ ] Configurar plugin chartjs-plugin-annotation
- [ ] Crear box annotation para zona 40-75 (azul claro, opacidad 0.15)
- [ ] Crear box annotation para zona 125-160 (azul claro, opacidad 0.15)
- [ ] Verificar que las zonas no interfieren con la interaccion de puntos

**Criterio de aceptacion:**
Dos franjas horizontales azul claro visibles en la grafica, sin afectar funcionalidad.

---

### TASK 4: Sincronizacion inputs <-> puntos
**Prioridad:** Alta
**Dependencias:** Task 2

**Subtareas:**
- [ ] Agregar event listeners `input` a cada campo numerico
- [ ] Al cambiar un input: validar rango (40-160), actualizar dato en dataset, llamar chart.update()
- [ ] Clampear valores fuera de rango al limite mas cercano (40 o 160)
- [ ] Manejar inputs vacios sin errores (mantener ultimo valor valido)

**Criterio de aceptacion:**
Al escribir un numero en cualquier input, el punto correspondiente se mueve en la grafica en tiempo real.

---

### TASK 5: Drag & drop de puntos
**Prioridad:** Alta
**Dependencias:** Task 2

**Subtareas:**
- [ ] Configurar plugin chartjs-plugin-dragdata
- [ ] Restringir drag solo al eje Y (magnet.x deshabilitado)
- [ ] Configurar rango de drag: minimo 40, maximo 160
- [ ] En callback `onDragEnd`: actualizar el input correspondiente con el nuevo valor
- [ ] Redondear valor al entero mas cercano al soltar el punto
- [ ] Cursor `grab` al hover sobre puntos, `grabbing` al arrastrar

**Criterio de aceptacion:**
Los puntos se pueden arrastrar verticalmente y el input correspondiente se actualiza automaticamente.

---

### TASK 6: Exportacion a PNG
**Prioridad:** Media
**Dependencias:** Task 2

**Subtareas:**
- [ ] Agregar event listener al boton "Exportar PNG"
- [ ] Obtener imagen del canvas con `chart.toBase64Image('image/png')`
- [ ] Crear elemento `<a>` temporal con atributo `download="perfil-wisc-v.png"`
- [ ] Trigger click para descargar automaticamente
- [ ] Feedback visual al exportar (breve cambio de color del boton)

**Criterio de aceptacion:**
Al hacer click en "Exportar PNG", se descarga una imagen PNG de la grafica con todos los elementos visibles.

---

### TASK 7: Pruebas y ajustes finales
**Prioridad:** Media
**Dependencias:** Tasks 1-6

**Subtareas:**
- [ ] Verificar renderizado en Chrome, Firefox, Edge
- [ ] Verificar responsividad en pantallas pequenas
- [ ] Probar todos los inputs con valores limite (40, 75, 100, 125, 160)
- [ ] Probar drag & drop en cada punto
- [ ] Verificar que la exportacion PNG incluye zonas sombreadas y linea
- [ ] Ajustar espaciados y tamanos si es necesario

**Criterio de aceptacion:**
La aplicacion funciona correctamente en los navegadores principales sin errores en consola.

---

## 8. Riesgos y Mitigaciones

| Riesgo | Impacto | Mitigacion |
|--------|---------|------------|
| Plugin dragdata incompatible con Chart.js 4.x | Alto | Verificar compatibilidad de versiones, usar versiones pinneadas |
| Zonas annotation no se exportan en PNG | Medio | Usar plugin beforeDraw como fallback para dibujar zonas manualmente |
| Drag impreciso en pantallas tactiles | Bajo | Agregar touch events si es necesario en version futura |

---

## 9. Estructura de Archivos

```
Graficas Sari/
  index.html          <- aplicacion completa
  docs/
    plans/
      2026-03-07-wisc-v-graph-design.md   <- este documento
```

---

## 10. Definicion de Completado

El proyecto se considera completo cuando:
1. La grafica se renderiza con todos los ejes y zonas correctas
2. Los 6 inputs controlan los puntos en tiempo real
3. Los puntos se pueden arrastrar y actualizan los inputs
4. La exportacion PNG descarga una imagen fiel de la grafica
5. La interfaz refleja la paleta verde agua / violeta con fuente Quicksand
