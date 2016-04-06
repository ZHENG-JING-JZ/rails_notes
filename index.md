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

## Use gem **combine_pdf** to combine pdf files

After using Prawn to generate two pdf files, I used gem [**combine_pdf**](https://github.com/boazsegev/combine_pdf) to combine them together.

The simplest example:

{% highlight ruby %}
pdf1 = Invoice.new(student, class_sessions)
pdf2 = Receipt.new(student, class_sessions)
pdf = CombinePDF.new
pdf << CombinePDF.parse(pdf1.render)
pdf << CombinePDF.parse(pdf2.render)
{% endhighlight%}

To save the file:

{% highlight ruby %}
pdf.save "combine.pdf"
{% endhighlight%}

To display the file in browser:

{% highlight ruby %}
respond_to do |format|
  format.pdf do
    send_data pdf.to_pdf, :filename => "combine.pdf", :type => "application/pdf", :disposition => "inline"
  end
end
{% endhighlight%}

hint: When I use `send_data pdf, :filename => "combine.pdf", :type => "application/pdf", :disposition => "inline"`, the browser gives **error loading pdf file**. The reason is, pdf is the object, while pdf.to_pdf is the PDF file stream that could be displayed.