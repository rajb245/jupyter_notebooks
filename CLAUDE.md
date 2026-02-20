# CLAUDE.md — AI Assistant Guide for jupyter_notebooks

## Repository Overview

This is a collection of Jupyter notebooks by **Daniel Estévez (EA4GPZ)** for signal processing, radio astronomy, space mission telemetry analysis, and wireless communications research. The notebooks document real-world reverse engineering and decoding of satellite, spacecraft, and terrestrial radio signals — often from publicly received IQ recordings.

The repository has no build system, test suite, or CI/CD pipeline. It is a research archive organized by mission/topic. New material is added as new spacecraft are launched or new experiments are conducted.

---

## Repository Structure

### Top-level layout

```
jupyter_notebooks/
├── <MissionName>/         # Mission-specific subdirectories (most content)
├── <TopicName>/           # Topic-specific subdirectories
├── *.ipynb                # Standalone notebooks at root
├── *.grc                  # GNU Radio Companion flowgraph at root (rare)
├── README.md
├── LICENSE-MIT
├── LICENSE-CC-BY
└── .gitattributes         # Configures git-annex backends for data files
```

### Mission/spacecraft directories

Each directory typically contains one or more Jupyter notebooks plus associated data files, GNU Radio flowgraphs, and Python helper scripts. Examples:

| Directory | Content |
|-----------|---------|
| `5G/` | 5G NR downlink decoding (PBCH, PDCCH, PDSCH) |
| `LTE/` | LTE downlink/uplink analysis; V16 beacon NB-IoT |
| `CE5/` | Chang'e 5 lunar sample return mission telemetry |
| `Tianwen/` | Tianwen-1 Mars mission telemetry analysis |
| `dslwp/` | DSLWP-B lunar cubesat (Longjiang-2): GMSK, SSDV, VLBI |
| `Voyager1/` | Voyager 1 signal demodulation and Reed-Solomon decoding |
| `JWST/` | JWST telemetry frame capture |
| `ESCAPADE/` | ESCAPADE spacecraft frame analysis |
| `BGM-1/` | BGM-1 (Queqiao-2 relay) Doppler and telemetry |
| `eshail2/` | Es'hail 2 / QO-100 amateur satellite analysis |
| `galileo-relativity/` | Galileo GNSS relativistic clock corrections |
| `BGDs/` | Galileo broadcast group delay study |
| `FLL-band-edge/` | GNU Radio FLL band-edge filter analysis |
| `ArtemisI/` | Artemis I companion cubesat frame decoding |
| `AmicalSat/` | AmicalSat nRF24-based image downlink |
| `IEEE_802.11ah/` | IEEE 802.11ah (Wi-Fi HaLow) analysis |
| `aoa/` | Angle of arrival estimation (APRS, GRAVES radar) |
| `gnss-influxdb/` | GNSS metrics pipeline (C + Python + InfluxDB) |
| `GPS_timing/` | GPS timing notebook with C receiver code |
| `SLIM/` | SLIM lunar lander Doppler and reflection analysis |
| `Hera/` | ESA Hera mission frame decoding |
| `JUICE/` | ESA JUICE mission frame decoding |
| `Psyche/` | NASA Psyche mission frame decoding |
| `DART/` | NASA DART frame decoding |
| `Europa_Clipper/` | Europa Clipper frame decoding |
| `CAMRAS-EVE/` | Earth-Venus-Earth radar experiment analysis |
| `solar_orbiter/` | Solar Orbiter and BepiColombo frame decoding |

### Root-level standalone notebooks

| Notebook | Topic |
|----------|-------|
| `Aircraft Doppler.ipynb` | Doppler analysis from aircraft ADS-B |
| `Amateur VLBI.ipynb` | Amateur VLBI (Very Long Baseline Interferometry) |
| `BPSK postprocessing.ipynb` | BPSK signal postprocessing |
| `Band-edge filter.ipynb` | GNU Radio band-edge FLL filter design analysis |
| `BY02 beacon.ipynb` | BY02 amateur satellite beacon decoding |
| `LEO Doppler.ipynb` | LEO satellite Doppler modelling |
| `TLE variation.ipynb` | TLE orbital element variation study |
| `m-FSK symbol error rate.ipynb` | m-FSK modulation SER simulation |
| `LDPC+RFC5170.ipynb` | LDPC and RFC 5170 error correction |
| `GRAVES.ipynb` | GRAVES radar meteor scatter |
| `Sprites.ipynb` | Transient luminous events (sprites) analysis |
| `IONEX.ipynb` | IONEX ionospheric maps |
| `P25 Vocoder FEC.ipynb` | P25 digital radio vocoder FEC |
| `FossaSat-1 RTTY packet.ipynb` | FossaSat-1 RTTY telemetry |
| `LoRa balloon.ipynb` | LoRa high-altitude balloon |
| `VoyagerGBT.ipynb` | Voyager 1 via Green Bank Telescope |
| `fmt.ipynb` | Frequency measurement and analysis |
| `Rain backscatter 10 GHz.ipynb` | 10 GHz rain backscatter study |

---

## Data Management: git-annex

Large binary data files (IQ recordings, NumPy arrays) are managed with **git-annex** rather than stored directly in git. They appear as broken symlinks unless retrieved.

### git-annex backends (from `.gitattributes`)

| Extension | Meaning |
|-----------|---------|
| `.c64` | Complex 64-bit IQ (2× float32 interleaved) |
| `.f32` | 32-bit float samples |
| `.u8` | 8-bit unsigned raw IQ |
| `.npy` | NumPy array |
| `.npz` | NumPy compressed array |
| `.gz` | Gzip-compressed data |

### Retrieving data files

```bash
# Add the remote annex store
git remote add -f eala http://eala.destevez.net/~daniel/jupyter_notebooks.git

# Get a specific file
git annex get fmt.all

# Get all data files
git annex sync eala --content
```

Data files not retrieved will appear as broken symlinks — notebooks that depend on them will fail with `FileNotFoundError`.

---

## File Types and Their Roles

| Extension | Tool | Purpose |
|-----------|------|---------|
| `.ipynb` | Jupyter | Main analysis notebooks |
| `.grc` | GNU Radio Companion | SDR signal capture/preprocessing flowgraphs |
| `.py` | Python | Helper scripts (parsers, converters, processors) |
| `.c` | C compiler | High-performance computations (GNSS, GPS) |
| `.u8` | — | Raw 8-bit unsigned IQ frames (output of GNU Radio) |
| `.c64` | — | Complex 64-bit IQ recordings |
| `.f32` | — | Float32 samples |
| `.sigmf-data` / `.sigmf-meta` | SigMF | Annotated radio recordings |
| `.pcap` | Wireshark | Decoded packet captures (LTE, 802.11) |
| `.asn1` | asn1tools | Protocol definitions (LTE RRC, NR RRC) |
| `.script` | GMAT / OREKIT | Orbital mechanics scripts |
| `.oem` | CCSDS OEM | Orbit ephemeris files |
| `.sp3` | GNSS | Precise satellite orbit files |
| `.rnx` / `.gz` | GNSS | RINEX navigation/observation files |

---

## Key Python Dependencies

The notebooks collectively use the following libraries. Not all are needed for every notebook.

### Core scientific
- `numpy` — array computations (used in virtually every notebook)
- `scipy` — signal processing, FFT, optimization, statistics
- `matplotlib` — plotting (virtually every notebook uses `%matplotlib inline`)

### Astronomy / orbital mechanics
- `astropy` — FITS, time systems, coordinate transformations
- `skyfield` — satellite tracking from TLE, planetary ephemeris
- `pymap3d` — geodetic/ECEF/ENU coordinate conversions
- `spiceypy` — SPICE kernel interface for precise spacecraft ephemeris
- `ephem` — PyEphem for quick satellite/planet positioning
- `sgp4` — SGP4/SDP4 TLE propagator

### GNSS
- `georinex` — RINEX file reading
- `gnss-influxdb/libgnsscalcs.c` — custom C library for GNSS calculations (compiled via Makefile)

### Radio / signal processing
- `gnuradio` — GNU Radio Python bindings (used in notebooks that interface with GR)
- `construct` — declarative binary data parsing (CCSDS frames, protocol PDUs)
- `scapy` — packet dissection
- `asn1tools` — ASN.1 schema parsing (LTE/NR RRC messages)
- `sigmf` — SigMF metadata reading

### Error correction codes
- `galois` — Galois field arithmetic, Reed-Solomon, LDPC
- `ccsds` — CCSDS-specific codecs

### Data handling / visualization
- `pandas` — tabular data
- `xarray` — N-dimensional labelled arrays
- `dask` — parallel array processing
- `h5py` — HDF5 file access
- `healpy` — HEALPix sky maps
- `cartopy` — geographic mapping
- `seaborn` — statistical plotting
- `numba` — JIT compilation for performance

### Utilities
- `PIL` / `Pillow` — image handling
- `tabulate` — pretty-print tables
- `wget` — data downloading
- `yaml` — configuration files

---

## Development Conventions

### Notebook style
- First cell typically sets up matplotlib: `%matplotlib inline` and `plt.rcParams['figure.figsize'] = (...)`.
- NumPy arrays are the primary data container. Avoid pandas unless tabular data is genuinely needed.
- Computations flow linearly from raw data → preprocessing → decoding → visualization.
- Inline plots are expected; `plt.show()` is rarely called explicitly.
- Cell outputs (plots, printed data) are preserved in the `.ipynb` file — this is intentional, as the notebooks double as analysis reports.

### Directory conventions
- One directory per mission or experiment.
- The directory contains all related assets: notebooks, GRC flowgraphs, Python helpers, data files, and external data/configuration files.
- Naming follows the mission or spacecraft name (e.g., `ESCAPADE/`, `Hera/`, `BGM-1/`).
- When a mission has multiple phases or dates, multiple notebooks coexist (e.g., `CE5/` has one notebook per observation session).

### GNU Radio workflow
1. Record IQ data using a GRC flowgraph (`.grc` file).
2. Output frames or resampled data to `.u8`, `.c64`, or `.f32` files.
3. Process the output in a Jupyter notebook.

GRC flowgraphs are not executed from notebooks — they are run separately with `gnuradio-companion` or `grcc`. The `.u8`/`.c64` output files are then the notebook's input.

### Python helper scripts
- Scripts in mission subdirectories (e.g., `Tianwen/ccsds.py`, `LTE/lte.py`, `dslwp/vlbi.py`) are imported by notebooks in the same directory using `sys.path` manipulation or as local modules.
- These are not packaged — there is no `setup.py` or `pyproject.toml`.

### C code
- Some subdirectories have `.c` files and `Makefile`s (e.g., `gnss-influxdb/`, `GPS_timing/`, `galileo-outage/`).
- Build with `make` in that directory before running the associated notebook.
- Outputs are typically shared libraries (`.so`) loaded via `ctypes`, or standalone binaries.

---

## Common Patterns in Notebooks

### Loading raw IQ data

```python
import numpy as np

# Complex 64-bit (IQ pairs as float32)
x = np.fromfile('recording.c64', dtype='complex64')

# Raw 8-bit unsigned (center at 127.5, scale to [-1, 1])
raw = np.fromfile('frames.u8', dtype='uint8').astype('float32')
x = (raw - 127.5) / 128.0  # convert to float

# NumPy arrays
data = np.load('data.npy')
```

### FFT and spectrum analysis

```python
import matplotlib.pyplot as plt
import numpy as np

NFFT = 4096
faxis = np.fft.fftshift(np.fft.fftfreq(NFFT, d=1/sample_rate))
spectrum = np.fft.fftshift(np.fft.fft(x[:NFFT]))
plt.plot(faxis, 20 * np.log10(np.abs(spectrum)))
plt.xlabel('Frequency (Hz)')
plt.ylabel('Power (dB)')
```

### CCSDS frame parsing with `construct`

```python
from construct import *

SpacePacketHeader = Struct(
    'version' / BitsInteger(3),
    'type' / BitsInteger(1),
    ...
)
```

### Orbital mechanics with skyfield

```python
from skyfield.api import load, EarthSatellite
from skyfield.api import wgs84

ts = load.timescale()
satellite = EarthSatellite(tle_line1, tle_line2, 'NAME', ts)
t = ts.utc(2024, 1, 19, 12, 0, 0)
geocentric = satellite.at(t)
```

---

## Licensing

- **Source code** (`.py`, `.c`, notebook code cells): MIT License (`LICENSE-MIT`)
- **Data** (IQ recordings, `.npy`, etc.): CC BY 4.0 (`LICENSE-CC-BY`) where applicable; some data retains original copyright from third-party sources.

If unsure about a specific file's license, open an issue on GitHub.

---

## Working with This Repository as an AI Assistant

### DO
- Read the notebook's existing cells top-to-bottom to understand the analysis pipeline before making changes.
- Preserve all existing cell outputs (plots, printed results) unless explicitly asked to clear them.
- Use numpy-centric idioms consistent with the existing code style.
- Check if a `.grc` file exists alongside a notebook — it shows the signal capture setup.
- When adding new analysis, follow the existing linear notebook structure (no class-heavy abstractions).
- Keep helper code in the same directory as the notebook that uses it.

### DON'T
- Run git-annex commands unless the user requests data retrieval — broken symlinks are expected without the annex remote.
- Install packages globally — the environment is assumed pre-configured.
- Introduce test frameworks, CI configuration, or packaging (`setup.py`, `pyproject.toml`) — this is a research archive, not a software package.
- Refactor or restructure existing notebooks beyond what the task requires.
- Commit large binary data files directly to git — they belong in git-annex.
- Clear existing cell outputs from notebooks unless the user asks.

### Adding new material
When adding a new mission or experiment:
1. Create a new subdirectory named after the mission (e.g., `NewMission/`).
2. Place the analysis notebook, GRC flowgraphs, and Python helpers inside it.
3. Use `git annex add` for large binary data files, not `git add`.
4. Write a descriptive commit message following the existing style (e.g., `Add <MissionName> materials` or `Add <feature> to <MissionName>`).

---

## Git Workflow

```bash
# Standard commit flow
git add NewMission/NewMission\ analysis.ipynb
git annex add NewMission/recording.u8    # for binary data
git commit -m "Add NewMission frame analysis"
git push -u origin <branch>
```

Commit messages are short and descriptive, following the pattern seen in git log:
- `Add <Mission> materials`
- `Update <Mission> <topic>`
- `Add <feature> to <topic>`
- `Fix <description>`
