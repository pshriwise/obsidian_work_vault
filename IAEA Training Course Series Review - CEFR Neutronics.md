#iaea #openmc #education #training

Notes on a review of a TCS document for the IAEA

## CEFR: China Experimental Fast Reactor

High-level design notes:
 - 65 MWth (20MWe) pool-type sodium-cooled fast reactor
 - UO2 fuel
- Inlet temperature: 360 °C, outlet temp: 530 °C
- sub-assemblies are fixed at the bottome w/ spacers at the top to accomodate expansion

### Startup Tests
- approach to critical by replacing mock fuel assemblies with real ones
	- last real assembly is just enough to overcome geometric buckling
- next control rod worth is measured
	- for clean core, measured at 3 different locations, critical insertion depth is then extrapolated from a control rod worth curve

### Control Rod Worth Tests
For a given configuration of all control rods in the core, one is moved and the reactivity difference is measured

$$ \rho = \frac{|k^{after}_{eff} - k^{before}_{eff}|}
{k^{after}_{eff} \times k^{before}_{eff}} \times 10^{5} (pcm)$$

### Sodium Void Coefficient Measurement
 A single assembly is replaced with a vacuum-sealed assembly containing no sodium. The reactivity change is measured as

$$ \rho = \frac{k^{void}_{eff} - k^{orig}_{eff}}
{k^{void}_{eff} \times k^{orig}_{eff}} \times 10^{5} (pcm)$$
## Neutronics Model Parameters
- Temperatures are all at 250 °C
- Material compositions calculated from natural abundances for isotopes in the nuclear dataset
- Densities and axial lengths account for expansion coefficient
- Volumes are calculated analytically
- Atomic and mass densities of fuel isotopes are used to calculate fuel composition at startup (2.97% enriched)

### Notes on Code

#### E71_CEFR_main_72assy_250C_CR_0700mm.py

Overall notes:
  - There are a number of commented lines. These should be removed so they don't confuse learners.
  - I had to change the temperature settings in OpenMC to get the model to load by adding:
```python
settings_file.temperature = {'method': 'nearest',
							 'multipole': True,
							 'tolerance': 300}
```
   Was a custom data library being used to generate the report data?
   After making these changes I got a k-eff of 1.00021 +/- 112 pcm, which is statistically similar to the eigenvalue listed in the report.
   - The flow of the file may be a little easier to follow if the surface definitions are closer to the cell definitions. I'd recommend placing the material definitions before the surfaces to remedy this.
   - It looks like there are some redundant surfaces in the model. I would recommend either removing those in the code itself or by specifying `remove_surfs=True` when exporting the geometry to XML.
```python
cyl_CR_HCO_OU = openmc.ZCylinder(x0=0, y0=0, r=2.61076, name='cyl_CR_HCO_OU') # Head connector outer radius
...
cyl_CR_LSH = openmc.ZCylinder(x0=0, y0=0, r=2.61076, name='cyl_CR_LSH') # Lower shielding inner radius
```
- Just to shorten up some of the pincell definitions. The lines
```python
p_FU_LGP = openmc.Universe()
p_FU_LGP.add_cells([
c_FU_LGP1,
c_FU_LGP2,
c_FU_LGP3
])
```
could become
```python
p_FU_LGP = openmc.Universe(cells=[
c_FU_LGP1,
c_FU_LGP2,
c_FU_LGP3
])
```
- There are a lot of numbers that might be more clear as variable names. The pincell hex lattice pitch being one of those values.
- The `HexLattice` universe definitions look really nice.
- 

- Line comments:
  - L65: There are many commented lines in the section containing surface definitions. If they aren't being used and don't contain helpful information, I'd recommend that they be removed.
  - L410-417: For the mixture materials that are created using one other material, I'd suggest using the `openmc.Material.clone` method and updating the name after.
  - L467: Setting the `openmc.Materials.cross_sections` attribute to a hard-coded path is going to cause a problem. I'd suggest setting this attribute as if the cross sections are included in the same directory as the Python script and add a comment above that line on how to set the environment variable `OPENMC_CROSS_SECTIONS` in case the learner would prefer to do this instead.
  - L4240: I'd recomment placing the comment on the group structure at the end of this array above this line for clarity.

