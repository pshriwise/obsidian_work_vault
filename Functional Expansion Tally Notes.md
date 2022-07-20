#openmc #tallies
## Background and Motivation
Functional expansion tallies (FETs) are a form of tallies that analytically describe a value across some portion of space. Instead of contributing to a tally value associated with a discrete region of phase space, particle information is used to update the coefficient of the polynomials used to represent the field information. 

FETs are extremely useful for providing continuous tally values over some region of the domain. They are most commonly used to tally the heating distribution across a fuel pin, employing Legendre polynomials in the axial direction and Zernike polynomials in the radial direction.

## Theory

Let the flux along some dimension of the problem be represented as:

$$ \phi(z') = \sum_{n=0}^{N} a_{n}P_{n}(z') $$
where z is normalized to the range $[-1, 1]$ . This method assumes that we select a polynomial, $P_{n}(z')$, that is able to capture the shape of the flux well over the tally region.
For an axial distribution, we'll use a Legendre polynomial. 

### Legendre Polynomials

This polynomial has several properties that make it suitable for this use:

  - orthogonal basis functions
  - recursion relation

These polynomials take the form 

$$ \frac{1}{\sqrt{1 - 2xt + t^{2}}} = \sum_{n=0}^{N} P_{n}(z') t^{n}  $$
where the coefficient of $t^n$ is a polynomial in $z'$ of degree $n$.

  - $t^n$ forms the rigid basis set of the function -- determining its general form and bounds. 
  - $P_{n}(z')$ provides a set of coefficients that define the shape of the polynomial.


Importantly, these polynomials have a recursion relation that allow higher-order coefficients to be defined based on lower-order coefficients. As with most polynomials of this type the zero and first order coefficients can be defined directly:

$$ P_{0} = 1$$
$$ P_{1} = z' $$
The remaining coefficients can be defined using the following recursion relation:

$$ (n + 1)P_{n+1}(z') = (2n + 1)z'P_{n}(z') - nP_{n-1}(z') $$

Given a polynommial form, we now need to estimate the coefficients of this polynomial

$$ a_{n} = \frac{2n + 1}{2} \int_{-1}^{1} P_{n}(z')\phi(z') dz' $$
in the simulation. 

### Tally Contributions

When a particle moves through the tally region, it's information is used to update the coefficients of the polynomial. Before combining the particle's information with previous results, the coefficients of the Legendre polynomial are computed as though this particle is the only one to contribute. When the contributions from all particles are combined, the polynomial coefficients approach their correct value -- very similar to any other tally.

For a particle in the tally region (bounded by $z_{min}$ and $z_{max}$), the normalized position of the particle along the axis of the tally is used to set the zero and first coefficients:

$$ s = 2.0 * (p.z - z_{min}) / (z_{max} - z_{min}) - 1.0 $$
$$ P_{0} = 1 $$
$$ P_{1} = s $$

The remaining coefficient contributions are then computed as

$$ P_{n} = \frac{(2(n-1) + 1) s P_{n-1} - (n-1)P_{n-2}}{n-1} $$

which is just a slightly different form of the recursion relation above.
