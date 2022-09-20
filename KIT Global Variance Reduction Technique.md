### Required XML Elements
  - "iteration" - an array of double's
  - "tally" - index of the tally to use for ww generation
  - "source_space" - 
  - "id" - ID of the `WeightWindows` object
  - "particle_type" - type of particle for weight window generation

### New methods

#### `WeightWindows::calculate_WW()`

Steps:
  - get the neutron flux value from the specified tally
  - compute the `t_value` for the tally based on the number of realizations
  - get the max and min flux for each energy group on the tally
  - compute new weight window values
```c++
// for each energy group

for (int ee = 0; ee < energy_size; ++ee) {

// celar all data before each energy group

double PS_k = 0.4/log(1/near_source);

double PS_b = 0.5 - log(max_flux_data[ee]/min_flux_data[ee])*PS_k;

  

// mesh

for (int mm = 0; mm < mesh_size; ++mm) {

lower_ww_[mm+ee*mesh_size] = -1;

upper_ww_[mm+ee*mesh_size] = -5;

if (flux_data[mm+ee*mesh_size] <= 0) continue;

if (flux_data[mm+ee*mesh_size] >= near_source*max_flux_data[ee]) lower_ww_[mm+ee*mesh_size] = log(flux_data[mm+ee*mesh_size]/min_flux_data[ee]) * PS_k + PS_b;

else lower_ww_[mm+ee*mesh_size] = 
 0.1*flux_data[mm+ee*mesh_size]/(near_source*max_flux_data[ee]);

 upper_ww_[mm+ee*mesh_size] = 5*lower_ww_[mm+ee*mesh_size];
}

}
```