CS494 Extra-Credit
==================

In this guide, I will go over how to do the NYTimes extra credit assignment while
covering the basics of JS and web development best practices. If you follow along
you should be able to complete the assignment quite easily.

###Some html basics

Just in case some of you might not have known this, I will go over the basics of
setting up links and scripts in your html.

An HTML file has two main DOM elements, the `head` and the `body`. Think of this
as two sections in a document. The `head` is where you put elements that will not
be displayed, while the `body` consists of everything else.

```HTML
<html>

    <head>
        <!-- things that will not be displayed -->
    </head>
    <body>
        <!-- things that will be displayed -->
    </body>

</html>
```

The HTML file will always be read from the top down, procedurally. Best practices
states that you should keep all your `css` link tags and `Javascript` script tags
inside the `<head>` tag. This will make sure that the user does not see the page
until the essential css and scripts needed for the site are loaded already.

Sometimes `Javascript` needs to interact with the DOM ( the HTML elements ). For
these scripts, they must run after the DOM in the body as loaded. This can be done
using the `document.ready`, or `Jquery ready` functions ( will talk about this later )but we also usually still
put the tags at the end of body so that the user may see the page sooner.


```HTML
<html>
    <head>
        <!-- links and script tags to load before body -->
        <!-- these do not require the body to have finished loading -->
    </head>
    <body>
        <!-- your body elements -->

        <!-- script tags for scripts with DOM manipulation -->
        <!-- also scripts that are not essential, we can have them load after so that the user sees the page sooner -->
    </body>
</html>
```

Now let's setup our app. It will look something like this. Note that I will omit
`doctype` and `meta` tags.

```HTML
<html>
    <head>
        <!-- meta tags here -->

        <!-- here we add bootstrap, you may use a css framework like this if you like -->
        <link href="//netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap.min.css">
        <link href="href='//netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap-theme.min.css">

        <!-- here add css of plugins etc. We will add a datepicker that we got from github here just as an example -->
        <link href="css/datapicker3.css">

        <!-- our own stylesheets -->
        <!-- keep in mind that in HTML5, you don't need rel="stylesheet" -->
        <link href="css/main.css">


        <!-- some essential scripts, here we will add things like JQuery and bootstrap -->
        <!-- in HTML5 we don't need to specify type="javascript" -->
        <script src="http://code.jquery.com/jquery-1.11.0.min.js"></script>
        <script src="http://netdna.bootstrapcdn.com/bootstrap/3.1.1/js/bootstrap.min.js"></script>
        <script src="scripts/bootstrap-datepicker.js"></script>

    </head>

    <body>

        <!-- where all our displaying DOM elements will go -->



        <!-- script tag for our main app JS goes right before the end of body -->
        <script src="scripts/app.js"></script>
    </body>
</html>
```

Now that we've setup the HTML, all our code will go inside the `<body>` tag, right
before the `<script>` tag of our `app.js`.


###The main HTML elements

For this assignment, we need to create a NYTimes search app. The requirements are
very simple, we need to send a GET request to the NYTimes API. The only queries
requred are "start date" and "end date" and "search string". So for this, the
basic HTML elements we need is a form consisting of:

- `<input type="search">`
- `<input type="date">`
- `<input type="date">`

The problem with `type="date"` is that the browsers don't support it very well
since it's a very new HTML5 specification. Because of this, we will use
`bootstrap-datepicker`. find it on github <a href="https://github.com/eternicode/bootstrap-datepicker" target="_blank"> here</a>.
I will go over how to use it. It is very simple.

Using the bootstrap-datepicker and bootstrap, you can create your search elements like this.
Bootstrap applies styles by adding classes to elements, saving you the trouble of
writing a ton of the terrible CSS language.
Refer to the amazing bootstrap
<a href="http://getbootstrap.com/css" target="_blank"> documentation</a>
for how to use bootstrap elements.

```HTML
    <input class="form-control" type="search" placeholder="search term">
    <!-- form-control class is a bootstrap styling -->
    <!-- placeholder, is text that is shown when the input is first loaded and empty -->

    <input data-provide="datepicker">
    <!-- this will create a date picker with the bootstrap-datepicker plugin -->
    <!-- you can also initialize it with Javascript instead of data-provide -->
```

Remember you also need a button, or link to click on!

**Note that you will need to add other elements and styling on your own. These
are the basic elements to create the search and date inputs**


###Using Javascript

You should now have the basic HTML with the layout finished so we can use Javascript
to complete the app. I will first go over how to set up a basic structure for your
script.

We will use a function to keep our code organized. This function will act like a
class in other languages. It is basically a constructor. It will look something
like this.

```js
function App() {


    // here is where you define private variables that can only be accessed within the object
    // app = this, is so that we have a reference to the object when we are in other functions
    var app = this;
    // the URL to the API, which we can find on NYTimes dev website
    var URL = 'http://api.nytimes.com/svc/search/v2/articlesearch.json';
    var APIKEY = 'YOUR SECRET KEY HERE';

    // all your private functions are declared here just like regular functions.
    // their scope is only within this constructor, and is encapsulated.
    function something(){
    }

    // here you can declare public variables like this
    // but we will not require any for this app
    this.youCanGetThisVariableAnywhere = null;

    // your object methods are declared like this, these can be called after your
    // object is created. These are basically called public functions in other languages
    this.something = function() {
    };

}
```

We want to keep this object only containing app logic, not DOM logic. It will not
access, or manipulate the DOM. The DOM manipulation will go inside the JQuery ready
function, which runs after the DOM is loaded. This way we have nice modularization
and separation of concerns. And when you write the App function, you do not need
to think about the DOM, HTML, or css at all.

Here is the layout of our Javascript file.

```js
function App() {
    // application logic
}

// this is shorthand for $(document).ready(  )
$(function(){

    // create our app object with the App constructor
    var app = new App();

    // where we handle DOM manipulation using JQuery and our app object
});
```

###Writing the App function

Let's first write the App constructor function. This will be very short because
really all the app needs is to send a AJAX request.

Here is the finished result.

```js
function App() {

    var app = this;
    var URL = 'http://api.nytimes.com/svc/search/v2/articlesearch.json';
    var APIKEY = 'YOUR SECRET HERE';

    // this is the success function called when the AJAX request has
    // succeeded you can see that in the this.search function below
    // we use this success function
    function success(data) {
        // data is the data sent to us by NYTimes. However, the real data of
        // the articles is in data.response.docs, which is an array of
        // articles
        var response = data.response.docs;
        // we set the useful data to a variable and trigger a custom event
        // on the app object, passing, the useful data to be used by us later
        // to update the DOM.
        $(app).trigger('receive', [response]);
    }

    // this is a method(public function) which we will use in the other
    // section of our code to send a request to search for articles, when
    // the user clicks the button
    this.search = function(formData){
        formData['api-key'] = APIKEY;
        $.ajax({
            url: URL,
            jsonp: 'callback',
            data: formData,
            success: success,
            dataType: 'json'
        });
    };
}
```

This is really all you need for tha app logic. A few things to note here.

The JQuery trigger function will create an event, much like a click or key press.
If you have used the `$('somebutton').on('click')` function, you will be familiar with this.
Whenever trigger is run, an event is triggered, you can then do something at this
event anywhere else in your code with ` $(app).on('receive')`.

The `this.search` function has a parameter `formData`, this will be an object
which will be passed with the GET request to NYTimes. For example, a formData of
`{p: 'something', begin: 20140402, end_date: 20140505}` will create a string
after the URL like "p=something&begin_date=20140402&end_date=20140505". This is
much easier than creating a Query string ourselves. JQuery takes care of it for us
if we just pass an object to the data attribute in `$.ajax` method.

###DOM manipulations

Although we have our App basically working, we can't do anything with it since
we haven't connected it to the DOM. In this assignment, we basically only have to
do two things in the DOM manipulation section.

- Listen to search click -
Wrap search string, start date, and end date in an object then call app.search()
passing in the object.

- Listen to the receive event triggered by our app Object, which indicates that the
NYTimes document has been received -
Create DOM elements with the received data.

### listen to search click -> Wrap search data, then call app.search()

This is the easiest step, we can use a Jquery click handler to do this. 1, and 2
are essentially done together in the click handler.
```js

// search-btn is the id of the button
$('#search-btn').click(function(e) {
    // this prevents the default behavior of the button in case it is submit
    e.preventDefault();
    var date;
    // this is how you use bootstra-datepicker to get the date
    var startDate = $('#startDate').datepicker('getDate');
    var endDate = $('#endDate').datepicker('getDate');

    // note that variables beginning with $ were initialized before in the form
    // $('#searchText')
    // we want to make sure the search text isn't empty
    if ($searchText.val()) {
        // this is how we check that the dates are valid, and not empty
        // we are basically saying, if we use getDay() we should get back a number
        if (!isNaN(startDate.getDay()) && !isNaN(endDate.getDay())) {
            // here we create the data object
            // NYTimes wants us to give them a 'q' variable which is the
            // query string, a begin_date and end_date variable for dates
            data = {
                q: $searchText.val(),
                begin_date: formatDate(startDate),
                end_date: formatDate(endDate)
            };
        }
        else {
            // if dates are not valid or empty
            // we will just search string, with no dates
            // so create the data object with only q
            data = {
                q: $searchText.val();
            }
        }
        // now we can call the search method
        app.search(data);
    }
})
```

Note that we needed a formatDate function because Date is an object in javascript,
but to send it off to NYTimes, we need a string in the format YYYYMMDD.
Here is the function we can use.

```js
function formatDate(date) {

    // getFullYear, getMonth, and getDate are
    // Javascript core language functions defined
    // in the Javascript date object
    var year = date.getFullYear();
    var month = date.getMonth() + 1;
    var day = date.getDate();

    // NYTimes wants 05 for May not 5 so we format it
    if (month.toString().length === 1){
        month = '0' + month;
    }
    if (day.toString().length === 1){
        day = '0' + day;
    }
    return '' + year + month + day;
}
```

###Listen to receive event and show the documents received from NYTimes

We are at the final step, we have just created the necessary code to send AJAX
request to NYTimes when someone clicks the search button. Now we need to listen
to when the received event is triggered and show the data received.

Remember our `trigger('receive')` event? Now we can listen to that event and do
something with it.

```js
$(app).on('receive', function(e, response) {

    // remember that we sent the response as an array.
    // this is good because the data from NYTimes is an array of articles

    // with this we can loop through with the Javascript forEach function
    response.forEach(function(article){
        // article is the current article
        // article.headline.main is the current article's headline
        // article.web_url is the link to the current article
        // article.byline.original is the current article's author
        // article.lead_paragraph is the curreent article's leading paragraph
        // article.pub_date is published date
        // article.abstract is the abstract

    })

    // log out the response so you can see what's in there
    // for yourself! there's a lot more than just the ones
    // i've stated up in the forEach function.
    console.log(response);

})
```
I will leave this part up to you, It should not take too many lines of code.
You can use the article data anyway you wish but what I did was simply append it
onto the page and nothing fancy. Remember to check the Chrome Dev tools to see
the response data!

Good Luck
=========










