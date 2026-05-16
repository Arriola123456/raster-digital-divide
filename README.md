# raster-digital-divide

Análisis geoespacial de la **brecha digital territorial** en la región Cusco (Perú), cruzando dos fuentes raster satelitales:

- **NASA Black Marble VNL 2025** — radiancia nocturna como proxy de urbanización y población.
- **OSIPTEL 2019** — kernel de densidad de cobertura móvil a 50 m como proxy de acceso a internet.

## Pregunta de investigación

¿Qué zonas de la región Cusco concentran la mayor brecha digital, entendida como presencia de actividad humana detectable (luz nocturna) pero sin cobertura móvil? ¿Cuánto del territorio se encuentra en exclusión digital total?

## Estructura del repo

```
raster-digital-divide/
├── data/                              (gitignored)
│   ├── VNL_cusco_2025.tif
│   └── kernel_cobmovil2019_50m.tif
├── notebooks/
│   └── digital_divide_cusco.ipynb
├── output/
│   ├── vnl_norm.tif
│   ├── conn_norm.tif
│   ├── ibd_brecha_digital.tif
│   ├── clasificacion_brecha.tif
│   └── dashboard_brecha_digital.png
├── README.md
└── requirements.txt
```

## Setup

```bash
pip install -r requirements.txt
```

Descargar los rasters del Drive del curso y colocarlos en `data/` (no se trackean):

- `data/VNL_cusco_2025.tif`
- `data/kernel_cobmovil2019_50m.tif`

## Cómo correr el notebook

```bash
jupyter notebook notebooks/digital_divide_cusco.ipynb
```

O end-to-end desde CLI:

```bash
jupyter nbconvert --to notebook --execute --inplace notebooks/digital_divide_cusco.ipynb
```

El pipeline produce los 4 GeoTIFFs alineados a la grilla VNL y el dashboard composite en `output/`.

## Archivos de output

| Archivo | Descripción |
|---------|-------------|
| `vnl_norm.tif` | VNL normalizado p2–p98 a [0, 1] |
| `conn_norm.tif` | Cobertura móvil reproyectada a EPSG:4326 y normalizada |
| `ibd_brecha_digital.tif` | IBD = VNL_norm − Conn_norm, rango [−1, 1] |
| `clasificacion_brecha.tif` | Clasificación territorial 2×2 (1=Urban Connected, 2=Urban Divide, 3=Rural Connected, 4=Critical Divide) |
| `dashboard_brecha_digital.png` | Composite final de los 6 mapas clave a 150 dpi |

## Hallazgos principales

- El **90.84% del área** (≈196,300 km²) cae en **Critical Divide**: ni luz nocturna detectable ni cobertura móvil, consistente con la altiplanicie y cordillera rural de Cusco.
- La **brecha activa** se concentra en el **3.39% del territorio** (≈7,317 km², clase Urban Divide): zonas con presencia humana visible pero sin servicio móvil. Es el target accionable para inversión en infraestructura.
- La correlación Pearson entre luminosidad y conectividad es moderada (**r = 0.347**), confirmando que ambos fenómenos se concentran en los corredores urbanos del valle del Vilcanota, pero no son redundantes.
- El test de Welch entre Urban Connected y Critical Divide sobre VNL da **Cohen's d ≈ 29.8**, magnitud estructural muy por encima del ruido muestral.
