# FontAwesome Icon (secret) Styling
Here is another interesting thing to know about using FontAwesome Icons as Vue components. When we wire up our icon as a 
Vue component, some more magic goes on in the background that adds style classes to the component. If you examine one of icons
with a browser's development tools you will see the element looks something like . . . 
```
<svg data-v-75bed3d5="" aria-hidden="true" focusable="false" data-prefix="fab" data-icon="twitter-square" role="img"
 xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512" class="svg-inline--fa fa-twitter-square fa-w-14">
<path data-v-75bed3d5="" fill="currentColor"></path></svg>
```
In the above snippet, notice that there is a 'class' attribute with three assigned classes: 1) svg-inline--fa 2) fa-twitter-square, and 3) fa-w-14. These classes are added to the component during creation and we can target any of
them with CSS styling code. These classes are basically 'secret' and don't show in the code; they are only visible with 
the developer tools in the browser. So it is important to know that they are there (in hiding) as other developers may 
have targeted these classes, leaving you to wonder what the heck is going on. 