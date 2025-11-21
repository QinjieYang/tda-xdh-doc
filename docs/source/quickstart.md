# Installation
## Install PySCF
Prerequisite for TDA-xDH is [PySCF](https://github.com/pyscf/pyscf) >=2.2.

The version >=2.2 and <=2.4 of PySCF is fully compatible with TDA-xDH. Some functions of PySCF-TDA-xDH are incompatible for the higher version of PySCF.

> ⚠︎ **Warning:** PySCF is not supported natively on Windows. Install the Windows Subsystem for Linux (WSL) if you want to run PySCF on Windows.

You can install PySCF 2.4 with Conda. It is recommended to create an new environment like
```bash
conda create -n tda-xdh -c conda-forge -c pyscf pyscf=2.4
conda activate tda-xdh
```
**Optional**: We recommend to install [pyscf-tblis](https://github.com/pyscf/pyscf-tblis) for higher efficiency:
```bash
pip install pyscf-tblis
```
## Install PySCF-TDA-xDH
PySCF-TDA-xDH will be released after our paper is accepted.

After getting the package of PySCF-TDA-xDH, define the `PYSCF_EXT_PATH` environment variable to point to your local `tda-xdh` directory:
```bash
export PYSCF_EXT_PATH=/path/to/tda-xdh:\$PYSCF_EXT_PATH">> ~/.bashrc
```


# Quickstart
You may start a TDA-xDH calculation in two manners:

(1) Start from a ground-state xDH calculation;

(2) Start from a TDA-DFT calculation with a conventional hybrid functional (e.g. B3LYP in the case of XYG3), skipping DH calculation for ground-state. (**Compatible with PySCF >= 2.2 and <= 2.7**)
## (1) Start from a Ground-state xDH Calculation
```python
from pyscf import gto, scf, dft, tddft, dh, cispd
### Build a Molecule
mol = gto.Mole()
mol.build(
    atom = '''
C   -0.00124094            0.00000000            0.46104598
C   -0.93054640            0.00000000           -0.71424991
O    1.20624568            0.00000000            0.38083840
H   -0.49162964            0.00000000            1.44957198
H   -1.57671241            0.87868017           -0.66257994
H   -1.57671241           -0.87868017           -0.66257994
H   -0.37065389            0.00000000           -1.64609256
 ''',
    basis = 'cc-pvdz',
    unit = 'Angstrom', # 'Angstrom' or 'Bohr'. Default: 'Angstrom'
    symmetry = True,
)
### Start a Ground-state xDH Calculation
# You can use xDH keywords such as XYG3, XYGJ_OS or xDH_PBE0
mydh = dh.DH(mol, xc='XYG3').run()

### Start a TDA-xDH Calculation
mytdaxdh = cispd.rtdaxdh.RTDADH(mydh)
mytdaxdh.nstates = 10 # The number of states to be calculated
mytdaxdh.singlet = True # To set `False` if you want to calculate triplet. Default: True
# Run TDA-xDH
mytdaxdh.kernel()
# Print result of TDA-xDH
mytdaxdh.analyze()
mytdaxdh.analyze(verbose=4)
```
The output is 
```text
converged SCF energy = -153.837608420741
[RESULT] Energy of exchange 0.8033*HF:     -16.061930724071
[RESULT] Energy of process_energy_low_rung (non_exx): -4.516866191025728
[RESULT] Energy of process_energy_low_rung (total): -20.57879691509675
[RESULT] Energy corr MP2 of same-spin:      -0.1695274423
[RESULT] Energy corr MP2 of oppo-spin:      -0.5111041344
[RESULT] Energy corr MP2 of total:          -0.6806315767
[RESULT] Energy of correlation MP2(0.3211, 0.3211):      -0.218550799276
[RESULT] Energy of 0.8033*HF + 0.2107*B88 - 0.014*LDA, 0.6789*LYP + MP2(0.3211, 0.3211):    -153.714017740648
Excited State energies (eV)
[ 4.31938087  7.80083832  8.58839036  8.70934606  9.20948665  9.37414375  9.48444991  9.63846319 10.64433155 10.79767408]
Excited State energies (eV)
[ 4.81032638 10.4199738  11.16918844 10.43433257 12.21760745 12.02225347 10.61061005 11.46963456 12.34532429 12.55927058]
nbatch =  364
Excited State energies (eV)
[ 4.48092549  8.32201983  8.93139717  9.75249123  9.9746156  10.0690438   9.99102134 10.45351627 11.53859982 11.64822209]

** Singlet excitation energies and oscillator strengths **
Excited State   1:   A"      4.48093 eV    276.69 nm  f=0.0000
Excited State   2:   A'      8.32202 eV    148.98 nm  f=0.0134
Excited State   3:   A'      8.93140 eV    138.82 nm  f=0.1823
Excited State   4:   A"      9.75249 eV    127.13 nm  f=0.0007
Excited State   5:   A"      9.97462 eV    124.30 nm  f=0.0201
Excited State   6:   A'     10.06904 eV    123.13 nm  f=0.0337
Excited State   7:   A"      9.99102 eV    124.10 nm  f=0.0002
Excited State   8:   A'     10.45352 eV    118.61 nm  f=0.2308
Excited State   9:   A"     11.53860 eV    107.45 nm  f=0.0257
Excited State  10:   A"     11.64822 eV    106.44 nm  f=0.0194

** Singlet excitation energies and oscillator strengths **
Excited State   1:   A"      4.48093 eV    276.69 nm  f=0.0000
      12 -> 13        0.70585
Excited State   2:   A'      8.32202 eV    148.98 nm  f=0.0134
      11 -> 13       -0.15371
      12 -> 14        0.68754
Excited State   3:   A'      8.93140 eV    138.82 nm  f=0.1823
      12 -> 15        0.70178
Excited State   4:   A"      9.75249 eV    127.13 nm  f=0.0007
      10 -> 13        0.69440
Excited State   5:   A"      9.97462 eV    124.30 nm  f=0.0201
       9 -> 13       -0.10577
      12 -> 16        0.69318
Excited State   6:   A'     10.06904 eV    123.13 nm  f=0.0337
      11 -> 13        0.14947
      12 -> 17        0.68568
Excited State   7:   A"      9.99102 eV    124.10 nm  f=0.0002
       9 -> 13        0.69151
      12 -> 16        0.11212
Excited State   8:   A'     10.45352 eV    118.61 nm  f=0.2308
       8 -> 13       -0.32031
      11 -> 13        0.54987
      12 -> 14        0.11968
      12 -> 17       -0.15802
      12 -> 18       -0.17218
Excited State   9:   A"     11.53860 eV    107.45 nm  f=0.0257
       7 -> 13        0.13209
      11 -> 14        0.68819
Excited State  10:   A"     11.64822 eV    106.44 nm  f=0.0194
       7 -> 13        0.67779
      11 -> 14       -0.13056

** Transition electric dipole moments (AU) **
state          X           Y           Z        Dip. S.      Osc.
  1        -0.0000     -0.0050      0.0000      0.0000      0.0000
  2        -0.2388     -0.0000      0.0938      0.0658      0.0134
  3         0.0769     -0.0000     -0.9096      0.8332      0.1823
  4        -0.0000     -0.0542     -0.0000      0.0029      0.0007
  5        -0.0000     -0.2870      0.0000      0.0824      0.0201
  6        -0.3467     -0.0000     -0.1290      0.1368      0.0337
  7        -0.0000      0.0298     -0.0000      0.0009      0.0002
  8        -0.9142     -0.0000     -0.2559      0.9013      0.2308
  9        -0.0000      0.3016      0.0000      0.0910      0.0257
 10         0.0000     -0.2605      0.0000      0.0679      0.0194

** Transition velocity dipole moments (imaginary part, AU) **
state          X           Y           Z        Dip. S.      Osc.
  1        -0.0000     -0.0064      0.0000      0.0000      0.0002
  2        -0.0716     -0.0000      0.0071      0.0052      0.0113
  3         0.0286     -0.0000     -0.1933      0.0382      0.0776
  4        -0.0000     -0.0152     -0.0000      0.0002      0.0004
  5        -0.0000     -0.0600      0.0000      0.0036      0.0065
  6        -0.0579     -0.0000     -0.0725      0.0086      0.0155
  7        -0.0000      0.0081     -0.0000      0.0001      0.0001
  8        -0.0771     -0.0000     -0.0728      0.0112      0.0195
  9         0.0000      0.1127      0.0000      0.0127      0.0200
 10         0.0000     -0.0348     -0.0000      0.0012      0.0019

** Transition magnetic dipole moments (imaginary part, AU) **
state          X           Y           Z
  1        -1.1196     -0.0000      0.0877
  2        -0.0000     -0.2759      0.0000
  3         0.0000      0.2535     -0.0000
  4        -0.3602      0.0000     -0.7873
  5        -0.3306      0.0000     -0.0294
  6         0.0000      0.1547     -0.0000
  7         0.2302     -0.0000     -1.0603
  8        -0.0000     -0.0121      0.0000
  9        -0.4533      0.0000      0.7485
 10         0.4064      0.0000      0.3735
```
`converged SCF energy = -153.837608420741` is the SCF energy of B3LYP whose SCF density and orbitals are utilized for non-SCF calculation of XYG3. 

> **Note:**  resolution-of-indentity (RI) or density fitting is forced to be applied.

`[RESULT] Energy of 0.8033*HF + 0.2107*B88 - 0.014*LDA, 0.6789*LYP + MP2(0.3211, 0.3211):    -153.714017740648` is the final ground-state energy of XYG3.

There are three `Excited State energies (eV)` outputs. The first of them is the TDA result of B3LYP, the second one is the contribution of the non-SCF hybrid part of XYG3, and the last one is the final TDA result of XYG3. 

The details of the TDA result of XYG3 is printed at last.

## (2) Start from a TDA-DFT Calculation with a Conventional Hybrid Functional
You can also start from a TDA-DFT calculation with a conventional hybrid functional, skipping xDH calculation for ground-state, and obtain TDA-xDH excitation energies directly.

This manner is compatible with PySCF >= 2.2 and <= 2.7.
```python
from pyscf import gto, scf, dft, df, tddft, dh, cispd
### Build a Molecule
mol = gto.Mole()
mol.build(
    atom = '''
C   -0.00124094            0.00000000            0.46104598
C   -0.93054640            0.00000000           -0.71424991
O    1.20624568            0.00000000            0.38083840
H   -0.49162964            0.00000000            1.44957198
H   -1.57671241            0.87868017           -0.66257994
H   -1.57671241           -0.87868017           -0.66257994
H   -0.37065389            0.00000000           -1.64609256
 ''',
    basis = 'cc-pvdz',
    unit = 'Angstrom', # 'Angstrom' or 'Bohr'. Default: 'Angstrom'
    symmetry = True,
)
### Start a SCF Calculation and a TDA-DFT Calculation with a Conventional Hybrid Functional
mf = dft.RKS(mol).density_fit()
mf.xc = 'B3LYPG' # B3LYP (=B3LYPG) should be used for XYG3 and XYGJ_OS while PBE0 should be used for xDH_PBE0. See below `mytdaxdh`.
mf.kernel() # Run SCF calculation

mytd = tddft.TDA(mf)
mytd.nstates = 10 # The number of states to be calculated
mytd.singlet = True # To set `False` if you want to calculate triplet. Default: True
mytd.kernel() # Run TDA-DFT calculation

### Start a TDA-xDH Calculation
mytdaxdh = cispd.rtdaxdh.RTDAxDH_from_TDA(mytd,xc='XYG3')# Choose an xDH here. See above `mf.xc`.
mytdaxdh.kernel() # Run TDA-xDH calculation

# Print result of TDA-xDH
mytdaxdh.analyze()
mytdaxdh.analyze(verbose=4)
```
The output is
```text
converged SCF energy = -153.837608420741
Excited State energies (eV)
[ 4.31938087  7.80083832  8.58839036  8.70934606  9.20948665  9.37414375  9.48444991  9.63846319 10.64433155 10.79767408]
Excited State energies (eV)
[ 4.81032638 10.4199738  11.16918844 10.43433257 12.21760745 12.02225347 10.61061005 11.46963456 12.34532429 12.55927058]
Excited State energies (eV)
[ 4.48092549  8.32201983  8.93139717  9.75249123  9.9746156  10.0690438   9.99102134 10.45351627 11.53859982 11.64822209]

** Singlet excitation energies and oscillator strengths **
Excited State   1:   A"      4.48093 eV    276.69 nm  f=0.0000
Excited State   2:   A'      8.32202 eV    148.98 nm  f=0.0134
Excited State   3:   A'      8.93140 eV    138.82 nm  f=0.1823
Excited State   4:   A"      9.75249 eV    127.13 nm  f=0.0007
Excited State   5:   A"      9.97462 eV    124.30 nm  f=0.0201
Excited State   6:   A'     10.06904 eV    123.13 nm  f=0.0337
Excited State   7:   A"      9.99102 eV    124.10 nm  f=0.0002
Excited State   8:   A'     10.45352 eV    118.61 nm  f=0.2308
Excited State   9:   A"     11.53860 eV    107.45 nm  f=0.0257
Excited State  10:   A"     11.64822 eV    106.44 nm  f=0.0194

** Singlet excitation energies and oscillator strengths **
Excited State   1:   A"      4.48093 eV    276.69 nm  f=0.0000
      12 -> 13        0.70585
Excited State   2:   A'      8.32202 eV    148.98 nm  f=0.0134
      11 -> 13        0.15371
      12 -> 14       -0.68754
Excited State   3:   A'      8.93140 eV    138.82 nm  f=0.1823
      12 -> 15       -0.70178
Excited State   4:   A"      9.75249 eV    127.13 nm  f=0.0007
      10 -> 13        0.69440
Excited State   5:   A"      9.97462 eV    124.30 nm  f=0.0201
       9 -> 13       -0.10577
      12 -> 16        0.69318
Excited State   6:   A'     10.06904 eV    123.13 nm  f=0.0337
      11 -> 13       -0.14947
      12 -> 17       -0.68568
Excited State   7:   A"      9.99102 eV    124.10 nm  f=0.0002
       9 -> 13        0.69151
      12 -> 16        0.11212
Excited State   8:   A'     10.45352 eV    118.61 nm  f=0.2308
       8 -> 13        0.32031
      11 -> 13       -0.54987
      12 -> 14       -0.11968
      12 -> 17        0.15802
      12 -> 18        0.17218
Excited State   9:   A"     11.53860 eV    107.45 nm  f=0.0257
       7 -> 13        0.13209
      11 -> 14        0.68819
Excited State  10:   A"     11.64822 eV    106.44 nm  f=0.0194
       7 -> 13       -0.67779
      11 -> 14        0.13056

** Transition electric dipole moments (AU) **
state          X           Y           Z        Dip. S.      Osc.
  1        -0.0000     -0.0050      0.0000      0.0000      0.0000
  2         0.2388      0.0000     -0.0938      0.0658      0.0134
  3        -0.0769      0.0000      0.9096      0.8332      0.1823
  4         0.0000     -0.0542     -0.0000      0.0029      0.0007
  5        -0.0000     -0.2870     -0.0000      0.0824      0.0201
  6         0.3467      0.0000      0.1290      0.1368      0.0337
  7         0.0000      0.0298      0.0000      0.0009      0.0002
  8         0.9142     -0.0000      0.2559      0.9013      0.2308
  9         0.0000      0.3016      0.0000      0.0910      0.0257
 10        -0.0000      0.2605      0.0000      0.0679      0.0194

** Transition velocity dipole moments (imaginary part, AU) **
state          X           Y           Z        Dip. S.      Osc.
  1        -0.0000     -0.0064      0.0000      0.0000      0.0002
  2         0.0716      0.0000     -0.0071      0.0052      0.0113
  3        -0.0286      0.0000      0.1933      0.0382      0.0776
  4         0.0000     -0.0152     -0.0000      0.0002      0.0004
  5        -0.0000     -0.0600     -0.0000      0.0036      0.0065
  6         0.0579      0.0000      0.0725      0.0086      0.0155
  7         0.0000      0.0081     -0.0000      0.0001      0.0001
  8         0.0771     -0.0000      0.0728      0.0112      0.0195
  9         0.0000      0.1127     -0.0000      0.0127      0.0200
 10        -0.0000      0.0348      0.0000      0.0012      0.0019

** Transition magnetic dipole moments (imaginary part, AU) **
state          X           Y           Z
  1        -1.1196     -0.0000      0.0877
  2         0.0000      0.2759      0.0000
  3        -0.0000     -0.2535     -0.0000
  4        -0.3602     -0.0000     -0.7873
  5        -0.3306      0.0000     -0.0294
  6        -0.0000     -0.1547     -0.0000
  7         0.2302     -0.0000     -1.0603
  8        -0.0000      0.0121      0.0000
  9        -0.4533      0.0000      0.7485
 10        -0.4064      0.0000     -0.3735
```
This result is the same as that in the previous section.