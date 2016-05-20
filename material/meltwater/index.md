---
layout: page2
description: "Icebergs and basal melt fluxes for ocean models"
title: "Freswater in the Southern Ocean"
header-img: "img/weather_icebergs2.jpg"
---
  


  <script type="{{site.baseurl}}/text/javascript">
    $(function(){
      SyntaxHighlighter.all();
    });
    $(window).load(function(){
      $('.flexslider').flexslider({
        animation: "slide",
        slideshow: false,
        slideshowSpeed: 12000,
        pausePlay: true,
        start: function(slider){
          $('body').removeClass('loading');
        }
      });
    });
  </script>

<!-- 
<div class="container">
	<div class="row">
        <div class="col-lg-8 col-lg-offset-2 col-md-10 col-md-offset-1">
 -->

Glacial freshwater forcing in the Southern Ocean

#####Antarctic origin freshwater fluxes can be divided into two different categories depending on its sources. In one hand, the meltwater injected offshore into the ocean due to the melting of icebergs along their trajectories in the Southern Ocean. In the other hand, the basal melt from the ice shelves, in its majority at the grounding line, controlled by the intrusion of warm waters into the ice shelf cavity. Contrary to Greenland, the Antarctica Ice Sheet does not present any significant surface runoff due to the warming of the ice surface in summer . This quantity can be neglected in most of the continent with the exceptions of some north parts of the Antarctic Penninsula. 


#####Those freshwater fluxes are commonly plugged in a very simple manner into the model. For instance, iceberg fluxes sometimes are modelled with a constant and homogeneous in longitude flux and weigthed in latitude south of 60°S. Tha basal melt from ice shelves is commonly averaged and distributed in the coastal points around the Antarctic coastal line, sometimes including a seasonality.


#####The spatial and temporal distribution of the freshwater may notably impact the ocean surface properties, including the sea-ice net production. Based on recent glaciological estimations the representation of the freshwater sources in ocean models has been improved:

#####   Basal melt from ice shelves: The melt rates per ice shelf are extracted from the work of Mathieu Depoorter (Depoorter et al. 2013). We have identify in the ORCA025 grid the location of each ice shelf, and the flux of the corresponding ice shelf is homogeneously distributed along its coastal grid points. In NEMO 3.5 it is possible to distribute those fluxes along the vertical from the grounding line depth to the calving front depth. According to the findings of Pierre Mathiot (not published), distributing the fluxes in this way, help the model to create the ice shelf cavity overturning when ice shelves cavities are not explicitly included in the model. The rest of the fluxes provided by Depoorter (called upscalling values in his work) in order to match the total budget of a given oceanic setor, are homonenously distributed in the coastal grid points of the corresponding oceanic sector. We decided not to include seasonality in the basal melt fluxes. Most of the melt water comes from the interior of the ice shelf cavity, where the seasonal variation of the water temeprature is commonly negligible.

#####   Iceberg Fluxes: The icebergs fluxes are constructed from a ORCA025 simulation coupled with an improved version of NEMO-ICB iceberg model (merino et al. 2016 in Rev). The input of the iceberg model corresponds to the calving fluxes provided by Depoorter et al. 2013. The iceberg fluxes climatology is the result of 11 year of iceberg simulation after the needed spin-up, the original version of this climatology is constructed with a climatological year-repeted atmospherical forcing for the 1980-2010 period. We have recently founded that the interannual atmospherical forcing affects the icebergs trajectories and may better distributes the iceberg fluxes between the different oceanic basins.

#####Both climatological icebergs fluxes (with interannual forcing and without) are available to be used to force model. It is shown that the essential of the impact of icebergs on the sea-ice can be captured with a cheap forcing strategy (Merino et al 2016).


###Download Files:

- #####Iceberg climatology with climatological atmospheric forcing.

- #####Iceberg climatology with interannual forcing.

- #####Ice shelves basal melt fluxes

###References:

- #####Merino, N. J. Le Sommer, G. Durand, N. Jourdain, G. Madec, P. Matthiot and J. Tournadre. Antarctic icebergs melt over the Southern Ocean: climatology and impact on sea-ice. Accepted for publication in Ocean Modelling, in press..

- #####.Depoorter, M. A., Bamber, J. L., Griggs, J. A., Lenaerts, J. T. M., Ligtenberg, S. R. M., van den Broeke, M. R., & Moholdt, G. (2013). Calving fluxes and basal melt rates of Antarctic ice shelves. Nature, 502(7469), 89-92.
 

</div>            
