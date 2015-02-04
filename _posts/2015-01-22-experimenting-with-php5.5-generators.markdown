---
layout: post
title: "Experimenting with PHP 5.5 Generators"
description: "Testing the memory advantages of using generators in PHP 5.5"
category: PHP
author: Dwayne Maye
tags: [PHP, PHP Optimizations, PHP 5.5, Generators]
---
One of the new features introduced in PHP 5.5 is the **generator syntax**.  The docs on php.net define it as:

<blockquote>
A generator function looks just like a normal function, except that instead of returning a value, a 
generator yields as many values as it needs to.
</blockquote>

As an example the following 2 functions would return the same results. 

{% highlight php %}
<?php
$people = [
    ['firstName' => 'Jon', 'lastName' => 'Doe'],
    ['firstName' => 'Jane', 'lastName' => 'Doe'],
    ['firstName' => 'Travis', 'lastName' => 'Doe'],
];

function arrayNames($people)
{
    $names = [];
    foreach ($people as $person) {
        $names[] = implode(' ', $person);
    }
    return $names;
}

function yieldNames($people)
{
    foreach ($people as $person) {
        yield implode(' ', $person);
    }
}

foreach (arrayNames($people) as $name) {
    echo $name . PHP_EOL;
}

foreach (yieldNames($people) as $name) {
    echo $name . PHP_EOL;
}
?>
{% endhighlight %}

## So yea, less code?

There you have it.  You can now write less code in all your iterations, and have all the free time in the world to tackle
 the important things in like like knitting...  But wait.  There has to be more to it then that.  
 
### There is and it's Memory Usage 

Among the benefits of using generators the one that stood out to me the most what the promise of decreased memory usage. 
Reducing the memory footprint helps free resources to serve more requests and squeeze the most out of your hardware.  For 
the purpose of this experiment I would like to put that to the test.  
   
## Testing it out

To check the difference in memory usage I wrote a simple test.  First I went to [http://www.json-generator.com/](http://www.json-generator.com/) 
and generated some sample json data.  Then I wrote a quick script that would allow me to test out the two approaches and 
see how much a difference there is.  Here is the gist of it:

{% gist immersegfx/61a93a282ec441381385 %}

### Results

The sample json data I used contained around 100 records and I ran the test 5 times each running PHP 5.5.9 on Ubuntu 14.04. 
**Note:** I didn't include the individual results because they all were the same.

#### Array test result
{% highlight Text %}
$ php TestYield.php array
Running [array] test
Start Memory Usage: 1087592
After test function: 1112744 Diff: 24.56 kb
................................................................
.....................................
After loop: 1113416 Diff: 25.22 kb
{% endhighlight %}

#### Yield test result
{% highlight Text %}
$ php TestYield.php yield
Running [yield] test
Start Memory Usage: 1087592
After test function: 1088800 Diff: 1.18 kb
.................................................................
....................................
After loop: 1088984 Diff: 1.36 kb
{% endhighlight %}

## Observations

### Dumping values
One thing I noticed in testing this out is that running <code>var_dump</code> or <code>print_r</code> on the yieldNames
function returns an empty **Generator Object**.  This may be cumbersome at some point when debugging as so far the only
means I have found to dump the content is to loop over the results.

### Using return
Returning any value from a generator will result in a compile error.  The recommendation from php.net is:
<blockquote>
A generator cannot return a value: doing so will result in a compile error. An empty return statement is 
valid syntax within a generator and it will terminate the generator. 
</blockquote>

## Conclusion

It does indeed appear that using PHP 5.5 generators has an impact on memory usage.  This test shows that using generators
consumes about 4% of the memory array does.  Now I'm sure YMMV and there could be other things that effect
these results but as an example it does look promising.  Once PHP 5.5 usage is more universal I would expect libraries like 
Zend Framework would benefit greatly from these optimizations.