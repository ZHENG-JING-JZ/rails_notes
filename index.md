---
layout: default
---


## Accessing rails scope objects:

{% highlight ruby %}
['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday', 'sunday'].each do |wkd|
  @class_sessions.send(wkd)
end
{% endhighlight %}

## Kill rails process in cmd

	```
	lsof -wni tcp:3000
	```

This will return PID number and other information of the process

	```
	kill -9 PID
	```

## Use Prawn gem to generate pdf

Draw a dash line:

{% highlight ruby %}
dash(4, :space => 4, :phase => 1)
stroke_horizontal_line 0, 500
{% endhighlight %}

 Change back to solid line:

{% highlight ruby %}
undash()
{% endhighlight %}

then

{% highlight ruby %}
stroke_horizontal_line 0, 500
{% endhighlight %}

  	Setting dash line will result in dash lines in following tables. Without using `undash()`, the following tables will have dash lines as well.