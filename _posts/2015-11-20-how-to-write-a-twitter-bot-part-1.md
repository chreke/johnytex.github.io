---
layout: post
title:  "How To Write A Twitter Bot In 5 Minutes"
date:   2015-11-20 11:00:00
categories: tutorial
---

Have you ever wanted to write your own Twitter bot? I did too, but for
some reason I had the notion that it would be really complicated.

However, it turns out I was wrong; using Python and the Twitter API you can go
from zero to "Hello world!" in just five minutes. Read on and I'll show you
how.

Register our App
----------------

<!--
Twitter requires two kinds of authentication before we can start
posting on someone's timeline:

 * A set of API keys that gives us permission to access the Twitter
   API.
 * An authentication token that gives us permission to interact with
   Twitter on behalf of a certain user.

To get the first set of credentials we need to register a Twitter app.
Go to https://apps.twitter.com and click the "Create New App" button.
-->

In order to get access to the Twitter API we must first register an
app. Log in to Twitter, go to [https://apps.twitter.com][apps] and
click the "Create New App" button.

You'll be prompted for a name, description and website URL - as long
as you don't plan on doing anything serious with your app you can put
placeholder information here for the time being.

The "Callback URL" field is only relevant if you have a web service
which handles user authentication, so skip that for now.

When you've filled out the necessary fields, click "Create Your
Twitter Application". You'll be taken to an overview page with
information about your app.

Next, click the "Keys and Access Tokens" tab - here you'll find your
**Consumer Key** and **Consumer Secret** keys, which we'll use in the
next step.

Setup
-----

Remember that one of the greatest virtues of a programmer is
[laziness][laziness]! To that end, we'll use a library called
[Tweepy][tweepy] to interact with the Twitter API.

Tweepy can be installed via [pip][pip], like so:

    pip install tweepy

Let's see if it works - open up a new Python prompt and type this:

{% highlight python %}
import tweepy

# Substitute these with the Consumer Key / Secret you were given in
# the previous step:
CONSUMER_TOKEN = 'Your consumer token'
CONSUMER_SECRET = 'Your consumer secret'

auth = tweepy.OAuthHandler(CONSUMER_TOKEN, CONSUMER_SECRET)
print(auth.get_authorization_url())

{% endhighlight %}

If everything worked as expected the program should now print an
**authorization URL**, which we'll use shortly.

Who Are You?
------------

Twitter apps use [OAuth][oauth] for authentication, which allows an
app to act on a user's behalf without the user having to share her
password. Instead, the app uses an **access token** to authenticate
with the API.

Before we can get the token we must get permission from the user, which is what
the authorization URL is for. Open the authorization URL you got in the
previous stepg and grant the app access to your account. 

After granting the app access, you'll be given a **verification
code**, which we can trade for an OAuth token. Copy the verification
code, then go back to the Python shell and enter the following
commands:

{% highlight python %}
# Substitute this with your verification code:
VERIFICATION_CODE = 'Your verification code'
oauth_key, oauth_secret = auth.get_access_token(VERIFICATION_CODE)

print(oauth_key)
print(oauth_secret)
{% endhighlight %}

The OAuth token consists of a key and a secret, both of which are
valid forever or until you revoke the app's access. In the next step,
we'll use the key and secret to authenticate with the API, so write
them down.

Time to Post
------------

Now we have everything we need to start posting! Create a new Python
script with the following lines:

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

Try running the script; it should post "Hello world!" on your
timeline. Go to Twitter and admire your handiwork---you've created
your first bot!

Getting Sophisticated
---------------------

You would probably lose a lot of followers if all you ever posted was
"Hello world!", so let's mix things up a bit.

Since everyone loves famous quotes I've compiled a list of
tweet-length nuggets of wisdom from [Wikiquote][wikiquote] and put
them in a plaintext file. To use them, download [quotes.txt][quotes]
and save it in the same directory as your Python script.

In order to be able to pick a random quote we need to read the quotes
into a list. Since each quote is on its own line, all we need to do is
to open the file and use the `list()` function:

{% highlight python %}
with open('quotes.txt') as f:
    quotes = list(f)
{% endhighlight %}

To make selecting quotes easier we can use Python's `random` module.
Put this at the start of the script:

{% highlight python %}
import random
{% endhighlight %}

Instead of "Hello world!" we can now use `random.choice` to pick
a random quote from the list:

{% highlight python %}
api.update_status(random.choice(quotes))
{% endhighlight %}


[apps]: https://apps.twitter.com
[laziness]: http://c2.com/cgi/wiki?LazinessImpatienceHubris
[tweepy]: http://www.tweepy.org/
[pip]: https://docs.python.org/2.7/installing/
[wikiquote]: https://en.wikiquote.org
[oauth]: https://en.wikipedia.org/wiki/OAuth
[quotes]: https://gist.githubusercontent.com/johnytex/5e6d900ac2bc0ef282fd/raw/181f9df9c03baf460e431964b40bd7f896dd142c/quotes.txt
