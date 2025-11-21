# User Guide
## Choose an xDH
### xDH Keyword List
Following xDH Keyword are available:
- `XYG3`
- `XYGJ_OS`
- `xDH_PBE0`
> ⚠︎ **Warning:** You must use underline `_` instead of hyphen `-` in keyword.
>> However, you should use hyphen `-` instead of underline `_`, such as `XYGJ-OS` or `xDH-PBE0` in your publication.
### User-defined xDH
You can customize an xDH by specifying an xc functional for SCF calculation and an xc functional for energy evaluation.

Taking XYG3 as example, the following three ways all give same result for ground-state xDH calculation:
```python
# (1) Use keyword 'XYG3'
mydh = dh.DH(mol, xc='XYG3').run()

# (2) Specify an xc functional for SCF and an doubly hybrid xc functional for energy
xc_scf = 'B3LYPG'
xc_eng = '0.8033*HF + 0.2107*B88 - 0.014*LDA, 0.6789*LYP + 0.3211*MP2'
mydh = dh.DH(mol, xc=(xc_scf,xc_eng)).run()


# (3) is similar to (2), but furtherly divide MP2 term into SS-MP2 and OS-MP2 components.
xc_scf = 'B3LYPG'
xc_eng = '0.8033*HF + 0.2107*B88 - 0.014*LDA, 0.6789*LYP + 0.3211*MP2_SS + 0.3211*MP2_OS'
mydh = dh.DH(mol, xc=(xc_scf,xc_eng)).run()
```
If you want to start a TDA-xDH calculation from a conventional TDA-DFT calculation (see [Quickstart: (2)](#quickstart:quickstart), you can set SCF functional and xDH energy functional in following way:
```python
xc_scf = 'B3LYPG'
xc_eng = '0.8033*HF + 0.2107*B88 - 0.014*LDA, 0.6789*LYP + 0.3211*MP2'
# ...
mf = dft.RKS(mol).density_fit()
mf.xc = xc_scf
# ...
mytdaxdh = cispd.rtdaxdh.RTDAxDH_from_TDA(mytd,xc=xc_eng)
# ...
```

##  Singlets and Triplets
As shown in [Quickstart](#quickstart:quickstart), you can set `RTDADH.singlet = True` to calculate singlets and set `RTDADH.singlet = False` to calculate triplets.
If you want to calculate singlets and triplets in one task, just run twice TDA-xDH with different settings of `RTDADH.singlet`:
```python
### Singlets
mytdaxdh_s = cispd.rtdaxdh.RTDADH(mydh)
mytdaxdh_s.nstates = 10 # The number of states to be calculated
mytdaxdh_s.singlet = True # Set `True` to calculate singlets.
mytdaxdh_s.kernel()
mytdaxdh_s.analyze()
mytdaxdh_s.analyze(verbose=4)

### Triplets
mytdaxdh_t = cispd.rtdaxdh.RTDADH(mydh)
mytdaxdh_t.nstates = 10 # The number of states to be calculated
mytdaxdh_t.singlet = False # Set `False` to calculate triplets.
mytdaxdh_t.kernel()
mytdaxdh_t.analyze()
mytdaxdh_t.analyze(verbose=4)
```
If you want to start a TDA-xDH calculation from a conventional TDA-DFT calculation (see [Quickstart: (2)](#quickstart:quickstart), you can set `TDA.singlet` in a similar way.
```python
### Singlets
mytd_s = tddft.TDA(mf) 
mytd_s.nstates = 10 # The number of states to be calculated
mytd_s.singlet = True # Set `True` to calculate singlets.
mytd_s.kernel()
mytdaxdh_s = cispd.rtdaxdh.RTDAxDH_from_TDA(mytd,xc="XYG3")# Choose an xDH here.
mytdaxdh_s.kernel()
mytdaxdh_s.analyze()
mytdaxdh_s.analyze(verbose=4)

### Triplets
mytd_t = tddft.TDA(mf)
mytd_t.nstates = 10 # The number of states to be calculated
mytd_t.singlet = False # Set `False` to calculate triplets.
mytd_t.kernel()
mytdaxdh_t = cispd.rtdaxdh.RTDAxDH_from_TDA(mytd,xc="XYG3")# Choose an xDH here.
mytdaxdh_t.kernel()
mytdaxdh_t.analyze()
mytdaxdh_t.analyze(verbose=4)
```
