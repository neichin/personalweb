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

Glacial freshwater forcing in the Soutern Ocean

####Antarctica origin freshwater fluxes can be divided in two different categories. In one hand, the meltwater injected offshore into the ocean due tu the melting of icebergs along their trajectories in the Southern Ocean. In the other hand, the basal melt of the ice shelves which mostly happend at the grounding line depth due to intrusion of warm waters into the ice shelf cavity. Contrary to Greenland, the Antarctica Ice Sheet does not present significative surface runoff due to summer warming in the surface. This quantity can be neglected in most of the continent with the exceptions of some north parts of the Antarctic Penninsula. 
####Those freshwater fluxes are commonly applied in a very simple manner. For instance, iceberg fluxes sometimes are modelled with a constant homogeneous in longitude flux and weigthed in latitude south of 60°S. Tha basal melt from ice shelves is commonly homogenously distributed in the coastal points around the Antarctic coastal line, sometimes including a seasonality.

####The spatial and temporal distribution of the freshwater may  notably impact the ocean surface properties, including the sea-ice net production. Based on recent glaciological estimations we have improved the representation of the freshwater sources in ocean models, either the ice-shelves origin, either icebergs meltwater.

####Basal melt from ice shelves: The total budgets per ice shelf are extracted from the work of Mathieu Depoorter (Depoorter et al. 2013). We have identify in the OCRCA025 grid the location of each ice shelf, and the corresponding flux is homogeneously distributed along the ice shelf front grid points. In NEMO 3.5 it is possible to distribute those fluxes along the vertical from the grounding line depth to the calving front depth. According to the findings of Pierre Mathiot (not published), distributing the fluxes in that way, help the model to create the ice shelf cavity overturning when ice shelves cavities are not included in the model. The rest of the fluxes provided by Depoorter (called upscalling values in his work) in order to match the total budget of a given oceanic setor, are homonenously injected in the ocean grid points out of the ice shelves of the corresponding oceanic sector. 

####Iceberg Fluxes: The icebergs fluxes are constructed from a ORCA025 simulation coupled with an improved version of NEMO-ICB iceberg model. The input of the iceberg model corresponds to the calving fluxes provided by Depoorter. The iceberg fluxes climatology is the result of 11 year of iceberg simulation after the needed spin-up, the original version of the climatology is constructed with a climatological year-repeted atmospherical forcing for the 1980-2010 period. We have find recently that the interannual atmospherical forcing affects the icebergs trajectories and better distribute the iceberg fluxes into the different oceanic basins according to observations.

####Both climatological icebergs fluxes (with interannual forcing and without) are available to be used to force model. It is shown that the essential of the signature of icebergs in the sea-ice can be captured with a cheap precipitation strategy (Merino et al 2016). Please cite MERINO et al 2016 when using the material described here


####Download Files:
####Iceberg climatology with climatological atmospheric forcing into ORCA025 grid
####Iceberg climatology with interannual forcing into ORCA025 grid
####Ice shelves basal melt fluxes into ORCA025 grid 
 

</div>            
