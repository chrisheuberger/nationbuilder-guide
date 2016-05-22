---
layout: article
title: Web Development in NationBuilder
date: 2016-03-30
modified:
excerpt: Principles and Best Practices for Developing Websites with NationBuilder
image: 
  feature:
  teaser: preview-website.png
  thumb: preview-website.png
ads: false
---

![NationBuilder guide](../images/website-hero.png){: class="hero"}

## Developing a Website on NationBuilder

{% include toc.html %}

# For Loops and Conditionals

In this installment of our NationBuilder series, we’re going over for the two primary methods for adding logic using Liquid to a NationBuilder theme: **for loops** and **conditionals**.

![code sweater](../images/preview-community.png)

## Conditionals

Let’s begin with a few basic if statements.

{% highlight html %}{% raw %}
{% if user.name == 'Mika' }
  Hello Mika
{% elsif user.name == 'Kygo' }
  Hello Kygo
{% endif }
{% endraw %}{% endhighlight %}

or

{% highlight html %}{% raw %}
{% if request.current_user.is_volunteer? == true %}
  Thank you for volunteering!
{% else %}
  Will you volunteer?
{% endif %}
{% endraw %}{% endhighlight %}

{% highlight html %}{% raw %}
{% if request.current_user.is_volunteer? == true %}
  Thank you for volunteering!
{% else %}
  Will you volunteer?
{% endif %}
{% endraw %}{% endhighlight %}

Both of these examples use a Liquid object to determine something about the user and display different content based on that. Building on that, let’s see how to target a user’s membership type using conditionals.

For example, to make certain pages (or parts of pages) only viewable to certain membership types, first set up [membership types](http://nationbuilder.com/create_membership_types){:target="_blank"} in your nation. Next, add a member type in the NationBuilder control panel called Sustaining. Lastly, wrap your page content inside Liquid conditionals that check for membership levels or tags—or otherwise hide the content—like this:

{% highlight html %}{% raw %}
{% if request.signup.membership_level_name contains "sustaining" %}
  <!-- Paying Member Content -->
{% else %}
  <!-- Non Paying Member Content -->
{% endif %}
{% endraw %}{% endhighlight %}

![for loops](../images/forloop.png)

## For Loops

For loops are great for quickly targeting a group of pages or posts.

**For Loop Example 1:**

You could use a for loop to access a page's children with the the page.children object.

So this would go in the parent page’s template:

{% highlight html %}{% raw %}
{% for page in page.children %}
  {{ page.slug }}
{% endfor %}
{% endraw %}{% endhighlight %}

And that would list the slug of each subpage.

**For Loop Example 2:**

Images that are uploaded to the Files section of a page (attachments) are accessible in a for loop this way:

{% highlight html %}{% raw %}
{% for attachment in page.attachments limit: 10 %}
  <img src="{{ attachment.url }}" />
{% endfor %}
{% endraw %}{% endhighlight %}

The above code could go in a partial **named _gallery.html** and if a page had <code>{% raw %}{% include "gallery" %}{% endraw %}</code>, it would display all the images that have been uploaded to that page.

**For Loop Example 3:**

If you want to cycle through iterations where the tag issue is used and show the content only on the homepage, you could put this in **layout.html**:

{% highlight html %}{% raw %}
{% if page.is_homepage? == true %}
  {% tag "issue" with "homepage_content_block" %}
{% endif %}
{% endraw %}{% endhighlight %}

in **_homepage_content_block.html**:

{% highlight html %}{% raw %}
{% for page in tag.most_recent_published_pages_no_pagination %}
  <p>page slug: {{ page.slug }}<br></p>
  <p> page name: {{ page.name }}</p>
  <p> author%u2019s first name: {{ page.author.first_name }}</p>
{% endfor %}
{% endraw %}{% endhighlight %}

<code>most_recent_published_pages_no_pagination</code> will give you a list of the most recently updated 500 pages, and the tag prefix lets it access the <code>tag_slug</code> used in the original <code>{% raw %}{% tag %}{% endraw %}</code> call.

**For Loop Example 4:** Similarly, this is a for loop that will display an attachment image within a feed of pages with a certain tag:

{% highlight html %}{% raw %}
{% for page in tag.most_recent_published_pages limit: 3 %}
  <a href="{{ page.url }}"><img src="{{ page.attachments.first.url }}" /></a>
{% endfor %}
{% endraw %}{% endhighlight %}

So if the code above was in a partial named **_show_books.html**, than this line would go in a template file:

{% highlight html %}{% raw %}{% raw %}{% tag "books" with "show_books" %}{% endraw %}{% endhighlight %}

And that would display the first image from each of the 3 most recently published pages tagged with books.

## Let’s Bring Them Together

We’ll end with a simple example of an if statement inside a for loop:

{% highlight html %}{% raw %}
{% for child in page.children }
  {% if child.type_name == 'Question' }
    <li><a href="{{ child.url }}">{{ child.headline }}</a></li>
  {% endif }
{% endfor }
{% endraw %}{% endhighlight %}

This code would iterate over every subpage for a particular page and for any whose name contained the word “Question”, that page’s headline would be displayed as a list item link to the page.

When it comes to Liquid logic, keep it simple and in separated in clean chunks. If it helps, draw or write out your process in pseudo code. Think explicitly about what you want to end with and what you’re able to target to begin with. Pretty soon, you’ll find for loops and conditionals useful thinking tools for any problem.

# Images, Assets, and Subpages

In this, my fourth post on developing with NationBuilder, I’m going to cover some useful concepts and techniques for working with images and subpages. If you want to check out previous posts, there’s one on [getting started](http://radishlab.com/2015/10/a-practical-introduction-to-nationbuilder/){:target="_blank"}, [Liquid basics](http://radishlab.com/2015/12/introduction-nationbuilder-liquid-basics/){:target="_blank"}, and [Liquid logic](http://radishlab.com/2015/12/an-introduction-to-nationbuilder-for-loops-and-conditionals/){:target="_blank"}.

![images and assets](../images/assets.png)

## Images, Assets, and Scripts

All images or assets used for a specific page only should be uploaded to **Website > Template > Files** but any images or assets used for your overall theme should be uploaded to **Website > Theme > Files**. No path is necessary when referencing images or assets in your theme template:

{% highlight html %}{% raw %}
<img src="myfile.jpg" />
{% endraw %}{% endhighlight %}

{% highlight html %}{% raw %}
.example { background-image: url('myfile.jpg'); }
{% endraw %}{% endhighlight %}

Upload script files here too and then reference them at the bottom but inside the body tag in **layout.html**:

{% highlight html %}{% raw %}
<script type="text/javascript" src="fancyzoom.js"></script>
</body>
</html>
{% endraw %}{% endhighlight %}

If you need to link to an absolute URL, upload the file, then right click on it and choose **Copy Link Address**. That link will look something like this:

{% highlight html %}{% raw %}
https://nationslug.nationbuilder.com/assets/pages/1/filename.jpg
{% endraw %}{% endhighlight %}

If you are referencing an image or file within a `<script>` tag, upload the file to **Website > Theme > Files** and use the following code:

{% highlight html %}{% raw %}
{{ theme['myfile.jpg'] }}
{% endraw %}{% endhighlight %}

## Attachments vs Meta Images

An attachment is a file that is uploaded to the files section of a page. The meta image is defined under the **Social Media** section of the page settings tab, and is used primarily to define a meta image for use when sharing on Facebook, Twitter, etc. A page can have only one meta image, but can have multiple attachments.

[These snippets](http://nationbuilder.com/liquid_template_examples#imagefeed){:target="_blank"} can be used to display images from a page when you don’t know the filename but know that the files will be attachments on that page, which is why they’re housed in for loops.

![nb-subpages](../images/subpages.png)

## Subpages

When dealing with subpages, you can loop through them to display each one or elements from each one on the main parent page. You’d be accessing the <code>{% raw %}page.children{% endraw %}</code> object by means of a for loop. So you’d place this in the parent page’s template:

{% highlight html %}{% raw %}
{% for page in page.children %}
  {{ page.slug }}
{% endfor %}
{% endraw %}{% endhighlight %}

The above code would list the slug of each subpage.

**Subpage Example 1**

You could use the <code>{% raw %}{% subpage %}{% endraw %}</code> tag to access an attachment on another page. If the attachment is on **page1** and you want to call it on **page2**, put this line on **page2**:

{% highlight html %}{% raw %}
{% subpage "page1" with "partial_attachment_include" %}
{% endraw %}{% endhighlight %}

and then **_partial_attachment_include.html** would contain:

{% highlight html %}{% raw %}
{% for attachment in page.attachments %}
  {% if attachment.url contains "header" %}
    <img src="{{ attachment.url }}">
  {% endif %}
{% endfor %}
{% endraw %}{% endhighlight %}

In this case, **page2** would see the subpage command, run **page1** through the logic in the above HTML snippet, which would cycle through **page1**’s attachments, and only render if an **attachment.url** included **header**.

**Subpage Example 2**

If you have an image named **header.jpg** in the files section of a page with slug **page_a**, you could create a file named **_partial_image_call.html** which contained:

{% highlight html %}{% raw %}
<img src="{{ page['header.jpg'] }}" />
{% endraw %}{% endhighlight %}

and that would allow you to call that image from anywhere else by putting this line on any page you wanted the image to appear:

{% highlight html %}{% raw %}
{% subpage 'page_a' with 'partial_image_call' %}
{% endraw %}{% endhighlight %}

You can only use the line in that partial in conjunction with the **subpage** call but weirdly, the **subpage** tag does not require that a page be the child of another to work!

So that’s an overview on where to store images and assets on NationBuilder and how to call them up no matter what page you’re on. Check back for my next post on the People and Communication sections of the NationBuilder control panel and how they connect to your nation’s website.

# Signups, Forms, and Actions

![signup form](../images/signup.png)

Welcome to my sixth and final post on developing websites in NationBuilder. We've covered [getting started](http://radishlab.com/2015/10/a-practical-introduction-to-nationbuilder/){:target="_blank"}, [using Liquid](http://radishlab.com/2015/12/introduction-nationbuilder-liquid-basics/){:target="_blank"}, [applying logic and tags](http://radishlab.com/2015/12/an-introduction-to-nationbuilder-for-loops-and-conditionals/), [organizing assets and pages](http://radishlab.com/2016/01/an-introduction-to-nationbuilder-images-assets-and-subpages/), and [communicating with your users](http://radishlab.com/2016/01/introduction-nationbuilder-people-communication/){:target="_blank"}. But today, we're going to wrap it up with some tips and tricks around signing up members and enabling actions. That is, how do you allow your users to engage with _you_?

We’ll start in the NationBuilder Control Panel, where you can customize action settings for any action page type.

![nb-6-members](../images/members.png)

## Action Settings

Many NationBuilder page types allow for visitors to take an action like sign a petition, make a donation, or RSVP to an event. And for each action, there are settings you can control by going to **[Page name] > Settings > [Action] Settings**. So for a page with page type **Signup**, that tab would be labeled **Signup settings**. This is where you can do things like automatically add tags to any person who signs up on your site.

To add or remove a tag associated with a signup form, go to: **Signup settings > Radio buttons**. You can allow users to select multiple tags here: **Signup settings > Basics** in which case the **Radio buttons** section will be renamed **Checkboxes**. For more info, check out how to [create a new tag for a signup form](http://nationbuilder.com/how_to_signup#create){:target="_blank"}.

![unlock](../images/unlock.png)

## Data and Forms

Very often, you want a field in your form that NationBuilder doesn’t have as a default. To include such a field, you’ll need to do a bit of customizing.

The first step to customizing forms is knowing how to direct the data to the correct place. Data from forms are either stored in one of the already existing signup fields ([signup variables](http://nationbuilder.com/signup_variables){:target="_blank"}) or in [custom fields](http://nationbuilder.com/when_to_use_custom_fields){:target="_blank"}.

To allow users to signup for an email blast, for example, you would create a signup page (or use the existing join page) to control the settings (tag, path, followup, membership assignments, redirects after form submission, whether or not autoresponses are triggered, etc.), and then create a partial HTML template to control the form fields you’d be asking for. You’d associate the two via either the **subpage** or **include** Liquid tags.

**Signup Form Example:**

{% highlight html %}{% raw %}
{% include "signup_form" with site.signup_page %}
{% endraw %}{% endhighlight %}

This associates a partial HTML template (**_signup_form.html** in the theme files section) with the site’s default signup page. So **_signup_form.html** would then contain:

{% highlight html %}{% raw %}
{% if request.logged_in? %}
  {% elsif request.sorta_logged_in? %}
    <div class="box">
      Hi <strong>{{ request.current_signup.name_or_email }}.</strong>
      <br/>
      <div class="social">
        Sign in with <a href="{{ request.facebook_connect_url }}">Facebook</a>, <a href="{{ request.twitter_connect_url }}">Twitter</a> or <a href="/login">email</a>.
        <br/>
        Not {{ request.current_signup.name_or_email }}? <a href="{{ request.current_signup.logout_url }}">click here</a>.
      </div>
    </div>
  {% else %}
    <div class="email-signup form">
      {% form_for signup %}
        {% hidden_field_tag "page_id", page.id %}
        <div class="form-errors">{% error_messages_for signup %}</div>
        {% email_field "email", class:"text", placeholder:"Email address", required: "required" %}{% submit_tag "Join", class:"submit-button" %}
        <div class="form-submit"></div>
      {% endform_for %}
    </div>
  {% endif %}
{% endraw %}{% endhighlight %}

The above code first checks for login status; if the user isn’t logged-in, it would display only the email address field. Then, the form housed in this partial would be submitted through the associated page (default signup page in this case).

So there you have it: our six-part introductory development guide to help you harness the power of NationBuilder. Thanks for coming along with me!

If you have any questions or need some help with your site, just [reach out](mailto:chris@radishlab.com){:target="_blank"}, and we’d be glad to help. And in the meantime, may your theme always sync and may your Liquid never fail to render.
