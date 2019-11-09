# Add Font Awesome Icons
<a id="add_icons"></a>
So you want some cool icons, but do not have time to make them yourself. This is where Font Awesome comes in. Font Awesome
as a bunch of no free icons and also provides a Vue plugin to use them as mini Vue components.

To start we need to add three NPM packages to the project.

```
npm install --save @fortawesome/fontawesome-svg-core 
npm install --save @fortawesome/free-solid-svg-icons 
npm install --save @fortawesome/vue-fontawesome
``` 

Once these are installed, remember to stop and restart your Vue development server, if you had it running, so that it can pick up the changes.
 
Now turn your attention to 'main.js'. This is where we need to do our 'global' imports of NPM modules. If you did the 'makerspace-website-base'
tutorial, you will recall that this is where we imported the United States Web Design System (USWDS). We can do our imports
in any Vue component, but when we do the importing in 'main.js', we will have access to those imports in any Vue component in the 
project. Imports in other Vue components are only accessible from the component in which they were imported.

In 'main.js' we will first import the 'fontawesome' library. This will give us the 'library' object and allows us to 'add'
icons to it.
```
import { library } from '@fortawesome/fontawesome-svg-core'
```

Next we will import the icon we want to use. Again we could do this in a specific Vue component, but doing it here will
let us use the icon anywhere in the project. 
```
import { faLayerGroup } from '@fortawesome/free-solid-svg-icons'
```
The icon we are using is called 'layer-group', which is the image of a set of stacked layers, and I found it by browsing the Font Awesome Gallery (https://fontawesome.com/icons?d=gallery).
Now you may have noticed that I said the icon is called 'layer-group', but we are importing 'faLayerGroup'. You may also be asking yourself,
'what is up with that?' And if you are, that is a reasonable question. Honestly, I do not know the answer, but I can tell 
you how to find the information you need to make the icon names work in Vue.

Step one - find the icon you want to use in the gallery mentioned previously. Somewhere near the icon you want to use it will say something like . . .
```
<i class="fas fa-layer-group"></i>
```
In old fashioned HTML markup this is code is what we would use, but this is not the syntax we want to use to include the icon in Vue. However, we do want to make note of the name. In this case,
'fa-layer-group'. Notice how similar that is to both our import of 'faLayerGroup' and our icon name of 
'layer-group'. Most of the time it is probably possible to guess the information we need from the 'class' information, 
however we can dig deeper to be sure.

Step two - find the icon in the 'node_modules' folder

So when we did our NPM installs, that process added a folder called '@fortawesome' (yes,'fort' not 'font') to
the 'node-modules' folder. Inside this folder, there are (at least) three more folders, one for each of the 
NPM installs we did earlier. In the 'free-solid-svg-icons' folder we will find a bunch of TypeScript and JavaScript
files. These files have names like 'faAddressBook.js' and 'faAddressBook.ts'. At this point, you may have caught on to the fact that
these files have the same name (minus the file extension) as the one in our import statement 'import { faLayerGroup } from '@fortawesome/free-solid-svg-icons''

If you do not remember, here it is again . . .
```
import { faLayerGroup } from '@fortawesome/free-solid-svg-icons'
```
You can safely click on one of the '.js' files such as 'faLayerGroup.js'. When this file opens, you will see that it has
information about the layer-group icon. Specifically (among other things), it says 'var iconName = 'layer-group';'. So we now know, definitively, the name
of our icon, which we will use when we place the icon on the page. This also confirms that we have imported our
icon from the correct '@fortawesome' subfolder, in this case 'free-solid-svg-icons'.

Alright, cool. We finally have this import stuff straightened away and can now move on to getting our little icon to work.
Step one - activate the special powers of the 'vue-fontawesome' module we installed and then imported in previous steps.
This module allows us to use the Font Awesome icons as 'mini' Vue components. To get this to work, we must
first 'register' it as a Vue component and give it a name like so . . .
```
Vue.component('font-awesome-icon', FontAwesomeIcon)
```
This calls the Vue method to register the 'FontAwesomeIcon' model we imported and names it 'font-awesome-icon'. 
You can name it anything you like, but for consistency it is probably best to stick with 'font-awesome-icon'. This is 
name you will use later when the icon is added to our Vue template(s) as a Vue component--which in old 
HTML would have been called an 'element'.

Step two - add the icon to the 'library' object. Since we globally imported the FontAwesome 'library' object, we can now use it throughout the application.
We can add our icon to the 'library' object with this code.
```
library.add(faLayerGroup)
```
Sweet! Our importing and registering work in 'main.js' is done for now. If we want to add other Font Awesome icons
to our project later we just need to 1) import the specific icon from the 'node_modules' folder, and 2) add it to the 
'library' object. 

Now your 'main.js' should look something like . . .
```
import Vue from 'vue'
import App from './App.vue'
import uswds from 'uswds'

import { library } from '@fortawesome/fontawesome-svg-core'
import { faLayerGroup } from '@fortawesome/free-solid-svg-icons'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'

Vue.component('font-awesome-icon', FontAwesomeIcon)

library.add(faLayerGroup)

Vue.config.productionTip = false
Vue.use(uswds)

new Vue({
  render: h => h(App),
}).$mount('#app')
``` 

That was the hard part. Now we can go about merrily stuffing our new icon anywhere we like in our Vue project.
The process is this: 1) Go to the component where you would like to add the icon 2) In the 'template' element of that 
component add the icon as 'mini' Vue component like so . . .
```
<font-awesome-icon icon="layer-group" />
```
This is where the names we mentioned earlier come in to play. First, since we have registered 'font-awesome-icon' as
an Vue component, we can use it as HTML tag to create a Document Object Model (DOM) element. To this element, called
'font-awesome-icon', we will add the attribute 'layer-group', which as was also mentioned earlier is the name
of our icon. This tells Vue to stuff our 'layers-group' icon into the 'font-awesome-icon' element and display it on the web page.
If we were to add other Font Awesome icons, we could add them to the page using the same 'font-awesome-icon'
tag. The only change (assuming that you registered the new icons in 'main.js' (and I am sure you did)) is to change the name
of the 'icon' attribute to that of the new icon. For example . . .
```
<font-awesome-icon icon="layer-group" /> <!-- this adds the 'layer-group' icon -->
<font-awesome-icon icon="coffee" /> <!-- this adds the 'coffee' icon (if you registered it in 'main.js)' -->
```
Pretty straightforward, right? Well . . . now that you know the pattern it is.