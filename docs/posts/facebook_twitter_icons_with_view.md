# Using Facebook and Twitter icons with Vue
Update - Okay here is another odd twist. In the above discussion, we talked about adding FontAwesome icons from the 
'free-solid-svg-icons' collection, and we basically worked out all the kinks in that process. However, there are other icons
out in the wilds of FontAwesome land that are 'free' to use, but are not in the 'free-solid-svg-icons' package that we 
previously imported. A good example of these are the ones typically used for links to Facebook and Twitter. If you look in 
the 'free-solid-svg-icons' package, you won't find them, because they are in a different package called 'free-brands-svg-icons'
that needs to be imported with NPM in a separate process.
```
npm i --save @fortawesome/free-brands-svg-icons
```
Once you have the package imported you can use the icons with the same steps as mentioned above, as far as going to 'main.js'
and importing the individual icons and adding them to fontAwesome 'library' object. For example . . .

```
// Font Awesome-related initialization
import { library } from '@fortawesome/fontawesome-svg-core'
import { faFacebook, faTwitter } from '@fortawesome/free-brands-svg-icons' // this are your new icons
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'

// Add the specific imported icons
library.add(faFacebook) // add them individually to the library object
library.add(faTwitter)

// Enable the FontAwesomeIcon component globally
Vue.component('font-awesome-icon', FontAwesomeIcon)
```
BUT! At this point things get weird. Previously, we would have gone to one of our components and added the icon in a pattern
something like this:
```
<template>
  <div class="icons">
    <font-awesome-icon icon="twitter"/> // BUT!! Don't 'cause this will fail
    <font-awesome-icon icon="facebook"/> // Same here, total FAIL
  </div>
</template>
```
So if we attempt to add the icons way we did in the earlier section they will not work and you will get and error like .  . .
```
[Error] Could not find one or more icon(s) (2)
{prefix: "fas", iconName: "twitter"}
{}
```
Okay, this is annoying, but as you may have surmised, it is caused by the icons coming from a different FontAwesome package
than in the previous example. The error notes that it seeking an icon with the prefix 'fas'. The prefix 'fas' stands for 
'Font Awesome Solid' which kinda makes sense for icons in the 'free-solid-svg-icons' package but not so much for those in 
'free-brands-svg-icons'. But why is it looking for the prefix 'fas'? We never told it to do that! Well, apparently
this is related to behind-the-scenes coding of the 'FontAwesomeIcon' component which, you may remember, we wired our imported
icon to in the last step of our work in 'main,js'.
```// Enable the FontAwesomeIcon component globally
Vue.component('font-awesome-icon', FontAwesomeIcon)
```
Inside the code of the FontAwesomeIcon component there is a section that basically defaults to the most common prefix, of 'fas', unless 
you specifically tell it to do otherwise. To keep things brief, here is the magic way to tell the FontAwesomeIcon component 
to use 'fab' rather than 'fas' ('fab' for Font Awesome Brands verses 'fas' for Font Awesome Solid). Note, you only need add the
extra bit of JSON to icons outside of the 'fas' package, since the component creation code will default to 'fas' as the
icon prefix.
```
<template>
  <div class="icons">
    <font-awesome-icon :icon="{ prefix: 'fab', iconName: 'twitter' }"/>
    <font-awesome-icon :icon="{ prefix: 'fab', iconName: 'facebook' }"/>
  </div>
</template>
```