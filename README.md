# Starmap — Milky Way Explorer (v2)

A browser-based, interactive 3D view of the Milky Way, built with Three.js.

## What's new in v2

- English interface, documentation and project naming.
- Star points use a circular fragment shader with simple sphere lighting, so they read as tiny luminous spheres rather than square sprites.
- Camera panning is enabled with **right-mouse drag** (or two-finger trackpad pan where supported), in addition to orbit and zoom.
- **WASD** or the **arrow keys** pan the view; hold **Shift** to move faster.
- Blue spacecraft-HUD styling: translucent blurred panels, cyan accents and clipped panel corners.
- Search for a Gaia DR3 `source_id`, or use the shortcuts `Sun` and `Sgr A*`.

## Run locally (Debian / Linux)

The browser needs network access to load Three.js and the public astronomical catalogues.

```bash
cd Starmap
python3 -m http.server 8000
```

Then open <http://localhost:8000>.

Serve the folder over HTTP rather than opening `index.html` directly with `file://`; browsers can block JavaScript modules and cross-origin requests in that mode.

## Navigation

| Action | Control |
|---|---|
| Orbit | Left mouse drag |
| Pan | Right mouse drag |
| Zoom | Mouse wheel / trackpad scroll |
| Inspect a rendered star | Left click |
| Pan with keyboard | WASD or arrow keys |
| Faster keyboard pan | Shift + WASD / arrows |
| Find a star | Enter a Gaia DR3 `source_id`, then press Enter or LOCATE |
| Find the Sun | Search `Sun` |
| Find the Galactic Centre | Search `Sgr A*` |

## Data and scientific limitations

### Gaia DR3 field stars

The star field is loaded from the precompiled `stars.bin` file in the upstream project. Its records contain eight little-endian float32 values per star:

`x, y, z, bp_rp, g_mag, red, green, blue`

The positions are galactocentric coordinates in kiloparsecs. The compact rendering file does **not** contain Gaia `source_id` values. Therefore, clicking a rendered point displays its position and photometry but cannot reveal its exact Gaia identifier.

The search box works around that limitation by querying the official ESA Gaia DR3 TAP service for a numeric `source_id`, converting its astrometry to galactocentric coordinates, and placing a highlighted marker in the scene. The displayed field-star catalogue is a subset, not all of Gaia DR3.

### Spiral arms and masers

The four spiral-arm curves are schematic logarithmic guides, not measured physical boundaries. The upstream project also distributes Reid et al. (2019) maser anchors; the current prototype loads those points when the remote JSON file is available. The v1 prototype's maser layer is not yet a full arm-classified scientific overlay.

### Coordinate convention

The source catalogue stores Galactic Cartesian coordinates `(X, Y, Z)`. In the Three.js scene, the vertical scene axis is Galactic `Z`, so the mapping is `(scene X, scene Y, scene Z) = (Galactic X, Galactic Z, Galactic Y)`.

## Sources and credits

- **Gaia DR3**: ESA Gaia Archive and documentation. Gaia-derived data are subject to the applicable ESA/Gaia attribution and licence terms.
  - <https://www.cosmos.esa.int/web/gaia>
  - <https://gea.esac.esa.int/archive/documentation/GDR3/>
  - TAP service: <https://gea.esac.esa.int/tap-server/tap/sync>
- **Milky Way field-star binary and reference implementation**: <https://github.com/hertjo/milky-way-sim>
- **Reid et al. (2019)**, *Trigonometric Parallaxes of High-Mass Star Forming Regions: Our View of the Milky Way*, ApJ 885, 131; VizieR catalogue J/ApJ/885/131.

The upstream repository describes its code as MIT-licensed and its bundled Gaia-derived data as CC-BY-SA; retain the relevant attribution and licence notices when redistributing data. The Starmap prototype does not bundle the remote astronomical catalogues.

## Known limitations / next steps

- Catalogue loading and star search require an internet connection.
- Gaia source lookup currently accepts numeric DR3 `source_id` values, not common star names.
- Search results require a positive Gaia parallax to calculate a 3D position.
- Search results are highlighted with a marker; they are not inserted into the precompiled field-star point cloud.
- Spiral-arm guides remain illustrative. A future version can add dedicated Cepheid and arm-coloured maser layers, plus object detail cards and a larger locally cached catalogue.
