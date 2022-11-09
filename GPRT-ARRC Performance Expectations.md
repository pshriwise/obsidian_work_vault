#ray-tracing #random-ray #arrc

DAGMC ray fire times: ~1-2 $\mu s$
GPRT ray fire times: ~1-10 $ns$

Meaning our time spent tracing rays onthe CAD model will drop dramatically. However, as the superimposed mesh resolution increases, more of our time is spent tracing the regular mesh rather than the CAD model -- we can see that reflected in the TPI I think. My understanding is that the reason the simulation takes longer is that the smaller FSRs resulting from the increased mesh resolution take longer to converge.

![[Screenshot 2022-11-07 at 11.47.45 PM.png]]

![[Pasted image 20221107235139.png]]
Also, should we try ignoring FSRs when we travel very small distances through them?
