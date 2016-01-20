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
> Cartopy Nemo and monthly data ...

> Do you want to deal with map coordinates projections in Python?

Cartopy is the best solutions I found to do so. The integration with Matplotlib library is perfect and almost all his plot features remains as easy as working with cartesian coordinates.

In this example I want to show how to easily produce a .mp4 video with some ORCA025 NEMO Ocean Model results. 

```python

class animationGeneral(object):
# First set up the figure, the axis, and the plot element we want to animate
    def __init__(self, champ, stepNum,lon,lat,vmin,vmax,animationname):
        self.drawCMP=True
        self.MassVar=champ
        self.lonVar=lon
        self.latVar=lat
        self.t=0
        self.vmin=vmin
        self.vmax=vmax
        self.fig = plt.figure()
        #anim = animation.FuncAnimation(self.fig, self.update,np.arange(0,stepNum), init_func=self.setup_plot, interval=1, blit=True)
        anim = animation.FuncAnimation(self.fig, self.update,np.arange(stepNum), interval=1, blit=False)
        mywriter = animation.FFMpegWriter()
        anim.save(animationname, writer=mywriter,fps=1, extra_args=['-vcodec', 'libx264'])
        
    
    
    
    
    def setup_plot(self):
    
    # animation function.  This is called sequentially
    def update(self,i):
        t=i
        print t,'setup'
        self.fig.clear()
        ax = plt.axes(projection=ccrs.SouthPolarStereo())
        ax.set_extent([-200, 200, -45, -45],ccrs.PlateCarree())
        ax.coastlines()
        index=np.where(self.MassVar[t,:,:]*(self.latVar[:,:]<-30)!=0.)
        scat2=ax.scatter(self.lonVar[index], self.latVar[index], c=self.MassVar[t,index[0],index[1]]
            ,vmax=self.vmax
            ,vmin=self.vmin
            , norm = LogNorm()
            ,transform=ccrs.PlateCarree()
            ,edgecolor='none',s=0.6)
        cbar=plt.colorbar(scat2)
        cbar.set_label('hola')
        return scat2,

    def show(self):
        plt.show()

if __name__ == '__main__':
    
    ncfile = netCDF4.Dataset('/Users/imerino/Documents/These/python/Files/mesh_hgr.nc','a')
    lonVar= np.array(ncfile.variables['nav_lon'])[0:300,:]
    latVar= np.array(ncfile.variables['nav_lat'])[0:300,:]
    e1tVar= np.array(ncfile.variables['e1t'])[:,0:300,:]
    e2tVar= np.array(ncfile.variables['e2t'])[:,0:300,:]
    ncfile.close()
    
    path='/Users/imerino/Documents/These/Results/ClimatoICB/climatoMonthGNM016_1998-2009.nc'
    ncfile = netCDF4.Dataset(path,'a')
    RunoffVar = np.array(ncfile.variables['Melt'])[:,0:300,:]*3600*24 #Kg/s-->mm/day
    ncfile.close()
    
    ChampVar=RunoffVar
    animationname=path+'/test.mp4'
    a = animationGeneral(ChampVar,12,lonVar,latVar,-1,1,animationname)
    #a.show()
```



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
