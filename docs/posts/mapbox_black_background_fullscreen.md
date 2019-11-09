# Mapbox GL - Background Black in Fullscreen mode
Just a quick note, when styling a map for use with Mapbox gl we ran into this problem, the background appeared as intended 
when in the standard mode, but when switching to 'fullscreen' the background turned black. We are not sure of the internal Mapbox
gl reasons for this, however the cause was the use of an 'alpha' channel on the background. This issue will occur whether 
you use 'rgba' or 'hsla' for the  color definition. The weird part of this issue is not that the background appears black while
in  'fullscreen' mode but that it acts correctly in the normal mode. Additionally, if a background color is not set, any color
fills using an alpha channel will also turn black upon entering 'fullscreen' mode.
```
To avoid the black background follow these steps
1 - always set a backgound color
2 - always use and color definition without an 'alpha' channel for the background - example rgb(0, 0, 0)

note - If you set the background color as stated above, you will have no issues with using colors with 'alpha' channels 
with other layer color fills. If not, well . . . who knows what evils will result
```
