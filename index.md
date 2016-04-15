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

## Using **delayed_job** in Rails application

Add gem into gemfile:

{% highlight ruby %}
gem 'delayed_job'
gem 'delayed_job_active_record'
{% endhighlight%}

Create model and database table for delayed job:

{% highlight ruby %}
rails g delayed_job:active_record
rake db:migrate
{% endhighlight%}

## Using gem [**sidekiq**](https://github.com/mperham/sidekiq) to run long time jobs in background and [**sidekiq_status**](https://github.com/cryo28/sidekiq_status) to track job status
Jobs could be done using Worker in sidekiq. Example code:
Create a worker ruby file in **app/workers** folder

{% highlight ruby %}
class MyWorker
  include SidekiqStatus::Worker
  def perform(variable)
    do something...
  end
end
{% endhighlight%}

Note that `perform` is an instance method, whereas `perform_async` is called on the class.  
In controller:

{% highlight ruby %}
jid = MyWorker.perform_async(variable)
{% endhighlight%}

Note: To make any changes on the `perform` method or any other methods which are performed by sidekiq, the sidekiq server should be restarted to make the changes apply.

`jid` is the job id which is returned by the `perform_async` method. It can be used to track the job status.

{% highlight ruby %}
container = SidekiqStatus::Container.load(jid)
container.status       # => 'working'
container.at           # => 50
container.total        # => 200
container.pct_complete # => 25
container.message      # => 'Processing object #{50}'
{% endhighlight%}

## Compress multiple files to zip and providing downloading in Rails

{% highlight ruby %}
  require 'rubygems'
  require 'zip'
  m = params[:month].to_i
  if m > 8
    y_m = "#{Date::MONTHNAMES[m]} #{CurrentYear.first.year}"
  else
    y_m = "#{Date::MONTHNAMES[m]} #{CurrentYear.first.year+1}"
  end
  @paperclip_objects = xxx
  t = Tempfile.new('tmp-zip-' + request.remote_ip)
  Zip::OutputStream.open(t.path) do |zos|
    @paperclip_objects.each do |i|
      zos.put_next_entry(i.pdf_file_name)
      zos.print IO.read(i.pdf.path)
    end
  end
  send_file t.path, :type => "application/zip", :filename => "myzip.zip"
  t.close
{% endhighlight%}

Hint: `pdf` is the attachment field in the objects. This piece of code uses `Tempfile` so these files could be deleted when calling `close`.

## Pass parameters in `link_to`

{% highlight ruby %}
= link_to 'button', my_controller_path(a: 123, b: 321), class: 'btn'
{% endhighlight%}

In controller:
{% highlight ruby %}
@a_value = params[:a]
{% endhighlight%}











