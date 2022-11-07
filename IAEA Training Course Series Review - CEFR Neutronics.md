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
							 'tolerance': 300}
```
   Was a custom data library being used here to 
- Line comments:
  - L467: Setting the `openmc.Materials.cross_sections` attribute to a hard-coded path is going to cause a problem. I'd suggest setting this attribute as if the cross sections are included in the same directory as the Python script and add a comment above that line on how to set the environment variable `OPENMC_CROSS_SECTIONS` in case the learner would prefer to do this instead.