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
#Cartopy Nemo and monthly data ...

## Do you want to deal with map coordinates projections in Python?

#### Cartopy is the best solutions I found to do so. The integration with Matplotlib library is perfect and almost all his plot features remains as easy as working with cartesian coordinates.

#### Some problems come through when we want to animate our NEMO results. In this post we'll try to deal with them in the asiest way
 
####The easiest way to produce animations with Python is using the animation module of Matplotlib. Hopefully the Python library Cartopy allow us to deal with geospatial coordinates using Matplotlib at the same time.
The basic idea of the animation module is yo create and update function to be called recursively by the animation generator. This update function produce a set of images that will be merged when the save method is called.
The difficult part is to clean the previously plotted data without the necessity to create new axes. This is specially interesting in my case because my axes are produced with a very heavy routine.
My initial idea (the most clean and elegant) was to create the axes once, and use the deepcopy method of the copy module at the interior of the update function. This would allow me to plot a new scatter plot every time the update function is called.
Sadly, deepcopy method crashes with axis objects:

```
NotImplementedError: TransformNode instances can not be copied. Consider using frozen() instead.
```

The second idea was to use the set_array and the set_offets methods of the scatter plot to update the plot data. Those methods those does not seems to be implemented yet with the cartopy interface, so they does not take the transformation argument. and the updated data cannot be projected.

Finally the best and quickest solution is to use the remove() method.

#### The main program just open the data with the shape (temporal, latitud, longitud). In this example I only need the 300 first latitude values. Temporal dimension is 12 (one per month).
#### Main looks as simple as this:

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

### The main routine basically open the data and create and object of the class animationGeneral. This object is the film with the given name.


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

#### Since The Antarctic continent is plotted using the very heavy routine draAntarctica, axes has to be defined out of the update function, so the routine will be called once.
the set_offsets and set_array matplotlib functions does not seems to work properly when using cartopy. The self.scat2.remove() call is the only way to erase the previously plotted data by wonserving the axis



> This is Markdown Cheatsheet for **MAD4Jekyll**, this Jekyll theme. Please check the raw content of this file for the markdown usage.

## Typography Elements

This is a paragraph. **This text is bolded.** This text is normal! _This text is italic._ We can  also **_combine_** them. A highlighted code looks like `ThisIsMyCode()`. This text is a [hyperlink](#) or [http://www.example.com](http://www.example.com).

___

## Headings H1 to H6

# H1 Heading

## H2 Heading

### H3 Heading

#### H4 Heading

##### H5 Heading

###### H6 Heading

___

## Footnote

If you have some text that you want to refer with a footnote, do as follows. This is an example for the footnote number one [^1]. Add more footnotes, with link. [^2]

___

## Blockquote

> The roots of education are bitter, but the fruit is sweet. --Aristotle

___

## List Items

1. First order list item
2. Second item

* Unordered list can use asterisks
- Or minuses
+ Or pluses

___

## Code Blocks

```javascript
var s = "JavaScript syntax highlighting";
alert(s);
```

```python
s = "Python syntax highlighting"
print s
```

```
No language indicated, so no syntax highlighting.
But let's throw in a <b>tag</b>.
```

___


## Table

### Table 1: With Alignment

| Tables        | for           | Markdown  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | ok? |
| col 2 is      | centered      |   Got it? |
| col 1 is | left-aligned      |    Alright!!! |

### Table 2: With Typography Elements

Another | table | here
--- | --- | ---
*I* | `am` | **row**
1 | two | III

___

## Horizontal Line

The HTML `<hr>` element is for creating a "thematic break" between paragraph-level elements. In markdown, you can create a `<hr>` with any of the following:

* `___`: three consecutive underscores
* `---`: three consecutive dashes
* `***`: three consecutive asterisks

renders to:

___

---

***


## Media

### YouTube Embedded Iframe

<iframe width="560" height="315" src="https://www.youtube.com/embed/OuoaKai_L00" frameborder="0" allowfullscreen></iframe>

### Image

![Spectrocat](http://octodex.github.com/images/spectrocat.png)


You also have the html option of adding an image in a post at a small size and the ability to click on it and zoom in a lightbox.

`<p style="float: left; font-size: 9pt; margin-right:1em;"> 
   <a href="{{ site.baseurl }}/img/blog/lb-lrg/img1.jpg" data-lightbox="gallery1" data-title="The first image" style="float: left; margin-right: -10%; margin-bottom: 1em;">
     <img src="{{ site.baseurl }}/img/blog/lb-sm/lbs01.png">Image#01</a></p>`

## For a more detailed syntax with Markdown, please visit [DaringFireball.net](http://daringfireball.net/projects/markdown/syntax)

###### Image Source: [UNSPLASH](https://unsplash.com/photos/6g0KJWnBhxg)


[^1]: Footnote number one.

[^2]: A footnote you can link to - [click here!](#)
