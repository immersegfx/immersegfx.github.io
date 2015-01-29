---
layout: post
title: "Check for required PHP extensions with Composer"
description: "Using Composer to verify PHP extensions are installed"
category: composer
author: Dwayne Maye
tags: [PHP, Composer]
---
<section>
Thanks to creation the Composer PHP Dependency manager, using and managing 3rd party libraries has become a breeze.  Gone
 are the days of committing dependencies and manually keeping things up to date.  Additionally using Composer removes the 
 possibility of someone making changes to a 3rd party library and committing it...  Don't do it!
</section>

<section>
Recently I began adding Composer to some of my legacy projects and refactoring dependencies like PHPMailer over to 
Swiftmailer.  Also thanks to PHP 5.4 and its built in web server reformatting things to make development easier.  Throughout a 
projects lifecycle I may work on multiple different systems.  Maybe my laptop when working on-site or my own using Windows 
or Linux.  The awesomeness of being able to run <code>php composer.phar install</code> after updating my sources just makes
me smile.  
</section>

That brings me to today and preparing to deploy a new Linode instance.  After provisioning my new server, grabbing the 
sources and running the Composer install I sat back, put my feet up on the desk and thought life is just grand.

## Houston we have a problem

Roughly 30 minutes later I get a notification from NewRelic informing me that my error rate is at 10%, Doh!  So back to 
the server logs to see what's up and find.

{% highlight Text %}
PHP Fatal error:  Class 'Imagick' not found
{% endhighlight %}

Well that explains things.  Just need to install the PHP Imagick module and we are back in business.  But how can I make
sure this doesn't happen again?  I could configure a build system using Phing or Ant, or just write a bash build script
to automate it in the future.  Both would work abet be overkill for the scope of the project.  That's when I turned 
to composer and found information on [Platform packages](https://getcomposer.org/doc/02-libraries.md#platform-packages).

## Composer Platform Packages

<blockquote>
Composer has platform packages, which are virtual packages for things that are installed on the system but are not 
actually installable by Composer. This includes PHP itself, PHP extensions and some system libraries. 
</blockquote>

Woo hoo! That's exactly what I was looking for. To validate the availability of PHP extensions, all that is needed is to 
preface its name with ***ext-*** and add it to the ***requre*** section of the <code>package.json</code> file.  

### Example 
{% highlight Json %}
{
  "name": "composer/example",
  "require": {
    "php": ">=5.4.0",
    "swiftmailer/swiftmailer": "5.3.0",
    "ext-imagick": "*"
  }
}
{% endhighlight %}

**Note:** Notice the specified version is set to <strong>*</strong>.  The Composer docs state that:
<blockquote>
Versioning can be quite inconsistent here, so it's often a good idea to just set the constraint to *. 
</blockquote>

## Testing it out

Now to test I locally removed the imagick extension and ran composer again which returned:

{% highlight Text %}
Loading composer repositories with package information
Updating dependencies (including require-dev)
Your requirements could not be resolved to an installable set of packages.

  Problem 1
    - The requested PHP extension ext-imagick * is missing from your system.
{% endhighlight %}

## Conclusion

With that in place now I can track these dependencies in the future.  It would be nice to have it handle the installation
 of the missing extensions but at least knowing makes things a whole lot easier.  