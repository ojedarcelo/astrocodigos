# Experiencia 1

## Objetivos

- Trabajar con datos fuera de los CCD típicos de ópticos
- Transformar "visibilidades" a productos más fáciles de procesar y analizar

ALMA: rango milimétrico

Deberían quedar imagenes distintas para todos, aprender a usar los mejores parámetros

**Análisis morfo-cinemático del gas molecular de galaxias cercanas**
- Usar datos de ALMA calibradas de galaxias cercanas (correr el pipeline). Detección de emisión de continuo a ~1mm y detecciones de la línea de emisión 12CO(2-1) (¿Qué tipo de continuo vemos a esta longitud de onda?)

- CASA, wrapper de python para trabajar con datos de ALMA, para crear cubos, imágenes, sustracción de continuo, momentos de la línea y análisis.
- Obtener productos "limpios" y comparar la distribución de gas molecular con otras $\lambda$, ver distribución de densidad superficial de gas molecular y análisis cualitativo de something

**datos:** https://cloudia.astro.puc.cl/nextcloud/index.php/s/HNEREQEy8c6xEq9 

es nuestra responsabilidad encontrar más información sobre estas galaxias (buscar las imágenes en el óptico, color, contornos de gas molecular, contornos de continuo)

.ms: measurement set

me toca NGC 5995

**CASA:** https://casa.nrao.edu/

primera tarea: instalar CASA a ver si funciona

**CARTA** (ya lo tengo)

**TCLEAN**: función de CASA (```casatasks.imaging.tclean```), aprender qué significa cada parámetro (weithing, **robust**) y los valores de cada parámetro

### Tarea para el **JUEVES**:
    
- hacer funcionar CASA
- descargar el .ms
- correr listobs('Galaxia.ms') y tratar de entender el output.

observing with ALMA - A primer, ALMA Cycle 11 Technical Handbook

https://theskylive.com/sky/deepsky/ngc5995-object

## Observaciones en Radio

### La ventana de radio

Atmósfera es opaca en algunos rangos. Ventanas transparentes son mainly optico y radio.
Ventana de ALMA es sensible al agua, por eso hay q llevarlo más alto. De repente se observan opacidades y líneas asociadas a agua

- El profe dice de acostumbrarnos a usar frecuencias

La transmisión a altas frecuencias depende mucho del agua

- Tenemos banda 6

**Jansky**: $1\hspace{1mm}\text{Jy} = 10^{-26} $

**Intensidad específica**: $I_{\nu} = \left[\frac{\text{Jy}}{\text{beam}}\right]$

**Densidad de flujo**

**Flujo**

### Mecanismos de emisión

- **Continuo**:
    - Polvo frío (cuerpo negro de baja temperatura, 20~30 K)
    - Bresstrahlung (espectro plano)
    - Sincrotrón

*Identificar la naturaleza de la radiación en el continuo de nuestros datos*

- **Líneas**:
    - Cambios de niveles de energía por moléculas vibrando (baja frecuencia)
        - CO brillantes para galaxias
    - Transiciones de niveles de energía en átomos


### Tipos de radio telescopios

- **Single dish (antenas individuales)**
    - Green Bank
    - Algunas de ALMA
    - APEX
    - Beam (resolución angular) depende del tamaño de la antena como $\theta \approx 1.2 \times \frac{\lambda}{D}$

- **Interferómetros**
    - Múltiples antenas trabajando juntas para aumentar la resolución angular del telescopio
    - VLT, ALMA, NOEMA, ATCA, EHT
    - Están conectados por fibra óptica
    - Beam primario vs Beam sintetizado
        - Beam primario esta dado por el tamaño de los telescopios (qué tan grande es el área del cielo es sensible a observaciones)
        - Beam sintetizado dado por la separación entre telescopios (PSF obtenida del interferómetro)
    - Baseline
        - plano u-v
        - iFFT --> Dirty Beam: respuesta del sistema a una fuente puntual
        - Deconvolución o Cleaning
        - Kernel de convolución

### Visibilidades

Un interferómetro mide directamente los componentes de la transformada de Fourier de la distribución de intensidad del cielo

- Amplitud, fase

Para fuente puntual:

- La amplitud va a ser constante para todas las u-v distances
- Fase = 0

### Convoluciones

### Dirty beam o PSF

- Grilla de visibilidades
- **Natural weighting**: peso dado por la cantidad de visibilidades que caen en una celda. Maximiza sensibiildad pero beam sintetizado grande y sidelobes considerables
- **Uniform weighting**: Mismo peso para todas las celdas. Maximiza angular resolution y disminuye sidelobes
- **Briggs weighting**: Permite combinar natural y uniform con un parámetro Robust

- A veces es mejor usar Tapered, si hay emisiones mas extendidas usar uniform. Depende todo de las emisiones que se observan.

- Hay que lograr que el residuo sea solo ruido

- TCLEAN ajusta una gaussiana al peak central del dirty beam --> clean beam
- Tratar con el CLEAN y quizaaas con Multi-Scale

**TAREA PARA EL MARTES**
- plotms() para graficar las visibilidades
    - U-V plane, Amplitud vs U-V distance, Amplitude vs frequency, Phase vs uv-distance

```python
tclean(vis='Galaxia.ms',
    field='XX',
    imagename='Primer_Imagen_Dirty',
    imsize='XXXX',
    cell='XXXarcsec',
    specmode='mfs',  # multi frequency system
    niter=0,
    interactive=False
)

tclean(vis='Galaxia.ms',
    field='XX',
    imagename='Primer_Cubo_Dirty',
    imsize='XXXX',
    cell='XXXarcsec',
    specmode='cube',
    width='XXkm/s',  # acepta distintas unidades
    niter=0,
    nchan=-1,
    interactive=False
)
```

CASA permite que nos equivoquemos en los parámetros. Si pongo algo que no hace sentido no va a tirar error, corre con valores default, así que **ojo con los Types de cada uno de los parámetros**

## Cleaning y caracterización del continuo y líneas

    (Pregunta) Si tengo un beam sintetizado de 5 arcsec, cuál es el tamaño adecuado para el cell size/pixel en segundos de arco?

    R: Alrededor de 1 arcsec, pixeles más grandes subsamplean, pixeles más pequeños oversamplean y ocupan mucho espacio de cómputo y almacenamiento

    (Pregunta) Si quiero obtener una buena resolución angular (beam sintetizado pequeño) con ALMA, que configuración del array debo pedir?

    R: La configuración más extendida: Antenas más separadas

    (Pregunta) Si quiero maximizar mi sensitividad en una observación de ALMA, que weighting/peso debo usar en tclean?

    R: Natural

    (Pregunta) Si quiero maximizar mi resolución angular en una observación de ALMA, que weighting/peso debo usar en tclean?

    R: Uniform

### Setup espectral 

https://splatalogue.online/#/basic

### PLOTMS

se puede cambiar el bineo promediando por algún parámetro (e.g. para el plot frequency vs amp se puede promediar por un rango de tiempo)

### Cleaning interactivo

interactie: True, iteraciones mayores a 0

(ir agregando mascaras region por region donde pensemos que hay emisión)
(limpiar hasta que el residuo parezca ruido)

### Sustracción del continuo

Una vez encontrado el rango de frecuencias de las líneas

```python
uvcontsub(vis='myMS.ms', fitspw='23.684~23.704GHz', excludechans=True, combine ...)
```

### Probando parámetros

Dentro de tclean

- deconvolver = multiscale
- usemask = auto-multithresh

### Momentos de las líneas

para momento 0: tener flujo integrado de la línea

- immoments(cubo)

```python
immoments(
    'clean.image',
    moments=[-1,0,1,2,8],
    chans='20~30',
    axis='spec',
    mask='"clean.image">0.5',
    outfile='mom_withmask.im',
    includepix=(1e-3,1)
)
```

(en el informe hay que dejar bien claro cómo se hicieron los momentos)

### Corrección por el Primary Beam

immarith o pbcor

.pbcor (primary beam correction)

### TAREA PARA EL MARTES

- Crear imágenes del continuo y cubos limpios
- Identificar el rango de frecuencias de las líneas y sustraer el continuo
- Crear momentos de las líneas
- Aplicar la corrección de PB al momento-0
- Próximas semanas veremos cómo hacer fotometría!

    

