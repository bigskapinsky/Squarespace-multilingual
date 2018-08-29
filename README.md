# Squarespace-multilingual
> How to make your SquareSpace site multilingual in just a few minutes !

The concept is quite simple:
 1. The user clicks one of the language links, s/he is temporarily redirected to the "change-language" page
 2. The "change-language" page sets the requested language variable in a cookie.
 3. Each page runs a script that reads this variable and hides the languages that don't apply.

## Things you'll need:
 * A SquareSpace PRO subscription (to be able to inject code).
 * An HTML/Javascript editor. (never hurts to check your syntax.)
 * I recommend doing this *before* writing all the content in your website. You'll need to rewrite everything...
 
# A word of caution to this tale !
> Please note that this is a quickfix that only works on the front-end. It works well with simple websites that don't have much user input. I wouldn't recommend this method if you have an online shop with complex interactions. Also, even though it's quite simple, you're still dealing with code. It is therefore possible that your website may break! I can't be held responsible if that happens. **Proceed at your own risk!**

Now that that's out of the way...
 
## Getting started, The pages structure
 1. You'll need to add a blank page that isn't linked in your navigation. Don't add anything yet, just name it "Language change" with the URL Slug ```your-site.com/language-change```
 2. Next, you add links to the secondary navigation so it appears at the bottom (you can also add them at the top if you so wish).
 3. Each link has the address of the language change page, followed by a hash and a language code, for example:
   * ```/language-change#en```
   * ```/language-change#fr```
   * ```/language-change#de```
## Next, the code
 1. We need to add a bit of code to the "head" section of the page to load just as the content does. This code will look for the cookie, determine the language to display (or add it if there isn't one selected) and apply the style. In your SquareSpace editor, go to ```Settings > Advanced > Code Injection``` and add the following in the *head* section :
 
 ```html
<!-- Import JQuery -->
  <script
    src="https://code.jquery.com/jquery-3.3.1.min.js"
    integrity="sha256-FgpCb/KJQlLNfOu91ta32o/NMZxltwRo8QtmkMRdAu8="
    crossorigin="anonymous"></script>
<script>

//default language
var default_lang = "en";
function get_language_from_cookie(){

  //get the right variable (set the variable name to what you want)
  var name = "mysite_lang" + "=";

  var decodedCookie = decodeURIComponent(document.cookie);
  var ca = decodedCookie.split(';');
  for(var i = 0; i <ca.length; i++) {
    var c = ca[i];
    while (c.charAt(0) == ' ') {
      c = c.substring(1);
    }
    if (c.indexOf(name) == 0) {
      return c.substring(name.length, c.length);
    }
  }

  //set default to english

  set_cookie_lang(default_lang)
  return default_lang;
}

//this function takes a language code parameter and hides the others. 
//Set your languages here
//for this example, I used french, english, german, and italian
function hide_other_languages(lang) {
  var hide_langs = [];
  switch (lang) {
    case("fr"):
      hide_langs = ["en", "de", "it"];
      break;
    case("en"):
      hide_langs = ["fr", "de", "it"];
      break;
    case("de"):
      hide_langs = ["fr", "en", "it"];
      break;
    case("it"):
      hide_langs = ["fr", "en", "de"];
      break;
    default:
      //display english
      hide_other_languages(default_lang);
      return;
  }


  //prepare the css to hide unwanted languages
  var style_inner = "";
  hide_langs.forEach(function (l) {
    style_inner = style_inner + l + "{display:none}\n"
  });

  //add the css to the head of the document.
  $('head').append("<style type='text/css'>" + style_inner + "</style>");
}

//set the cookie language with a duration of one year.
function set_cookie_lang(lang){
  var d = new Date() + 1000 + 60 + 60 + 24 + 365;
  document.cookie = "sdb_lang=" + lang + "; expires=" + d + "; path=/";
}

//this code is executed when the page loads
$(function () {
                               
  //hide the other languages
  hide_other_languages(get_language_from_cookie());
                               
 //scroll to top (in case of back button)
  $(window).bind("pageshow", function() {
        $("html, body").animate({
           scrollTop: 0
        }, 200);
    });
});
</script>
 ```
 
 ## Now, the content
Now, sadly, we can't use SquareSpace's builtin blocks for this. Since we're dealing with custom tags, you have to write the website content using code blocks.
In the blocks, you write the content in several languages within different tags.
```html
<en>
  <h2>
    This is a title in english.
  </h2>
  <p>
    That is a paragraph in english
  </p>
</en>
<fr>
  <h2>
    Voici un titre en français
  </h2>
  <p>
    Voilà un paragraphe en français.
  </p>
</fr>
```
## The Final touch
Remember the page you created in step 1 ? Now's the time to add a final script there to work the magic.
When the page loads, the script will look at the url, determine which language the user clicked on, and set the variable in the cookie. Then, the user is redirected back to the page s/he came from.

In the page, add a *code block*, and add the following code:
```html
<script>
  var requested_lang = window.location.hash.substr(1);
  set_cookie_lang(requested_lang);
  window.history.back();
</script>
```

# And that's it !
Your links should now change the language, and since it's stored in the cookie, even if you close the browser and open it again, the language stays the same.

> //TODO : I still have to write some clean code to change the nav text and some dynamic text generated on the page due to said nav...
