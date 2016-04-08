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

Hint: When I use `send_data pdf, :filename => "combine.pdf", :type => "application/pdf", :disposition => "inline"`, the browser gives **error loading pdf file**. The reason is, pdf is the object, while pdf.to_pdf is the PDF file stream that could be displayed.

## Set indentation of Sublime Text 3

To set indentation to two spaces, we can simply click **view -> indentation** to set. But this can only change the setting for the current tab.  
To change the default setting, click **Sublime Text -> Preferences -> Settings Default**, this file is the default setting, users are not supposed to change it. We can copy its content, and paste it to **Sublime Text -> Preferences -> Settings User**. Find the indentation settings and change it:

```
"tab_size": 2,
"translate_tabs_to_spaces": true,
```

The setting sometimes does not work because it only works for chosen languages. For example, when I did this setting, I was opening a ruby file. After I saved the settings, I found it does not work on haml files. To make the setting apply to haml, first open an haml file, then click **Sublime Text -> Preferences -> Settings -> More -> Syntax Specific -> User**. Copy the above setting code into the file and save. Repeat this action if some other languages files need to be set.

## Set encoding while import csv files

The previous encoding code: 

{% highlight ruby %}
CSV.parse(csv,skip_blanks: true, headers:true, return_headers: false,encoding:"iso-8859-1").each do |row|
...
end
{% endhighlight%}

There are invalid characters in the csv file. We can replace these characters with "".

{% highlight ruby %}
CSV.parse(csv.encode!("utf-8", "iso-8859-1", invalid: :replace, replace: ''), headers: true, skip_blanks: true, return_headers: false).each do |row|
...
end
{% endhighlight%}















