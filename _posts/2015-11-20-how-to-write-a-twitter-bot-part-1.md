---
layout: post
title:  "How To Write A Twitter Bot"
date:   2015-11-20 11:00:00
categories: tutorial
---

Ever wanted to write your own Twitter bot? 

In this tutorial I'll show you how to write a Twitter bot that will
post a random quote in your feed when you run it. Impress your friends
with your wisdom while learning how the Twitter API works - what could
be better?

Register our App
----------------

Twitter requires two kinds of authentication before we can start
posting on someone's timeline:

 * A set of API keys that gives us permission to access the Twitter
   API.
 * An authentication token that gives us permission to interact with
   Twitter on behalf of a certain user.

To get the first set of credentials we need to register a Twitter app.
Go to https://apps.twitter.com and click the "Create New App" button.

You'll be prompted to fill in a name, description and website URL for
your app - as long as you don't plan on doing anything serious with
your app you can put placeholder information here for the time being.

The "Callback URL" field is used if you have a web service which you
can use for user authentication, but we'll leave that blank for now.

When you've filled out the necessary information, click "Create Your
Twitter Application". You'll be taken to an overview of your
application.

Next, click the "Keys and Access Tokens" tab - here you'll find your
"Consumer Key" and "Consumer Secret" keys, which you'll need later.

That's it! Now we can move on to the actual code.

Installing Tweepy
-----------------

Remember that one of the [greatest virtues][laziness] of a programmer
is laziness! To that end, we'll use a library called [Tweepy][tweepy]
to help us interact with the Twitter API, which will save us a lot of
work.

Tweepy can be installed via pip, like so:

    pip install tweepy

Let's see if it works - create a new Python file and type in the
following:

{% highlight python %}
import tweepy

# Substitute these with the Consumer Key / Secret you were given in
# the previous step:
CONSUMER_TOKEN = 'Your consumer token'
CONSUMER_SECRET = 'Your consumer secret'

auth = tweepy.OAuthHandler(CONSUMER_TOKEN, CONSUMER_SECRET)
print(auth.get_authorization_url())

{% endhighlight %}

If everything worked as expected the program should now print an URL.

Who Are You?
------------

Twitter uses OAuth for authentication, which allows an app to act on
a user's behalf without having to share her password. Instead of the
user's password, we'll be given an *authentication token* that we can
use to log in to the Twitter API.

However, before we get the token the user must grant us permission to
use her account, which is what the authorization URL is used for.
After the user has approved our app she'll get a *verification code*
which we can exchange for an OAuth token.

The following lines will ask for the user's verification code and
fetch an OAuth token from the Twitter API:

{% highlight python %}
verification_code = raw_input('Enter verification code: ')
oauth_key, oauth_secret = auth.get_access_token(verification_code)
{% endhighlight %}

The OAuth token consists of a key and a secret, both of which are
valid forever, as the user doesn't revoke our access. That means if we
save the token the user won't have to log in every time she wants to
use the app.

However, let's settle for printing the token for now:

{% highlight python %}
print(oauth_key)
print(oauth_secret)
{% endhighlight %}

Run the script again. When it prints the authorization URL, open the
it in a web browser. You'll be asked to grant the app some
permissions. After you've done that, you'll get the verification code
which you can enter into the app.

If everything worked as expected the app should now spit out the
values for `OAUTH_KEY` and `OAUTH_SECRET`. Make sure to write them
down - you'll need them later!

Time to Post
------------

Now we have everything we need to start posting! Create a new Python
and enter the following lines:

{% highlight python %}
import tweepy

CONSUMER_TOKEN = 'Your consumer token'
CONSUMER_SECRET = 'Your consumer secret'

OAUTH_KEY = 'Your OAuth key'
OAUTH_SECRET = 'Your OAuth secret'

auth = tweepy.OAuthHandler(CONSUMER_TOKEN, CONSUMER_SECRET)
auth.set_access_token(OAUTH_KEY, OAUTH_SECRET)
api = tweepy.API(auth)
api.update_status('Hello world!')
{% endhighlight %}

If everything works as expected, the program should post "Hello
world!" to your timeline. Congratulations, you've created your first
Twitter bot!

Getting Sophisticated
---------------------

You would probably lose a lot of followers if all you ever posted was
"Hello world!", so let's mix things up a little.

To make things easier I've compiled a list of tweet-length nuggets of
wisdom from [Wikiquote][wikiquote] and put them in a plaintext file:
(link here) Download it and put it in the same directory as your
Python script.

We'll start by creating a list of quotes. Each quote is on its own
line, so all we have to do is read the file into a list of lines:

{% highlight python %}
with open('quotes.txt') as f:
    quotes = list(f)
{% endhighlight %}

To make things easier we can use Python's `random` module. Put this at
the start of the script:

{% highlight python %}
import random
{% endhighlight %}

Now we can use `random.choice` to pick a random quote from the list:

{% highlight python %}
api.update_status(random.choice(quotes))
{% endhighlight %}

[laziness]: http://c2.com/cgi/wiki?LazinessImpatienceHubris
[tweepy]: http://www.tweepy.org/
[pip]: https://docs.python.org/2.7/installing/
[wikiquote]: https://en.wikiquote.org
