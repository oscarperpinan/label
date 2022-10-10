# Label placement with spplot and lattice

This repository contains functions and methods for label placement with spplot and lattice:

- Point labels: `panel.pointLabel`, `sp.pointLabel`.
- Line labels: `lineLabel`, `sp.lineLabel`, `label`.

They have been included in the `maptools` package, which is to be archived.

## Usage

### Point labels
```
library(lattice)
library(RColorBrewer)
library(sp)

n <- 15
x <- rnorm(n)*10
y <- rnorm(n)*10
labels <- as.character(round(x, 5))


myTheme <- list(add.text=list(
                  cex=0.7,
                  col='midnightblue',
                  fontface=2,
                  fontfamily='mono'))
library(lattice)
xyplot(y~x,
       labels=labels,
       par.settings=myTheme, 
       panel=function(x, y, labels, ...){
         panel.xyplot(x, y, ...)
         panel.pointLabel(x, y, labels=labels, ...)
       })



data(meuse.grid)
coordinates(meuse.grid) = ~x+y
proj4string(meuse.grid) <- CRS("+init=epsg:28992")
gridded(meuse.grid) = TRUE


pts <- spsample(meuse.grid, n=15, type="random")

Rauthors <- readLines(file.path(R.home("doc"), "AUTHORS"))[9:28]
someAuthors <- Rauthors[seq_along(pts)]

sl1 <- list('sp.points', pts, pch=19, cex=.8, col='midnightblue')
sl2 <- list('sp.pointLabel', pts, label=someAuthors,
            cex=0.7, col='midnightblue',
            fontfamily='Palatino')

myCols <- adjustcolor(colorRampPalette(brewer.pal(n=9, 'Reds'))(100), .85)
spplot(meuse.grid["dist"], col.regions=myCols, sp.layout=list(sl1, sl2))
```

### Line labels
```
library(lattice)
library(RColorBrewer)
library(sp)

data(meuse.grid)
coordinates(meuse.grid) = ~x+y
proj4string(meuse.grid) <- CRS("+init=epsg:28992")
gridded(meuse.grid) = TRUE

data(meuse)
coordinates(meuse) = ~x+y
data(meuse.riv)
meuse.sl <- SpatialLines(list(Lines(list(Line(meuse.riv)), "1")))

myCols <- adjustcolor(colorRampPalette(brewer.pal(n=9, 'Reds'))(100), .85)

labs <- label(meuse.sl, 'Meuse River')

## Maximum depth
sl1 <- list('sp.lineLabel', meuse.sl, label=labs,
            position='below', textloc='maxDepth',
            spar=.2,
            col='darkblue', cex=1,
            fontfamily='Palatino',
            fontface=2)

spplot(meuse.grid["dist"],
       col.regions=myCols, 
       sp.layout = sl1)

## Constant slope
sl2 <- modifyList(sl1, list(textloc = 'constantSlope')) ## Default

spplot(meuse.grid["dist"],
       col.regions=myCols, 
       sp.layout = sl2)

## Location defined by its numeric index
sl3 <- modifyList(sl1, list(textloc = 140, position='above'))

spplot(meuse.grid["dist"],
       col.regions=myCols, 
       sp.layout = sl3)
```

