---
layout: post
title: Animations with Cartopy in NEMO
subtitle: 
date:       2016-1-20 12:00:00
author:     "Nacho"
header-img: "img/blog/header/post-bg-02.jpg"
thumbnail: /img/blog/thumbs/thumb02.png
tags: [Nemo, Cartopy, Python]
categories: [Nemo, Python]
---
#Animate NEMO results with Cartopy

##### Cartopy is the best solutions I found to plot the NEMO results. The integration with Matplotlib library is perfect and almost all his plot features remains as easy as working with cartesian coordinates, Resulting in very intuitive scripts

##### Some problems appear when we want to animate our NEMO results. In this post we'll try to solve some of then in the cleanest way.
 
#####The easiest way to produce animations with Python is using the animation module of Matplotlib. Hopefully, as mentioneg above, the Python library Cartopy allow us to deal with geospatial coordinates using Matplotlib at the same time. The basic idea of the animation module is to create an "update function" which will be called recursively by the animation generator. This "update function" produce a set of images that will be merged when the save method of the animation module is called.
#####In our case, since we are plotting data in maps, the axes are made with some heavy features as bathymetry contour lines, specific continent coastal lines, raster image... which make the axes generation very slow. Produce a simple montly animation by generating and regenerating the axis every single time the "update method" is called can make the animation script veeery slow.
#####The challenge here is to clean up the data that have be previously plotted, without removing the axes. That means that we can not use for instance fig.clean() or similar calls. My first idea (the most clean and elegant) was to create the axes once, and use the deepcopy method of the copy module at the interior of the update function. This would allow me to plot a new scatter plot every time the update function is called. Something like:
```python
def update(self)
	axesLocal=copy.deepcopy(axes)
```
#####Sadly, deepcopy method crashes with axis objects:

```
NotImplementedError: TransformNode instances can not be copied. Consider using frozen() instead.
```


#####The second idea was to use the set_array and the set_offets methods of the scatter plot to update the plot data. Which is a very nice practice when producing animations, Those methods do not seem to be implemented yet with the cartopy interface, and they do not take the transformation argument. The results is the data cannot be projected in your cartopy axes.

#####Finally the best and quickest solution is to use the scatter.remove() method. This method removes the scatter instance that was created and asociated to a global varible by the "update method" called before. The axis and the figure remains 


##### The main program just open the data with the shape (temporal, latitud, longitud). In this example I only need the 300 first latitude values. Temporal dimension is 12 (one per month).
##### Main looks as simple as this:

```python

import netCDF4
import numpy as np
import matplotlib.pyplot as plt
import cartopy.crs as ccrs
import pylab as m
import matplotlib.cm as cm
from matplotlib import animation
from matplotlib.colors import LogNorm

if __name__ == '__main__':
    
    #opening lon and lat values
    ncfile = netCDF4.Dataset('/Path/to/Data/mesh_hgr.nc','a')
    lonVar= np.array(ncfile.variables['nav_lon'])[0:300,:]
    latVar= np.array(ncfile.variables['nav_lat'])[0:300,:]
    ncfile.close()
    
    #Opening the Icebergs Flux Data
    path='/Path/to/Data/Climato_Icebergs.nc'
    ncfile = netCDF4.Dataset(path,'a')
    RunoffVar = np.array(ncfile.variables['Melt'])[:,0:300,:]*3600*24 #Kg/s-->mm/day
    ncfile.close()
    
    ChampVar=RunoffVar
    
    #The MP4 file I want to produce
    animationname=path+'/test.mp4'
    
    #aniationGeneral(Field I want to plot,
    #				Time steps , 
    #				Longitud Field, 
    #				Latitud Field,
    #				Min Value of the colorbar,
    #				Max Value of the colorbar,
    #				File name
    a = animationGeneral(ChampVar,12,lonVar,latVar,-1,1,animationname)
    
    #Show at the end
    #a.show()
```

#### The main routine basically open the data and create and object of the class animationGeneral. This object is the film with the given name.


```python
class animationGeneral(object):
# First set up the figure, the axis, and the plot element we want to animate
    def __init__(self, champ, stepNum,lon,lat,vmin,vmax,animationname):
    	#Global definitions
        self.MassVar=champ
        self.lonVar=lon
        self.latVar=lat
        self.t=0
        self.vmin=vmin
        self.vmax=vmax
        
        #Figure
        self.fig = plt.figure()
        
        #Axes definition
        self.ax = plt.axes(projection=ccrs.SouthPolarStereo())
        self.ax.set_extent([-200, 200, -45, -45],ccrs.PlateCarree())
        
        #The plot to animate
        self.scat2=self.ax.scatter(0, 90, c=1
            ,vmax=self.vmax
            ,vmin=self.vmin
            #,cmap='coolwarm'
            , norm = LogNorm()
            ,transform=ccrs.PlateCarree(),edgecolor='none',s=0.6)
        cbar=plt.colorbar(self.scat2)
        cbar.set_label('Melt water Flux (mm/yr)')
        
        #Adding my own Antarctica graphics
        drawAntarctica(self.ax)
        
        #Animation definitio
        anim = animation.FuncAnimation(self.fig, self.update,np.arange(stepNum), interval=1, blit=False)
        
        #Writer FFMpeg needed for mp4 film
        mywriter = animation.FFMpegWriter()
        anim.save(animationname, writer=mywriter,fps=1, extra_args=['-vcodec', 'libx264'])
        
    #No init_plot needed
    def setup_plot(self):


    
    # animation function to be called nsteps times plus the initialisation since init_func isn't defined
    def update(self,i):
        t=i
        print t,'setup'
        
        #Index of the dat to be extrated
        index=np.where(self.MassVar[t,:,:]*(self.latVar[:,:]<-30)!=0.)
        #Remove previous data plotted
        self.scat2.remove()
        #The plot with Logaritmic scale
        self.scat2=self.ax.scatter(self.lonVar[index], self.latVar[index], c=self.MassVar[t,index[0],index[1]]
            ,vmax=self.vmax
            ,vmin=self.vmin
            #,cmap='coolwarm'
            , norm = LogNorm()
            ,transform=ccrs.PlateCarree(),edgecolor='none',s=0.6)
        
        return self.scat2,    

    
    def show(self):
        plt.show()
```

######In this example, `draAntarctica(ax)` is only called once, doing the animation script much lighter

<iframe width="420" height="315" src="files/animationIcebergsMonth.mp4" frameborder="0" allowfullscreen></iframe>

