---
layout: default
---
<link rel="stylesheet" href="styles/default.css">
<script src="highlight.pack.js"></script>
<script>hljs.initHighlightingOnLoad();</script>
## Bootstrap [**DateTimePicker**](https://github.com/TrevorS/bootstrap3-datetimepicker-rails)

DateTimePicker can be used to display and select date and time. 

{% highlight haml %}
.span3= f.input :created_at, as: :string, 
  input_html: {value: @student.created_at? ? @student.created_at.to_s(:hk) : Time.now, class: 'datetimepicker'}
{% endhighlight%}

{% highlight javascript %}
$('.datetimepicker').datetimepicker({
      format: 'YYYY/MM/DD HH:mm',
      sideBySide: true,
      inline: true,
      locale: 'zh-tw'
});
{% endhighlight%}

This 'datetimepicker' gem is designed for bootstrap3. It raises javascript error `Uncaught Error: datetimepicker component should be placed within a relative positioned container`. To solve this issue, define one of its parent elements as a relative positioned container. For example:

{% highlight haml %}
.span3{style: 'position: relative'}
  = f.input :created_at, as: :string, input_html: {value: @student.created_at? ? @student.created_at.to_s(:localdb) : Time.now, class: 'datetimepicker'}
{% endhighlight%}

## Memory issue on staging server

After deployed to staging server, the project raised an error when uploading file using paperclip: `Errno::ENOMEM (Cannot allocate memory - file -b --mime '/tmp/d5243f00b80eed0af24ebac9046b66af20160429-30082-ynxdk2.pdf')`. This error has never occured in my development environment. The reason is, paperclip uses ImageMagick for image processing, gem `cocaine` is the way it invokes ImageMagick. It needs to use fork in linux system, which means if the parent process uses 500 MB memory, the child process will use exact the same size. This is widely used in Rails apps. It makes Rails cost more memory. To solve this problem, we can try using `gem 'posix-spawn'`. In my project, the error raises again after using this gem. We ended up updating memory of server. 

ref: [**blog**](http://blog.sundaycoding.com/blog/2014/02/05/fighting-paperclip-errno-enomem-error/)

## Starting `sidekiq` on staging server

`sidekiq` is a gem that can run jobs in background. It can be deployed using `gem 'capistrano-sidekiq'`. In our project, sidekiq is not started on server after each deploy. The reason is to be determined. One way to start sidekiq on server manually is:

{% highlight shell %}
bundle exec sidekiq -e staging -d -L log/sidekiq.log -C config/sidekiq.yml 
{% endhighlight%}

Meaning of parameters:  
`-d`, Daemonize process  
`-L`, path to writable logfile  
`-C`, path to YAML config file  
`-e`, Application environment  

## Facebook crawler

My task is to craw the page content of a Facebook page of a closed group. The input is the url of the group. After some research, this is what I have found.  
First of all, to access such a page in browser, we need to login with the Facebook username and password. Normal crawler gems like `Nokogiri` do not support login well. The best way to log into Facebook is to use [**Facebook Graph API**](https://developers.facebook.com/docs/graph-api). However, using Facebook API means we have to follow Facebook rules and cannot get information from a closed group. The solution to this task is to use `phantomjs` to simulate a browser programmatically. Here I choose [**`gem 'poltergeist'`**](https://github.com/teampoltergeist/poltergeist) and [**`gem 'capybara'`**](https://github.com/jnicklas/capybara). Below is the code:

Create a file 'setup-capybara.rb':
{% highlight ruby %}
# Require the gems
require 'capybara/poltergeist'

# Configure Poltergeist to not blow up on websites with js errors aka every website with js
# See more options at https://github.com/teampoltergeist/poltergeist#customization
Capybara.register_driver :poltergeist do |app|
  Capybara::Poltergeist::Driver.new(app, js_errors: false, timeout: 120)
end

# Configure Capybara to use Poltergeist as the driver
Capybara.default_driver = :poltergeist
{% endhighlight%}

Create another file 'test.rb':
{% highlight ruby %}
require './setup-capybara.rb'

url = 'https://www.facebook.com/groups/1468981113348561/'
browser = Capybara.current_session
browser.visit url
# browser.save_page
browser.fill_in 'email', with: '123@gmail.com'
browser.fill_in 'pass', with: '1234'
browser.click_on 'loginbutton'
# p browser.current_url
img = browser.find('.coverPhotoImg')
p img['src']
number = browser.find_by_id('count_text')
p number.text
{% endhighlight%}

This method is used to test website, so it can perfectly simulate a browser and all the actions like fill in form and click button. It can also get html elements by id, class or other selectors. A great online example is [**here**](http://tutorials.jumpstartlab.com/topics/scraping-with-capybara.html)

## Rails flash message
Rails flash message is delete after `redirect` not `render`, so sometimes it is still shown after another redirect.

## Use Rails console on staging server

To use Rails console like development environment, simply run `bundle exec rails c staging`. Note that this command needs to state which environment to run, like "staging", "production".

## Sidekiq settings

Sidekiq will by default retry a job if it fails. To change this setting, we need to run `rails console`:
{% highlight ruby %}
require 'sidekiq/api'
Sidekiq::RetrySet.new.size   # check the size of retry queue
Sidekiq::RetrySet.new.clear  # clear the retry queue
Sidekiq::Queue.new.size      # check the size of jobs queue
Sidekiq::Queeu.new.clear     # clear the jobs queue
{% endhighlight%}

## Operating Mysql on staging server

Access the remote server using SSH, then access mysql using command like `mysql -u root`. 

In mysql: 

* `show databases;` to list all the databases.

* `use mydb_name;` to change current database.

* `show tables;` to list all the tables in the current database.

* `drop database mydb_name;` to drop a database.

* `create database mydb_name;` to create a database.

* `source /path_to/mydump.sql` to import the sql file into current database.

* `ALTER DATABASE mydb_name CHARACTER SET utf8 COLLATE utf8_general_ci` before import data to avoid the error `Mysql2::Error: Illegal mix of collations (latin1_swedish_ci,IMPLICIT) and (utf8_general_ci,COERCIBLE)`